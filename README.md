# Developer Notes

## Course Website

Markdown mostly works as expected, beware LaTeX, callouts (via `> [!NOTE]`, etc.), and some other syntaxes do not work, even if it renders properly on GitHub file preview.

## Workflows

Utilities to deal with GitHub Classroom being dumb, which you can run locally or remotely. Mostly uses the [GitHub CLI](https://cli.github.com/manual/) with [automatic token authentication](https://docs.github.com/en/actions/security-for-github-actions/security-guides/automatic-token-authentication).

> On GitHub Actions, I can't get [`$GITHUB_TOKEN`](https://docs.github.com/en/actions/security-for-github-actions/security-guides/automatic-token-authentication) to have the right permissions. For now, you should only run these locally.

### Onboard students

Students who accepted an assignment can only access their private forked repo and any public ones. Moving them to the `students` team gives them its visibility permissions, e.g. to see Discussions under this repo.

Precisely, this invites users of the "Outside Collaborators" role to `$TEAM`, which also promotes them to the "Members" role.

```bash
gh api \
    /orgs/$ORG/outside_collaborators --paginate | \
jq -r ".[].login" | \
xargs -I {} \
gh api \
    --method PUT \
    /orgs/$ORG/teams/$TEAM/memberships/{} \
    -f "role=member"
```

### Actually sync assignments

The "Sync assignments" button in GitHub Classroom opens a PR in each student's repo/fork but doesn't merge them. Run this after to do so.

> Not tested with merge conflicts, but I assume it'll fail if so.

```bash
gh api \
    /orgs/$ORG/repos --paginate | \
    jq -r ".[].name" | \
    xargs -I {} gh api /repos/$ORG/{}/pulls | \
    jq -r ".[] | select(.user.login == \"github-classroom[bot]\") .html_url" | \
    xargs -I {} gh pr merge {} -s
```
