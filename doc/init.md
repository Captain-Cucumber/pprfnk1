Sample init scripts and service configuration for pprfnkd
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/pprfnkd.service:    systemd service unit configuration
    contrib/init/pprfnkd.openrc:     OpenRC compatible SysV style init script
    contrib/init/pprfnkd.openrcconf: OpenRC conf.d file
    contrib/init/pprfnkd.conf:       Upstart service configuration file
    contrib/init/pprfnkd.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "pprfnkcore" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes pprfnkd will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, pprfnkd requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, pprfnkd will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that pprfnkd and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If pprfnkd is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running pprfnkd without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/pprfnk.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/pprfnkd`  
Configuration file:  `/etc/pprfnkcore/pprfnk.conf`  
Data directory:      `/var/lib/pprfnkd`  
PID file:            `/var/run/pprfnkd/pprfnkd.pid` (OpenRC and Upstart) or `/var/lib/pprfnkd/pprfnkd.pid` (systemd)  
Lock file:           `/var/lock/subsys/pprfnkd` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the pprfnkcore user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
pprfnkcore user and group.  Access to pprfnk-cli and other pprfnkd rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/pprfnkd`  
Configuration file:  `~/Library/Application Support/PprfnkCore/pprfnk.conf`  
Data directory:      `~/Library/Application Support/PprfnkCore`
Lock file:           `~/Library/Application Support/PprfnkCore/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start pprfnkd` and to enable for system startup run
`systemctl enable pprfnkd`

4b) OpenRC

Rename pprfnkd.openrc to pprfnkd and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/pprfnkd start` and configure it to run on startup with
`rc-update add pprfnkd`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop pprfnkd.conf in /etc/init.  Test by running `service pprfnkd start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy pprfnkd.init to /etc/init.d/pprfnkd. Test by running `service pprfnkd start`.

Using this script, you can adjust the path and flags to the pprfnkd program by
setting the PPRFNKD and FLAGS environment variables in the file
/etc/sysconfig/pprfnkd. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.pprfnk.pprfnkd.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.pprfnk.pprfnkd.plist`.

This Launch Agent will cause pprfnkd to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run pprfnkd as the current user.
You will need to modify org.pprfnk.pprfnkd.plist if you intend to use it as a
Launch Daemon with a dedicated pprfnkcore user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
