# ROS 工具

A useful command that can be used with RancherOS is ros which can be used to control and configure the system. ros requires you to be the root user, so with the rancher user, you will need to use sudo.

SUB COMMANDS

| Command | Description | |—————|———————————————————————————————–| |config, c | Configure Settings | |dev, d | dev spec |env, e | Run a command with RancherOS environment | |service, s | Command Line interface for services and compose. | |os | Operating System Upgrade/Downgrade | |tls | Setup TLS configuration | |install | Install RancherOS to Disk | |help, h | Shows a list of commands or help for one command |

RANCHEROS VERSION

If you want to check what version you are on, just use the -v option.

```$ sudo ros -v
ros version v0.4.0```

HELP

To list available commands, run any ros command with -h or --help. This would work with any subcommand within ros.

```bash
$ sudo ros -h
NAME:
    ros - Control and configure RancherOS

USAGE:
    ros [global options] command [command options] [arguments...]

VERSION:
    v0.4.0

AUTHOR(S): 
    Rancher Labs, Inc.  

COMMANDS:
    config, c   configure settings
    dev, d      dev spec
    env, e      env command
    service, s  Coomand line interface for services and compose.
    os          operating system upgrade/downgrade
    tls         setup tls configuration
    install     install RancherOS to disk
    help, h     Shows a list of commands or help for one command

GLOBAL OPTIONS:
    --help, -h                  show help
    --generate-bash-completion	
    --version, -v               print the version
 ```
