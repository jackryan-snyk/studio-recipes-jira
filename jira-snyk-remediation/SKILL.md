# Jira Snyk Remediation Skill

## Goal
Automate the resolution of security vulnerabilities mentioned in Jira tickets.

## Workflow
1. **Fetch:** Use `mcp_atlassian_get_issue` to read the Jira ticket. Get the ticket description. If the Atlassian MCP Server is not available then use the TWG CLI or Atlassian CLI to get the Jira ticket with description.
2. **Validate Snyk Ticket:** The Jira ticket should be for a Snyk Security vulnerability. If it is not, then exit the skill and tell the end user.
3. **Validate Repo:** Look for the project name ("Impacted project:") in the Jira Ticket. Infer the repo name from this and confirm this is the same repo that is opened locally. If it is not, then exit the skill and tell the end user.
4. **New Branch:** Create a new branch. The name of the new branch should contain the Jira ticket ID.
5. **Use Snyk**: Use the snyk-fix skill to remediate the vulnerability. Pass the ticket description into the snyk-fix skill. Do not fix anything other than the vulnerability referenced in the Jira ticket description. No batch fixes. Use the skills secure at inception and secure-dependency-health-check when appropriate. Snyk-fix asks the user to create a PR. Dont forget that part. 
6. **Transition**: Move the Jira ticket to "In progress".