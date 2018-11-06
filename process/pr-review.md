# Pull Request Reviews

When requested to review a PR, there are 3 possible options you can choose after reviewing: `approve`, `request changes` or `comment`.

- **ONLY** select `approve` if you:
    - have thoroughly reviewed all of the changes to the code, 
    - understand how those changes will affect the existing code base, and
    - are either sure that CI test suites cover everything or you have manually tested.
- If you do not select `approve` you should still complete the checklist below.
- If you skipped an item (maybe you didn't have time) but the item applies, please indicate in the comments.

## PR Review Checklist

When reviewing a PR, copy the list below into the "Review summary" textbox when you `accept`/`request changes`/`comment`.

```
## PR Review Checklist
<!--
- Tick the box ([x]) ONLY IF THE PR SATISFIES THE REQUIREMENT
- Leave it blank if:
    - it needs more work (indicate what needs work in the comments 
    section below and/or inline with code comments) or
    - you skipped the item (indicate it was skipped in the comments)
- If the task is not applicable to the PR, please signal ([N/A])
-->

* [ ] Tested changes manually
* [ ] Checked accidental architectural/style changes
* [ ] Reviewed entire diff
* [ ] Unit tests
* [ ] Documentation
* [ ] Filenames and locations

### PR Reviewer Comments
```

### Checklist Details

PRs differ in size and scope, so not all PRs will require all of the checklist tasks, which are explained below in more detail. Please familiarize yourself with each of them.

1. I tested the PR code manually and it works. (I downloaded the branch and ran the code to review/test the changes).
1. I attest that changes are not accidentally introducing architectural or code style changes.
1. I reviewed the entire diff and approve of the code changes.
1. If code has been added, I have checked that new unit tests are included and verified that they work correctly.
1. If documentation is needed, I have reviewed the documents and am satisfied that they cover the changes thoroughly.
1. The added/edited files are named appropriately and located correctly in the project directory structure.

 A simple change to the README file would only require the documentation item to be confirmed. Adding a new README would require the last two. A new feature being introduced would likely require all items.

## PR Process

1. New PR is created by PR creator and at least 1 PR reviewer is selected, including one from QE team if required (see below).
1. PR creator self-assigns the PR, enabling everyone to scan down the list of open PRs and easily see who is responsible.
1. PR reviewer references the PR checklist above to determine which items are relevant to the PR.
1. PR reviewer reviews the code changes.
1. PR reviewer decides whether to `approve`, `request changes` or `comment`.
1. PR reviewer copies the PR checklist and paste it into his/her "Review summary" comment.
1. PR reviewer completes each item in the checklist to confirm the PR satisfies all requirements.
1. PR creator only merges PR if (a) at least one PR reviewer `approve`s AND (b) all relevant items from the checklist have been filled-in and completed.

### QE Review Requirement

QE should review all additions and new features: all major/minor code changes or bug fixes to functional or testing code. In fact, they should be involved with the definition of "done" for the checklist tracking ticket for all major new features, and capture those requirements in automated functional tests.

QE should NOT be required for documentation-only updates (unless the documentation defines new features we plan to build, and their feedback is necessary to help define the definition of done for that feature).

## Guidelines

- It all boils down to responsibility. It is important that responsibility is correctly distributed between the PR creator and the PR reviewers. By approving a PR, you are basically making yourself responsible for that PR as much as the creator, but the creator is responsible for merging PRs even after they are approved. If the PR creator merges a PR that does not have the checklist properly completed by the PR reviewers, both the PR creator and the PR reviewers are shirking their responsibilities. The PR creator is the ultimate controller who ensures the PR was reviewed correctly and completely.
- A single PR should not introduce architectural changes AND features at the same time.
- PR reviewers - you are strongly encouraged to thoroughly review large PRs.
- PR creators - you are strongly encouraged to keep PRs **as short as possible** so you don't place too much burden on reviewers and jam up the PR process.
