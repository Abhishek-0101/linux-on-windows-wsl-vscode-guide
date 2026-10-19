# Linux on Windows with WSL 2 + VS Code

A beginner-friendly, step-by-step guide to running Linux on a Windows machine with **Windows Subsystem for Linux (WSL 2)**, **Ubuntu**, and **Visual Studio Code**.

**Audience:** beginners learning Linux, DevOps, cloud computing, scripting, Terraform, or backend development.

## Contents
- [Why use Linux on Windows?](#why-use-linux-on-windows)
- [How the pieces fit together](#how-the-pieces-fit-together)
- [Prerequisites](#prerequisites)
- [1. Install WSL 2 and Ubuntu](#1-install-wsl-2-and-ubuntu)
- [2. Set up Ubuntu](#2-set-up-ubuntu)
- [3. Essential WSL commands](#3-essential-wsl-commands)
- [4. Install VS Code and the WSL extension](#4-install-vs-code-and-the-wsl-extension)
- [5. Open a Linux project in VS Code](#5-open-a-linux-project-in-vs-code)
- [6. Install developer tools inside Ubuntu](#6-install-developer-tools-inside-ubuntu)
- [7. Understand Linux and Windows paths](#7-understand-linux-and-windows-paths)
- [8. Use the integrated terminal](#8-use-the-integrated-terminal)
- [9. Practice exercise](#9-practice-exercise)
- [Troubleshooting](#troubleshooting)
- [Security and good practices](#security-and-good-practices)
- [Useful commands](#useful-commands)
- [Official documentation](#official-documentation)

## Why use Linux on Windows?

Linux is widely used for servers, cloud infrastructure, containers, automation, and DevOps tooling. You do not need to replace Windows or dual-boot your machine to begin learning it.

WSL lets you run a Linux distribution such as Ubuntu alongside Windows. WSL 2 uses a lightweight virtualized environment with a real Linux kernel. VS Code can connect to that environment, so you edit files and run tools in Linux while keeping your Windows desktop.

Benefits:
- Practise Linux commands, permissions, processes, and file management.
- Use Linux-native tools such as Git, Python, Terraform, and AWS CLI.
- Learn a workflow similar to many cloud and server environments.
- Keep using Windows apps without rebooting into another operating system.
- Use VS Code's editor, extensions, source control, and terminal.

**Note:** WSL is not identical to every production Linux server. Networking, services, hardware access, and some kernel behaviours can differ. It is an excellent learning environment, but test production-specific assumptions in a matching environment.

## How the pieces fit together

| Component | Role |
|---|---|
| Windows | Main desktop operating system |
| WSL 2 | Runs a Linux environment alongside Windows |
| Ubuntu | Linux distribution and command-line tools |
| VS Code | Editor and user interface |
| VS Code WSL extension | Connects the editor to Linux so tools can run in the correct environment |

```text
Windows desktop
├── VS Code (editor / UI)
│   └── WSL extension
│       └── Ubuntu in WSL 2
│           ├── Linux filesystem: /home/<user>/
│           ├── Linux terminal
│           └── Linux-installed tools: Git, Python, Terraform, AWS CLI...
└── Windows apps and files
```

When the lower-left corner of VS Code shows `WSL: Ubuntu` (or your distribution's name), that VS Code window is connected to Linux.

## Prerequisites

- Supported, updated Windows 10 or Windows 11.
- Administrator access for installation.
- Internet access.
- Enough disk space for the Linux distribution and tools.

Commands below are labelled **PowerShell** or **Ubuntu**. Do not paste a command into the other shell unless a step explicitly says to.

## 1. Install WSL 2 and Ubuntu

Open **PowerShell as Administrator** and run:

```powershell
wsl --install -d Ubuntu
```

Restart if requested. Open Ubuntu from the Start menu, or run `wsl -d Ubuntu` in PowerShell. Complete Ubuntu's first-time setup.

Check your installed distributions and their WSL versions in PowerShell:

```powershell
wsl --list --verbose
```

Look for Ubuntu and confirm `VERSION` is `2`. If needed, and supported by your installation:

```powershell
wsl --set-version Ubuntu 2
wsl --set-default-version 2
```

If `wsl --install` fails, update Windows and follow Microsoft's official installation guide in the links below. Some managed devices restrict virtualization or WSL.

## 2. Set up Ubuntu

On first launch, Ubuntu asks you to create a Linux username and password. This account is separate from your Windows account. When typing a Linux password, the terminal normally shows no characters; that is expected.

Update package information and install updates:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y git curl unzip
```

- `sudo`: runs a command with elevated Linux privileges.
- `apt update`: refreshes the package index.
- `apt upgrade -y`: installs available upgrades and automatically confirms prompts.
- `apt install`: installs packages.

Check your environment:

```bash
whoami
pwd
uname -a
```

## 3. Essential WSL commands

Run these in **PowerShell**:

```powershell
wsl --list --verbose
wsl --status
wsl -d Ubuntu
wsl --shutdown
```

- `wsl --list --verbose`: lists installed distributions.
- `wsl --status`: shows general WSL configuration.
- `wsl -d Ubuntu`: launches the distribution named `Ubuntu`.
- `wsl --shutdown`: stops all running WSL distributions. Save work first.

Your distribution might have another name, such as `Ubuntu-D`. Use the exact name shown by `wsl --list --verbose`.

To stop only one distribution:

```powershell
wsl --terminate Ubuntu
```

Replace `Ubuntu` with the actual distribution name.

### Optional: Store a distribution on another drive

WSL supports exporting and importing a distribution. This is an advanced operation; verify paths and make a backup first.

```powershell
wsl --export Ubuntu D:\WSL\ubuntu-backup.tar
wsl --import Ubuntu-D D:\WSL\Ubuntu D:\WSL\ubuntu-backup.tar --version 2
wsl -d Ubuntu-D
```

This creates a new registered distribution named `Ubuntu-D`; it does **not** remove the original. Verify the new distribution and your files before considering cleanup.

**Warning:** `wsl --unregister <DistroName>` deletes that distribution's data. Never run it unless you are certain you have the correct name and a verified backup or no longer need the data.

## 4. Install VS Code and the WSL extension

1. Install VS Code for Windows from <https://code.visualstudio.com/>.
2. Open VS Code and open Extensions with `Ctrl+Shift+X`.
3. Search for **WSL** and install Microsoft's **WSL** extension.
4. Open the Command Palette with `Ctrl+Shift+P`.
5. Select **WSL: Connect to WSL** or **WSL: Connect to WSL using Distro**.
6. Choose your Ubuntu distribution.

The bottom-left corner should show `WSL: Ubuntu` or the name of your selected distribution. If prompted, allow VS Code to install its server components inside WSL.

VS Code's interface runs on Windows, while the remote workspace and many extension components run in WSL. Tools installed only on Windows are not automatically installed inside Ubuntu.

## 5. Open a Linux project in VS Code

In the Ubuntu terminal:

```bash
mkdir -p ~/projects/linux-practice
cd ~/projects/linux-practice
code .
```

- `mkdir -p`: creates the directory, including missing parent directories.
- `cd`: changes the current directory.
- `code .`: opens the current directory in VS Code. The dot means “this directory.”

If `code .` is not recognized, connect to WSL from the VS Code Command Palette, ensure the Microsoft WSL extension is installed, open a WSL terminal, and try again.

### Where should projects live?

For Linux-native development, prefer the Linux filesystem, for example:

```text
/home/<linux-username>/projects/
```

This usually gives better performance and fewer permission surprises than working directly under `/mnt/c` or `/mnt/d`.

Windows drives are available under `/mnt`:

```bash
cd /mnt/c/Users
ls
```

Use `/mnt/d` for the Windows D: drive. This is useful for sharing files with Windows, but Linux development projects generally belong in the Linux filesystem unless you have a specific reason otherwise.

## 6. Install developer tools inside Ubuntu

Install tools inside Ubuntu when you want them to run in Linux. A Windows installation does not automatically make the Linux command available.

### Git

```bash
sudo apt update
sudo apt install -y git
git --version
```

Optional commit identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Python

```bash
sudo apt install -y python3 python3-pip python3-venv
python3 --version
python3 -m venv .venv
source .venv/bin/activate
deactivate
```

Run the virtual-environment commands inside a project directory. `deactivate` exits the environment.

### Terraform and AWS CLI

Use the vendors' current official Linux installation instructions. Verify from the **Ubuntu terminal**:

```bash
terraform -version
aws --version
```

If a command is missing, install the Linux version inside WSL. If you need AWS access, configure credentials using an approved method; never put credentials in a repository.

## 7. Understand Linux and Windows paths

| Path | Meaning |
|---|---|
| `/home/abhishek/` | Example Linux home directory; your username may differ |
| `~/projects/` | Projects directory inside your Linux home |
| `/mnt/c/` | Windows C: drive as seen from WSL |
| `/mnt/d/` | Windows D: drive as seen from WSL |

Useful Ubuntu commands:

```bash
pwd
ls -la
cd ~
explorer.exe .
```

- `pwd`: prints the current directory.
- `ls -la`: lists files, including hidden files.
- `cd ~`: moves to your Linux home directory.
- `explorer.exe .`: opens the current location in Windows File Explorer.

Use VS Code connected to WSL to edit Linux project files. Avoid editing Linux system files using Windows tools that do not understand Linux filesystem permissions.

## 8. Use the integrated terminal

In a VS Code window connected to WSL:

1. Select **Terminal → New Terminal** or press ``Ctrl+Shift+` ``.
2. Run:

   ```bash
   pwd
   whoami
   git --version
   ```

3. Confirm the terminal is Linux/Ubuntu, not an ordinary Windows PowerShell session.

Some extensions run on Windows; others need to be installed in the WSL environment. VS Code indicates when an extension should be installed remotely.

## 9. Practice exercise

Create a small project and run it inside WSL:

```bash
mkdir -p ~/projects/hello-wsl
cd ~/projects/hello-wsl
printf '# Hello from WSL\n\nThis file was created in the Linux filesystem.\n' > README.md
printf 'print("Hello from Ubuntu on WSL 2!")\n' > hello.py
python3 hello.py
code .
```

Expected output:

```text
Hello from Ubuntu on WSL 2!
```

Try changing the message, running `pwd`, listing files with `ls -la`, and initializing Git with `git init`.

## Troubleshooting

### `wsl` is not recognized
Run PowerShell as Administrator, install/update WSL using Microsoft's guide, reopen the terminal, and restart Windows if requested.

### Password characters do not appear in Ubuntu
That is normal. Type the password and press Enter. If you have forgotten it, use Microsoft's documented WSL password-reset procedure.

### `code .` is not recognized
Install VS Code on Windows, install Microsoft's WSL extension, connect to your distribution, then try from a new WSL terminal.

### VS Code connects to the wrong distribution
Run `wsl --list --verbose` in PowerShell and select the intended distro through the Command Palette. The name may be `Ubuntu-D`, not `Ubuntu`.

### A tool works in Windows but not Ubuntu
Windows and WSL have separate environments and PATHs. Install the Linux version inside Ubuntu and verify with `command -v <tool>` or `<tool> --version`.

### `sudo` asks for a password
This is normal. Enter the Linux password created during Ubuntu setup. Typed characters will not appear.

### Files are slow or permissions behave strangely
Move Linux development projects into the Linux filesystem, such as `~/projects/`, and open them from a WSL-connected VS Code window.

### Disk space is running low
Check Windows drive space and WSL storage. Do not manually delete files from WSL's internal storage folders. If moving a distribution, export and verify it first. Only remove an old distribution after confirming the new one contains the files you need.

## Security and good practices

- Never commit private SSH keys, AWS credentials, `.env` files, or Terraform state files to a public repository.
- Add a `.gitignore` before using `git add .` on infrastructure projects.
- Treat `wsl --unregister <DistroName>` as destructive: it deletes the distribution and its data.
- Use `sudo` only when needed; do not disable password protection globally for convenience.
- Keep Windows, WSL, Ubuntu packages, VS Code, and extensions updated.
- Use official installation instructions for tools such as Terraform and AWS CLI.
- Inspect every file and commit for secrets before publishing a repository.

## Useful commands

### PowerShell / Windows

```powershell
wsl --list --verbose
wsl --status
wsl -d Ubuntu
wsl --shutdown
```

### Ubuntu / Linux

```bash
whoami
pwd
ls -la
cd ~
sudo apt update
sudo apt upgrade -y
mkdir -p ~/projects/my-project
cd ~/projects/my-project
code .
python3 --version
git --version
```

## Official documentation

- Microsoft: Install WSL — <https://learn.microsoft.com/windows/wsl/install>
- Microsoft: Basic WSL commands — <https://learn.microsoft.com/windows/wsl/basic-commands>
- Microsoft: Set up a WSL development environment — <https://learn.microsoft.com/windows/wsl/setup/environment>
- VS Code: WSL documentation — <https://code.visualstudio.com/docs/remote/wsl>
- Ubuntu documentation — <https://documentation.ubuntu.com/>
- Git documentation — <https://git-scm.com/doc>
- Terraform install documentation — <https://developer.hashicorp.com/terraform/install>
- AWS CLI install documentation — <https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html>

## Contributing

Corrections and beginner-friendly improvements are welcome. Do not include private keys, cloud credentials, or personal machine details in issues or pull requests.

## License

This guide is shared under the MIT License. See [`LICENSE`](LICENSE).
