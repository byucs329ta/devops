# Unit testing

Now that you have the JWT Pizza Service in your hands you can start to assure its quality by writing unit tests. As part of your testing you will generate a coverage report.If the coverage requirements of your tests do not meet the required thresholds, then an error is generated.

If you haven't already done so, make sure you fork the [jwt-pizza-service](../jwtPizzaService/jwtPizzaService.md) repository and clone it to your development environment.

Within your fork of the jwt-pizza-service repository, follow the pervious instruction and install the [Jest](../jest/jest.md) testing framework.

```sh
npm install -D jest supertest
```

```json
  "devDependencies": {
    "jest": "^29.7.0",
    "supertest": "^6.3.4"
  }
```

In order to test service endpoints you need to decompose the creation of the Express app from the use of it for serving HTTP requests or running tests.

![](../serviceTesting/endpointRequests.png)

This is already done in `jwt-pizza-service` and so you don't need to worry about it, but you should clearly understand the implications of the pattern.

## Coverage

Add the Jest config file, `jest.config.json`, so coverage would be turned on.

```json
{
  "collectCoverage": true,
  "coverageReporters": ["json-summary", "text"]
}
```

This requests the generation of different coverage reports. The `text` report generates a summary that is output to the console window. The `json-summary` report is created in the `coverage` directory and contains a JSON representation of the coverage information.

Note that the `.gitignore` file ignores the `coverage` directory so that the resulting coverage reports don't get added to GitHub.

## NPM test script

Add the `test` script to `package.json` so that it knows to use Jest for testing.

```json
  "scripts": {
    "run": "node index.js",
    "test": "jest"
  },
```

## Creating the first test

Now that you have set up jwt-pizza-service to be tested with Jest we can make sure it is all working right by writing a simple test. Create a file named `authRouter.test.js` in the `src/routes` directory and place the following `hello world` test in file.

```sh
test('hello world', () => {
  expect('hello' + ' world').toBe('hello world');
});
```

Now execute the test by either using the VS Code Jest extension or by opening up a command console and running `npm test`. If you run it from the command console you should see the following result.

```sh
➜  npm test

 PASS  src/routes/authRouter.test.js
  ✓ hello world (1 ms)

----------|---------|----------|---------|---------|-------------------
File      | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
----------|---------|----------|---------|---------|-------------------
All files |       0 |        0 |       0 |       0 |
----------|---------|----------|---------|---------|-------------------
Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
```

This is not very interesting from a coverage perspective, but it does demonstrate that you are configured correctly.

Make sure you commit and push you code at this important milestone.

## Write a real test

Now you get to start writing all the tests necessary to get at least 80% coverage of the `jwt-pizza-service` code. You should review everything you learned in the following instruction topics before proceeding.

- [Jest](../jest/jest.md)
- [Service Testing](../serviceTesting/serviceTesting.md)

Let's write the first test together. Replace the `hello world` test found in `src/authRouter.test.js` with the following.

```js
const request = require('supertest');
const app = require('../service');

const testUser = { name: 'pizza diner', email: 'reg@test.com', password: 'a' };
let testUserCookie;

beforeAll(async () => {
  testUser.email = Math.random().toString(36).substring(2, 12) + '@test.com';
  const registerRes = await request(app).post('/api/auth').send(testUser);
  testUserCookie = registerRes.headers['set-cookie'];
});

test('login', async () => {
  const loginRes = await request(app).put('/api/auth').send(testUser);

  expect(loginRes.status).toBe(200);

  const cookies = loginRes.headers['set-cookie'];
  expect(cookies[0]).toMatch(/token=.+; Path=\/; HttpOnly; Secure; SameSite=None/);

  const { password, ...user } = { ...testUser, roles: [{ role: 'diner' }] };
  expect(loginRes.body).toMatchObject(user);
});
```

In this code the `beforeAll` function registers a random user every time the tests run. You can use the user and its associated cookie for tests that require an existing registered user.

The `login` test logs the test user in and verifies that it gets back an authorization token cookie along with the expected user information.

This one test should bump your line coverage up to **50%**. Only 30% more to go. You can verify this by running the test.

```sh
➜  npm test

 PASS  src/routes/authRouter.test.js
  ✓ login (65 ms)

---------------------|---------|----------|---------|---------|-----------------------------------
File                 | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
---------------------|---------|----------|---------|---------|-----------------------------------
All files            |   49.83 |     32.3 |      40 |   50.51 |
 src                 |   82.85 |       50 |   42.85 |   82.35 |
  config.js          |     100 |      100 |     100 |     100 |
  endpointHelper.js  |   66.66 |      100 |   66.66 |      60 | 3-4
  service.js         |   85.71 |       50 |      25 |   85.71 | 26,33,40-41
 src/database        |   41.46 |    46.66 |   53.57 |   41.71 |
  database.js        |   40.32 |    45.45 |   43.47 |   40.65 | 23-26,39-41,58,70-184,193-197,297
  testData.js        |      45 |       50 |     100 |      45 | 7-17,26-48,57-96,107-134
 src/model           |     100 |      100 |     100 |     100 |
  model.js           |     100 |      100 |     100 |     100 |
 src/routes          |   51.04 |    16.12 |      20 |   53.26 |
  authRouter.js      |   65.78 |    45.45 |   44.44 |   69.44 | 39-44,50-53,61,81-82
  franchiseRouter.js |   34.21 |        0 |       0 |   36.11 | 38,46-52,60-65,73-78,86-91,99-105
  orderRouter.js     |      55 |        0 |       0 |      55 | 25,34,43-54
---------------------|---------|----------|---------|---------|-----------------------------------
Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
```

Now it is your turn. Keep writing tests until you have at least 80% coverage.

## Linting

In addition to assuring the quality of the code with automated tests we also want to make sure that there isn't any lint in there. So let's install `eslint` and see what it reports.

We start by installing eslint as a development dependency. Review, or following, the [lint instructions](../lint/lint.md) if you are unclear how to do this. Once this is done you can run eslint from your command console.

```sh
npm run lint

/Users/lee/Desktop/demo/student-jwt-pizza-service/src/routes/authRouter.test.js
   6:1  error  'beforeAll' is not defined  no-undef
  11:1  error  'test' is not defined       no-undef
  13:3  error  'expect' is not defined     no-undef
...
```

This will spew out a bunch of errors. Most of them should be because we are using Jest, and we haven't told `eslint` about the types that Jest defines. You can correct this by modifying the `eslint.confg.mjs` file to include the Jest definitions.

```js
  { languageOptions: { globals: globals.jest } },
```

The config file should look something like this when you are done.

```js
import globals from 'globals';
import pluginJs from '@eslint/js';

export default [
  { files: ['**/*.js'], languageOptions: { sourceType: 'commonjs' } },
  { languageOptions: { globals: globals.node } },
  { languageOptions: { globals: globals.jest } },
  pluginJs.configs.recommended,
];
```

Now when you run `npm run lint` you should only see errors that are due to problems in your code or the `jwt-pizza-service` code. Go fix the problems you created until there are no linting errors.

## Reporting problems with JWT Pizza Service

If during the testing or linting process you discover problems with `jwt-pizza-service` then you can open an issue or pull request on the repository to report them. This will help future students and also give you experience with this vital development process.
