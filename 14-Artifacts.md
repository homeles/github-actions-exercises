# Artifacts
Artifacts can simply be described as some kind of output from a build process. This could be a binary, supporting files, or pretty much anything generated during build-time.

These "artifacts" could eventually be used as the running application itself (deployed to a host), and play a pivotal part in a Continuous Delivery model.

GitHub offers built-in artifact functionality for saving and reusing artifacts in different workflows or jobs.

The exercise below will walk you through updating the previously created `Continuous Integration` workflow (see [13-Continuous-Integration](./13-Continuous-Integration.md)) with the artifact action that will handle saving the artifact created from our `Golang` build process.

## Prerequisite Exercises
- [13-Continuous-Integration](./13-Continuous-Integration.md)

## Step 1: Add the artifact save action
1. From the __default__ branch of your repository, create a new branch of code called `feature/artifacts`
2. Open the file named `.github/workflows/ci.yaml`
3. Replace the contents of the file with:

```yaml
name: Continuous Integration
on:
  pull_request:
  workflow_dispatch:
defaults:
  run:
    shell: bash
    working-directory: golang_app
jobs:
  build-and-test:
    name: Build, Lint, & Test
    runs-on: ubuntu-latest
    steps:
      - name: Clone
        uses: actions/checkout@v3.1.0
      - name: Build
        run: go build
      - name: Run Linting
        uses: golangci/golangci-lint-action@v3
        with:
          working-directory: golang_app
      - name: Run Tests
        run: go test
      - name: Store Artifact
        uses: actions/upload-artifact@v3.1.0
        with:
          name: golang_app
          path: golang_app/app
```

4. Add & commit your changes, then publish your branch.
5. Go to your repository, and view the Pull Requests tab.
6. Create a pull request to merge `feature/artifacts` into your __default__ branch.
7. Click the `Show All Checks` link on the status checks in your pull request, then click the `Details` link next to `Continuous Integration`.

![status checks successful](./images/14-status-checks.png)

8. Click the `Summary` link on the left-hand navigation.

![link to summary](./images/14-summary-link.png)

The result will be an artifact that authenticated users can download. Additionally, this artifact can be used in deployment or other automation using the `actions/artifact-download` action.

It should be noted that artifact retention is only as long [configured](https://docs.github.com/en/organizations/managing-organization-settings/configuring-the-retention-period-for-github-actions-artifacts-and-logs-in-your-organization) for your enterprise or organization.

![artifacts on build dashboard](./images/14-artifacts.png)