---
name: Release
about: Cut a Crossplane release
labels: release
---

<!--
Issue title should be in the following format:

    Cut vX.Y.0 Release on DATE

For example:

    Cut v1.3.0 on June 29, 2021.

Please assign the release manager to the issue.
-->

This issue can be closed when we have completed the following steps (in order).
Please ensure all artifacts (PRs, workflow runs, Tweets, etc) are linked from
this issue for posterity. Refer to this [prior release issue][release-1.14.0] for
examples of each step, assuming release vX.Y.0 is being cut.

### Code Freeze

- [ ] Determine if any patch releases are needed in addition to this main release and open a [new patch release issue][new-patch-release-issue] for each.
- [ ] **[In Crossplane Runtime]**: Prepared the release branch `release-X.Y`:
  - [ ] Confirm that all security/critical dependency update PRs from Renovate are merged into `main`
    - https://github.com/crossplane/crossplane-runtime/pulls?q=is%3Apr+is%3Aopen+label%3Aautomated
  - [ ] Created the release branch using the [GitHub UI][create-branch].
  - [ ] (On the **Main** Branch) Created and merged a PR to add the new release branch to the `baseBranches` list in `.github/renovate.json5`.
  - [ ] (On the **Main** Branch) Run the [Tag workflow][tag-workflow-runtime] with the release candidate tag for the next release `vX.Y+1.0-rc.0`. Message suggested, but not required: `Release candidate vX.Y+1.0-rc.0`.
  - [ ] (On the **Release** Branch) Run the [Tag workflow][tag-workflow-runtime] with the release candidate tag for the next release `vX.Y.0-rc.1` (assuming the latest rc tag for `vX.Y.0` is `vX.Y.0-rc.0`). Message suggested, but not required: `Release candidate vX.Y.0-rc.1`.
- [ ] **[In Core Crossplane]:** Prepared the release branch `release-X.Y`:
  - [ ] Confirm that all security/critical dependency update PRs from Renovate are merged into `main`
    - https://github.com/crossplane/crossplane/pulls?q=is%3Apr+is%3Aopen+label%3Aautomated
  - [ ] Created the release branch using the [GitHub UI][create-branch].
  - [ ] (On the **Main** Branch) created and merged a PR bumping the Crossplane Runtime dependency to the release candidate tag from , `vX.Y+1.0-rc.0`.
  - [ ] (On the **Release** Branch) created and merged a PR bumping the Crossplane Runtime dependency to the release candidate tag on the release branch, `vX.Y.0-rc.1`.
  - [ ] (On the **Main** Branch) Run the [Tag workflow][tag-workflow] with the release candidate tag for the next release, `vX.Y+1.0-rc.0`. Message suggested, but not required: `Release candidate vX.Y+1.0-rc.0`.
  - [ ] (On the **Main** Branch) created and merged a PR to add the new release branch to the `baseBranches` list in `.github/renovate.json5`.
- [ ] **[In Core Crossplane]:** Cut a Crossplane **release candidate** from the release branch `release-X.Y`:
  - [ ] (On the **Release** Branch) Run the [Tag workflow][tag-workflow] with the release candidate tag for the release `vX.Y.0-rc.1` (assuming the latest rc tag for `vX.Y.0` is `vX.Y.0-rc.0`). Message suggested but not required: `Release candidate vX.Y.0-rc.1`.
  - [ ] (On the **Release** Branch) Run the [CI workflow][ci-workflow] and verified that the tagged build version exists on the [releases.crossplane.io] `build` channel, e.g. `build/release-X.Y/vX.Y.0-rc.1/...` should contain all the relevant binaries.
  - [ ] (On the **Release** Branch) Run the [Promote workflow][promote-workflow] with version `vX.Y.0-rc.1` and channel `stable`, ticking the box for `This is a pre-release` and verify:
    - [ ] The tagged build version exists on the [releases.crossplane.io] `stable` channel at `stable/vX.Y.0-rc.1/...`.
    - [ ] Ensured that the release candidate is visible on the stable helm repo with: `helm repo add crossplane-stable https://charts.crossplane.io/stable --force-update &&  helm search repo crossplane-stable --devel`.
  - [ ] Published a [new release] for the tagged version as `pre-release`, with the same name as the version, taking care of generating the changes list selecting as "Previous tag" `vX.<Y-1>.0`, so the first of the releases for the previous minor.
    - [ ] Select the `Set as a pre-release` and `Create a discussion for this release` checkboxes.
    - [ ] Do NOT select the `Set as the latest release` checkbox.
    - [ ] Set the body of the release to:

          # ❗ Important

          Crossplane version `vX.Y.Z-rc.1` is a release candidate intended to collect input from the community and offer users an opportunity to experiment with Crossplane in non-production environments before the official release of version `vX.Y.Z`.

          > [!WARNING]
          > This is a pre-release; do not use it in production environments!

          To install Crossplane with this release:

          ```shell
          helm repo add crossplane-stable https://charts.crossplane.io/stable --force-update
          helm install crossplane --namespace crossplane-system --create-namespace crossplane-stable/crossplane --devel
          ```

          To install the Crossplane CLI with this release:

          ```shell
          curl -sL https://raw.githubusercontent.com/crossplane/crossplane/v1.19.0-rc.1/install.sh | XP_VERSION=v1.19.0-rc.1 sh
          ```

          # 🚨 Breaking Changes

          # 🎉 Highlights

          # 📖 Full Changelog

          **Full Changelog**: https://github.com/crossplane/crossplane/compare/vX.Y-1.X...vX.Y.Z-rc.1


  - [ ] Ensured that users have been notified about the release candidate on the `#announcement` channel of the Crossplane's Slack workspace.

### Release

- [ ] Opened a [docs release issue]
- [ ] Checked that the [GitHub milestone] for this release only contains closed issues.
- [ ] Cut a Crossplane Runtime version and consume it from Crossplane.
  - [ ] **[In Crossplane Runtime]**:
    - [ ] Run the [Tag workflow][tag-workflow-runtime] on the `release-X.Y` branch with the proper release version, `vX.Y.0`. Message suggested, but not required: `Release vX.Y.0`.
    - [ ] Published a [new release][new runtime release] for the tagged version, with the same name as the version, taking care of generating the changes list selecting as "Previous tag" `vX.<Y-1>.0`, so the first of the releases for the previous minor.
    - [ ] Update the `baseBranches` list in `.github/renovate.json5` on `main`, removing the now old unsupported release.
  - [ ] **[In Core Crossplane]:** (On the **Release** Branch) Update the Crossplane Runtime dependency to `vX.Y.0`.
- [ ] (On the **Release** Branch) Run the [Tag workflow][tag-workflow] with the proper release version, `vX.Y.0`. Message suggested, but not required: `Release vX.Y.0`.
- [ ] (On the **Release** Branch) Run the [CI workflow][ci-workflow] and verified that the tagged build version exists on the [releases.crossplane.io] `build` channel, e.g. `build/release-X.Y/vX.Y.0/...` should contain all the relevant binaries.
- [ ] (On the **Release** Branch) Run the [Promote workflow][promote-workflow] with channel `stable` and verified that the tagged build version exists on the [releases.crossplane.io] `stable` channel at `stable/vX.Y.0/...`.
- [ ] Select a release MVP from the community that had significant impact on the release and include recognition of them in the release notes and blog post.
- [ ] Published a [new release] for the tagged version, with the same name as the version and descriptive release notes
  - [ ] generate the changes list by selecting the "Previous tag" as `vX.<Y-1>.0`, i.e., the first of the releases for the previous minor.
  - [ ] Ensure the release MVP is recognized in the release notes.
  - [ ] Before publishing the release notes, set them as Draft and ask the rest of the team to double check them.
- [ ] Checked that the [docs release issue] created previously has been completed.
- [ ] Updated, in a single PR, the following on `main`:
  - [ ] The [releases table] in the `README.md`, removing the now old unsupported release and adding the new one.
  - [ ] The `baseBranches` list in `.github/renovate.json5`, removing the now old unsupported release.
- [ ] Closed the GitHub milestone for this release.
- [ ] Request @jbw976 to perform a CloudFront cache invalidation on https://charts.crossplane.io/stable/ and https://releases.crossplane.io/stable/
- [ ] Publish a blog post about the release to the [crossplane blog]
  - [ ] Ensure the release MVP is recognized in the blog post
- [ ] Ensured that users have been notified of the release on all communication channels:
  - [ ] Slack: `#announcements` channel on Crossplane's Slack workspace.
  - [ ] Twitter: reach out to a Crossplane maintainer or steering committee member, see [OWNERS.md][owners].
  - [ ] Bluesky: same as Twitter
  - [ ] LinkedIn: same as Twitter
- [ ] Request @jbw976 to remove all old docs versions from Google Search
- [ ] Remove any extra permissions given to release team members for this release


<!-- Named Links -->
[new-patch-release-issue]: https://github.com/crossplane/release/issues/new?assignees=&labels=release&projects=&template=patch_release.md
[Code Freeze]: https://docs.crossplane.io/knowledge-base/guides/release-cycle/#code-freeze
[ci-workflow]: https://github.com/crossplane/crossplane/actions/workflows/ci.yml
[configurations-workflow]: https://github.com/crossplane/crossplane/actions/workflows/configurations.yml
[create-branch]: https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-and-deleting-branches-within-your-repository
[docs release issue]: https://github.com/crossplane/docs/issues/new?assignees=&labels=release&template=new_release.md&title=Release+Crossplane+version...+
[new release]: https://github.com/crossplane/crossplane/releases/new
[new runtime release]: https://github.com/crossplane/crossplane-runtime/releases/new
[owners]: https://github.com/crossplane/crossplane/blob/main/OWNERS.md
[promote-workflow]: https://github.com/crossplane/crossplane/actions/workflows/promote.yml
[release-1.14.0]: https://github.com/crossplane/crossplane/issues/4871
[releases table]: https://github.com/crossplane/crossplane#releases
[releases.crossplane.io]: https://releases.crossplane.io
[tag-workflow-runtime]: https://github.com/crossplane/crossplane-runtime/actions/workflows/tag.yml
[tag-workflow]: https://github.com/crossplane/crossplane/actions/workflows/tag.yml
[GitHub milestone]: https://github.com/crossplane/crossplane/milestones
[crossplane blog]: https://blog.crossplane.io
