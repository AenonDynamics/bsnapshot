bsnapshot
=============================

systemd based backup schedules for [restic](https://restic.net/) with per-instance config files

## Features ##

* Simple bash script to create btrfs snapshots
* Snapshot groups for daily/weekly/monthly/yearly tasks
* Set custom retention policies for each backup group
* Time based locking to avoid unwanted snapshots (execute script multiple times in a row)

## Usage ##

bsnapshot has one magic command called `backup` - it executed the daily/weekly/monthly/yearly schedules and finally applies the backup retention policies to remove outdated snapshot.

**Example**

```
 # ./bsnapshot backup
 > daily snapshots enabled
no previous snapshot found
Create a readonly snapshot of '/mnt/backup' in '/mnt/data/.snapshots/daily/daily-20201020T190904'
snapshot created

 > weekly snapshots enabled
no previous snapshot found
Create a readonly snapshot of '/mnt/backup' in '/mnt/data/.snapshots/weekly/weekly-20201020T190904'
snapshot created

 > monthly snapshots enabled
no previous snapshot found
Create a readonly snapshot of '/mnt/backup' in '/mnt/data/.snapshots/monthly/monthly-20201020T190905'
snapshot created

 > yearly snapshots enabled
no previous snapshot found
Create a readonly snapshot of '/mnt/backup' in '/mnt/data/.snapshots/yearly/yearly-20201020T190905'
snapshot created

```

### List backups ###

To get an overview of all snapshots, just run the `list` command:

**Example**

```
 # ./bsnapshot list
/mnt/data/.snapshots
├── daily
│   └── daily-20201020T190904
├── monthly
│   └── monthly-20201020T190905
├── weekly
│   └── weekly-20201020T190904
└── yearly
    └── yearly-20201020T190905
```

## Package Installation ##

### Via Aenon-Dynamics Repository ###

See [AenonDynamics/CPR](https://github.com/AenonDynamics/CPR#debian-packages)

```
apt-get install bsnapshot
```

### Manual setup ###

* Copy `bsnapshot` to `/usr/sbin/bsnapshot`
* Create the configuration file in `/etc/restic`

## Configuraton ##

### Volumes ###

You have to set a source directory (a btrfs subvolume + valid mountpoint) from which the snapshot should be created as well as a directory within the same btrfs filesystem which contains the snapshots. That's it!

File: `/etc/bsnapshot.conf`

```conf
...
# btrfs source volume
SOURCE_DIR="/mnt/data"

# snapshot storage directory within btrfs source volume
SNAPSHOT_DIR="/mnt/data/.snapshots"

```
### Data retention schedule ###

The build-in data retention policy is executed after each backup. You can define how log a snapshot (subvolume) should exist before it's automatically removed by bsnapshot.

By setting a policy to zero (or left empty) the backup group **will be disabled** - no snapshot is created for the schedule nor the retention policy is applied


**Configuration**

File: `/etc/bsnapshot.conf`

```conf
...
# keep 14 daily snapshots
RETENTION_POLICY_DAYS=14

# keep 12 weekly snapshots
RETENTION_POLICY_WEEKS=12

# keep 18 monthly snapshots
RETENTION_POLICY_MONTHS=18

# keep 2 yearly snapshots
RETENTION_POLICY_YEARS=2
```

### systemd-timer or cron invocation ###

bsnapshot is designed to be executed ones a day. It's up to you to setup a systemd-timer or cron.

## Contribution ##

The **.deb** package is automatically generated via a **Continuous Delivery Pipeline** - please do not build packages manually!

## License ##
bsnapshot is OpenSource and licensed under the Terms of [Mozilla Public License 2.0](https://opensource.org/licenses/MPL-2.0). You're welcome to [contribute](docs/CONTRIBUTING.md)