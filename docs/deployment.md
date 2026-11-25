# Deployment and publishing

## Profile publishing

GitHub displays the root README of this public username-matching repository as the profile README. GitHub's [README documentation](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes) describes that behavior. The repository also contains a static React/Vite companion app under `world/`.

Publishing a normal documentation or asset change means merging or pushing it to the default branch according to repository settings. The root README remains the profile's main presentation.

## Automated file updates

GitHub Actions runs the workflows listed below. They write generated content to the repository; they are not a general application deployment pipeline.

- The 3D contribution workflow runs daily and on manual dispatch.
- The blog-post workflow runs hourly and updates the marked blog list in the profile README.
- The snake workflow runs every six hours and writes the light and dark SVGs.
- The productive-time workflow runs daily; it writes the card only when SUMMARY_GITHUB_TOKEN exists and output differs.
- The README timestamp workflow runs on manual dispatch and removes legacy timestamp lines when present.
- The Developer Universe Pages workflow builds `world/` with npm on changes under that directory and on manual dispatch. It deploys through the official Pages artifact flow.
- The Developer Universe data workflow runs daily and on manual dispatch, refreshes public GitHub profile/repository/language JSON, and skips replacement when the API fails and an existing fallback is present.

Most workflow events caused by a push authenticated with GITHUB_TOKEN do not start another workflow run. See [GitHub Actions token behavior](https://docs.github.com/en/actions/concepts/security/github_token).

## Manual runs and permissions

Each workflow with workflow_dispatch can be started from the repository's Actions page by someone with the necessary permissions. A manual run can still produce no file change if the generated result matches the current file. Workflows that push changes need repository content write permission; the Pages deployment additionally needs Pages write and OIDC token permissions. Set repository Pages source to **GitHub Actions** once before the first deployment.

## Repository secrets for brajesh31-dev

Create secrets at [Settings > Secrets and variables > Actions](https://github.com/brajesh31-dev/brajesh31-dev/settings/secrets/actions). Only the following two names require manual setup. Create fresh tokens while signed in as **brajesh31-dev**.

- **METRICS_TOKEN**: a classic GitHub personal access token used by `metrics.yml` and `github-achievements.yml` to read account-wide profile, activity, repository, language, and achievement data. Select **`read:project`** because the pinned achievements fix queries `projectsV2`; [GitHub requires this scope for Projects queries](https://docs.github.com/en/issues/planning-and-tracking-with-projects/automating-your-project/using-the-api-to-manage-projects). Other scope checkboxes can remain unchecked for the current public-profile setup. The [Metrics setup guide](https://github.com/lowlighter/metrics/blob/master/.github/readme/partials/documentation/setup/action.md) and [achievements guide](https://github.com/lowlighter/metrics/blob/master/source/plugins/achievements/README.md) describe optional scopes for private or organization data. Repository commits use the built-in token separately.
- **SUMMARY_GITHUB_TOKEN**: a GitHub personal access token used by `update-productive-time.yml`. For public data, use a fine-grained token with public repositories read-only and default account permissions, or a classic token with `public_repo` and `read:user`, as described in the [summary-card setup guide](https://github.com/vn7n24fzkq/github-profile-summary-cards#setting-up-your-github-token). This workflow pushes the generated card with the checkout's built-in token.

**GITHUB_TOKEN** (also available as `github.token`) is supplied automatically by GitHub Actions. Do not create it as a repository secret. The workflows declare their own required write permissions. The Pages workflow uses built-in OIDC authentication; no Pages deployment key is needed.

When a custom token is missing, the affected workflows keep existing artwork and print a setup warning instead of attempting generation with an empty token.

No WakaTime, Medium, Gemini, OpenAI, AWS, or Azure API key is referenced by these workflows. The README's project technology lists describe separate projects. Existing WakaTime charts, Holopin badges, portfolio, and social links keep their independently registered account names.

## First migration run

The public profile repository is [brajesh31-dev/brajesh31-dev](https://github.com/brajesh31-dev/brajesh31-dev). The companion app deploys to `https://brajesh31-dev.github.io/brajesh31-dev/`; its Vite base matches the artifact root uploaded by the Pages workflow.

After configuring the two secrets and Pages, run the generation workflows from Actions to replace the copied contribution graphics with the new account's activity. Skyline, Pacman, Minesweeper, and Breakout workflows create their output branches on their first successful runs. Existing copied artwork is retained until refreshed. Public GitHub JSON is refreshed from brajesh31-dev as part of migration.

Flagship repository links now point to brajesh31-dev. Those project repositories must be restored separately before their links become available. Provider-managed widgets such as GitAnimals or Git Ranker may also need registration for the new GitHub account.

## Rollback

Generated files can be restored through a reviewed commit on the default branch. Before reverting an automated commit, identify the workflow and input that produced it; otherwise the next scheduled run may recreate the change. Avoid rewriting shared history.
