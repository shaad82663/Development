# Linux Notes: Simplified Guide with Examples

## Core Concepts

### Everything in Linux is a File
In Linux, almost everything is treated as a file, including hardware devices, directories, and processes. For example, a hard drive might be represented as `/dev/sda`.

**Example**: 
- To view a hardware device file: 
  ```bash
  ls /dev/sda
  ```

### Small Programs for Complex Operations
Linux emphasizes small, single-purpose programs that can be combined via pipes (`|`) or scripts to perform complex tasks.

**Example**:
- Combine `ls` and `grep` to list only `.txt` files:
  ```bash
  ls | grep ".txt$"
  ```

### Avoid Captive User Interfaces
A "captive user interface" refers to programs that lock you into their interface, limiting flexibility (e.g., GUI-based tools that don’t allow scripting). Linux favors command-line tools for automation and flexibility.

**Example**:
- Instead of a GUI text editor, use `vim` or `nano` for scriptable editing.

### Configuration Stored in Files
Linux stores configurations in plain text files, often in `/etc/`. This makes them easy to edit and script.

**Example**:
- Edit the SSH configuration:
  ```bash
  sudo vim /etc/ssh/sshd_config
  ```

### Popular Linux Distributions
- **Desktop**: Ubuntu, Linux Mint, Fedora, Arch Linux
- **Server**:
  1. **Red Hat Enterprise Linux (RHEL)**: Not fully open-source, enterprise-focused.
  2. **Ubuntu Server**: User-friendly, widely used.
  3. **CentOS Stream**: Community-driven, RHEL-based.
  4. **SUSE Linux Enterprise Server**: Enterprise-grade.
  5. **Amazon Linux**: Optimized for AWS.

### Package Management Families
- **Debian-based** (e.g., Ubuntu): Uses `.deb` packages, managed with `apt`.
- **RPM-based** (e.g., RHEL, CentOS, Fedora): Uses `.rpm` packages, managed with `dnf` or `yum`.

**Example**:
- Install a package on Ubuntu:
  ```bash
  sudo apt install tree
  ```
- Install a package on CentOS:
  ```bash
  sudo dnf install tree
  ```

## Common Commands

### User and System Information
- `whoami`: Displays the current user.
  ```bash
  whoami
  # Output: john
  ```
- `sudo -i`: Switch to the root user (requires sudo privileges).
  ```bash
  sudo -i
  ```
- `exit`: Return to the previous user or close the terminal.
  ```bash
  exit
  ```

### File System Navigation
- `/bin`: Contains user-executable programs (e.g., `ls`, `cat`).
- `/sbin`: Contains system binaries for root/admin tasks (e.g., `fdisk`).

### File Operations
- **Copy Files/Directories**:
  - `cp <source> <destination>`: Copy a file.
    ```bash
    cp myText.txt /home/user/backup/
    ```
  - `cp -r <source> <destination>`: Copy a directory recursively.
    ```bash
    cp -r myDir /home/user/backup/
    ```
  - Check help: `cp --help`

- **Move/Rename Files**:
  - `mv <source> <destination>`: Move or rename a file/directory.
    ```bash
    mv myText.txt testDir/
    ```

- **View Command History**:
  - `history`: Lists previously executed commands.
    ```bash
    history
    ```

### Directory Creation
- `mkdir <path>`: Create a directory.
  - Fails if parent directories don’t exist.
    ```bash
    mkdir a/b/c/dd  # Fails if 'a' or 'b' doesn’t exist
    ```
  - Use `-p` to create parent directories if needed:
    ```bash
    mkdir -p a/b/c/dd
    ```

### Symbolic Links
- `ln -s <target> <link_name>`: Create a symbolic link (shortcut) to a file.
  ```bash
  ln -s /a/b/c/f.txt cmds
  cat cmds  # Accesses f.txt
  ```
- If the target file is moved or deleted, the link becomes dead.
- Remove a link:
  ```bash
  unlink cmds
  ```

### Listing Files by Timestamp
- `ls -lt`: List files in descending order of modification time (newest first).
  ```bash
  ls -lt
  ```
- `ls -ltr`: List in ascending order (oldest first).
  ```bash
  ls -ltr
  ```

## Text Processing

### Searching with `grep`
- `grep <search_term> <file>`: Search for text in a file.
  ```bash
  grep "error" log.txt
  ```
- `grep -i <search_term> <file>`: Case-insensitive search.
  ```bash
  grep -i "error" log.txt
  ```
- `grep -R <search_term> <dir>`: Recursive search in a directory.
  ```bash
  grep -R "SELINUX" /etc/*
  ```
- `grep -v <search_term> <file>`: Exclude lines matching the term.
  ```bash
  grep -v "fire" log.txt
  ```

### Viewing Files
- `less <file>`: View file with scrollable navigation. Use `/<term>` to search.
  ```bash
  less log.txt
  ```
- `more <file>`: Similar to `less`, but less flexible.
  ```bash
  more log.txt
  ```
- `head -n <num> <file>`: View the first `num` lines.
  ```bash
  head -20 log.txt
  ```
- `tail -n <num> <file>`: View the last `num` lines.
  ```bash
  tail -20 log.txt
  ```
- `tail -f <file>`: Monitor live updates (e.g., for logs).
  ```bash
  tail -f /var/log/syslog
  ```

### Output Redirection
- `>`: Overwrite a file with output.
  ```bash
  echo "Hello" > file.txt
  ```
- `>>`: Append to a file.
  ```bash
  echo "World" >> file.txt
  ```
- Redirect to `/dev/null` (discard output):
  ```bash
  echo "Discard this" > /dev/null
  ```
- Clear a file:
  ```bash
  cat /dev/null > file.txt
  ```
- Redirect errors (`stderr`) to a file:
  ```bash
  free -m 2>> /tmp/error.log
  ```

### Piping
- Use `|` to pass output of one command as input to another.
  ```bash
  ls | grep "host"
  ```

### File Search
- `find <path> -name <pattern>`: Search for files by name.
  ```bash
  find /etc -name "host*"
  ```

## Vim Editor

### Modes
1. **Command Mode**: Default mode for navigation and commands.
2. **Insert Mode**: Enter with `i` to edit text.
3. **Extended Mode**: Enter with `:` for commands like saving or quitting.

### Common Commands
- `:w`: Save the file.
- `:wq`: Save and quit.
- `:wq!`: Force save and quit.
- `:q!`: Quit without saving.
- `:set nu`: Show line numbers.
- `Shift + G`: Go to the bottom of the file.
- `gg`: Go to the top of the file.

### Copy, Paste, and Delete
- `yy`: Copy the current line.
- `5yy`: Copy 5 lines.
- `p`: Paste below the cursor.
- `P`: Paste above the cursor.
- `dd`: Delete (cut) the current line.
- `5dd`: Delete 5 lines.
- `u`: Undo the last action.
- **Copy-Paste**: `yy` + `p`
- **Cut-Paste**: `dd` + `p`

### Search
- `/<term>`: Search for a term in the file.
  ```vim
  /error
  ```

## Users and Groups

### Adding Users
- `useradd <username>`: Create a user (requires root).
  ```bash
  sudo useradd personal
  ```
- Set a password:
  ```bash
  sudo passwd personal
  ```
- Verify user creation:
  ```bash
  tail -1 /etc/passwd
  # Output: personal:x:1001:1001::/home/personal:/bin/bash
  ```

### Adding Groups
- `groupadd <groupname>`: Create a group.
  ```bash
  sudo groupadd devops
  ```
- Add user to a group:
  ```bash
  sudo usermod -aG devops personal
  ```
- Verify:
  ```bash
  id personal
  # Output: uid=1001(personal) gid=1001(personal) groups=1001(personal),1002(devops)
  ```

### Switching Users
- `su - <username>`: Switch to another user.
  ```bash
  su - personal
  ```

### Deleting Users/Groups
- `userdel <username>`: Delete a user.
  ```bash
  sudo userdel personal
  ```
- `userdel -r <username>`: Delete user and their home directory.
  ```bash
  sudo userdel -r personal
  ```
- `groupdel <groupname>`: Delete a group.
  ```bash
  sudo groupdel devops
  ```

### List Open Files by User
- `lsof -u <username>`: Show files opened by a user.
  ```bash
  lsof -u personal
  ```

## File Permissions

### Understanding Permissions
- Example output of `ls -l`:
  ```bash
  -rw-------.  1 root root 1668 Sep  5 2024 anaconda-ks.cfg
  ```
  - First character (`-`): File type (`-` for file, `d` for directory).
  - Next 3 (`rw-`): Permissions for the owner (read, write, no execute).
  - Next 3 (`---`): Permissions for the group (no permissions).
  - Next 3 (`---`): Permissions for others (no permissions).

- Directory example:
  ```bash
  drwxr-xr-x. 19 root root 4096 Sep  5 2024 zfs
  ```
  - `d`: Directory.
  - `rwx`: Owner can read, write, execute.
  - `r-x`: Group/others can read and execute.

- Check directory permissions:
  ```bash
  ls -ld /path/to/dir
  ```

### Changing Permissions
- `chmod <permissions> <file>`: Change file permissions.
  - Numeric: `chmod 644 file.txt` (owner: read/write, group/others: read).
  - Symbolic: `chmod u+x file.sh` (add execute permission for owner).
  ```bash
  chmod 755 script.sh
  ```

## Sudo Access

### Granting Sudo Privileges
- Non-sudo users can’t run privileged commands:
  ```bash
  useradd tempuser
  # Output: useradd: Permission denied.
  ```
- With `sudo`:
  ```bash
  sudo useradd tempuser
  ```
- Add a user to the sudoers file:
  1. Edit `/etc/sudoers.d/personal`:
     ```bash
     sudo vim /etc/sudoers.d/personal
     ```
  2. Add:
     ```
     personal ALL=(ALL) ALL
     ```

## Package Management

### RPM-Based (RHEL, CentOS, Fedora)
- Use `dnf` (modern) or `yum`:
  ```bash
  sudo dnf install tree
  ```
- Install a package like Jenkins:
  ```bash
  sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
  sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
  sudo dnf install fontconfig java-21-openjdk
  sudo dnf install jenkins
  sudo systemctl daemon-reload
  ```

### Debian-Based (Ubuntu)
- Use `apt`:
  ```bash
  sudo apt update
  sudo apt install tree
  ```
- Search for a package:
  ```bash
  apt search tree
  ```
- Remove a package:
  ```bash
  sudo apt remove tree
  ```
- Purge (remove with config files):
  ```bash
  sudo apt purge tree
  ```

### Set Default Editor (Ubuntu)
- Set `vim` as default editor:
  ```bash
  export EDITOR=vim
  ```
- Persist across sessions:
  ```bash
  echo "export EDITOR=vim" >> ~/.bashrc
  ```

## Services

### Managing Services
- Check service status:
  ```bash
  systemctl status httpd
  ```
- Start a service:
  ```bash
  systemctl start httpd
  ```
- Enable service on boot:
  ```bash
  systemctl enable httpd
  ```
- Check if active/enabled:
  ```bash
  systemctl is-active httpd
  systemctl is-enabled httpd
  ```

## Processes

- `top`: Real-time process monitoring.
  ```bash
  top
  ```
- `ps aux`: List all running processes.
  ```bash
  ps aux
  ```