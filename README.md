# E2E code coverage with NX + Cypress + Angular + Istanbul

This is a starter repository to have code coverage with Istanbul in an Angular v16 with Nx and Cypress.

Enjoy!

## Create NX Workspace

```
npx create-nx-workspace@latest
```

Reply "NO" standalone components and "YES" to routing

## Install Dependencies

```
npm i ngx-build-plus @cypress/code-coverage @istanbuljs/nyc-config-typescript @jsdevtools/coverage-istanbul-loader istanbul-lib-coverage nyc --save-dev
```

## Create Webpack config

Create a `webpack.coverage.js` file in the root of your nx workspace. And add this config:

```
const path = require('path');

module.exports = {
  module: {
    rules: [
      {
        test: /\.(js|ts)$/,
        loader: '@jsdevtools/coverage-istanbul-loader',
        options: { esModules: true },
        enforce: 'post',
        include: [path.join(__dirname, '{your-app-directory}')],
        exclude: [/\.(cy|spec)\.ts$/, /node_modules/],
      },
    ],
  },
};
```

## Modify your app project.json

Open your app `project.json` and add this to `targets`:

```
"serve-nx-demo-app-e2e": {
  "executor": "ngx-build-plus:dev-server",
  "defaultConfiguration": "",
  "options": {
    "browserTarget": "{your-app-directory}:build:development",
    "extraWebpackConfig": "./webpack.coverage.js",
    "port": 3000
  }
}
```

## Modify your e2e project.json

Add a new configuration to add coverage inside `targets.e2e.configurations`:

```
"coverage": {
  "devServerTarget": "{your-app-directory}:serve-nx-demo-app-e2e"
}
```

## Import cypress code coverage library

Open `apps/your-app-directory-e2e/src/support/e2e.ts` and add this line:

```
import '@cypress/code-coverage/support';
```

## Update cypress.config.ts

Open `apps/your-app-directory-e2e/cypress.config.ts` and replace it with this:

```
import { defineConfig } from 'cypress';
import { nxE2EPreset } from '@nx/cypress/plugins/cypress-preset';

export default defineConfig({
  e2e: {
    ...nxE2EPreset(__dirname),
    setupNodeEvents(on, config) {
      // eslint-disable-next-line @typescript-eslint/no-var-requires
      require('@cypress/code-coverage/task')(on, config);
      return config;
    }
  }
});
```

Run `nx e2e nx-demo-app-e2e --configuration=coverage` to generate your coverage then open in your browser `apps/your-app-directory-e2e/coverage/lcov-report/index.html` to view your coverage results.
