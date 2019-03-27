# Contributing

1. `master` is always a protected branch — no one can commit or push to it. To contribute changes, branch off of master and make a pull request back to it.
1. Always `git checkout -b` out of `master`.
1. Once you're happy with your branch and think it's ready for code review, make a PR.
1. Make sure the PR title is well written, informative, and as short as possible. Leave out of the title any references to issues — that will go in the description.
1. Make sure the PR description gives good information on what changes the PR is introducing. This will both serve for historical purposes, where context is mostly lost, and help code reviewers greatly.
1. Add `Fixes #x` or `Progress on #y` at the beginning of the PR description, one by line, separated by one empty line from the rest of the description. GitHub will automagically close the referenced issues when the PR is merged.
1. Make sure the automatic checks are passing (CircleCI, Netlify, etc). Our CI pipeline will run all tests automatically for all submitted pull requests, including linting (npm run lint). You can run `npm run lint:fix` for quick, automatic lint fixes.
1. Request code review.
1. Once the review is approved and checks are passing, your branch will be squash merged by a repo owner.
