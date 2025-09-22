# Detect OS and Runtime Versions Action

Detects your runner's operating system and installed tool versions, providing detailed outputs for
conditional workflow steps.

## Features

Works across Linux, macOS, and Windows runners using multiple fallback detection methods. The action
identifies your OS distribution and architecture, then checks for installed development tools. Each
tool provides semantic versioning components (major, minor, patch) so you can conditionally install
updates only when needed.

## Usage

### Basic example

```yaml
steps:
  - uses: https://code.forgejo.org/actions/checkout@v5

  - name: Detect OS and tools
    id: detect
    uses: https://git.tomfos.tr/actions/detect-versions@v1

  - name: Show detection results
    run: |
      echo "OS: ${{ steps.detect.outputs.name }}"
      echo "Version: ${{ steps.detect.outputs.version }}"
      echo "Architecture: ${{ steps.detect.outputs.arch }}"
      echo "Node.js: ${{ steps.detect.outputs.node_version }}"
```

### Conditional tool installation

```yaml
steps:
  - name: Detect OS and tools
    id: detect
    uses: https://git.tomfos.tr/actions/detect-versions@v1

  - name: Setup Node.js if missing or outdated
    if: steps.detect.outputs.node_major == '' || steps.detect.outputs.node_major < '20'
    uses: https://github.com/actions/setup-node@v5
    with:
      node-version: 22
```

### Smart cache keys

```yaml
steps:
  - name: Detect OS and tools
    id: detect
    uses: https://git.tomfos.tr/actions/detect-versions@v1

  - name: Cache dependencies
    uses: actions/cache@v3
    with:
      path: ~/.cache
      key: deps-${{ steps.detect.outputs.slug }}-${{ steps.detect.outputs.arch }}-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        deps-${{ steps.detect.outputs.slug }}-${{ steps.detect.outputs.arch }}-
        deps-${{ steps.detect.outputs.slug }}-
```

## Outputs

### Operating system outputs

| Output | Description | Example Values |
|--------|-------------|----------------|
| `name` | Operating system name | `Ubuntu`, `Debian`, `CentOS`, `macOS`, `Windows` |
| `version` | OS version number | `22.04`, `11`, `13.2`, `2022` |
| `slug` | Combined OS identifier | `Ubuntu-22.04`, `macOS-13`, `Windows-2022` |
| `arch` | System architecture | `x64`, `arm64`, `arm` |

### Tool version outputs

Each tool provides `{tool}_version` (full version), `{tool}_major`, `{tool}_minor`, and
`{tool}_patch` outputs:

- **cmake** - CMake build system (e.g. `3.28.1`)
- **docker** - Docker container platform (e.g. `24.0.7`)
- **git** - Git version control (e.g. `2.43.0`)
- **go** - Go programming language (e.g. `1.21.5`)
- **java** - Java runtime (e.g. `21.0.1` or `1.8.0_392`)
- **node** - Node.js JavaScript runtime (e.g. `20.11.1`)
- **python** - Python interpreter (e.g. `3.11.5`)
- **rust** - Rust programming language (e.g. `1.75.0`)

Returns empty strings for tools that aren't installed.

## Supported platforms

Supports Linux distributions (Ubuntu, Debian, CentOS, RHEL, Fedora), macOS 11+, and Windows
Server 2019/2022. Uses multiple detection methods starting with the most reliable - `lsb_release`
on Linux, `sw_vers` on macOS, and `systeminfo` on Windows, with appropriate fallbacks.

## Example outputs by platform

| Output | Ubuntu | macOS | Windows |
|--------|--------|-------|---------|
| `name` | `Ubuntu` | `macOS` | `Windows` |
| `version` | `22.04` | `13.2` | `2022` |
| `slug` | `Ubuntu-22.04` | `macOS-13.2` | `Windows-2022` |
| `arch` | `x64` | `x64` | `x64` |

## Contributing

Issues and pull requests can be submitted via the GitHub mirror at
<https://github.com/tcpipuk/detect-versions>

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.
