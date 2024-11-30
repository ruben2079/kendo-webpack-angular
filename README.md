# KendoWebpackAngular

This project was generated using [Angular CLI](https://github.com/angular/angular-cli) version 19.0.0.

## Development server

To start a local development server, run:

```bash
ng serve
```

Once the server is running, open your browser and navigate to `http://localhost:4200/`. The application will automatically reload whenever you modify any of the source files.

## Code scaffolding

Angular CLI includes powerful code scaffolding tools. To generate a new component, run:

```bash
ng generate component component-name
```

For a complete list of available schematics (such as `components`, `directives`, or `pipes`), run:

```bash
ng generate --help
```

## Building

To build the project run:

```bash
ng build
```

This will compile your project and store the build artifacts in the `dist/` directory. By default, the production build optimizes your application for performance and speed.

## Running unit tests

To execute unit tests with the [Karma](https://karma-runner.github.io) test runner, use the following command:

```bash
ng test
```

## Running end-to-end tests

For end-to-end (e2e) testing, run:

```bash
ng e2e
```

Angular CLI does not come with an end-to-end testing framework by default. You can choose one that suits your needs.

## Additional Resources

For more information on using the Angular CLI, including detailed command references, visit the [Angular CLI Overview and Command Reference](https://angular.dev/tools/cli) page.


<!--

==================== webpack.config.js -- SHELL APP ===========================

const ModuleFederationPlugin = require("webpack/lib/container/ModuleFederationPlugin");
const path = require("path");

module.exports = {
  output: {
    uniqueName: "portalWebApp",
    publicPath: "auto",
    scriptType: "text/javascript",
  },
  optimization: {
    runtimeChunk: false,
  },
  resolve: {
    alias: {},
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: ["html-loader"],
      },
    ],
  },
  experiments: {
    outputModule: true,
  },
  plugins: [
    new ModuleFederationPlugin({
      // For remotes (please Add this 5 Line)
      name: "portalWebApp",
      filename: "remoteEntry.js",
      remotes: {
        astrPortFolioMasterWebApp:
          "astrPortFolioMasterWebApp@http://localhost:3002/remoteEntry.js",
        mfe1: "mfe1@http://localhost:4001/remoteEntry.js",
      },

      shared: {
        "@angular/core": { singleton: true, strictVersion: true, eager: true },
        "@angular/common/": {
          singleton: true,
          strictVersion: true,
          eager: true,
        },
        "@angular/router": {
          singleton: true,
          strictVersion: true,
          eager: true,
        }
        // react: { singleton: true, eager: true },
        // "react-dom": { singleton: true, eager: true },
      },
    }),
  ],
  // devServer: {
  //   port: 1112,
  //   open: true,
  //   hot: false,
  //   liveReload: false,
  //   historyApiFallback: true,
  //   static: {
  //     directory: path.join(__dirname, 'dist')
  //   }
  // }
};

==================== webpack.config.js -- SHELL APP -- END ===========================

==================== SHELL APP - Route ===============================================

import { loadRemoteModule } from '@angular-architects/module-federation';
import {
  WebComponentWrapper,
  WebComponentWrapperOptions,
} from '@angular-architects/module-federation-tools';
import { Routes } from '@angular/router';
import { MsalGuard } from '@azure/msal-angular';
// import { MsalGuard } from '@azure/msal-angular';
// import { UserNotAllowedComponent } from './layout/user-not-allowed/user-not-allowed.component';
// import { ServerErrorComponent } from './layout/server-error/server-error.component';

export const routes: Routes = [
  {
    path: '',
    pathMatch: 'full',
    loadChildren: () =>
      import('./content-providers/content-provider.module').then(
        (m) => m.ContentProviderModule
      ),
    canActivate: [MsalGuard],
  },
  {
    path: 'mfe1',
    loadChildren: () => {
      return loadRemoteModule({
        type: 'module',
        remoteEntry: 'http://localhost:4001/remoteEntry.js',
        exposedModule: './OrderModule',
      })
        .then((m) => m.OrderModule)
        .catch((e) => console.log(e));
    },
  },
  {
    path: 'astrPortFolioMasterWebApp',
    loadChildren: () => {
      return loadRemoteModule({
        type: 'module',
        remoteEntry: 'http://localhost:3002/remoteEntry.js',
        exposedModule: './astrPortFolioMasterWebAppModule',
      })
        .then((m) => m.astrPortFolioMasterWebAppModule)
        .catch((e) => console.log(e));
    },
  },
  {
    path: 'angular1',
    component: WebComponentWrapper,
    data: {
      remoteEntry:
        'https://nice-grass-018f7d910.azurestaticapps.net/remoteEntry.js',
      remoteName: 'angular1',
      exposedModule: './web-components',
      elementName: 'angular1-element',
    } as WebComponentWrapperOptions,
  },
  {
    path: 'react1',
    component: WebComponentWrapper,
    data: {
      remoteEntry:
        'https://witty-wave-0a695f710.azurestaticapps.net/remoteEntry.js',
      remoteName: 'react',
      exposedModule: './web-components',
      elementName: 'react-element',
    } as WebComponentWrapperOptions,
  },
  {
    path: 'vue',
    component: WebComponentWrapper,
    data: {
      remoteEntry:
        'https://mango-field-0d0778c10.azurestaticapps.net/remoteEntry.js',
      remoteName: 'vue',
      exposedModule: './web-components',
      elementName: 'vue-element',
    } as WebComponentWrapperOptions,
  },

  //   {
  //     path: 'usernotallowed',
  //     component: UserNotAllowedComponent,
  //   },

  //   {
  //     path: '**',
  //     component: ServerErrorComponent,
  //   },
];


==================== SHELL APP - Route ===============================================

==================== webpack.config.js -- MFE / REMOTE APP ===========================

const ModuleFederationPlugin = require("webpack/lib/container/ModuleFederationPlugin");
const {shareAll, SharedMappings} = require("@angular-architects/module-federation/webpack");
const Path = require("path");
const sharedMappings = new SharedMappings();

sharedMappings.register(
  Path.join(__dirname, 'tsconfig.json'), []
)
module.exports = {
    output: {
      uniqueName: "astrPortFolioMasterWebApp",
      publicPath: "auto",
      scriptType: "text/javascript",
    },
    optimization: {
      runtimeChunk: false
    },  
    resolve: {
      alias: {
        ...sharedMappings.getAliases(),
      }
    },
    experiments: {
      outputModule: true
    },
    plugins: [
      new ModuleFederationPlugin({
   
          // For remotes (please Add this 5 Line)
          name: "astrPortFolioMasterWebApp",
          filename: "remoteEntry.js",
          exposes: {
              './astrPortFolioMasterWebAppModule': './src/app/layout/layout.module.ts',
          },       
         
          shared: {
            "@angular/core": { singleton: true, strictVersion: true, eager: true },
            "@angular/common/": { singleton: true, strictVersion: true, eager: true },
            "@angular/router": { singleton: true, strictVersion: true, eager: true },
            }
      }),
      sharedMappings.getPlugin()
    ]
   };

==================== webpack.config.js -- MFE / REMOTE APP - END =====================

==================== MFE / Remote - Route =======================================================

import { Routes } from '@angular/router';
import { MsalGuard } from '@azure/msal-angular';
import { UserNotAllowedComponent } from './layout/user-not-allowed/user-not-allowed.component';
import { ServerErrorComponent } from './layout/server-error/server-error.component';

export const routes: Routes = [
  // {
  //   path: '',
  //   pathMatch: 'full',
  //   redirectTo: 'portfolio'
  // },
  {
    path: 'portfolio',
    loadChildren: () =>
      import('./layout/layout.module').then((m) => m.MainLayoutModule),
      canActivate: [MsalGuard]
  },

  {
    path: 'usernotallowed',
    component: UserNotAllowedComponent,
  },

  {
    path: '**',
    component: ServerErrorComponent,
  },
];
==================== MFE / Remote - Route - END =======================================================
-->
