# Fork native validation

This validation workflow runs only in `oskal675-design/nekobox`. It has read-only
repository permissions and uses immutable source revisions and action commits.

## What is checked

* Windows Server 2022 / Visual Studio 2022 / MSVC x64: the complete production
  `nekobox` CMake target, including actual application headers, Thrift generation,
  moc/uic/rcc, and final executable linking. No extracted test-only translation
  unit substitutes for this build. GUI feature defaults remain enabled, including
  the JavaScript updater and leveldb. `BUILD_GO_PARTS=OFF` skips the separate Go
  executables, matching the existing workflow's C++ build.
* Ubuntu 24.04: the existing XHTTP import test using real Qt6, yaml-cpp, Boost,
  and production enum definitions, followed by all eight mutation checks. Seven
  behavior mutations must fail assertions; restoring the invalid enum/string
  comparison must fail compilation for the intended reason. The existing
  fixture's factory/storage adapters remain and are documented in its README.

A green configure step alone is not a successful build. The Windows compile/link
step must succeed and produce `build/nekobox.exe`. Neither that executable nor a
proxy/core process is started. This does not test VPN connectivity, persistence,
Windows 7 compatibility, installer/deployment packaging, or runtime behavior.

## Revision and dependency evidence

The checkout is the exact triggering push/manual SHA.
The workflow records this independently of the workflow/event SHA. It never
substitutes a moving `main` branch. Logs include submodule commits, source hashes,
compiler/dependency versions, build commands, and the linked executable hash.

The initial CI-only branch is `ci/native-validation`, rooted at repair commit
`a366d5efd23cb6d9510272e1342c39b166492fb0` (upstream PR #336). Its initial workflow
commit is `f60d60bb540a625399949ac4bc330b13117eaed0`; only CI and documentation files
differ from the repair. The reusable default-branch workflow accepts any exact
40-character lowercase commit SHA reachable from a branch in this fork, and fails
early for mutable refs, unrelated commits, or absent regression harnesses.

Qt remains version 6.10.0. CI uses official MSVC2022 x64 binaries through the
pinned PyPI `aqtinstall` 3.3.0 package, rather than the upstream maintainer's custom
Windows 7 Qt distribution. Microsoft vcpkg is pinned at
`9e593bb18ea69cc5095e012465dcd675a822ed0d` (2026.07.29), the stable release selected
by the upstream workflow's floating latest-release dependency setup when this
workflow was prepared. Existing repository submodule pins are retained.
Ubuntu development packages follow the Ubuntu 24.04 distribution; their resolved
versions are logged. Runner image/toolchain security updates can change over time.

## Safety and use

There is no release, deployment, signing, vendor/source archive upload, dependency
cache upload, private profile access, or new secret/PAT. vcpkg binary caching and
app-local dependency deployment are disabled. Only build/test/provenance text logs
are retained as artifacts for seven days. The GitHub Actions token is not persisted
by checkout. Dependency setup uses official actions, Microsoft vcpkg, normal PyPI
packages, and official Qt distribution packages.

## Validate a fix before publishing its upstream PR

1. Push the fix branch to this fork and copy its exact 40-character commit SHA.
2. Open **Actions → Fork native validation → Run workflow**. Select `main` for
   the workflow and paste the fix commit into `source_sha`.
3. Wait for both the native Windows compile/link job and the native Qt regression
   job to finish successfully. Verify the logged `compiled_sha` equals that fix
   commit, and include the run link with the review evidence.

Leaving `source_sha` empty validates the selected workflow branch's event SHA.
The fork's older `main` currently lacks the required regression harness, so select
an appropriate repair/fix SHA rather than treating that older source as validated.
No moving branch name is accepted as `source_sha`.

Automatic pushes run for `ci/**` branches containing this workflow file. PRs
opened **in this fork** run for same-fork branch heads. Branches based directly
on upstream may not contain the fork-only workflow. An upstream pull request does
not trigger this fork's pull-request event; use the explicit manual SHA check
above for those fixes. The reusable workflow imposes no fixed repair ancestor.

The default-branch installation preserves the fork's source and history. All six
legacy build/publish jobs and the legacy Pages deployment job are gated to the
upstream repository, preventing their release/vendor upload paths in this fork.
There is no upstream repository change or upstream PR metadata change.
