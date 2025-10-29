# Root Finder

A cross-platform (WIP) post-exploitation privilege escalation enumeration tool, built in Go.

## Core Problem

Automate the post-exploitation enumeration of common privilege escalation vectors on a compromised system.

## Implementation (v0.1 - Linux)

The tool is written in Go (Golang) to produce a single, static binary with zero runtime dependencies. This avoids "Living off the Land" (LotL) issues where interpreters like Python or Perl may be missing or restricted.

The v0.1 (hackathon) scope is limited to Linux and automates the execution of common shell commands via the os/exec package. All findings are currently output to stdout to be piped to a report file.

Checks Implemented (v0.1)
| Check Category | Target Command / File | Purpose |
| :--- | :--- | :--- |
| **Sudo Permissions** | `sudo -l` | Check for `(ALL) ALL` or `NOPASSWD` entries. |
| **SUID Binaries** | `find / -perm -u=s -type f 2>/dev/null`| Find binaries that run as `root`. |
| **World-Writable Files**| `find / -perm -o=w -type f 2>/dev/null`| Find config files or scripts editable by any user. |
| **World-Writable Dirs**| `find / -perm -o=w -type d 2>/dev/null`| Find directories for potential file-drop attacks. |
| **Cron Jobs** | `cat /etc/crontab` & `/etc/cron.d/*` | Check for system-wide scheduled tasks. |
| **Sensitive Files** | `cat /etc/shadow` (read attempt) | Check for readable shadow file. |
| **PATH Variable** | `echo $PATH` | Check for user-writable directories in `PATH`. |

## Build
```js
Go 1.18+ is required.
```

# Clone the repository
```js
git clone [https://github.com/](https://github.com/)[your-username]/root-finder.git
cd root-finder
```

# Tidy dependencies (if any are added)
```js
go mod tidy
```
# Build for current architecture
```js
go build -o rootfinder main.go
```

# Cross-compile for a typical 64-bit Linux target
```js
GOOS=linux GOARCH=amd64 go build -o rootfinder.linux main.go
```


## Usage

Upload the compiled binary (rootfinder.linux) to the target machine.

### Make executable
```js
chmod +x ./rootfinder.linux
```

### Run and save report
```js
./rootfinder.linux > report.txt
```

### Or run a specific check (Future Feature)
```js
./rootfinder.linux --check sudo
```


Future Scope (v1.0+)

The choice of Go is intentional for future expansion:

[ ] Concurrency: Re-write checks to run simultaneously using goroutines for a massive speed increase.

[ ] Cross-Platform: Add build targets and check-modules for:

[ ] Windows: (e.g., checkUnquotedServices, checkAlwaysInstallElevated, checkRegistry)

[ ] macOS: (e.g., checkLaunchAgents, checkTCC, checkKeychain)

[ ] Stealth: Re-implement "noisy" shell commands (like cat) with native Go os.Read() functions to better evade basic EDR/logging.

[ ] Output Format: Add a -o json flag for JSON output to make the tool chainable with other C2 frameworks or reporting engines.

License

This project is licensed under the GNU GPLv3. See the LICENSE file for full details.