# Po.et Engineering Testing Process

These are the objectives that we aim to follow when writing new software or modifying existing software.

### Code Coverage

We aim for at least 80% unit test coverage on all new code. Whenever existing code is refactored, we make sure unit tests are written for it if they do not exist already, and we review/refactor existing unit tests if necessary.

As TDD knowledge in the team matures, we can aim for higher coverage up to 90%.

### Test Types

We make use of a mix of unit, integration and functional tests as appropriate. For details on how we use them, see our [Testing Glossary & Definitions](#testing-glossary--definitions).

### Code Review

Part of the code review process should include reviewing the associated unit/integration/functional tests written for the new or refactored code. This includes ensuring the tests are adequate and thorough, and that they are used correctly (i.e., don’t write a unit test with a lot of mocking when a functional test might better serve the purpose).

### Functional Tests

Functional tests need to support true continuous delivery, which means that all user flows need to be tested and confirmed working. Features to test include:

- login
- password change
- api token creation
- posting a work
- viewing all works
- viewing a single work

Functional tests are written in javascript (not typescript) using [TestCafe](https://testcafe.devexpress.com/).
Functional tests and supporting files reside in `tests/functional`

- `tests/functional/features/` contains the tests, broken down by application feature.
- `tests/functional/helpers.js` contains shared helper functions and data.
- `tests/functional/page-objects/` contains selectors and functions for interacting with a particular page.

See [this article](https://medium.com/tech-tajawal/page-object-model-pom-design-pattern-f9588630800b) for more information on page-objects.

## Testing Glossary & Definitions

"We _**unit test**_ a standalone component (**unit**) of code. Then the _**integration test**_ is used to **integrate** two or more components to ensure they work correctly together. Finally, the _**functional test**_ is used to make sure all of the components (i.e., the app) **function** the way our users are expecting."

### Glossary

**Unit tests** - ensure that individual components work in isolation from each other

**Integration tests** - ensure that various units work together correctly

**Functional tests** - automated tests which ensure that the application does what it’s supposed to do from the point of view of the user

**End-to-end (E2E) tests** - just another name for functional tests (let's stick with "functional tests" :smile:)

**Smoke tests** - a subset of functional tests that are safe to run against the production environment after a deployment to ensure critical functionality in the app still work as expected

### Definitions

It's important that we have a shared understanding when we use terms like "functional test", since they might have different meanings for each of us based on our past experience. This is a condensed version of [Eric's article](https://www.sitepoint.com/javascript-testing-unit-functional-integration/) just for our reference (please read the whole article), and to put us all on the same page when we talk about tests.

>Unit tests, integration tests, and functional tests are all types of automated tests which form essential cornerstones of continuous delivery. Each type of test has a unique role to play. Use all of them, and make sure you can run each type of test suite in isolation from the others.
>
>* **Unit tests** ensure that individual components of the app work as expected. Assertions test the component API.
>* **Integration tests** ensure that component collaborations work as expected. Assertions may test component API, UI, or side-effects (such as database I/O, logging, etc...)
>* **Functional tests** ensure that the app works as expected from the user’s perspective. Assertions primarily test the user interface.
>
>During continuous integration, tests are frequently used in three ways:
>
>* **During development**, for developer feedback. Unit tests are particularly helpful here.
>* **In the staging environment**, to detect problems and stop the deploy process if something goes wrong. Typically the full suite of all test types are run at this stage.
>* **In the production environment**, a subset of production-safe functional tests known as smoke tests are run to ensure that none of the critical functionality was broken during the deploy process.

Let's aim to keep our testing definitions focused on these three terms, unless we eventually find the need to introduce another term. For now, these three terms cover us well from development through to production, so let's keep it simple.
