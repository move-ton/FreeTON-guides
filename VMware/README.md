# VMware optimization 6.7+

### NVMe perfomance trick

You need to run ssh to perform this optimization to acheive maximum perfomance of NVMe storage:

`esxcli storage core claimrule add -r 10 -t vendor -V=NVMe -M=* -P HPP --force-reserved`

Reboot host.
