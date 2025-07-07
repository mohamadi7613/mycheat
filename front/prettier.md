


# Prettier

+ Prettier is an opinionated code formatter that works with many editors
+ It works with many languages including JavaScript, TypeScript, HTML, CSS, JSON, Markdown, and more.
+ Makes code look the same across teams and editors
+ How it works?
    Automatically adds/removes spaces, semicolons, line breaks
    Normalizes code style (indentation, quotes, trailing commas, etc.)

```bash
npm install -D prettier             # install as dep
npx prettier --write myfile.js      # foramt this file
npx prettier --check myfile.js      # check is formated or not
```

## config .prettierrc
```json
{
    "arrowParens": "always",               // Always include parentheses in arrow functions, e.g., (x) => x
    "bracketSpacing": true,                // Add spaces between brackets in object literals: { foo: bar }
    "embeddedLanguageFormatting": "auto",  // Format embedded code (e.g., in Markdown) automatically
    "htmlWhitespaceSensitivity": "css",    // Respect CSS display property when formatting HTML
    "insertPragma": false,                 // Do NOT add @format pragma to file headers
    "jsxBracketSameLine": false,           // Put `>` of a multi-line JSX element on its own line
    "jsxSingleQuote": false,               // Use double quotes in JSX attributes: <div className="example">
    "printWidth": 80,                      // Wrap lines at 80 characters max
    "proseWrap": "preserve",               // Do not change wrapping in markdown/text (preserve user input)
    "quoteProps": "as-needed",             // Only add quotes around object properties when required
    "requirePragma": false,                // Don’t format files unless they have a @format pragma
    "semi": false,                         // Omit semicolons at the ends of statements
    "singleQuote": false,                  // Use double quotes instead of single quotes
    "tabWidth": 4,                         // Set the number of spaces per indentation level
    "trailingComma": "es5",                // Add trailing commas where valid in ES5 (objects, arrays, etc.)
    "useTabs": false,                      // Use spaces instead of tabs for indentation
    "vueIndentScriptAndStyle": false       // Don't indent `<script>` and `<style>` blocks in Vue files

}
```

### .prettierignore

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





