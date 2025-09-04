# systemctl and systemd

systemd is the popular  init system of almost linux distros.
systemctl is the command to manage the systemd.


## Core Syntax

		systemctl [OPTIONS] COMMAND [NAME...]

**Where:**

- **COMMAND** = operation (start, stop, enable, status, isolate, etc.)    
- **NAME** = unit name (e.g., `nginx.service`, `graphical.target`)

## Types of Units

| Unit Type  |  File Extension  |  Purpose |
| :---        |    :----:   |          :--- |
| **service**  | `.service` |  Daemons (e.g., `nginx.service`) |
| **socket** | `.socket` |  Socket activation |
| **target** | `.target` | Group of units (like runlevels) |
| **device** | `.device` | Hardware devices |
| **mount** | `.mount` | Filesystem mounts |
| **timer** | `.timer` | Scheduled tasks (cron replacement) |

 
## Common Commands

### Service Management

		systemctl start nginx.service      # Start service
		systemctl stop nginx.service       # Stop service
		systemctl restart nginx.service    # Restart service
		systemctl reload nginx.service     # Reload config (without restart)

### Enable/Disable at Boot

		systemctl enable nginx.service     # Start on boot
		systemctl disable nginx.service    # Don't start on boot
		systemctl is-enabled nginx.service # Check if enabled
		systemctl is-active <application[.service]>  # checking the activation of a service
		systemctl is-failed <application[.service]>
		systemctl list-units							# listing current active units
		systemctl list-units --all				# listing all units
		systemctl list units --all --state=inactive
		systemctl list-units --type=service
		systemctl list-unit-files	

- listing all inactive units, `S `.

- filtering by type, `$ `.

- listing all unit files, `$ s`.
		


### Status and Logs

		systemctl status nginx.service     # Show detailed status
		journalctl -u nginx.service        # Show logs for this service




## checking system status

- checking the status of a service, `$ systemctl status <application[.service]>`.

- checking enablement of a service, `$ systemctl is-enabled <application[.service]>`.

- checking the activation of a service, `$ systemctl is-active <application[.service]>`.

- checking if a service failed, `$ systemctl is-failed <application[.service]>`.

- listing current active units, `$ systemctl list-units`.

- listing all units, `s systemctl list-units --all`.

- listing all inactive units, `S systemctl list units --all --state=inactive`.

- filtering by type, `$ systemctl list-units --type=service`.

- listing all unit files, `$ systemctl list-unit-files`.

## unit management

- displaying a unit file, `$ systemctl cat <application[.service]>`.

- displaying dependencies, `$ systemctl list-dependencies <application[.service]>`.

- checking unit properties, `$ systemctl show <application[.service]>`.

- checking specific property, `$ systemctl show <application[.service]> -p <property-name>`.

## masking service

- masking, `$ systemctl mask <application[.service]>`.

- unmasking, `$ systemctl unmask <application[.service]>`.

## editing unit files

- editing a precedence snippet, `$ sudo systemctl edit <application[.service]>`. It creates directory `application.service.d` in `/etc/systemd/system`, a snippet `override.conf` was created and saved here.

- editing the full unit file, `$ sudo systemctl edit --full <application[.service]>`.

- removing additional snippet, remove the directory application.service.d.

- reloading the systemd after the editing, `$ sudo systemctl daemon-reload`.

## adjusting the system state with targets

targets are special unit files that describe a system state or synchronization point. the targets file have .target suffix.
units that are part of the process can sync with targets by indicating in their configuration that they are WantBy= or RequiredBy= the targets.
units that require the target to be available can specify this condition using the `Wants=`, `Requires=`, and `After=` specifications to indicate that nature of their relationship.
`.target` files and `.service` files locate at directory `/usr/lib/systemd/system/` .

- getting default target, `$ systemctl get-default`.

- setting default target, `$ sudo systemctl set-default <some.target>`.

- listing available targets, `$ systemctl list-unit-files --type=target`.

- listing all active targets `$ systemctl list-units --type=target`.

## isolating targets

- checking the dependencies of a target, `$ systemctl list-dependencies graphical.target`.

- it is possible to start all the units associated with a target and stop all units that are not part of the dependency tree. `$ sudo systemctl isolate multi-user.target`.

## using shortcuts for important events

- put the system into rescue (single-user) mode, `$ sudo systemctl rescue`.

- to halt the system, `$ sudo systemctl halt`.

- to initiate a full shutdown, `$ sudo systemctl poweroff`.

- restarting the system, `$ sudo systemctl reboot`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcwNTg5MjA5MywtMTExOTI4NTcxMywyMD
k3ODIzODgsMTcwNDEzMDQwMiwxMTk3MzkyNTc2XX0=
-->