# CPU Sentinel
CPU Sentinel is a script which scans for processes by name and deprioritizes
associated PIDs via the `renice` program, to mitigate chronically
resource-hogging processes (e.g., virus scanners, enterprise device management
daemons, auto-updaters, etc.)

CPU Sentinel can be run as a stand-alone script on macOS or any Linux/UNIX
system, or as a system service on macOS using the Launchd and Homebrew Services
mechanisms.  If run as a service, CPU Sentinel will automatically start upon
system startup and briefly re-scan for update PIDs to deprioritize every ten
minutes.


## Requirements
* bash
* renice
* root permissions (for renice privilege)
* Homebrew (for easy installation on macOS)


## Installation
Install via Homebrew:
1. `brew tap lstarrett/utilities`
2. `brew install cpu-sentinel`


## Usage
Run as a stand-alone script:
1. Clone or download this repository
2. `./cpu-sentinel --help` for usage info

Run as a Homebrew Service:
1. `cpu-sentinel --brew-config` to print the config and logging file paths
2. Edit the `procs.conf` config file with names of processes to deprioritize
3. `sudo brew services start cpu-sentinel` to start the CPU Sentinel service
4. `brew services list` to list running Homebrew services and their status
5. Check the log and error files (paths printed above) to ensure CPU Sentinel
   is running without errors

### NOTE:
CPU Sentinel requires root privileges to reprioritize processes with `renice`,
and therefore the Homebrew service must be started with `sudo` or the script
will fail each time the service is invoked. This will not cause an error with
the service itself, but the script will report "Operation not permitted" errors
in the error log file.


## Upgrading CPU Sentinel
1. `brew update`
2. `brew upgrade cpu-sentinel`
3. `sudo rm <previous version install directory>` (see NOTE below)
4. `brew services restart cpu-sentinel`

### NOTE:
Because the CPU Sentinel Homebrew service requires root privilege, Homebrew
will change the ownership of the CPU Sentinel installation directory upon
service invocation. Since Homebrew itself cannot be run as root, this will
prevent Homebrew from uninstalling the previous version of CPU Sentinel each
time CPU Sentinel is upgraded with `brew upgrade cpu-sentinel`.

The `brew upgrade` command will successfully upgrade CPU Sentinel, but will
fail to remove the root-owned previous version. Homebrew will offer the proper
command and path to remove the previous installation manually.

Run the remove command with `sudo` which Homebrew offers, and subsequently the
upgraded CPU Sentinel installation will be fully up-to-date and the service can
be safely restarted.


## Uninstalling CPU Sentinel
Uninstall via Homebrew:
1. `brew uninstall cpu-sentinel`
2. `sudo rm <install directory>` (see NOTE below)
3. `brew untap lstarrett/utilities`
4. `rm -r /usr/local/var/log/homebrew.mxcl.cpu-sentinel.log /usr/local/var/homebrew.mxcl.cpu-sentinel.err /usr/local/etc/cpu-sentinal`

### NOTE:
The `brew uninstall` command will stop the service and remove the launchd
service plist file, but it will not remove the installation directory
containing the CPU Sentinel script since the installation directory is owned by
root. Homebrew will offer the proper command and path to remove the install
directory manually.

Run the remove command with `sudo` which Homebrew offers to complete the
uninstallation.

The log files and config file for CPU Sentinel will persist after package
uninstallation, and must be removed manually with the command in step (4) above,
if desired.

