# Centos/RH system settings

It's important to optimize some settings to ensure stability of the validator node

### SYSCTL

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
