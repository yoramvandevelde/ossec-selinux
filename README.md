# ossec-selinux

This policy is written to contain the wide and varried access of [OSSEC](https://ossec.github.io).

This is a fork from the original policy written by Psi-Jack at [Linux-Help.org - Git](https://git.linux-help.org/Linux-Help/selinux-ossec/src/bugfix/style-organization/ossec.te). Many thanks to his hours of work on the original.

I have altered it with improvements and also added booleans to help limit unintentional access as described below. It should now be the correct style according to the [Tresys Style Guide](https://github.com/TresysTechnology/refpolicy/wiki/StyleGuide).

As listed below, what works and what isn't tested.


### Booleans needed for specific functions:
```
ossec_ar_can_edit_iptables --> off
ossec_ar_can_exec_system_bin --> off
ossec_remoted_can_network_connect --> off
ossec_can_read_all_system_logs --> off
```

### Suggestions:
* If you don't enable the read_all_system_logs boolean, you will need to add the logs you want OSSEC to read. Otherwise if you're fine with the access, enable: `setsebool -P ossec_can_read_all_system_lods on`


### Working:
* All basic functionality
* Using Active-Response to block IP addresses for iptables and firewalld in `/var/ossec/active-response/bin/firewall{d}-drop.sh`


### Not Tested/Broken:
* All of the following Active-Reponse scripts are untested: (`disable-account.sh  host-deny.sh  ip-customblock.sh  ossec-slack.sh  ossec-tweeter.sh  restart-ossec.sh  route-null.sh`)
* Using remote agents is untested and partially implemented with remoted_t. You will need to enable it's boolean. remoted uses ports 1514 and 514. ossec_remoted_port_t has been defined.
* csyslogd is untested
* dbd is untested


## Installation
```sh
# Clone the repo
git clone https://github.com/georou/ossec-selinux.git

# Compile the selinux module (see below)

# Install the SELinux policy module. Compile it before hand to ensure proper compatibility (see below)
semodule -i ossec.pp

# Restore all the correct context labels
restorecon -Rv /var/ossec
restorecon -v /etc/init.d/ossec-hids

# Start ossec-hids
systemctl enable ossec-hids.service
systemctl start ossec-hids.service

# Ensure it's working
ps -eZ | grep ossec
```


## Compatibility Notes
Built on CentOS 7.4 at the time with:
```
selinux-policy-devel-3.13.1-166.el7_4.5.noarch
policycoreutils-devel-2.5-17.1.el7.x86_64
policycoreutils-2.5-17.1.el7.x86_64
selinux-policy-targeted-3.13.1-166.el7_4.5.noarch
policycoreutils-python-2.5-17.1.el7.x86_64
selinux-policy-3.13.1-166.el7_4.5.noarch
```