---
name: jira-snyk-remediation
description: |
  Complete workflow to solve Jira Tickets that contain Snyk Vulnerabilities. Fetches a Jira Ticket, confirms the Snyk Vulnerability lives in a repo the agent has access to locally, calls the snyk-fix skill and transitions the Jira ticket to "In Progress".
compatibility: |
  Requires Atlassian account, MCP server, TWG CLI or ACLI. 
  Requires Snyk MCP server connection and authenticated Snyk account.
  GitHub CLI (gh) required for PR creation. Git repository required.
  Supports SAST for 20+ languages and SCA for all major package managers.
metadata:
  author: Jack Ryan
  version: 1.0.0
---

# Jira Snyk Remediation Skill

## Prerequisites
- **Tools**: Jira/Atlassian MCP Server, TWG CLI, or ACLI.  Git, Snyk MCP Server or Snyk CLI, and the `snyk-fix` skill.
- **Context**: Must be executed within a local Git repository.

## Inputs
- `ticket_id`: The Jira issue key (e.g., "ABC-123"). 

## Workflow Step
1. **Fetch Jira Ticket** 
Retrieve the details for the provided `ticket_id`. Use `mcp_atlassian_get_issue` to get the Jira ticket. Make sure to get the ticket description. If the Atlassian MCP Server is not available then use the TWG CLI or ACLI to get the Jira ticket with description.

2. **Validate Snyk Vulnerability** 
Ensure the ticket is actually a Snyk-related vulnerability. If not found, abort with error: "Ticket is not identified as a Snyk vulnerability."

3. **Validate Repository Match** 
Look for the project name ("Impacted project:") in the Jira Ticket. Infer the repo name from the project name. Confirm this is the same repo matches the current local directory. If they do not match, abort with error: "Local repository does not match the repository specified in the Jira ticket.

4. **Initialize Workspace** 
Prepare the local environment for the fix. Create and checkout a new branch. The name of the new branch should be fix/{jira_ticket_id}

5. **Use Snyk**
Invoke the specialized `snyk-fix` skill to analyze and remediate the vulnerability.
Pass the Jira ticket description into the `snyk-fix` skill. Do not fix anything other than the vulnerability referenced in the Jira ticket description. No batch fixes. Use the skills `secure-at-inception` and `secure-dependency-health-check` when adding new code and/or dependencies. `snyk-fix` asks the user to create a PR. Dont forget that part. 

6. **Transition** 
Transition the Jira ticket to reflect that work has started. Most likely this will be "In Progress". Add a comment to the Jira ticket: "Automated remediation started via `jira-snyk-remediation` skill on branch fix/{jira_ticket_id}. Link to pull request if one was created"

---

## Error Handling
 Situation | Action |
|-----------|--------|
| Auth error | If authentication to Atlassian or Snyk fails, retry once lif still failing STOP|
| Timeout/failure | Retry once; if still failing STOP and report |
| `snyk-fix` fails | the branch remains intact for manual inspection, but the Jira ticket status should not be moved to "In Progress" or should be updated with a "Failed" comment. |