

# Alternatives


| Feature          | NPM      | Yarn    | PNPM       | Bun        |
| ---------------- | -------- | ------- | ---------- | ---------- |
| Speed            | Medium   | Fast    | Very Fast  | Fastest    |
| Security         | Medium   | High    | High       | High       |
| Disk Usage       | High     | Medium  | Low        | Low        |
| Offline Support  | Partial  | Full    | Full       | Full       |
| Monorepo Support | Limited  | Good    | Excellent  | Good       |
| Compatibility    | ✅       | ✅      | ✅         | Limited    |
| Lockfile         | `package-lock.json` | `yarn.lock` | `pnpm-lock.yaml` | `bun.lockb` |

npm → Default, good for small projects.
Yarn → Faster than npm, good for large projects.
pnpm → Best for disk efficiency & monorepos.
Bun → Cutting-edge speed, experimental but promising.


## NPM

```bash
npm init           # Create package.json (step-by-step)
npm init -y        # Create package.json with default values
npm install        # Install all dependencies listed in package.json
npm search axios         # Search for a package
npm info axios           # Show info about a package
npm show axios           # Alias for info
npm install axios        # Install latest version (adds to dependencies)
npm install axios --save     # --save is default (adds to dependencies)
npm install axios -g     # Install globally
npm install axios --save-dev   # Install as dev dependency
npm install axios --production   # Install only in production
npm install axios@1.2.3  # Install specific version
npm uninstall axios      # Uninstall package
npm uninstall -g axios   # Uninstall global package
npm update               # Update all packages
npm update axios         # Update one package
npm outdated             # Check for outdated packages
npm list                 # List installed packages
npm list -g              # List globally installed packages
npm ls axios             # Show version of a specific package
npm prune                # Remove extraneous packages
npm cache clean --force  # Clear npm cache
npm audit                # Check for vulnerabilities
npm audit fix            # Fix vulnerabilities automatically
npm ci                   # Clean install from package-lock.json (used in CI)
npm login                # Login to npm registry
npm publish              # Publish your package
npm unpublish mypackage      # Unpublish (use with caution)
```


## Yarn

```bash
yarn init             # interactive init
yarn init -y          # init with default values
yarn add axios              # add to dependencies
yarn add axios@1.2.3        # specific version
yarn add axios -D           # add to devDependencies
yarn global add axios       # install globally
yarn remove axios           # remove from deps
yarn global remove axios    # remove global pkg
yarn upgrade                # upgrade all
yarn upgrade axios          # upgrade one
yarn upgrade axios@latest   # upgrade to latest
yarn run test      # run script definde in package.json
yarn start                  # shorthand for yarn run start
yarn list                   # list dependencies
yarn global list
yarn list --depth=0         # list top-level only
yarn info axios             # info about a package
yarn search axios            # seacrh for a package
yarn install                # install from yarn.lock
yarn install --force        # force reinstallation
yarn install --production   # skip devDependencies
yarn cache list             # list cache
yarn cache clean            # clean cache
```






















