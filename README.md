# setup-python (IBM architectures fork)

This repository is a fork of [actions/setup-python](https://github.com/actions/setup-python), maintained by IBM to provide support for architectures not officially covered upstream. It enables GitHub Actions workflows on IBM Power (**ppc64le**) and IBM Z (**s390x**), while remaining functionally compatible with upstream for all other platforms.

## How this fork delivers Python runtimes

- For **ppc64le** and **s390x** runners, we publish CPython tarballs in the [IBM/python-versions-pz](https://github.com/IBM/python-versions-pz) repository, and this action downloads them directly from those releases.
- For **x64** and **arm64** runners, we fall back to the upstream [actions/python-versions](https://github.com/actions/python-versions) data, so you receive the same artifacts as `actions/setup-python`.
- PyPy and GraalPy builds are handled exactly as upstream. If a version is not available for Power or Z, the action will report it just like the original project.

> ℹ️ If you only require the standard Microsoft-hosted architectures, continue using [actions/setup-python](https://github.com/actions/setup-python). Use this fork if you rely on IBM Power or IBM Z runners.

## Why IBM maintains this fork

1. Both upstream repositories (`actions/setup-python` and `actions/python-versions`) are owned by Microsoft. Supporting Power and Z requires changes to both code and release infrastructure.
2. Microsoft has not accepted our pull requests to add **ppc64le** and **s390x** support, which blocks official distribution of these Python builds.
3. Hosting the artifacts ourselves requires installing the GitHub App described in the blog post [“GitHub Actions for IBM architectures v1.1”](https://community.ibm.com/community/user/blogs/mick-tarsel/2025/10/01/gha-v1-1). Until the upstream projects adopt that app (or similar infrastructure), we must maintain this fork to keep Power and Z runners unblocked.

Our goal is to stay close to upstream so that pipelines can switch between the two actions with minimal friction, while IBM works with Microsoft on a long-term solution.

## Feature parity with upstream

All features available in `actions/setup-python`, including pinning versions, installing PyPy or GraalPy, enabling dependency caching, using `python-version-file`, and registering matchers, work the same here. The only intentional difference is the source of CPython artifacts:

| Architecture | Runtime source |
|--------------|----------------|
| ppc64le      | [IBM/python-versions-pz](https://github.com/IBM/python-versions-pz) releases |
| s390x        | [IBM/python-versions-pz](https://github.com/IBM/python-versions-pz) releases |
| x64          | [actions/python-versions](https://github.com/actions/python-versions) (upstream) |
| arm64        | [actions/python-versions](https://github.com/actions/python-versions) (upstream) |

## Quick start

```yaml
steps:
  - uses: actions/checkout@v5
  - uses: ibm/setup-pz@v6
    with:
      architecture: ppc64le
      python-version: '3.13'
  - run: python my_script.py
```

Key inputs (same as upstream):

- `python-version` or `python-version-file`: Specify the CPython, PyPy, or GraalPy release you need.
- `architecture`: Set to `ppc64le` or `s390x` when running on IBM hardware. Omit this field (or use `x64`/`arm64`) to fall back to upstream artifacts.
- `cache`, `cache-dependency-path`: Enable dependency caching for pip, pipenv, or poetry.

See [action.yml](action.yml) for the full list of inputs.

## Working with IBM Python builds

When targeting Power or Z architectures, the action resolves versions from [IBM/python-versions-pz](https://github.com/IBM/python-versions-pz):

- Releases mirror upstream tags (e.g., `3.12.6`, `3.13.0`) and include free-threaded variants when available.
- The repository contains build scripts and Dockerfiles, allowing you to reproduce or audit the binaries.
- Issues regarding missing versions or defects in the binaries should be filed directly in that repository.

For x64/arm64, no configuration change is required—this fork simply calls upstream APIs and mirrors the behavior of `actions/setup-python`.

## Keeping up to date

- We periodically rebase on upstream to incorporate new features, security fixes, and Node.js runner updates.
- Release tags follow upstream numbering (v6.x, etc.), so workflows do not need to change their `uses:` syntax when switching between forks.
- When the upstream project adopts IBM Power and IBM Z support—or when the GitHub App mentioned above is installed in those repositories—we plan to retire this fork and direct users back to the canonical action.

## Support & contributions

- **IBM Power or IBM Z issues**: Open an issue or pull request in this repository.
- **Python tarball problems** (missing versions, checksum questions): Use [IBM/python-versions-pz](https://github.com/IBM/python-versions-pz/issues).
- **General `setup-python` behavior**: Consult the upstream [actions/setup-python documentation](https://github.com/actions/setup-python#readme) to stay aligned with standard usage.

We welcome fixes that keep this fork healthy and close to upstream. For broader changes or new features unrelated to IBM architectures, please contribute upstream first so we can stay in sync.

## License

This fork retains the [MIT License](LICENSE) of the original project.
