# Course Website

Markdown mostly works as expected, beware LaTeX, callouts (via `> [!NOTE]`, etc.), and some other syntaxes do not work, even if it renders properly on GitHub file preview.

# Utilities

Short [GitHub CLI](https://cli.github.com/manual/) commands to deal with GH Classroom being dumb, which can run locally or remotely as workflows via GH Actions (if applicable).

> Workflows use [`$GITHUB_TOKEN`](https://docs.github.com/en/actions/security-for-github-actions/security-guides/automatic-token-authentication) for authentication but it may not have the right permissions. If not, run locally by copy-pasting the below commands and substituting any [`$` workflow variables](https://github.com/organizations/uiuc-cs448/settings/variables/actions).
> E.g. `ORG=uiuc-cs448`, `STUDENT_TEAM=students`, `SOURCE_SLUG=lab-0`

## Onboard students to the organization

Students who accepted an assignment only obtain access to their fork. [Invite them to join a team](https://docs.github.com/en/rest/teams/members?apiVersion=2022-11-28#add-or-update-team-membership-for-a-user) to enable group tagging, permissions, etc. This also promotes them from an "Outside Collaborator" to an organization "Member".

```bash
gh api "/orgs/$ORG/outside_collaborators" --paginate \
    | jq -r ".[].login" \
    | xargs -I {} \
        gh api --method PUT "/orgs/$ORG/teams/$STUDENT_TEAM/memberships/{}" -f "role=member"
```

## Fully sync assignments

The "Sync assignments" button in GH Classroom opens a PR in each student's repo/fork but doesn't merge them. You can either [merge all PRs](https://cli.github.com/manual/gh_pr_merge) or just [sync the forks](https://cli.github.com/manual/gh_repo_sync) from `$SOURCE` (this must be a slug, e.g. `lab-0`).

> Not thoroughly tested with merge conflicts, but it should abort if so.

### Merge GH Classroom PRs

```bash
gh api /orgs/$ORG/repos --paginate \
    | jq -r ".[].name" \
    | xargs -I {} gh api "/repos/$ORG/{}/pulls" \
    | jq -r ".[] | select(.user.login == \"github-classroom[bot]\") .html_url" \
    | xargs -I {} gh pr merge "{}" -s
```

### Sync forks

```bash
gh api orgs/$ORG/repos --paginate \
    | jq ".[].name | select(startswith(\"$SOURCE-\"))" \
    | xargs -I {} sh -c "echo \"Syncing fork {}:\" && gh repo sync \"$ORG/{}\" -s \"$ORG/$SOURCE\""
```

Leftover conflicts should error `can't sync because there are diverging changes...`, etc. You should be able to use "Sync assignments" afterwards to open PRs and manually resolve.

## Download student repos (local only)

The [GH Classroom CLI extension](https://docs.github.com/en/education/manage-coursework-with-github-classroom/teach-with-github-classroom/using-github-classroom-with-github-cli) makes this easy via an interactive command.

```bash
gh classroom clone student-repos
```

By default, this clones all submitted (?) forks under `$assignment` into a dir `$assignment-submissions`.
