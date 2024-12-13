# Using Policy Validation for GitHub with SignPath.io

This repository demonstrates how SignPath validates GitHub rulesets and other policies.

## References 
* See [.signpath/policies/release-signing.yml](.signpath/policies/executable/release-signing.yml) for the policy definition.
* See [.github/workflows/sign.yml](.github/workflows/sign.yml) for the workflow definition.

## Demo cases

### Prevention of weaker conditions
SignPath checks that the policies have been applied to the given branch. On the `releases/weaker-condition` branch, the **code scanning** ruleset rule is not enforced. SignPath will not allow signing builds from that release.

### Prevention of interrupted conditions
For the `releases/interrupted-condition` branch, the ruleset has been temporarily disabled between 2024-12-12 11:07 UTC and 2024-12-23 09:07 UTC, allowing e.g. commits without code reviews. SignPath catches such interruptions and prevents the software from being signed.

### Prevention of re-runs
See build [TODO](TODO) for an old failed build that has been re-run even though re-runs are not allowed. SignPath can optionally prevent re-runs to avoid situations where old, vulnerable states of the software are re-built and signed.

## Prerequisites for Setup

For this repository to work it requires the following configurations

### On SignPath.io

* An organization with the *Trusted Build System* **GitHub.com** configured
* A project with 
  * the slug `executable`
  * the Trusted Build System linked
  * an artifact configuration `zipped` for a `<pe-file>` within a `<zip-file>`
  * a signing policy `release-signing` with a *CI User* set as a submitter

### On GitHub

* The SignPath GitHub App needs to be installed and granted permission to this repository
* A ruleset for the given repository that enforces all the validated policies except the code-scanning for the default branch and all `releases/*` branches
  * The `releases/interrupted-condition` branch has to be explicitly removed temporarily
* A second ruleset that enforces code scanning for all branches but the `releases/weaker-condition` branch
* The organization ID added as a repository variable `SIGNPATH_ORGANIZATION_ID`
* The *API Token* of the *CI User* added to a repository secret `SIGNPATH_API_TOKEN`
