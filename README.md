# Deploy Preview Action

A GitHub action to deploy preview environments for static apps using GitHub Pages.

## How it works

This action expects you to checkout and build your static assets. After that, it will run next steps:

* Generate a unique slug from the branch name plus current date. For example, if your branch is `Update-landing-page`, it will generate a slug like `2021-05-08-update-landing-page`.
* Publish the contents from the folder of your choice into a branch where you should have configured GitHub Pages to be served.
* Use the slug generated in first step to create a sub-folder with your contents. That way, if your repository is `my-org/my-repo`, you will be able to access the preview env in https://my-org.github.io/my-repo/2021-05-08-update-landing-page
* If you run this action during `pull_request` or `pull_request_target` events: publish a comment after the preview env is ready, with the URL from previous step.

## Usage

```yaml
name: Build and Deploy

on:
  pull_request: null

jobs:
  build-and-deploy:
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Install and Build
        run: |
          npm install
          npm run build

      - name: Deploy
        uses: shlinkio/deploy-preview-action@v1
        with:
          branch: gh-pages # The branch from where the GitHub Pages are served (defaults to preview-env)
          folder: build # The folder where the artifacts to publish are (defaults to the project root)
```

## Configuration

| Param    | Description                                                                              | Required |
|----------|------------------------------------------------------------------------------------------|----------|
| `branch` | The name of the branch where GitHub Pages are getting served. Defaults to `preview-env`. | No       |
| `folder` | The folder including the static contents to deploy. Defaults to the project root.        | No       |

## Considerations

* Every deployment generates a sub-folder. This action does not take care of deleting them.
* Some static pages or statically generated pages expect to be served from the root of the domain. This action deploys on a sub-folder, so you may need extra steps to ensure your app works once deployed.
