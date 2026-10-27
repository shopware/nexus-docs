# Shopware Nexus documentation

*This repository is embedded into [developer-portal](https://github.com/shopware/developer-portal) under [/nexus/](https://developer.shopware.com/nexus/). This repository is also connected to the Shopware Dev Docs connector GitHub app which manages commit status checks in PRs and triggers production deployments.*

Nexus is a Shopware service, versioned independently of Shopware itself, so this documentation is not branched per Shopware version.

## Content

- `src/` holds the published pages: overview, getting started, workflow builder, user management, security, and the node reference under `src/nodes/`, one page per node grouped by category.
- Node pages are generated. Parameter lists, options, ports and built-in examples come from the node definitions shipped with the workflow builder. Overviews, credential setup, output notes, examples and common issues are written by hand. Change the source and regenerate rather than editing a generated page by hand.

## Development

1. Clone this repository

```
cd /www/
git clone git@github.com:shopware/nexus-docs.git
cd nexus-docs
```

2. Make sure you have your local copy of the developer-portal repository in the same parent directory.

```
pnpm docs:env
```

3. Link articles from your local copy of nexus-docs into the developer-portal.

```
pnpm docs:link
```

4. Start the development server.

```
pnpm docs:preview
```
