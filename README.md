this is a minimal reproduction of an issue i've noticed with [vitest workspaces](https://vitest.dev/guide/workspace.html).

corresponding issue: https://github.com/vitest-dev/vitest/issues/7759

## reproduction steps

below are the steps to reproduce the issue. see [`package.json`](./package.json) for node.js + `vitest` versions.

1. install the dependencies:

   ```sh
   npm ci
   ```

1. from the root of this directory, run the tests using the following command (and note how the tests pass as expected):

   ```sh
   npx vitest
   ```

1. navigate to the subdirectory and run the same command:

   ```sh
   cd packages/a
   npx vitest
   ```

   `vitest` unexpectedly throws the following error:

   ```
   ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Startup Error ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯
   Error: No projects were found. Make sure your configuration is correct. The workspace: [
       "packages/*"
   ].
       at resolveWorkspace (file:///[redacted]/vitest-workspaces-issue/node_modules/vitest/dist/chunks/cli-api.Ckwz_xSb.js:12027:11)
       at async Vitest._setServer (file:///[redacted]/vitest-workspaces-issue/node_modules/vitest/dist/chunks/cli-api.Ckwz_xSb.js:12532:22)
       at async handler (file:///[redacted]/vitest-workspaces-issue/node_modules/vitest/dist/chunks/cli-api.Ckwz_xSb.js:13488:11)
       at async _createServer (file:///[redacted]/vitest-workspaces-issue/node_modules/vite/dist/node/chunks/dep-DDxXL6bt.js:44191:20)
       at async createViteServer (file:///[redacted]/vitest-workspaces-issue/node_modules/vitest/dist/chunks/cli-api.Ckwz_xSb.js:10305:18)
       at async createVitest (file:///[redacted]/vitest-workspaces-issue/node_modules/vitest/dist/chunks/cli-api.Ckwz_xSb.js:13528:18)
       at async prepareVitest (file:///[redacted]/vitest-workspaces-issue/node_modules/vitest/dist/chunks/cli-api.Ckwz_xSb.js:13947:15)
       at async startVitest (file:///[redacted]/vitest-workspaces-issue/node_modules/vitest/dist/chunks/cli-api.Ckwz_xSb.js:13878:15)
       at async start (file:///[redacted]/vitest-workspaces-issue/node_modules/vitest/dist/chunks/cac.CeVHgzve.js:1595:17)
   ```

## workarounds

i've discovered a couple workarounds to get step 3 above working — both of which seem less than ideal. each bullet point below is a separate workaround.

- create an empty `vitest.config.ts` file in the subdirectory:

  ```sh
  touch packages/a/vitest.config.ts
  ```

  this isn't ideal because an empty config file shouldn't be necessary.

- delete the root `vitest.config.ts` file and create a file at the root of this directory called `vitest.workspace.ts` that contains the following:

  ```ts
  import { defineWorkspace } from "vitest/config";

  export default defineWorkspace(["packages/*"]);
  ```

  this isn't ideal because the `vitest.config.ts` file must be deleted.
