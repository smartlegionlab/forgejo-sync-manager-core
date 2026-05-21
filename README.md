# forgejo-sync-manager-core <sup>v1.0.1</sup>

Universal core library for Forgejo repository synchronization.

---

[![GitHub top language](https://img.shields.io/github/languages/top/smartlegionlab/forgejo-sync-manager-core)](https://github.com/smartlegionlab/forgejo-sync-manager-core)
[![GitHub license](https://img.shields.io/github/license/smartlegionlab/forgejo-sync-manager-core)](https://github.com/smartlegionlab/forgejo-sync-manager-core/blob/master/LICENSE)
[![GitHub release](https://img.shields.io/github/v/release/smartlegionlab/forgejo-sync-manager-core)](https://github.com/smartlegionlab/forgejo-sync-manager-core/)
[![GitHub stars](https://img.shields.io/github/stars/smartlegionlab/forgejo-sync-manager-core?style=social)](https://github.com/smartlegionlab/forgejo-sync-manager-core/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/smartlegionlab/forgejo-sync-manager-core?style=social)](https://github.com/smartlegionlab/forgejo-sync-manager-core/network/members)

[![PyPI - Downloads](https://img.shields.io/pypi/dm/forgejo-sync-manager-core?label=pypi%20downloads)](https://pypi.org/project/forgejo-sync-manager-core/)
[![PyPI](https://img.shields.io/pypi/v/forgejo-sync-manager-core)](https://pypi.org/project/forgejo-sync-manager-core)
[![PyPI - Format](https://img.shields.io/pypi/format/forgejo-sync-manager-core)](https://pypi.org/project/forgejo-sync-manager-core)
[![PyPI Downloads](https://static.pepy.tech/badge/forgejo-sync-manager-core)](https://pepy.tech/projects/forgejo-sync-manager-core)
[![PyPI Downloads](https://static.pepy.tech/badge/forgejo-sync-manager-core/month)](https://pepy.tech/projects/forgejo-sync-manager-core)
[![PyPI Downloads](https://static.pepy.tech/badge/forgejo-sync-manager-core/week)](https://pepy.tech/projects/forgejo-sync-manager-core)

---

## ⚠️ Disclaimer

**By using this software, you agree to the full disclaimer terms.**

**Summary:** Software provided "AS IS" without warranty. You assume all risks.

**Full legal disclaimer:** See [DISCLAIMER.md](https://github.com/smartlegionlab/forgejo-sync-manager-core/blob/master/DISCLAIMER.md)

---

## Overview

`forgejo-sync-manager-core` is a reusable Python library that provides the core functionality for synchronizing Forgejo repositories. It is designed to be used by both CLI and GUI applications, ensuring code reuse and consistency across different interfaces.

## Features

- Forgejo API client with token-based authentication
- Repository discovery with pagination support
- Git repository cloning and updating
- Repository recloning functionality
- Local repository existence checking
- Path management for local storage
- Cross-platform support (Linux, Windows, macOS)
- Abstract base classes for easy extension

## Implementations

This core library is used in the following projects:

| Project                      | Description                                | Repository                                                           |
|------------------------------|--------------------------------------------|----------------------------------------------------------------------|
| **forgejo-sync-manager-cli** | CLI tool for batch synchronization         | [GitHub](https://github.com/smartlegionlab/forgejo-sync-manager-cli) |
| **forgejo-sync-manager-gui** | Desktop GUI tool for batch synchronization | [GitHub](https://github.com/smartlegionlab/forgejo-sync-manager-gui) |

## Components

### Core Classes

| Class              | Description                                       |
|--------------------|---------------------------------------------------|
| `ForgejoAuth`      | Authentication container for server URL and token |
| `ForgejoAPIClient` | API client for Forgejo REST API                   |
| `ConfigManager`    | Configuration file management (JSON)              |
| `BaseSyncManager`  | Abstract base class for sync operations           |

### Sync Managers

| Class            | Purpose                                                      |
|------------------|--------------------------------------------------------------|
| `GUISyncManager` | Lightweight manager for GUI applications (no console output) |
| `CLISyncManager` | Full-featured manager with console progress output           |

## Requirements

- Python 3.8+
- Git
- Forgejo server with API access
- requests library

## Installation

```bash
git clone https://github.com/smartlegionlab/forgejo-sync-manager-core.git
cd forgejo-sync-manager-core
python -m venv venv
pip install -r requirements.txt
```

## Usage

### Basic Authentication

```python
from core.auth import ForgejoAuth

auth = ForgejoAuth(
    token="your_token",
    server_url="http://localhost:3000",
    username="your_username"
)
```

### API Client

```python
from core.api_client import ForgejoAPIClient

client = ForgejoAPIClient(auth)
user_info = client.get_user_info()
repositories = client.get_user_repos()
```

### Sync Manager for GUI Applications

```python
from core.sync_manager_gui import GUISyncManager

sync_manager = GUISyncManager(auth)
sync_manager.ensure_directories()

for repo in repositories:
    status = sync_manager.sync_repository(repo)
    # status: "CLONED", "UPDATED", "FAILED"
```

### Sync Manager for CLI Applications

```python
from core.sync_manager_cli import CLISyncManager

sync_manager = CLISyncManager(auth)
results = sync_manager.sync_all_repositories(repositories)
# results: {"cloned": 0, "updated": 0, "failed": 0}
```

### Check Repository Updates (CLI only)

```python
from core.sync_manager_cli import CLISyncManager

sync_manager = CLISyncManager(auth)
needs_update = sync_manager.check_updates(repositories)
print(f"Repositories needing update: {len(needs_update)}")
```

### Local Repository Management

```python
# Check if repository exists locally
exists = sync_manager.repo_exists_locally("repo-name")

# Get path to local repository
repo_path = sync_manager.get_repo_path("repo-name")
```

## Configuration

Configuration is stored in `~/forgejo-sync-manager/config.json`:

```json
{
    "token": "your_access_token",
    "server_url": "http://localhost:3000",
    "username": "your_username"
}
```

## Return Status Codes

| Status     | Description                                  |
|------------|----------------------------------------------|
| `CLONED`   | Repository was successfully cloned           |
| `UPDATED`  | Existing repository was successfully updated |
| `RECLONED` | Repository was deleted and re-cloned         |
| `FAILED`   | Operation failed                             |

## How It Works

1. **Authentication**: Token-based authentication via Forgejo API
2. **Repository Discovery**: Fetches complete repository list with pagination (50 per page)
3. **Update Detection** (CLI only): For each existing repository, executes `git fetch` and compares `HEAD` with `FETCH_HEAD`
4. **Sync Operations**: Uses authenticated URLs with embedded token for Git operations
5. **Directory Structure**: `~/forgejo-sync-manager/{username}/repositories/`

## License

[BSD 3-Clause License](LICENSE)

Copyright (©) 2026, [Alexander Suvorov](https://github.com/smartlegionlab)
All rights reserved.

## Related Projects

- [forgejo-sync-manager](https://github.com/smartlegionlab/forgejo-sync-manager-cli) - CLI implementation
- [forgejo-sync-manager-gui](https://github.com/smartlegionlab/forgejo-sync-manager-gui) - GUI implementation

