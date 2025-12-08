# CLAUDE.md - AI Assistant Guide for Disk Usage Checker

## Project Overview

**Disk Usage Checker** is a Python desktop application built with Tkinter that monitors disk usage across multiple remote Linux servers via SSH. The application provides a GUI interface with color-coded disk usage information and supports both light and night modes.

### Purpose
- Monitor disk usage on remote servers via SSH
- Display disk usage in a user-friendly GUI with color-coded warnings
- Support multiple server groups with flexible filesystem configurations
- Provide visual alerts based on disk usage thresholds

## Codebase Structure

```
disk-usage-checker/
├── servers.py           # Main application file (GUI + SSH logic)
├── servers.yml          # Server configuration file
├── README.md            # User-facing documentation
├── Disk_usage.png       # Application screenshot
└── CLAUDE.md           # This file - AI assistant guide
```

### File Descriptions

- **servers.py** (156 lines): Main Python application containing:
  - `DiskUsageApp` class: Tkinter GUI application
  - SSH connection logic using Paramiko
  - YAML configuration parsing
  - Base64 password decryption
  - Color-coded disk usage display

- **servers.yml** (29 lines): YAML configuration file defining:
  - Server groups (APP_PAYMENT, APP_DATABASE, etc.)
  - IP addresses for each group
  - Default and per-server grep patterns for filesystem filtering
  - Example structure with placeholder IPs (10.x.x.x)

## Key Technologies and Dependencies

### Required Python Packages
- **tkinter**: GUI framework (built-in with Python)
- **paramiko**: SSH client library for remote server connections
- **PyYAML** (pyyaml): YAML configuration file parsing
- **base64**: Password encryption/decryption (built-in)

### Installation Commands
```bash
pip install pyyaml
pip install paramiko
```

### Python Version
- Compatible with Python 3.x
- Includes PyInstaller support (see `resource_path()` function)

## Configuration Files

### servers.yml Structure

The configuration file uses YAML format with the following structure:

```yaml
GROUP_NAME:
  grep: <default_filesystem_pattern>
  ips:
    - <ip_address>                    # Simple IP format
    - ip: <ip_address>                # Extended format
      grep: <custom_grep_pattern>     # Override default grep
```

**Key Points:**
- Each group has a default `grep` pattern for filesystem filtering
- IPs can be simple strings or dictionaries with custom grep patterns
- Grep patterns typically target specific Linux device mappers (e.g., `/dev/mapper/rhel-root`)

### Hardcoded Configuration in servers.py

**Line 89**: SSH username is hardcoded as `'test'`
**Line 99**: Configuration file is read as `'servers.yaml'` (note: actual file is `servers.yml`)
**Line 111**: Default encrypted password: `"eW91cl9wYXNzd29yZA=="` (decodes to "your_password")

⚠️ **IMPORTANT**: There's a filename mismatch - code references `servers.yaml` but the actual file is `servers.yml`

## Architecture and Design Patterns

### Application Architecture

```
┌─────────────────────────────────────┐
│      Tkinter GUI (Main Window)      │
│  - ScrolledText widget (output)     │
│  - Check Disk Usage button          │
│  - Toggle Night Mode button         │
└─────────────┬───────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│      DiskUsageApp Controller        │
│  - Event handlers                   │
│  - SSH command execution            │
│  - YAML config parsing              │
└─────────────┬───────────────────────┘
              │
    ┌─────────┴──────────┐
    ▼                    ▼
┌─────────┐        ┌──────────┐
│  YAML   │        │ Paramiko │
│ Config  │        │   SSH    │
└─────────┘        └──────────┘
```

### Design Patterns

1. **Single Responsibility**: `DiskUsageApp` class handles GUI, SSH, and configuration
2. **Resource Path Abstraction**: `resource_path()` function supports both development and PyInstaller bundled execution
3. **Color-Coded Alerts**: Tag-based text formatting in Tkinter
4. **Group-Based Configuration**: YAML structure allows logical server grouping

## Development Workflows

### Making Changes to the Application

1. **Modifying SSH Logic** (servers.py:85-96)
   - `ssh_command()` method handles all SSH connections
   - Uses Paramiko's `AutoAddPolicy` for host key management
   - Returns UTF-8 decoded output or error messages

2. **Updating UI** (servers.py:22-84)
   - GUI is configured in `__init__()` method
   - Color tags defined in lines 57-62
   - Night mode toggle in `toggle_night_mode()` method

3. **Changing Configuration**
   - Edit `servers.yml` to add/remove servers or groups
   - Update grep patterns for different filesystem types
   - Supports both simple IP lists and complex per-server configurations

### Testing Workflow

**Manual Testing Steps:**
1. Update `servers.yml` with test server IPs
2. Update encrypted password in line 111 of `servers.py`
3. Run: `python servers.py`
4. Click "Check Disk Usage" button
5. Verify output displays correctly with appropriate colors

**Security Testing:**
- Test with invalid credentials
- Test with unreachable servers
- Verify error handling displays properly

## Security Considerations

### Critical Security Notes

⚠️ **SSH Credentials Management:**
- Username hardcoded in source code (line 89)
- Password stored as Base64-encoded string (NOT encryption, just encoding)
- Base64 is NOT secure - it's trivial to decode
- Recommendation: Use SSH keys or proper secret management

⚠️ **SSH Host Key Policy:**
- Uses `AutoAddPolicy()` which automatically trusts all hosts
- Vulnerable to man-in-the-middle attacks
- Better approach: Use `RejectPolicy()` with known_hosts file

⚠️ **Error Messages:**
- Full exception messages displayed to UI (line 93)
- Could leak sensitive information

### Security Best Practices for AI Assistants

When making changes:
1. **NEVER** commit real passwords or IP addresses
2. **ALWAYS** use placeholder values in examples
3. **RECOMMEND** SSH key-based authentication over passwords
4. **SUGGEST** environment variables or secure vaults for credentials
5. **WARN** users about Base64 encoding vs. actual encryption

## Code Conventions

### Naming Conventions
- **Classes**: PascalCase (e.g., `DiskUsageApp`)
- **Methods**: snake_case (e.g., `check_disk_usage`, `ssh_command`)
- **Variables**: snake_case (e.g., `disk_usage`, `encrypted_password`)
- **Constants**: UPPER_SNAKE_CASE (implied for YAML group names)

### Code Style
- **Encoding**: UTF-8 (declared in line 1)
- **Indentation**: 4 spaces
- **Line Length**: Generally under 120 characters
- **Comments**: Inline comments for important logic
- **Docstrings**: Used for `resource_path()` function

### GUI Conventions
- **Colors**: Defined with hex codes (#1c1c1c, #2c2c2c, etc.)
- **Fonts**: Courier for monospace output, Helvetica for UI elements
- **Window Size**: 1600x900 pixels
- **Layout**: Vertical packing with padding

## Color-Coded Disk Usage Thresholds

The application uses the following color scheme for disk usage visualization:

| Usage %    | Color  | Tag    | Meaning          |
|-----------|--------|--------|------------------|
| 0-50%     | Green  | green  | Healthy          |
| 51-70%    | Blue   | blue   | Monitor          |
| 71-90%    | Yellow | yellow | Warning          |
| 91-100%   | Red    | red    | Critical         |

**Implementation**: Lines 137-146 in servers.py

## Common Tasks for AI Assistants

### 1. Adding a New Server Group

```python
# In servers.yml, add:
NEW_GROUP_NAME:
  grep: /dev/mapper/new-pattern
  ips:
    - 10.x.x.x
    - 10.x.x.y
```

No code changes needed - application auto-loads all groups.

### 2. Changing SSH Username

**Location**: servers.py:89
```python
client.connect(ip, username='your_new_username', password=password)
```

### 3. Adding New Color Thresholds

**Location**: servers.py:137-146
```python
# Modify the threshold logic:
if usage > 95:  # New critical-critical threshold
    color = "dark_red"
elif usage > 90:
    color = "red"
# ... etc
```

Don't forget to add the new tag in `__init__()` method around line 57.

### 4. Fixing the servers.yaml vs servers.yml Discrepancy

**Location**: servers.py:99
```python
# Change from:
server_file = resource_path('servers.yaml')
# To:
server_file = resource_path('servers.yml')
```

### 5. Improving Security: Using SSH Keys

Replace password authentication with key-based auth:

```python
def ssh_command(self, ip, key_filename, command):
    client = paramiko.SSHClient()
    client.load_system_host_keys()
    client.set_missing_host_key_policy(paramiko.RejectPolicy())
    try:
        client.connect(ip, username='test', key_filename=key_filename)
        stdin, stdout, stderr = client.exec_command(command)
        result = stdout.read().decode('utf-8')
    except Exception as e:
        result = f"Error connecting to {ip}: {str(e)}"
    finally:
        client.close()
    return result
```

### 6. Adding More Disk Information

The `df -h` command returns: Filesystem, Size, Used, Avail, Use%, Mounted on

To add more information, modify the display format in lines 121-148.

### 7. Handling Different df Output Formats

Some systems might have different `df` output. Add error handling:

```python
if len(parts) >= 6:
    try:
        usage = int(parts[4].replace('%', ''))
        # ... rest of logic
    except (ValueError, IndexError) as e:
        self.text_area.insert(tk.END, f"Error parsing output for {ip}: {line}\n", "red")
        continue
```

## Known Issues and Quirks

### 1. Filename Mismatch
- Code references `servers.yaml` (line 99)
- Actual file is `servers.yml`
- **Impact**: Will cause FileNotFoundError
- **Fix**: Rename file or update code reference

### 2. Hardcoded Credentials
- Username hardcoded as 'test'
- Password hardcoded as Base64 string
- **Impact**: Security risk, inflexible
- **Fix**: Use environment variables or config file

### 3. SSH Host Key Policy
- Uses `AutoAddPolicy()` which trusts all hosts
- **Impact**: Vulnerable to MITM attacks
- **Fix**: Use `RejectPolicy()` with known_hosts

### 4. No Error Recovery
- Failed connections show error but don't retry
- **Impact**: Transient network issues cause failures
- **Fix**: Add retry logic with exponential backoff

### 5. Blocking UI
- SSH commands run synchronously
- **Impact**: GUI freezes during execution
- **Fix**: Use threading or asyncio

### 6. No Connection Pooling
- New SSH connection for each command
- **Impact**: Slow for many servers
- **Fix**: Reuse connections or use parallel execution

## Important Notes for AI Assistants

### When Modifying This Codebase:

1. **Always preserve the GUI responsiveness**: Consider threading for long operations
2. **Validate YAML structure**: Ensure backwards compatibility when changing config format
3. **Test with multiple server groups**: Application iterates through all groups
4. **Handle SSH errors gracefully**: Network issues are common
5. **Preserve color-coding logic**: Critical for user experience
6. **Don't break PyInstaller support**: Maintain `resource_path()` usage
7. **Consider cross-platform compatibility**: tkinter behaves differently on Windows/Linux/Mac

### Before Making Security Changes:

1. **Consult with user** about credential management preferences
2. **Document** all security improvements in commit messages
3. **Test** with real SSH connections if possible
4. **Consider** backward compatibility for existing users
5. **Recommend** migration path for credential updates

### Testing Recommendations:

Since this is a remote monitoring tool:
1. **Mock SSH responses** for unit testing
2. **Use test servers** with known states
3. **Test error conditions**: timeout, wrong credentials, unreachable hosts
4. **Verify UI rendering**: colors, layout, night mode
5. **Check YAML parsing**: various configuration formats

## Git Workflow

### Branch Naming
- Feature branches: `claude/` prefix with session ID
- Current branch: `claude/claude-md-mix06oqy7ns1z958-018QXMqJD6UJpX9trViNMAKu`

### Commit Message Style
Based on repository history:
- Use imperative mood: "Update README.md", "Delete password-encrypted.png"
- Be concise and descriptive
- Reference files changed when relevant

### When to Commit
- After completing functional changes
- After fixing bugs
- After updating documentation
- Before making major refactoring

## Quick Reference

### Application Entry Point
```bash
python servers.py
```

### Key Files to Check When Debugging
1. `servers.yml` - Configuration issues
2. `servers.py:85-96` - SSH connection problems
3. `servers.py:108-150` - Disk usage display logic
4. `servers.py:71-83` - UI theme issues

### Key Dependencies
- Paramiko: SSH functionality
- PyYAML: Configuration parsing
- Tkinter: GUI framework (standard library)

### Default Values
- Window size: 1600x900
- Font: Courier 15 for output, Helvetica for UI
- SSH username: 'test'
- SSH command: `df -h | grep <pattern>`
- Config file: servers.yml (but code says servers.yaml!)

## Resources for Understanding the Codebase

### External Documentation
- [Paramiko SSH library](https://www.paramiko.org/)
- [PyYAML documentation](https://pyyaml.org/wiki/PyYAMLDocumentation)
- [Tkinter GUI programming](https://docs.python.org/3/library/tkinter.html)
- [Linux df command](https://man7.org/linux/man-pages/man1/df.1.html)

### Key Linux Concepts
- **df -h**: Disk free command with human-readable output
- **/dev/mapper/**: Linux LVM (Logical Volume Manager) device paths
- **SSH**: Secure Shell protocol for remote server access
- **Base64**: Encoding scheme (not encryption!)

---

**Last Updated**: 2025-12-08
**Repository**: huseynbaghirli/disk-usage-checker
**Application Version**: Current (no version numbering in code)
