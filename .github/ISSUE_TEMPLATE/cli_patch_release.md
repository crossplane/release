---
name: CLI Patch Release
about: Cut a Crossplane CLI patch release
labels: release
---

<!--
Issue title should be in the following format:

    Cut CLI vX.Y.Z Release on DATE

For example:

    Cut CLI v2.3.2 on 10 June, 2026

Please assign the release manager to the issue.
-->

This issue can be closed when we have completed the following steps (in order).  Please ensure all artifacts (PRs, workflow runs, Tweets, etc) are linked from this issue for posterity.

- [ ] Confirm that Crossplane dependencies are up-to-date in the release branch. These dependencies should be updated routinely by Renovate, but may need a manual update if a CLI release is closely following a core release.
- [ ] Confirm that all security/critical dependency update PRs from Renovate are merged into `main`
  - https://github.com/crossplane/cli/pulls?q=is%3Apr+is%3Aopen+label%3Aautomated
- [ ] Run the [Tag workflow][tag-workflow] on the `release-X.Y` branch with the proper release version, `vX.Y.Z`. Message suggested, but not required: `Release vX.Y.Z`.
- [ ] Run the [CI workflow][ci-workflow] on the release branch and verified that the tagged build version exists on the [cli.crossplane.io] `build` channel, e.g. `build/release-X.Y/vX.Y.Z/...` should contain all the relevant binaries.
- [ ] (On the **Release** Branch) Run the [Promote workflow][promote-workflow] with version `vX.Y.Z` and channel `stable`, ticking the box for `This is a pre-release` if this patch is not for the most recent minor version. Then, verify:
  - [ ] The tagged build version exists on the [cli.crossplane.io] `stable` channel at `stable/vX.Y.Z/...`.
  - [ ] The `current` release in the `stable` channel is `vX.Y.Z` if `vX.Y` is the most recent minor version. If not, the `current` release should still be the most recent patch of the most recent minor version.
- [ ] Publish a [new release] for the tagged version as `pre-release`, with the same name as the version, taking care of generating the changes list selecting as "Previous tag" `vX.Y.<Z-1>`, so the first of the releases for the previous minor.
  - [ ] Select the `Set as the latest release` checkbox if this is a patch for the most recent minor version.
  - [ ] Use this [example](https://github.com/crossplane/cli/releases/tag/v2.3.2) for the body of the release.
- [ ] Request @jbw976 or @adamwg to perform a CloudFront cache invalidation on https://cli.crossplane.io/stable/.
- [ ] Update CLI reference documentation for the release.
  - [ ] Check out the [crossplane/docs] repository.
  - [ ] Download the new release binary by running `curl https://cli.crossplane.io/install.sh | XP_VERSION=vX.Y.Z sh` and ensure you get the new version. Download directly from [cli.crossplane.io] if needed.
  - [ ] In the docs repo, update the command reference using the new release: `./crossplane generate-docs -o content/cli/vX.Y/command-reference.md`.
  - [ ] Create and merge a PR with the updated content.
- [ ] Notify users about the release on the `#announcements` channel of the Crossplane Slack workspace.

<!-- Named Links -->
[ci-workflow]: https://github.com/crossplane/cli/actions/workflows/ci.yml
[new release]: https://github.com/crossplane/cli/releases/new
[promote-workflow]: https://github.com/crossplane/cli/actions/workflows/promote.yml
[cli.crossplane.io]: https://cli.crossplane.io
[tag-workflow]: https://github.com/crossplane/cli/actions/workflows/tag.yml
[crossplane/docs]: https://github.com/crossplane/docs
