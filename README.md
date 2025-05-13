# Split Monorepo Components

This GitHub Action splits components from a monorepo using git subtree and pushes them to separate remote repositories.

## Description

The Split Monorepo Components action allows you to maintain a monolithic repository while automatically splitting specific directories into their own repositories. This is useful for:

- Maintaining a single source of truth in your monorepo
- Allowing consumers to use only specific components
- Keeping your repository structure clean and organized
- Enabling independent versioning of components

The action uses git subtree split to extract the history of specified directories and push them to their respective remote repositories.

## Usage

Add this action to your GitHub workflow:

```yaml
name: Split Components

on:
  push:
    branches:
      - main
      - master

jobs:
  split:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Split Monorepo Components
        uses: docker://ghcr.io/ozo2003/split@master
        with:
          GH_TOKEN: ${{ secrets.GH_TOKEN }}
          REPO: your-org/your-repo
          BRANCH: main
          COMPONENTS_URL: https://example.com/components.json
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `GH_TOKEN` | GitHub token with repo access to both source and target repositories | Yes | N/A |
| `REPO` | Source GitHub repository in the format `org/repo` | Yes | N/A |
| `BRANCH` | Branch to split (e.g., main, master) | Yes | N/A |
| `COMPONENTS_URL` | URL to a JSON file defining the components to split | Yes | N/A |

## Components JSON Format

The components JSON file should be structured as follows:

```json
[
  {
    "path": "packages/component-a",
    "repo": "https://github.com/your-org/component-a.git"
  },
  {
    "path": "packages/component-b",
    "repo": "https://github.com/your-org/component-b.git"
  }
]
```

Each entry in the array defines:
- `path`: The directory path within your monorepo to split
- `repo`: The target repository URL where the component should be pushed

## Example Workflow

Here's a complete example of how to use this action in a GitHub workflow:

```yaml
name: Split Components

on:
  push:
    branches:
      - main

jobs:
  split:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Split Monorepo Components
        uses: docker://ghcr.io/ozo2003/split@master
        with:
          GH_TOKEN: ${{ secrets.GH_TOKEN }}
          REPO: your-org/your-repo
          BRANCH: main
          COMPONENTS_URL: https://raw.githubusercontent.com/your-org/your-repo/main/components.json
```

## Security Considerations

- The `GH_TOKEN` should have write access to all target repositories
- Store your components configuration in a secure location if it contains sensitive information
- Consider using repository secrets for sensitive URLs

## License

This project is licensed under the MIT License - see the LICENSE file for details.
