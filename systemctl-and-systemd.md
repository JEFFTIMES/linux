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

### Status and Logs

		systemctl status nginx.service     # Show detailed status
		journalctl -u nginx.service        # Show logs for this service
		systemctl is-enabled nginx.service # Check if enabled
		systemctl is-active <application[.service]>  # checking the activation of a service
		systemctl is-failed <application[.service]>
		systemctl list-units							# listing current active units
		systemctl list-units --all				# listing all units
		systemctl list units --all --state=inactive
		systemctl list-units --type=service
		systemctl list-unit-files					# listing all unit files

### Unit management

		systemctl list-units --type=service       # Running services
		systemctl list-unit-files --type=service # All installed services
		systemctl show <unit>                     # All properties of a unit
		systemctl cat <unit>                      # Show unit file
		systemctl daemon-reload                   # Reload after editing units

### Masking service

- masking, `$ systemctl mask <application[.service]>`.
- unmasking, `$ systemctl unmask <application[.service]>`.

### Editing unit files

- editing a precedence snippet, `$ sudo systemctl edit <application[.service]>`. It creates directory `application.service.d` in `/etc/systemd/system`, a snippet `override.conf` was created and saved here.

- editing the full unit file, `$ sudo systemctl edit --full <application[.service]>`.

- removing additional snippet, remove the directory application.service.d.

- reloading the systemd after the editing, `$ sudo systemctl daemon-reload`.

### Adjusting the system state with targets

targets are special unit files that describe a system state or synchronization point. the targets file have .target suffix.
units that are part of the process can sync with targets by indicating in their configuration that they are WantBy= or RequiredBy= the targets.
units that require the target to be available can specify this condition using the `Wants=`, `Requires=`, and `After=` specifications to indicate that nature of their relationship.
`.target` files and `.service` files locate at directory `/usr/lib/systemd/system/` .

- getting default target, `$ systemctl get-default`.

- setting default target, `$ sudo systemctl set-default <some.target>`.

- listing available targets, `$ systemctl list-unit-files --type=target`.

- listing all active targets `$ systemctl list-units --type=target`.

### isolating targets

- checking the dependencies of a target, `$ systemctl list-dependencies graphical.target`.

- it is possible to start all the units associated with a target and stop all units that are not part of the dependency tree. `$ sudo systemctl isolate multi-user.target`.

		systemctl isolate graphical.target   					# Switch to GUI mode
		systemctl isolate multi-user.target  				# Switch to text mode
		systemctl set-default graphical.target 			# Boot to GUI by default
		systemctl get-default                						# Show default target


### Systemd Targets & Boot

-   `graphical.target` → GUI + text services
-   `multi-user.target` → CLI + network
-   `rescue.target` → Single-user mode
-   `emergency.target` → Minimal shell
-   `poweroff.target` → Shutdown
-   `reboot.target` → Restart

### System Power Control

		systemctl reboot                # Reboot system
		systemctl poweroff              # Power off system
		systemctl halt                  # Halt (stop CPU)
		systemctl suspend               # Suspend to RAM
		systemctl hibernate             # Hibernate
		systemctl hybrid-sleep          # Suspend + Hibernate


### Emergency & Recovery

		systemctl isolate rescue.target    # Single-user mode
		systemctl isolate emergency.target # Minimal shell (root only)

## System Dependency Management

Systemd uses **unit files** that define:

-   **What** to start (service, target, socket, etc.)
    
-   **When** to start (dependencies, order)
    
-   **Conditions** for activation (path, timer, device, etc.)
    

Key directives in unit files:

-   `Requires=` → Hard dependency (must start together)
    
-   `Wants=` → Soft dependency (optional)
    
-   `Before=` / `After=` → Ordering only
    
-   `Conflicts=` → Mutually exclusive
    

Example **Nginx service unit file**:

		[Unit]
		Description=NGINX web server
		After=network.target

		[Service]
		ExecStart=/usr/sbin/nginx -g 'daemon off;'
		ExecReload=/bin/kill -s HUP $MAINPID
		Restart=on-failure

		[Install]
		WantedBy=multi-user.target


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4Nzc1MTU5MTQsMTEwMDA3MTYwNywtNz
IzODEyOTAyLC0xMTE5Mjg1NzEzLDIwOTc4MjM4OCwxNzA0MTMw
NDAyLDExOTczOTI1NzZdfQ==
-->