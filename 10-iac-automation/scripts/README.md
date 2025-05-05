# 📜 Scripting for Infrastructure Automation

This section contains scripts used for automating system configurations and task scheduling for both Linux and Windows environments.

## 🖥️ Scripting Overview

- **PowerShell 7** for Windows automation
- **Bash** for Linux automation
- Task scheduling via **Systemd** for Linux and **Task Scheduler** for Windows

## 💻 PowerShell Scripts (Windows)

1. **windows-setup.ps1**
   - A script that automates common setup tasks on Windows systems.
   
2. **schedule-tasks.ps1**
   - Schedules recurring tasks using **Task Scheduler** for Windows environments.
   - Example: Run a backup script every night at 3 AM.

## 🐧 Bash Scripts (Linux)

1. **linux-setup.sh**
   - A bash script to automate basic setup tasks on Linux systems.
   
2. **schedule-tasks.sh**
   - Schedules recurring tasks using **systemd timers** for Linux environments.
   - Example: Run a maintenance task every Sunday at 2 AM.

## 🗓️ Task Scheduling

### Windows (Task Scheduler)

- **schedule-tasks.ps1** example:
```powershell
$action = New-ScheduledTaskAction -Execute "C:\Path\To\Script.bat"
$trigger = New-ScheduledTaskTrigger -Daily -At "3am"
Register-ScheduledTask -Action $action -Trigger $trigger -TaskName "NightlyBackup"
```

## Linux (Systemd Timers)

### schedule-tasks.sh example:

```powershell
sudo nano /etc/systemd/system/nightly-backup.timer
Include the following content in the file:

``` 
```ini

[Unit]
Description=Run nightly backup

[Timer]
OnCalendar=03:00
Unit=nightly-backup.service

[Install]
WantedBy=timers.target
Then create the service:

```

```bash

sudo nano /etc/systemd/system/nightly-backup.service
Include the following:

```

```ini

[Unit]
Description=Run nightly backup

[Service]
ExecStart=/path/to/backup.sh
Start and enable the timer:

```
```bash

sudo systemctl start nightly-backup.timer
sudo systemctl enable nightly-backup.timer

```

## 🔄 Integration with Terraform & Ansible
Scripts like generate-linux-inventory.sh can be scheduled to run after successful Terraform apply, automating your infrastructure management pipeline.
