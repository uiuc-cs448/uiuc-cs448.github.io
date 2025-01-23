## Developer Notes

### Course Website

Markdown mostly works as expected, beware LaTeX, callouts (via `> [!NOTE]`, etc.), and some other syntaxes do not work, even if it renders properly on GitHub file preview.

### Onboarding Workflow

Automagically invite students that accepted their first assignment ("Outside Collaborators") to the `students` team ("Members"). Except I can't get [`$GITHUB_TOKEN`](https://docs.github.com/en/actions/security-for-github-actions/security-guides/automatic-token-authentication) to have the right permissions to read the list of outside collaborators and add them to the team. For now, ocasionally run this manually:

```bash
gh api \
    /orgs/uiuc-cs448/outside_collaborators --paginate | \
    jq -r ".[].login" | \
    xargs -I {} \
        gh api \
        --method PUT \
        /orgs/uiuc-cs448/teams/students/memberships/{} \
        -f "role=member"
```
