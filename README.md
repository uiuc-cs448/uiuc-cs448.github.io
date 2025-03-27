# Course staff GitHub notes

> Various explanations, scripts, etc. for the GitHub-related uses of CS 448.

## Course website

[GitHub Pages](https://docs.github.com/en/pages) with the [Jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll) site generator and [GitHub Flavored Markdown (GFM)](https://github.github.com/gfm/) processer.

Most markdown syntax renders as expected. However, some known exceptions:
- LaTeX
- Callouts (`> [!NOTE]`, etc.)

## Scripts

Short [GitHub CLI](https://cli.github.com/manual/) commands to run locally* to workaround some of GH Classroom's dumb quirks.

> *Trying to get these to be manually triggerable workflows on GH Actions. However, they use [`$GITHUB_TOKEN`](https://docs.github.com/en/actions/security-for-github-actions/security-guides/automatic-token-authentication) for authentication and I haven't set the right permissions. For now, run locally by copy-pasting the below commands and substituting/exporting any [`$` workflow variables](https://github.com/organizations/uiuc-cs448/settings/variables/actions). E.g. `ORG=uiuc-cs448`, `STUDENT_TEAM=students`, `SOURCE_SLUG=lab-0`

### Onboard students to the organization

Students who accept a GH Classroom assignment only get access to their fork of it. [Invite them to join a team](https://docs.github.com/en/rest/teams/members?apiVersion=2022-11-28#add-or-update-team-membership-for-a-user) to enable group tagging, permissions, etc. so you don't have to manage them individually. This also promotes them from an "Outside Collaborator" to an organization "Member" and doing this does not grant them access to other student's assignment repos (only repos that are explicitly set to be [viewable by them / their team](https://docs.github.com/en/organizations/managing-user-access-to-your-organizations-repositories/managing-repository-roles/managing-team-access-to-an-organization-repository).

```bash
gh api "/orgs/$ORG/outside_collaborators" --paginate \
    | jq -r ".[].login" \
    | xargs -I {} \
        gh api --method PUT "/orgs/$ORG/teams/$STUDENT_TEAM/memberships/{}" -f "role=member"
```

### (Actually) sync assignments

The "Sync assignments" button in GH Classroom opens a PR in each student's repo/fork but doesn't merge them. You can either [merge all PRs](https://cli.github.com/manual/gh_pr_merge) or just [sync the forks](https://cli.github.com/manual/gh_repo_sync) from `$SOURCE` (this must be a slug, e.g. `lab-0`), assuming they are non-conflicting.

> Not thoroughly tested with merge conflicts, but these scripts should abort the conflicting repos.

#### Merge GH Classroom PRs

```bash
gh api /orgs/$ORG/repos --paginate \
    | jq -r ".[].name" \
    | xargs -I {} gh api "/repos/$ORG/{}/pulls" \
    | jq -r ".[] | select(.user.login == \"github-classroom[bot]\") .html_url" \
    | xargs -I {} gh pr merge "{}" -s
```

#### Sync forks

```bash
gh api orgs/$ORG/repos --paginate \
    | jq ".[].name | select(startswith(\"$SOURCE-\"))" \
    | xargs -I {} sh -c "echo \"Syncing fork {}:\" && gh repo sync \"$ORG/{}\" -s \"$ORG/$SOURCE\""
```

> Leftover conflicts should error `can't sync because there are diverging changes...`, etc. You should be able to use "Sync assignments" afterwards to open PRs and manually resolve.

## Download student repos (local only)

The [GH Classroom CLI extension](https://docs.github.com/en/education/manage-coursework-with-github-classroom/teach-with-github-classroom/using-github-classroom-with-github-cli) makes this easy via an interactive command.

```bash
gh classroom clone student-repos
```

By default, this clones all submitted (?) forks under `$assignment` into a dir `$assignment-submissions`.
