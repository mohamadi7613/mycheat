


# Prettier

```bash
npm install -D prettier             # install as dep
npx prettier --write myfile.js      # foramt this file
npx prettier --check myfile.js      # check is formated or not
```

## config .prettierrc
```json
{
    "arrowParens": "always",
    "bracketSpacing": true,
    "embeddedLanguageFormatting": "auto",
    "htmlWhitespaceSensitivity": "css",
    "insertPragma": false,
    "jsxBracketSameLine": false,
    "jsxSingleQuote": false,
    "printWidth": 80,
    "proseWrap": "preserve",
    "quoteProps": "as-needed",
    "requirePragma": false,
    "semi": false,              // semicolon
    "singleQuote": false,
    "tabWidth": 4,
    "trailingComma": "es5",
    "useTabs": false,
    "vueIndentScriptAndStyle": false
}
```

## .prettierignore

```bash
node_modules     # it`s better to add this 
*.html           # so " npx --check and --write" will not work for html files
```

### script
Add this to `package.json`

```json
"scripts": {
    "prettier": "prettier --ignore-path .gitignore \"**/*.+(ts|tsx|js|jsx|json|css|md|mdx|html)\"",
    "format": "npm run prettier -- --write",
    "check-format": "npm run prettier -- --list-different",
}
```
### Editor extenstion
+ It`s better to use prettier with package bcz it has one version for all the team.
+ Any local configuration overwrite your global settings.










