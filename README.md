# Deploy a Dash App to Dash Enterprise 6 with GitHub Actions

Example repo that deploys a Dash app to Dash Enterprise 6 (`cs-k8s.plotly.host`) from GitHub Actions, using the [`plotly/de-deploy`](https://github.com/plotly/de-deploy) action. The app is the "Layout with Horizontal Controls" sample: Dash Design Kit [Cards](https://cs-k8s.plotly.host/docs/dash-design-kit/blocks) with horizontal controls.

DE5 version of this example: [RunQi-Han/de5-de-deploy](https://github.com/RunQi-Han/de5-de-deploy).

## How it works

The workflow is in [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml):

| Event | What happens on Dash Enterprise |
| --- | --- |
| Push to `main` (or run manually) | Creates the app `horizontal-controls` if needed, then deploys the commit |
| Pull request opened or updated | Deploys a preview app `horizontal-controls-<PR number>` |
| Pull request closed or merged | Deletes the preview app |

The action installs `de-client` from `https://cs-k8s.plotly.host/packages`, logs in with a Dash Enterprise username and password, and runs `de deploy`. It also adds an App Manager link to the commit as a status check.

## Setup

1. In the repo, go to **Settings > Secrets and variables > Actions** and add:

   | Secret | Value |
   | --- | --- |
   | `DE_USERNAME` | Dash Enterprise username the app will be deployed as (it becomes the app owner) |
   | `DE_PASSWORD` | That user's password |

   The account must be able to sign in with a username and password (a Keycloak user, not SSO-only). A dedicated service user is recommended for real projects.
2. Push to `main`, or run the workflow from the **Actions** tab.
3. Open `https://cs-k8s.plotly.host/horizontal-controls/` once the build finishes.

The commit status uses the built-in `GITHUB_TOKEN`, so no personal access token is needed.

## Notes for Dash Enterprise 6

- `de deploy` doesn't work on DE6 instances that have the GitHub integration enabled. Use the integration there instead.
- To deploy to another instance, change `DE_HOST` and `app_name` in the workflow.
- If your instance uses an internal CA, the runner also needs that CA for `pip` and `git`.

## Run the App Locally

```
pip install -r requirements.txt --extra-index-url https://cs-k8s.plotly.host/packages
python app.py
```
