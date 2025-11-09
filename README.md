# Setup Hammerspoon Action

Install and configure Hammerspoon for macOS CI environments.

## Overview

This action sets up [Hammerspoon](https://www.hammerspoon.org/) on macOS runners, making it easy to test Hammerspoon Spoons and configurations in GitHub Actions workflows. It handles installation, CLI configuration, and optional accessibility permissions for UI automation testing.

## Requirements

- macOS runner (e.g., `macos-latest`, `macos-13`, `macos-14`)
- GitHub-hosted or self-hosted macOS runner with Homebrew installed

## Usage

### Basic Setup

For unit testing and simple Spoon validation:

```yaml
steps:
  - uses: actions/checkout@v5
  - uses: brennovich/action-setup-hammerspoon@v1
  - run: hs -c "print('Hammerspoon is ready')"
```

### With Accessibility Permissions

For acceptance testing with UI automation:

```yaml
steps:
  - uses: actions/checkout@v5
  - uses: brennovich/action-setup-hammerspoon@v1
    with:
      accessibility-permissions: true
  - run: hs -c "hs.application.open('Calculator')"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `accessibility-permissions` | Grant accessibility permissions via TCC database | No | `false` |
| `start-hammerspoon` | Start Hammerspoon after installation. | No | `true` |

## What This Action Does

The action performs the following steps:

1. **Installs Hammerspoon** via Homebrew
2. **Enables CLI** by creating `~/.hammerspoon/init.lua` with `hs.ipc.cliInstall()`
3. **Grants accessibility permissions** (if enabled) using `tccutil` to modify the TCC database
4. **Starts Hammerspoon** and waits for it to be fully loaded (via CLI because the Github runner shell already has "System Events" permission)

## Common Use Cases

### Unit Testing Spoons

Test Spoon functionality without UI automation:

```yaml
jobs:
  test:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v5
      - uses: brennovich/action-setup-hammerspoon@v1
      - run: |
          hs -c "spoon = dofile('init.lua')"
          hs -c "print(spoon.name)"
```

### Acceptance Testing with UI Automation

Test Spoons that interact with the macOS UI:

```yaml
jobs:
  acceptance:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v5
      - uses: brennovich/action-setup-hammerspoon@v1
        with:
          accessibility-permissions: true
      - run: |
          hs -c "dofile('init.lua')"
          hs -c "spoon.MySpoon:start()"
```

## Verifying Setup

You can verify that Hammerspoon is properly configured:

```yaml
- run: hs -c "print(hs.host.localizedName())"
- run: hs -c "print(hs.accessibilityState())"
- run: test -f ~/.hammerspoon/init.lua
```

## License

MIT

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.
