# Phase 1 – System Hardening with `sudo visudo`

## Overview
The first phase focused on **least‑privilege enforcement** using the `sudoers` file. By editing with `visudo`, I defined which groups and users could execute commands with elevated privileges. This ensures that only authorized accounts can perform administrative tasks, reducing the attack surface.

## Key Practices
- **Group‑based sudo rules**
  - Replaced the default `%sudo ALL=(ALL:ALL) ALL` with a custom group:
    ```bash
    %sysadmins ALL=(ALL) ALL
    ```
  - This restricts sudo access to members of the `sysadmins` group only.

- **Defaults rules**
  - Added `Defaults` entries to enforce stricter behavior (e.g., logging, secure paths).
  - Example:
    ```bash
    Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
    ```
  - Ensures sudo commands run with a controlled environment.

- **Variants of rules**
  - **Full access**: `%group ALL=(ALL) ALL` → unrestricted sudo for group members.
  - **Limited access**: `%group ALL=(ALL) /usr/bin/systemctl` → only specific commands allowed.
  - **No password prompt**: `%group ALL=(ALL) NOPASSWD:ALL` → useful for automation, but risky in production.

## Documentation
- All changes were made using `sudo visudo` to prevent syntax errors.  
- Notes and examples were saved in `Hardening_04_05_26.txt` for reference.  
- This phase established the **baseline security policy** before moving into Phase 2 (watchers and detection).

## Directory Structure



# Phase 2 – Watcher Practices and Evidence Logs

## Overview
In this phase, I created **hunters/watchers** to monitor authentication events in `/var/log/auth.log`. Each watcher filters specific signals and writes them into custom log files for permanent evidence. Unlike `auth.log` (which is rotated and compressed by logrotate), these watcher logs persist until manually deleted.

## Watchers Implemented
- **Denied sudo attempts**
  ```bash
  sudo tail -f /var/log/auth.log | grep --line-buffered "NOT in sudoers" >> /var/log/sudo_denials.log
  sudo tail -f /var/log/auth.log | grep --line-buffered "session opened" >> /var/log/sessions_opened.log
  sudo tail -f /var/log/auth.log | grep --line-buffered "session closed" >> /var/log/sessions_closed.log


## Notes
- Phase 1 ensures **who can escalate privileges** is tightly controlled.  
- Phase 2 builds on this by **monitoring attempts and sessions**.  
- Together, they form a defense‑in‑depth approach: prevention + detection.
