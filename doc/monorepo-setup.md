# Monorepo Workspace Setup

## Overview

This guide explains the relationship between workspace paths and package names in an npm monorepo, and outlines best practices for scripts, dependencies, and versioning.

## Workspace Paths vs. Package Names

- **Workspace Paths**  
  Directories specified in the root `package.json` under the `"workspaces"` field.  
```json
"workspaces": [
  "api",
  "gateway/whatsapp",
  "localRelay"
]
  ```

- **Package Names**  
  Defined in each package's `package.json` under the `"name"` field.  
  Example in `api/package.json`:
  ```json
  {
    "name": "@beacon/api",
    ...
  }
  ```

### Resolving Commands

Use package names with the `-w` (or `--workspace`) flag to run workspace-specific scripts:
```bash
npm run dev -w @beacon/api
npm run dev -w @beacon/localRelay
npm run start -w @beacon/whatsapp
npm run start -w @beacon/localRelay
``` 

## Defining Workspace Dependencies

To depend on another workspace package, reference its package name using the `workspace:` protocol:
```json
"dependencies": {
  "@beacon/api": "workspace:^"
}
```
npm will resolve this to the local workspace instead of fetching from the registry.

## Root and Package Scripts

- **Root `package.json` scripts**:
```json
"scripts": {
  "bootstrap": "npm install",
  "dev:api": "npm run dev -w @beacon/api",
  "dev:whatsapp": "npm run dev -w @beacon/whatsapp",
  "dev:localRelay": "npm run dev -w @beacon/localRelay",
  "dev": "concurrently \"npm:dev:api\" \"npm:dev:whatsapp\" \"npm:dev:localRelay\"",
  "start": "npm run start --workspaces --if-present"
}
``` 

- **Individual package scripts** are defined within each package's `package.json` under `"scripts"`.

## Best Practices

1. Align directory names with package names for clarity.
2. Centralize shared dev dependencies (e.g., `eslint`, `prettier`, `nodemon`) in the root.
3. Keep packages private (`"private": true`) if not meant for registry publishing.
4. Add metadata fields in each `package.json` (`"repository"`, `"author"`, `"license"`).
5. Standardize entry points (e.g., use `src/index.js` or `app.js` consistently).
6. Add `test` and `build` scripts for automated workflows.
7. Use a monorepo management tool (Lerna, Nx) or maintain a consistent versioning strategy for scaling.
