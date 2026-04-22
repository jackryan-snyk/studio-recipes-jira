# Demo

## Pre-Reqs
1. Install [Snyk Security in Jira Cloud](https://marketplace.atlassian.com/apps/1230482/snyk-security-in-jira-cloud?hosting=cloud&tab=overview) and [create tickets](https://docs.snyk.io/integrations/jira-and-slack-integrations/snyk-security-in-jira-cloud-integration#automate-ticket-creation-in-jira) for the Snyk security vulnerabilities
2. Pick ADE of your choice - Cursor, Claude, etc...
3. Configure Atlassian Tool of your choice - MCP Server, TWG CLI or ACLI Jira
4. Configure Snyk MCP Server. Check mcp.json for an example
5. Clone repository you want to make changes to locally inside your ADE
6. Install [Snyk Studio Recipes](https://github.com/snyk/studio-recipes) from [Vercel skills.sh](https://skills.sh/snyk/studio-recipes/snyk-fix) or [Tessl registry](https://tessl.io/registry/skills/github/snyk/studio-recipes/snyk-fix)
```
npx skills add snyk/studio-recipes
```
or
```
npx tessl i github:snyk/studio-recipes 
```
7. Copy custom ./jira-snyk-remediation/SKILL.md into project

## Workflow
1. Run the Jira-Snyk skill: use slash command and provide the Jira ticket

Example:
```
/jira-snyk-remediation NGP-610
```
2. Let the agent solve the ticket.
3. Validate the remediation.


Additional Notes:
- the workflow is for a single ticket but could be modified for bulk


## TWG CLI
```
twg jira workitem get NGP-610
```

## ACLI 
```
acli jira auth login --web
acli jira workitem view NGP-610
acli jira workitem transition --key "NGP-610" --status "In Progress"
acli jira workitem transition --key "NGP-610" --status "Done"
```