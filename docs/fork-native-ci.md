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

The initial `ci/native-validation` branch starts at repair commit
`a366d5efd23cb6d9510272e1342c39b166492fb0` (upstream PR #336); its CI commits leave
that repair branch unchanged. Compare the CI commit with the repair SHA to verify
that only this workflow and its documentation were added.

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

Pushes to `ci/native-validation` run automatically. Both jobs are restricted to
that branch in this fork, and enforce that only the CI workflow and documentation
differ from the initial repair commit. Manual dispatch becomes
available once this workflow exists on the repository default branch. A workflow
file on this branch does not add checks to the separate upstream PR #336 head.
The legacy release-oriented build workflow is not used by this CI.
