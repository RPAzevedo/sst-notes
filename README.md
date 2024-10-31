# Monorepo Template

A template to create a monorepo SST ❍ Ion project.

## Get started

1. Use this template to [create your own repo](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template).

2. Clone the new repo.

   ```bash
   git clone MY_APP
   cd MY_APP
   ```

3. Rename the files in the project to the name of your app. 

   ```bash
   npx replace-in-file '/notes/g' MY_APP **/*.* --verbose
   ```

4. Deploy!

   ```bash
   npm install
   npx sst deploy
   ```

6. Optionally, enable [_git push to deploy_](https://ion.sst.dev/docs/console/#autodeploy).

## Usage

This template uses [npm Workspaces](https://docs.npmjs.com/cli/v8/using-npm/workspaces). It has 3 packages to start with and you can add more it.

1. `core/`

   This is for any shared code. It's defined as modules. For example, there's the `Example` module.

   ```ts
   export module Example {
     export function hello() {
       return "Hello, world!";
     }
   }
   ```

   That you can use across other packages using.

   ```ts
   import { Example } from "@aws-monorepo/core/example";

   Example.hello();
   ```

2. `functions/`

   This is for your Lambda functions and it uses the `core` package as a local dependency.

3. `scripts/`

    This is for any scripts that you can run on your SST app using the `sst shell` CLI and [`tsx`](https://www.npmjs.com/package/tsx). For example, you can run the example script using:

   ```bash
   npm run shell src/example.ts
   ```

### Infrastructure

The `infra/` directory allows you to logically split the infrastructure of your app into separate files. This can be helpful as your app grows.

In the template, we have an `api.ts`, and `storage.ts`. These export the created resources. And are imported in the `sst.config.ts`.

---

Join the SST community over on [Discord](https://discord.gg/sst) and follow us on [Twitter](https://twitter.com/SST_dev).

## Development

```bash
$ npx sst dev
```

```text
✓  Complete
   Api: https://clspnpviac.execute-api.ap-southeast-2.amazonaws.com
   ---
   Region: ap-southeast-2
   UserPool: ap-southeast-2_iBKiZdNsx
   UserPoolClient: 9ui8ca52cskupth1gubbm1l9m
   IdentityPool: ap-southeast-2:cab2002b-314e-47c9-a67b-37616d3e7667
```

### Create a Test User

Sign up a user with their email and password:

```bash
aws cognito-idp sign-up \
  --region <COGNITO_REGION> \
  --client-id <USER_POOL_CLIENT_ID> \
  --username admin@example.com \
  --password Passw0rd!
```

Verify the email:

```bash
aws cognito-idp admin-confirm-sign-up \
  --region <COGNITO_REGION> \
  --user-pool-id <USER_POOL_ID> \
  --username admin@example.com
```

### Test the API

```bash
$ npx aws-api-gateway-cli-test \
   --user-pool-id='<USER_POOL_ID>' \
   --app-client-id='<USER_POOL_CLIENT_ID>' \
   --cognito-region='<COGNITO_REGION>' \
   --identity-pool-id='<IDENTITY_POOL_ID>' \
   --invoke-url='<API_ENDPOINT>' \
   --api-gateway-region='<API_REGION>' \
   --username='admin@example.com' \
   --password='Passw0rd!' \
   --path-template='/notes' \
   --method='POST' \
   --body='{"content":"hello world","attachment":"hello.jpg"}'
```

```text
$ npx aws-api-gateway-cli-test \
   --user-pool-id='ap-southeast-2_iBKiZdNsx' \
   --app-client-id='9ui8ca52cskupth1gubbm1l9m' \
   --cognito-region='ap-southeast-2' \
   --identity-pool-id='ap-southeast-2:cab2002b-314e-47c9-a67b-37616d3e7667' \
   --invoke-url='https://clspnpviac.execute-api.ap-southeast-2.amazonaws.com' \
   --api-gateway-region='ap-southeast-2' \
   --username='admin@example.com' \
   --password='Passw0rd!' \
   --path-template='/notes' \
   --method='POST' \
   --body='{"content":"hello world","attachment":"hello.jpg"}'

Authenticating with User Pool
Getting temporary credentials
Making API request
{
  status: 200,
  statusText: 'OK',
  data: {
    userId: 'ap-southeast-2:b78d2223-befd-ca80-2b68-7edca52b1dc7',
    noteId: '19dd7660-9740-11ef-992d-3357df8a5900',
    content: 'hello world',
    attachment: 'hello.jpg',
    createdAt: 1730348703942
  }
}
```

### Test the Billing API

```bash
$ npx aws-api-gateway-cli-test \
   --username='admin@example.com' \
   --password='Passw0rd!' \
   --user-pool-id='<USER_POOL_ID>' \
   --app-client-id='<USER_POOL_CLIENT_ID>' \
   --cognito-region='<COGNITO_REGION>' \
   --identity-pool-id='<IDENTITY_POOL_ID>' \
   --invoke-url='<API_ENDPOINT>' \
   --api-gateway-region='<API_REGION>' \
   --path-template='/billing' \
   --method='POST' \
   --body='{"source":"tok_visa","storage":21}'
```

```text
npx aws-api-gateway-cli-test \
   --username='admin@example.com' \
   --password='Passw0rd!' \
   --user-pool-id='ap-southeast-2_iBKiZdNsx' \
   --app-client-id='9ui8ca52cskupth1gubbm1l9m' \
   --cognito-region='ap-southeast-2' \
   --identity-pool-id='ap-southeast-2:cab2002b-314e-47c9-a67b-37616d3e7667' \
   --invoke-url='https://clspnpviac.execute-api.ap-southeast-2.amazonaws.com' \
   --api-gateway-region='ap-southeast-2' \
   --path-template='/billing' \
   --method='POST' \
   --body='{"source":"tok_visa","storage":21}'
```

### Billing Logic

```bash
$ cd packages/core
$ pnpm test
```
