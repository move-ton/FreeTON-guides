# Centos/RH system settings

It's important to optimize some settings to ensure stability of the validator node

### Sysctl

A good idea is to change default values to sysctl

Place [file](sysctl_ton.conf) under `/etc/sysctl.d/`

### Time sync

It's an issue with time sync in Centos 8

We can easily fix this

1. Install/Enable ChronyD

`dnf -y install chrony`

`systemctl enable chronyd`

2. Create new config

`> /etc/chrony.conf`

`vi /etc/chrony.conf`

Place values

```
pool pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 10 3
rtcsync
```

3. Restart service

`service chronyd restart`

### Firewalld

Enable UDP 30310 for ADNL

```
firewall-cmd --zone=public --permanent --add-port=30310/udp
firewall-cmd --reload
```

### Free TON service

It's always better to let systemd manage validator-engine process to ensure availability.

I've got all keys of validator-engine to produce service file. Remember, that `-C` is mandatory.

```
validator-engine --help
validator or full node for TON network
-v|--verbosity<arg>     set verbosity level
-h|--help       prints_help
-C|--global-config<arg> file to read global config
-c|--local-config<arg>  file to read local config
-I|--ip<arg>    ip:port of instance
-D|--db<arg>    root for dbs
-f|--fift-dir<arg>      directory with fift scripts
-d|--daemonize  set SIGHUP
-l|--logname<arg>       log to file
-s|--state-ttl<arg>     state will be gc'd after this time (in seconds) default=3600
-b|--block-ttl<arg>     blocks will be gc'd after this time (in seconds) default=7*86400
-A|--archive-ttl<arg>   archived blocks will be deleted after this time (in seconds) default=365*86400
-K|--key-proof-ttl<arg> key blocks will be deleted after this time (in seconds) default=365*86400*10
-S|--sync-before<arg>   in initial sync download all blocks for last given seconds default=3600
-T|--truncate-db<arg>   truncate db (with specified seqno as new top masterchain block seqno)
-U|--unsafe-catchain-restore<arg>       use SLOW and DANGEROUS catchain recover method
-t|--threads<arg>       number of threads (default=7)
-u|--user<arg>  change user
```

My idea about threads:

I need to reserve 1 thread for NIC, at least one thread for NVMe LUN

My TON vm is virtualized with 20 threads; physical cores on hypervisor is 12 and 24 threads total.

I've decided to give 18 threads to validator-engine

1. Place [file](ton.service) (please correct absolute paths to validator-engine/config/db and threads via '-t') under `/lib/systemd/system/`

2. Create dir for log collection `mkdir -p /var/log/ton/` chown if you run service under special user (eg ton) `chown -R ton:ton /var/log/ton/`

3. Run and enable service

`service ton start`

`systemctl enable ton`

### Logrotate

Good idea is to setup logrotate

Put [file](ton) in `/etc/logrotate.d/`


### Rsyslog

Change `/etc/rsyslog.conf`

```
#module(load="imjournal"            # provides access to the systemd journal
#       StateFile="imjournal.state") # File to store the position in the journal
module(load="imjournal" RatelimitInterval="1" RatelimitBurst="800000" StateFile="imjournal.state")
````

Put [file](ton.conf) in `/etc/rsyslog.d/`

Restart rsyslog `systemctl restart rsyslog`
