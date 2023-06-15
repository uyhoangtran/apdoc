# S00sysfixtime
- set system time to the most recent time a file in /etc is modified.
# S10boot
- mount_root if previous mount not completed
- vconfig set_name_type
- create some directory and files
- *kmodloader* /etc/modules.d/
- duyhh2 mount /data
- run button daemon => TO-DO: button daemon is handling a lot of things in a signal handler
- setMACWLan
- detect wireless config
- board_detect
	- apply mac address for network config
- uci_apply_defaults
	- apply some reset factory config
- reload_config
- create /dev/root
- getCfgRst: apply config when switch firmware => TO-DO: should bring up right after /mount/data
# S10fwdd
- run fwdd
# S10system
- set timezone, hostname, log level, buffersize
# S11sysctl
run sysctl setting in /etc/sysctl.conf
# S12log
- start logd as service
- start logread as service
# S12rpcd
run ubus RPC daemon for backend server.

[[OpenWrt Wiki] rpcd: OpenWrt ubus RPC daemon for backend server](https://openwrt.org/docs/techref/rpcd)
# S19firewall
start firewall
# S20network
start netifd as a service
Also start wifi and init_switch after network.interface is up.
# S35odhcpd
start odhcpd as a service
# S40fstab
/sbin/block mount
# S50cron
start crond, Do notthing because /etc/crontabs is empty
# S50dropbear
start dropbear as a service and nothing special
# S50lighttpd
start lighttpd as a service and nothing special
# S50telnet
start telnet as a service and nothing special
# S50uhttpd
start uhttpd as a service and nothing special
# S60dnsmasq
do nothing
# S90lan_wan_nat
add nat rules in case fiirewall is disabled. TO-DO: should we remove it or add condition check?
# S91hwnat
insmod hw_nat.ko
# S95done
- mount_root done
- block mount
- rm -f /sysupgrade.tgz
- do rc.local
# S96led
not used
# S98sysntpd
run ntpd
# S99obuspa
start obuspa as a service
# S99obuspacheckhang
start checkhang daemon for obuspa. TO-DO: start along with obuspa service