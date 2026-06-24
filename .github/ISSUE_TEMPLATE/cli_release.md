---
name: CLI Release
about: Cut a Crossplane CLI minor version release
labels: release
---

<!--
Issue title should be in the following format:

    Cut CLI vX.Y.0 Release on DATE

For example:

    Cut CLI v2.3.0 on 21 May, 2026

Please assign the release manager to the issue.
-->

This issue can be closed when we have completed the following steps (in order).  Please ensure all artifacts (PRs, workflow runs, Tweets, etc) are linked from this issue for posterity.

### Code Freeze

- [ ] Determine if any patch releases are needed in addition to this main release and open a [new patch release issue][new-patch-release-issue] for each.
- [ ] Confirm that Crossplane dependencies are up-to-date in `main`. These dependencies should be updated routinely by Renovate, but may need a manual update if a CLI release is closely following a core release.
- [ ] Confirm that all security/critical dependency update PRs from Renovate are merged into `main`
  - https://github.com/crossplane/cli/pulls?q=is%3Apr+is%3Aopen+label%3Aautomated
- [ ] Create the release branch using the [GitHub UI][create-branch].
- [ ] (On the **Main** Branch) create and merge a PR to add the new release branch to the `baseBranches` list in `.github/renovate-base.json5`.
  - This **must** be done before creating tags below because of assumptions our CI/build process makes about tags and branches.
- [ ] (On the **Main** Branch) Run the [Tag workflow][tag-workflow] with the release candidate tag for the next release, `vX.Y+1.0-rc.0`. Message suggested, but not required: `Release candidate vX.Y+1.0-rc.0`.
- [ ] (On the **Release** Branch) Run the [Tag workflow][tag-workflow] with the release candidate tag for the release `vX.Y.0-rc.1` (assuming the latest rc tag for `vX.Y.0` is `vX.Y.0-rc.0`). Message suggested but not required: `Release candidate vX.Y.0-rc.1`.
- [ ] (On the **Release** Branch) Run the [CI workflow][ci-workflow] and verified that the tagged build version exists on the [cli.crossplane.io] `build` channel, e.g. `build/release-X.Y/vX.Y.0-rc.1/...` should contain all the relevant binaries.
- [ ] (On the **Release** Branch) Run the [Promote workflow][promote-workflow] with version `vX.Y.0-rc.1` and channel `stable`, ticking the box for `This is a pre-release` and verify:
  - [ ] The tagged build version exists on the [cli.crossplane.io] `stable` channel at `stable/vX.Y.0-rc.1/...`.
  - [ ] The `current` release in the `stable` channel is still the previous non-RC release.
- [ ] Publish a [new release] for the tagged version as `pre-release`, with the same name as the version, taking care of generating the changes list selecting as "Previous tag" `vX.<Y-1>.0`, so the first of the releases for the previous minor.
  - [ ] Select the `Set as a pre-release` and `Create a discussion for this release` checkboxes.
  - [ ] Do NOT select the `Set as the latest release` checkbox.
  - [ ] Use this [example](https://github.com/crossplane/cli/releases/tag/v2.3.0) for the body of the release.
- [ ] Notify users about the release candidate on the `#announcements` channel of the Crossplane Slack workspace.

### Release

- [ ] (On the **Release** Branch) Confirm that all security/critical dependency update PRs from Renovate are merged into `release-X.Y`:
  - https://github.com/crossplane/cli/pulls?q=is%3Apr+is%3Aopen+label%3Aautomated
- [ ] (On the **Release** Branch) Run the [Tag workflow][tag-workflow] with the proper release version, `vX.Y.0`. Message suggested, but not required: `Release vX.Y.0`.
- [ ] (On the **Release** Branch) Run the [CI workflow][ci-workflow] and verified that the tagged build version exists on the [cli.crossplane.io] `build` channel, e.g. `build/release-X.Y/vX.Y.0/...` should contain all the relevant binaries.
- [ ] (On the **Release** Branch) Run the [Promote workflow][promote-workflow] with channel `stable` and verified that the tagged build version exists on the [cli.crossplane.io] `stable` channel at `stable/vX.Y.0/...`.
- [ ] Select a release MVP from the community that had significant impact on the release and include recognition of them in the release notes and blog post.
- [ ] Publish a [new release] for the tagged version, with the same name as the version and descriptive release notes
  - [ ] Generate the changes list by selecting the "Previous tag" as `vX.<Y-1>.0`, i.e., the first of the releases for the previous minor.
  - [ ] Ensure the release MVP is recognized in the release notes.
  - [ ] Before publishing the release notes, set them as Draft and ask the rest of the team to double check them.
- [ ] Request @jbw976 or @adamwg to perform a CloudFront cache invalidation on https://cli.crossplane.io/stable/.
- [ ] Update CLI reference documentation for the release.
  - [ ] Check out the [crossplane/docs] repository.
  - [ ] In the docs repo, copy the `master` documentation for the new release: `cp -R content/cli/master content/cli/vX.Y`.
  - [ ] Download the new release binary by running `curl https://cli.crossplane.io/install.sh | sh` and ensure you get the new version. Download directly from [cli.crossplane.io] if needed.
  - [ ] In the docs repo, update the command reference using the new release: `./crossplane generate-docs -o content/cli/vX.Y/command-reference.md`.
  - [ ] Create and merge a PR with the updated content.
- [ ] Publish a blog post about the release to the [crossplane blog]
  - [ ] Ensure the release MVP is recognized in the blog post
- [ ] Notify users about the release on the `#announcements` channel of the Crossplane Slack workspace.

<!-- Named Links -->
[new-patch-release-issue]: https://github.com/crossplane/release/issues/new?assignees=&labels=release&projects=&template=cli_patch_release.md
[ci-workflow]: https://github.com/crossplane/cli/actions/workflows/ci.yml
[create-branch]: https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-and-deleting-branches-within-your-repository
[new release]: https://github.com/crossplane/cli/releases/new
[promote-workflow]: https://github.com/crossplane/cli/actions/workflows/promote.yml
[cli.crossplane.io]: https://cli.crossplane.io
[tag-workflow]: https://github.com/crossplane/cli/actions/workflows/tag.yml
[crossplane/docs]: https://github.com/crossplane/docs
[crossplane blog]: https://blog.crossplane.io
