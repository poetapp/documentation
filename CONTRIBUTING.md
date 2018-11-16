# Contributing

1. `master` is always a protected branch — no one can commit or push to it.
1. Always `git checkout -b` out of `master`.
1. Always reference the issue number the branch addresses in the branch name (e.g., `feature(#22)` or `some-new-thing-#23`). Doing this will trigger CodeTree to move the referenced issue from Backlog/Planning to In Progress.
1. Your branch is your branch. 
Commit whatever you want to it. 
Commit just to have Travis run while you continue coding if you want to. 
Commit even if you know it'll break the build. 
Commit with whatever commit message you want. Even "wip" and "typo" are acceptable.
1. Feel free to make a PR any time — but **DO** label it `work in progress` if it's not ready to be reviewed and merged, so people don't go and code-review it in vain.
1. Once you're happy with your branch and think it's ready for code review, make a PR if you hadn't already and remove the `work in progress` label.
1. Make sure the PR title is well written, informative, and as short as possible. Leave out of the title any references to issues — that will go in the description.
1. Make sure the PR description gives good information on what changes the PR is introducing. This will both serve for historical purposes, where context is mostly lost, and help code reviewers greatly.
1. Add `Fixes #x` or `Progress on #X` at the end of the PR description, one by line, separated by one empty line from the rest of the description. GitHub will automagically close the referenced issues when the PR is merged.
1. Make sure the automatic checks are passing (Travis, Netlify, etc).
1. Request code review.
1. Once the review is approved and checks are passing, you're ready to merge.
1. Only `squash merging` is allowed. All your many commits will be squashed into a single, beautiful commit. The `master` commit history should read as a list of features.
1. When you click `merge` GitHub will allow you to set the commit's title and description. Both will be pre-filled for you, using the PR's title for the commit title and the list of commit messages for the description. Delete the description completely, leaving it blank. Make sure the commit title matches the PR title, ending in (#\<pr-number>) so the commit links back to the PR.

## Example
See [this commit](https://github.com/poetapp/node/commit/d63ea002c91bbda59faedd47acc7ac15569fcad7). The commit message is `Fix Randomly Failing Integration Tests (#64)`. No extended description. You can click on the #64 and it'll take you to [the corresponding PR](https://github.com/poetapp/node/pull/64). 

That PR has a more detailed description, the results of the code review, and links to the [Integration Test getWork200 Fails Randomly](https://github.com/poetapp/node/issues/61) issue.
