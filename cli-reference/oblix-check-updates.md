# oblix check-updates

The `oblix check-updates` command checks if a newer version of Oblix is available and provides update information.

## Usage

```bash
oblix check-updates
```

## Description

This command queries the Oblix package repository to determine if a newer version is available. It compares your currently installed version with the latest release and provides information about updates if available.

When you run this command, Oblix will display your current version and notify you if an update is available or confirm that you're using the latest version.

## Output

When no update is available, the command displays a success message:

```
You are using the latest version of Oblix (1.2.3).
```

When an update is available, the command displays information about the new version:

```
Update available: 1.2.4 (current: 1.2.3)

What's new:
- Improved orchestration between edge and cloud models
- Added support for the latest Claude models
- Fixed connectivity monitoring in offline environments

To update, run:
pip install -U oblix
```

## Updating Oblix

If an update is available, you can install it using pip:

```bash
pip install -U oblix
```

For specific Python versions:

```bash
python3.11 -m pip install -U oblix
```

## Automatic Update Checks

Oblix periodically checks for updates automatically when you use the CLI. This ensures you're always aware of important updates and new features without having to explicitly run the `check-updates` command.

## Related Commands

All other Oblix commands will benefit from updating to the latest version:

- [`oblix chat`](oblix-chat.md)
- [`oblix server`](oblix-server.md)
- [`oblix models`](oblix-models.md)