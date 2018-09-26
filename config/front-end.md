# Front End Configuration 

## Netlify

### _redirects

Netlify supports _redirect file which we are using to forward from the front-end to `Frost-Api` for requests that use `Frost-Client`. We are using webpack to copy the `./_redirects.{environment}` into the `./_redirects` file for Netlify, where environment is currently defined in the package.json scripts. Netlify uses the context defined in `netlify.toml` to determine the correct script to run.


In `frost-web/_redirects.production`: this forwards from the current `/api/*` path to `https://api.frost.po.et/:splat.` The * following `/api/` indicates that anything following `/api/` will replace `:splat` in the rewrite. 200 indicates that we are rewriting the path.

```markdown
/api/*  https://api.frost.po.et/:splat  200
/* /index.html 200
```
---
In `frost-web/webpack.config.js`: This copies the ./_redirects.{environment} file into ./_reirects. POET_ENV is set in package.json build scripts.

```js
const environment = process.env.POET_ENV || 'development'
const redirects = `./_redirects.${environment}`
const nonDevelopmentPlugins = [
    new CopyWebpackPlugin([
      {
        from: redirects,
        to: './_redirects',
        toType: "file",
      }
    ])
  ]

  const environmentSpecificPlugins = environment === 'development'
    ? developmentPlugins
    : nonDevelopmentPlugins
```
---
In `/package.json`: Here is where we assign the POET_ENV variable.
```json
"scripts": {
  "build:production": "POET_ENV=production webpack",
  "build:testing": "POET_ENV=testing webpack",
  "build:preview": "stylelint './src/**/*.scss' && tslint -p ./tsconfig.json && npm run build:testing",
},
```
---
In `/netlify.toml`: Here is where the directory to publish in Netlify is set. `[context.CONTEXT]` determines the script to run.
```toml
[build]
  publish = "dist"
  
# Production context: All deploys to the main
# repository branch will inherit these settings.
[context.production]
  command = "npm run build:production"
  
# Deploy Preview context: All Deploy Previews
# will inherit these settings.
[context.deploy-preview]
  command = "npm run build:preview"
```
---
#### Example Adding New Frost-Api Endpoint

1) Create a new `./_redirects` file e.g. `frost-web/_redirects.staging` by copying one of the other redirect files and modifying the frost endpoint.

```markdown
/api/*  https://api.frost.staging.po.et/:splat  200
/* /index.html 200
```
---
2) In `package.json`: Create a new build script and assign `POET_ENV` variable.
```json
"scripts": {
  "build:production": "POET_ENV=production webpack",
  "build:testing": "POET_ENV=testing webpack",
  "build:preview": "stylelint './src/**/*.scss' && tslint -p ./tsconfig.json && npm run build:testing",
  "build:staging": "POET_ENV=staging webpack",
},
```
---
3) In `netlify.toml`: Create a new `[context.staging]` and set the build command to the newly created script.
```toml
[build]
  publish = "dist"
  
# Production context: All deploys to the main
# repository branch will inherit these settings.
[context.production]
  command = "npm run build:production"
  
# Deploy Preview context: All Deploy Previews
# will inherit these settings.
[context.deploy-preview]
  command = "npm run build:preview"
  
[context.staging]
  command = "npm run build:staging"
```
---
## Local Development Environment
Because we are using Netlify redirects, we have to handle the redirects for our local dev environment as well. This is handled inside of `devServer.js`. 

`devServer.js`: Redirects `/api` to local-development `Frost-Api` port.
```.js
const HOST_WEBPACK_SERVER = '0.0.0.0'
const PORT_API = 3000
const HOST_API_PROXY = isDockerized ? 'nginx' : HOST_WEBPACK_SERVER

const server = new webpackDevServer(compiler, {
//...
  proxy: {
    '/api': {
      target: process.env.FROST_API || `http://${HOST_API_PROXY}:${PORT_API}`,
      secure: false,
      pathRewrite: {'^/api' : ''}
    }
  },
//...
})
```

## Configuration + /env

`src/configuration.ts`: Defines Configuration interface and requires 'Configuration' which is built with webpack.
```ts
interface Configuration {
  readonly apiUrl: string
  readonly frostApiUrl: string
}

export const Configuration: Configuration = require('Configuration')
```
---
`webpack.config.js`: Determines which .env file to use to assign to Configuration variable based on `POET_ENV`, which is set in package.json build script. 
```js
const environment = process.env.POET_ENV || 'development'
const configurationPath = `./env/${environment}.json`
module.exports = {
  //...
  resolve: {
    extensions: ['.webpack.js', '.web.js', '.ts', '.tsx', '.js', '.json', '.css', '.scss'],
    alias: {
      Configuration: path.resolve(configurationPath)
    },
    modules:  [
      path.join(__dirname, "src"),
      "node_modules"
    ],
  },
  //...
}
```
---
`package.json`: The `POET_ENV` is set in the build script here.
```json
"scripts": {
  "build:production": "POET_ENV=production webpack",
  "build:development": "POET_ENV=development webpack",
},
```
---

`/env/production.json`: variable definitions for production environment.
```.json
{
  "apiUrl": "https://api.frost.po.et/",
  "frostApiUrl": "/api"
}
```
---
`/env/devlopment.json`: variable definitions for development environment.
```
{
  "apiUrl": "http://localhost:4000",
  "frostApiUrl": "/api"
}
```
---
#### Example Adding New Configuration Variable

1) In `src/configuration.ts`: Add new attribute `nodeUrl` to Configuration interface.
```ts
interface Configuration {
  readonly apiUrl: string
  readonly frostApiUrl: string
  readonly nodeUrl: string
}

export const Configuration: Configuration = require('Configuration')
```
---
2) In all files inside of `env/` folder add the new `nodeUrl` attribute with the correct assignment based on environment.

`/env/production.json`
```.json
{
  "apiUrl": "https://api.frost.po.et/",
  "frostApiUrl": "/api",
  "nodeUrl": "https://api.explorer.po.et"
}
```
---
`/env/devlopment.json`: 
```
{
  "apiUrl": "http://localhost:4000",
  "frostApiUrl": "/api",
  "nodeUrl": "https://localhost:18080"
}
```
---
#### Example Adding New Configuration Environment

1) Create a new file in `/env` with the new environment name: e.g `/env/staging.json`

`/env/staging.json`
```.json
{
  "apiUrl": "https://api.frost.staging.po.et/",
  "frostApiUrl": "/api",
  "nodeUrl": "https://api.explorer.staging.po.et"
}
```
---
2) In `package.json`: Create a new build script and assign `POET_ENV` variable.
```json
"scripts": {
  "build:production": "POET_ENV=production webpack",
  "build:development": "POET_ENV=development webpack",
  "build:staging": "POET_ENV=staging webpack",
},
```
