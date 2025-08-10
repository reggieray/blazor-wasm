# blazor-wasm

This is an example repository on how to deploy a Blazor Web Assembly application to GitHub Pages using GitHub Actions.

> This is the [live demo](https://reggieray.github.io/blazor-wasm/).

1. Create the Blazor WebAssembly application

```csharp
dotnet new blazorwasm -o Blazor.Wasm 
```

Where `Blazor.Wasm` is the name of your project. Commit changes and push to GitHub repository.

2. Setup GitHub Action

- source code: [here](https://github.com/reggieray/blazor-wasm/blob/main/.github/workflows/static.yml) - The code below is taken from here
- blazor sample: [here](https://github.com/dotnet/blazor-samples/blob/main/.github/workflows/static.yml)- The orignal sample code on setting up a GitHub Action

```yaml
# Simple workflow for deploying static content to GitHub Pages
name: Deploy static content to Pages
env:
  PUBLISH_DIR: src/Blazor.Wasm/bin/Release/net9.0/publish/wwwroot # 👈 update `src/Blazor.Wasm` to name of your project

on:
  # Runs on pushes targeting the default branch for the app's folder path
  push:
    paths:
      - src/Blazor.Wasm/** # 👈 update `src/Blazor.Wasm/**` to name of your project
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  # Single deploy job since we're just deploying
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}

    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2

      - name: Get latest .NET SDK
        uses: actions/setup-dotnet@3951f0dfe7a07e2313ec93c75700083e2005cbab # v4.3.0
        with:
          dotnet-version: '9.0'

      - name: Publish app
        run: dotnet publish src/Blazor.Wasm -c Release # 👈 update `src/Blazor.Wasm/**` to name of your project

      - name: Rewrite base href
        uses: SteveSandersonMS/ghaction-rewrite-base-href@5b54862a8831e012d4f1a8b2660894415fdde8ec # v1.1.0
        with:
          html_path: ${{ env.PUBLISH_DIR }}/index.html
          base_href: /blazor-wasm/ # 👈 update `/blazor-wasm/ ` to the GitHub page URL
        
      - name: Setup Pages
        uses: actions/configure-pages@983d7736d9b0ae728b81ab479565c72886d7745b # v5.0.0

      - name: Upload artifact
        uses: actions/upload-pages-artifact@56afc609e74202658d3ffba0e8f6dda462b719fa # v3.0.1
        with:
          path: ${{ env.PUBLISH_DIR }}

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@d6db90164ac5ed86f2b6aed7e0febac5b3c0c03e # v4.0.5
```

3. Trigger deployment

You can do this either one of two ways:

- make a change to the Blazor app and push to main, remember the trigger setup in the action on step 2.
```
on:
  # Runs on pushes targeting the default branch for the app's folder path
  push:
    paths:
      - src/Blazor.Wasm/** # 👈 update `src/Blazor.Wasm/**` to name of your project
    branches:
      - main
```
or
- You can trigger manually by going to the `Actions` tab, finding `Deploy static content to Pages` and click `Run workflow`.
