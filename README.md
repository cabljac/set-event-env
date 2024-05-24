# Set Event Data Environment Variable

![GitHub release (latest by date)](https://img.shields.io/github/v/release/cabljac/set-event-data)

This GitHub Action sets an environment variable with all the triggering event data as a stringified object. This allows you to pass event data to subsequent steps in your workflow, such as running scripts that require access to the full event payload.

## Example Usage

To use this action, include it as a step in your GitHub Actions workflow. Here is an example workflow that sets up Node.js, caches dependencies, and runs a TypeScript script with the event data:

```yaml
name: Example Workflow

on: 
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  run-script:
    runs-on: ubuntu-latest

    steps:
      - name: Check out repository
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '14'

      - name: Cache Node.js modules
        uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-

      - name: Install dependencies
        run: npm install

      - name: Set Event Data Environment Variable
        uses: cabljac/set-event-data@v0.0.1

      - name: Build TypeScript package
        run: npm run build
        working-directory: ./path-to-your-typescript-package

      - name: Run compiled script
        run: node lib/index.js
        working-directory: ./path-to-your-typescript-package
        env:
          EVENT_DATA: ${{ env.GITHUB_EVENT }}
