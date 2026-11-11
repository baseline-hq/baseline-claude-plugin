# Baseline for Claude

Baseline is a personal health log for meals, workouts, goals and Apple Health summaries. This plugin connects Claude to your Baseline account and adds skills for the things people do most.

## Use it

Install the plugin, then connect the Baseline connector from the plugin's Connectors tab and sign in with your Baseline account. After that you can:

- Tell Claude what you ate and it finds the foods, confirms the macros with you, and logs them.
- Ask how your week went for a summary of food, training and body trends against your targets.
- Ask for a workout and Claude builds or adjusts a routine from your recent training.

## Data

The plugin sends your requests to your Baseline account through https://baseline.chaitanyabhagwat.com/api/mcp, signed in with OAuth. It reads and writes your food log, routines, goals and health summaries there. The plugin itself stores nothing.

## Test locally

    claude plugin validate .
    claude --plugin-dir .

On claude.ai, zip this folder and upload it under Customize > Plugins > Add > Upload plugin.

## Interactive app (planned)

The next step is an MCP App served by the Baseline connector: a "Baseline Today" view with macros left and today's meals. It opens inline, in the side panel, or from the app list. See [docs/mcp-apps-plan.md](docs/mcp-apps-plan.md). The plan is shared with the other Baseline plugin repo.
