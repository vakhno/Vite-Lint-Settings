# Vite + React + TypeScript Setup Guide

This guide covers the pre-development setup steps for a modern React application using:
- Vite `6.3.5`
- React `19.1.0`
- TypeScript `5.8.3`
- Node `20.10.0`

## Table of Contents
- [ESLint Setup](#eslint-setup)
- [Path Aliases](#path-aliases)
- [Git Hooks with Husky](#git-hooks-with-husky)
- [GitHub Actions](#github-actions)
- [Lint-Staged](#lint-staged)

## ESLint Setup

We'll use Anthony Fu's excellent ESLint config, which combines ESLint and Prettier functionality with enhanced settings.

### 1. Install the config package

```bash
npm i -D @antfu/eslint-config@latest
```

### 2. Create the ESLint config file

Rename the default `eslint.config.js` to `eslint.config.mjs` and add the following content:

```javascript
import antfu from '@antfu/eslint-config';

export default antfu(
	{
		type: 'app',
		react: true,
		typescript: true,
		formatters: true,
		stylistic: {
			indent: 'tab',
			semi: true,
			quotes: "double",
		},
	},
	{
		rules: {
			'ts/no-redeclare': 'off',
			'ts/consistent-type-definitions': ['error', 'type'],
			'no-console': ['warn'],
			'antfu/no-top-level-await': ['off'],
			'node/prefer-global/process': ['off'],
			'node/no-process-env': ['error'],
			'perfectionist/sort-imports': ['error', { tsconfigRootDir: '.' }],
			'unicorn/filename-case': [
				'error',
				{
					case: 'kebabCase',
				},
			],
		},
	},
);
```

### 3. Update package.json scripts

Add the following scripts to your `package.json`:

```json
"lint": "eslint src/**/*.{ts,tsx,js,jsx,css,json} && tsc --noEmit --project tsconfig.app.json",
"lint:eslint": "eslint src/**/*.{ts,tsx,js,jsx,css,json}",
"lint:types": "tsc --noEmit --project tsconfig.app.json",
"lint:fix": "eslint src/**/*.{ts,tsx,js,jsx,css,json} --fix",
```

### 4. Install ESLint plugins

Run the lint script to automatically install the necessary plugins:

```bash
npm run lint
```

### 5. Configure VS Code

Create a `.vscode/settings.json` file with the following content:

```json
{
	"prettier.enable": false,
	"editor.formatOnSave": false,

	"editor.codeActionsOnSave": {
		"source.fixAll.eslint": "explicit",
		"source.organizeImports": "never"
	},

	"eslint.rules.customizations": [
		{ "rule": "style/*", "severity": "off", "fixable": true },
		{ "rule": "format/*", "severity": "off", "fixable": true },
		{ "rule": "*-indent", "severity": "off", "fixable": true },
		{ "rule": "*-spacing", "severity": "off", "fixable": true },
		{ "rule": "*-spaces", "severity": "off", "fixable": true },
		{ "rule": "*-order", "severity": "off", "fixable": true },
		{ "rule": "*-dangle", "severity": "off", "fixable": true },
		{ "rule": "*-newline", "severity": "off", "fixable": true },
		{ "rule": "*quotes", "severity": "off", "fixable": true },
		{ "rule": "*semi", "severity": "off", "fixable": true }
	],

	"eslint.validate": [
		"javascript",
		"javascriptreact",
		"typescript",
		"typescriptreact",
		"vue",
		"html",
		"markdown",
		"json",
		"jsonc",
		"yaml",
		"toml",
		"xml",
		"gql",
		"graphql",
		"astro",
		"svelte",
		"css",
		"less",
		"scss",
		"pcss",
		"postcss"
	]
}
```

## Path Aliases

Configure absolute imports using the `@/` prefix.

### 1. Install the path resolver

```bash
npm i -D vite-tsconfig-paths
```

### 2. Update TypeScript configuration

Modify `tsconfig.app.json`:

```json
"baseUrl": ".",
"paths": {
	"@/*": ["src/*"]
}
```

And update `tsconfig.json`:

```json
"compilerOptions": {
	"baseUrl": ".",
	"paths": {"@/*": ["src/*"]}
}
```

## Git Hooks with Husky

Set up pre-commit hooks to ensure code quality.

### 1. Install Husky

```bash
npm i -D husky
```

### 2. Initialize Git

```bash
git init
```

### 3. Set up Husky

```bash
npx husky init
```

### 4. Configure pre-commit hook

Edit `.husky/pre-commit`:

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run lint
```

## GitHub Actions

Add continuous integration to your project.

### 1. Create workflow file

Create a new file at `.github/workflows/lint.yaml` with the following content:

```yaml
name: CI

on: [push, pull_request]

jobs:
  build: 
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: '20.x'
        cache: 'npm'
    - run: npm ci
    - run: npm run lint
```

## Lint-Staged

Optimize the linting process by only checking staged files.

### 1. Install lint-staged

```bash
npm i -D lint-staged
```

### 2. Configure in package.json

Add the following to your `package.json`:

```json
"lint-staged": {
	"*": "npm run lint"
}
```