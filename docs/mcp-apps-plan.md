# Baseline MCP Apps plan (Claude and ChatGPT)

Updated 2026-11-11 for the Claude MCP Apps release of 2026-11-10 (side panel, entry points, app tools). This plan is shared by this repo and `baseline-hq/baseline-chatgpt-plugin`.

## Where the work lives

The plugin repos only package things: a manifest, the connector URL and skills. The MCP App itself (tools, `ui://` resources, the widget HTML) is served by the Baseline API at `https://baseline.chaitanyabhagwat.com/api/mcp`, in `chaitb/baseline-health` under `apps/api/src/mcp`. One server change shows up in both Claude and ChatGPT, because both hosts read the same MCP Apps standard.

Today the server has tools and OAuth (with dynamic client registration) but no `ui://` resources, so neither host shows any Baseline UI yet.

## What changed on 2026-11-10

| Feature | What it does | How to use it | ChatGPT equivalent |
| - | - | - | - |
| Side panel | A view opens next to the conversation and stays open while the user chats | List `fullscreen` in `availableDisplayModes`. Call `app.requestDisplayMode({ mode: "fullscreen" })` from a click, or set `_meta.ui.preferredDisplayMode: "fullscreen"` on the resource to open there without a click | `{ type: "thread" }` entrypoint (conversation panel) |
| Global entry point | The app is listed on Claude's Artifacts page, opens without asking Claude, and can be pinned to the sidebar | `_meta["openai/ui"] = { entrypoints: [{ type: "global" }] }` plus `_meta.ui.resourceUri` on the tool | The same key. ChatGPT shows it as a sidebar app |
| App tools | Claude calls tools that the open view registers, so it can change what is on screen | `app.registerTool(...)` in the view before `connect()` (ext-apps 1.7+, zod 4.2+) | No direct match. ChatGPT views use `callServerTool` and `ui/message` |

Already available and still useful: `updateModelContext` (feed what the view shows into Claude's next turn), `sendMessage`, host style variables and fonts, transparent backgrounds with `prefersBorder: false`, and instance supersession for views that show one piece of state.

The side panel, entry points and app tools are limited to some organizations and need the unified Claude experience (chat and Cowork in one). Every view must keep working inline without them.

## The convergence

One tool definition serves both hosts:

```ts
registerAppTool(server, "baseline_today_open", {
  title: "Baseline Today",
  description: "Open today's Baseline diary: macros left, meals logged and the next planned meal.",
  inputSchema: z.object({ date: z.string().optional() }),
  _meta: {
    ui: { resourceUri: "ui://baseline/today" },
    "openai/ui": { entrypoints: [{ type: "global" }, { type: "thread" }] },
  },
}, handler);
```

- Claude: Artifacts page tile, pinnable, opens full screen on the web, and the side panel when Claude calls it in a conversation.
- ChatGPT: sidebar app (global) and conversation panel (thread).
- Codex: approved ChatGPT integrations ship as Codex plugins, so the same server and skills carry over.

## First app: Baseline Today

Why this one: it's the screen people open most in the iOS app, and it's the simplest of the six widgets from the September plan. It also fits all three new features.

- **View `ui://baseline/today`**: macro rings for what's left, today's meals by slot, and the next planned meal. Inline it's a compact card with at most 4 data points and 2 actions. In the side panel or full screen it shows the full diary for the day.
- **Server tool `baseline_today_open`**: inputs are optional and `{}` means today, as entry points require. It returns the day in `structuredContent` with a `{ createdAt, seq }` key for supersession.
- **App tools in the view**: `show_day` (switch date), `highlight_meal`. These change only what's on screen. Writes still go through the existing server tools (`food_log_add_item` etc.), so the user approves them as today.
- **`updateModelContext`**: send the visible day ("1,240 kcal left, protein 62 g short, dinner not logged") so Claude can answer "what should I eat for dinner?" without another call.
- **Display**: `availableDisplayModes: ["inline", "fullscreen"]`. Skip `preferredDisplayMode` at first, so the card doesn't take over the panel uninvited.
- **Style**: transparent background, host tokens for structure, Baseline green `#98C9A3` as the only accent, light and dark.

## Server settings to get right

- `_meta.ui.domain` for Claude: `4a6d8af4550b4306c85af31b920be022.claudemcpcontent.com` (SHA-256 of the server URL, first 32 hex characters). ChatGPT uses its own format. Leave `domain` unset until a host requires it, because `registerAppResource` fills in per-host metadata.
- `_meta.ui.csp.resourceDomains: ["https://assets.claude.ai"]` so the view can load Anthropic Sans. Bundle everything else into the HTML.
- `@modelcontextprotocol/ext-apps` 2.x (latest 2.0.4) beside `@modelcontextprotocol/server` 2.3.1. zod must be 4.2 or later for `app.registerTool`.
- Tool names: no `__`, `..` or `._` in the name of a tool with an entry point.
- After changing an entry point, use Customize > Connectors > Baseline > Refresh tools list. Otherwise the change takes up to two hours to show up.

## Testing

1. Add `/api/mcp` as a custom connector in Claude (done) and in ChatGPT (chatgpt.com/plugins > + > Add custom MCP server).
2. Claude, web, unified experience, permission set to Manual: ask "open Baseline Today", then in a second message "show yesterday" to exercise the app tool.
3. Check the Artifacts page for the tile, then pin it.
4. ChatGPT: check the sidebar entry and the conversation panel.
5. iOS Claude app: the inline card renders in a WebView with no side panel, and safe-area insets apply.

## Later

- Remaining widgets from the September plan: plate review, routine, trend chart, weekly check-in.
- Directory listing: an entry point on a Community listing is hidden. Baseline needs Verified status in Claude's directory or stays a custom connector. A health-data listing also needs the privacy review from the original plan.
- ChatGPT-only extras: settings, file entrypoints (CSV import), @-mentions and forms.

## Sources

- https://claude.com/docs/connectors/building/mcp-apps/display-modes
- https://claude.com/docs/connectors/building/mcp-apps/entry-points
- https://claude.com/docs/connectors/building/mcp-apps/app-tools
- https://claude.com/docs/connectors/building/mcp-apps/design-guidelines
- https://claude.com/docs/connectors/building/mcp-apps/instance-supersession
- https://developers.openai.com/plugins/build/extensions
- https://developers.openai.com/plugins/build/chatgpt-ui
