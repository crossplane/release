---
name: Patch Release
about: Cut a Crossplane patch release
labels: release
---

<!--
Issue title should be in the following format:

    Cut vX.Y.Z Release on DATE

For example:

    Cut v1.3.1 on June 29, 2021.

Please assign the release manager to the issue.
-->

This issue can be closed when we have completed the following steps (in order).
Please ensure all artifacts (PRs, workflow runs, Tweets, etc) are linked from
this issue for posterity. Refer to this [prior release issue][release-1.11.1] for
examples of each step, assuming vX.Y.Z is being cut.

- [ ] Determine whether a Crossplane Runtime patch is needed by checking if the `release-X.Y` branch of `crossplane-runtime` has commits ahead of its latest tag.
  - One way to check this is `git log $(git describe --tags --abbrev=0 upstream/release-X.Y)..upstream/release-X.Y --oneline`, if it shows any commits then a patch release is needed.
  - If a Crossplane Runtime patch is needed, cut a Crossplane Runtime patch and consume it from Crossplane:
    - [ ] **[In Crossplane Runtime]**:
      - [ ] Confirm that all security/critical dependency update PRs from Renovate are merged into the `release-X.Y` branch
        - https://github.com/crossplane/crossplane-runtime/pulls?q=is%3Apr+is%3Aopen+label%3Aautomated
      - [ ] Run the [Tag workflow][tag-workflow-runtime] on the `release-X.Y` branch with the proper release version, `vX.Y.Z`. Message suggested, but not required: `Release vX.Y.Z`.
      - [ ] Published a [new release][new runtime release] for the tagged version, with the same name as the version, taking care of generating the changes list selecting as "Previous tag" `vX.Y.<Z-1>`, so the previous patch release for the same minor (or `vX.Y.0` for the first patch).
    - [ ] **[In Core Crossplane]:** (On the **Release** Branch) Open and merge a PR updating the Crossplane Runtime dependency to `vX.Y.Z`.
- [ ] Confirm that all security/critical dependency update PRs from Renovate are merged into the `release-X.Y` branch
  - https://github.com/crossplane/crossplane/pulls?q=is%3Apr+is%3Aopen+label%3Aautomated
- [ ] (On the **Release** Branch) Open and merge a PR bumping the `github.com/crossplane/crossplane/apis/v2` requirement in `go.mod` to `vX.Y.Z` (`release-2.3` and newer only). Can be folded into the Crossplane Runtime bump PR above.
- [ ] Run the [Tag workflow][tag-workflow] on the `release-X.Y` branch with the proper release version, `vX.Y.Z`. Message suggested, but not required: `Release vX.Y.Z`.
- [ ] Run the [CI workflow][ci-workflow] on the release branch and verified that the tagged build version exists on the [releases.crossplane.io] `build` channel, e.g. `build/release-X.Y/vX.Y.Z/...` should contain all the relevant binaries.
- [ ] Confirm the full set of patch versions that will be released and promote them from lowest to highest, so the **highest** version is the **last** to be promoted (e.g. `v1.12.2` should be promoted after `v1.11.3`), in order to avoid the promote workflow overwriting the latest patch release.
  - NOTE: This ordering requirement can be avoided by checking the "pre-release" checkbox in the promote workflow for the older releases, as described in [#5420].
- [ ] Run the [Promote workflow][promote-workflow] with channel `stable` on the `release-X.Y` branch and verified that the tagged build version exists on the [releases.crossplane.io] `stable` channel at `stable/vX.Y.Z/...`.
- [ ] Published a [new release] for the tagged version, with the same name as the version and descriptive release notes, taking care of generating the changes list selecting as "Previous tag" `vX.Y.<Z-1>`, so the previous patch release for the same minor. Before publishing the release notes, set them as Draft and ask the rest of the team to double check them.
- [ ] Request @jbw976 to perform a CloudFront cache invalidation on https://charts.crossplane.io/stable/ and https://releases.crossplane.io/stable/
- [ ] Ensured that users have been notified of the release on all communication channels:
  - [ ] Slack: `#announcements` channel on Crossplane's Slack workspace.
  - [ ] Twitter: reach out to a Crossplane maintainer or steering committee member, see [OWNERS.md][owners].
  - [ ] Bluesky: same as Twitter
  - [ ] LinkedIn: same as Twitter
- [ ] Remove any extra permissions given to release team members for this release

<!-- Named Links -->
[ci-workflow]: https://github.com/crossplane/crossplane/actions/workflows/ci.yml
[configurations-workflow]: https://github.com/crossplane/crossplane/actions/workflows/configurations.yml
[new release]: https://github.com/crossplane/crossplane/releases/new
[owners]: https://github.com/crossplane/crossplane/blob/main/OWNERS.md
[promote-workflow]: https://github.com/crossplane/crossplane/actions/workflows/promote.yml
[release-1.11.1]: https://github.com/crossplane/crossplane/issues/3796
[releases table]: https://github.com/crossplane/crossplane#releases
[releases.crossplane.io]: https://releases.crossplane.io
[tag-workflow]: https://github.com/crossplane/crossplane/actions/workflows/tag.yml
[tag-workflow-runtime]: https://github.com/crossplane/crossplane-runtime/actions/workflows/tag.yml
[new runtime release]: https://github.com/crossplane/crossplane-runtime/releases/new
[#5420]: https://github.com/crossplane/crossplane/issues/5420#issuecomment-2318294855
