Vite start.

Pre development steps for Vite (6.3.5) + React (19.1.0) + TypeScript (5.8.3) + Node (20.10.0).

1. Instal ESLint 'antfu' config. Combine 'ESLint' and 'Prettier' ('ESLint Stylistic'), has more setting, has no 'printWidth'.
  1) Install 'antfu/eslint-config'
	-------------------------------------------------
		npm i -D @antfu/eslint-config@latest
	-------------------------------------------------
  2) Rename 'eslint.config.js' to 'eslint.config.mjs' and paste.
	-------------------------------------------------
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
					quotes: 'double',
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
	-------------------------------------------------
3) Edit 'package.json' 'lint' script.
	-------------------------------------------------
		"lint": "eslint src/**/*.{ts,tsx,js,jsx,css,json} && tsc --noEmit --project tsconfig.app.json",
		"lint:eslint": "eslint src/**/*.{ts,tsx,js,jsx,css,json}",
		"lint:types": "tsc --noEmit --project tsconfig.app.json",
		"lint:fix": "eslint src/**/*.{ts,tsx,js,jsx,css,json} --fix",
	-------------------------------------------------
4) Run 'npm run lint' to install antfu plugins.
5) Create in the root directory '.vscode/settings.json' and paste.
	-------------------------------------------------
		{
			// Disable the default formatter, use eslint instead
			"prettier.enable": false,
			"editor.formatOnSave": false,

			// Auto fix
			"editor.codeActionsOnSave": {
				"source.fixAll.eslint": "explicit",
				"source.organizeImports": "never"
			},

			// Silent the stylistic rules in you IDE, but still auto fix them
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

			// Enable eslint for all supported languages
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
	-------------------------------------------------

2. Install vite-tsconfig-paths for absolute paths with '@/' (alias path import). 
	1) Install 'vite-tsconfig-paths'
		-------------------------------------------------
			npm i -D vite-tsconfig-paths
		-------------------------------------------------
	2) Edit 'tsconfig.app.json'
		-------------------------------------------------
			"baseUrl": ".",
    		"paths": {
      			"@/*": ["src/*"]
    		}
		-------------------------------------------------
	2) Edit 'tsconfig.json'
		-------------------------------------------------
			"compilerOptions": {
    			"baseUrl": ".",
    			"paths": {"@/*": ["src/*"]}
  			}
		-------------------------------------------------

3. Install husky for run pre-commit hooks
	1) Install 'husky'
		-------------------------------------------------
			npm i -D husky
		-------------------------------------------------
	3) Init 'git'
		-------------------------------------------------
			git init
		-------------------------------------------------
	3) Init 'husky'
		-------------------------------------------------
			npx husky init
		-------------------------------------------------
	4) Edit '.husky/pre-commit'
		-------------------------------------------------
			npm run lint
		-------------------------------------------------

4. Add github actions.
	1) Create '.github/workflows/lint.yaml'
	2) Add to '.github/workflows/lint.yaml'
		-------------------------------------------------
			name: CI

			on: [push]

			jobs:
			build: 
				runs-on: ubuntu-latest
				steps:
				- uses: actions/checkout@v4
				- uses: actions/setup-node@v4
					with: node-version: '20.x' cache: 'npm'
				- run: npm ci
				- run: npm run lint
		-------------------------------------------------

5. Install 'lint-staged' for linting only staged files.
	1) Install 'lint-staged'
		-------------------------------------------------
			npm i -D lint-staged
		-------------------------------------------------
	2) Edit 'package.json'
		-------------------------------------------------
			"lint-staged": {
				"*": "npm run lint"
			}
		-------------------------------------------------