# define_default_set_state
call /etc/diag.sh
define function and do nothing

# do_ramips
- ramips_board_detect
	- create /tmp/sysinfo/{board_name;model}

# do_checksummuing_disable
not jump to any condition

# ramips_set_preinit_iface
$ifname=eth0

# preinit_ip
ifconfig eth0 192.168.1.1 netmask 255.255.255.0 broadcast 192.168.1.255 up

# pi_inidicate_preinit
not jump to any condition

# do_sysinfo_generic
not jump to any condition

# failsafe_wait
"press f to enter failsafe mode"

# run_failsafe_hook
run failsafe hook

# indicate_regular_preinit
do_nothing

# initramfs_test
not jump to any condition

# do_mount_root
- mount_root
- check if /sysupgrade.tgz
=> tar xzf /sysupgrade.tgz

# run_init
preinit_ip_deconfig
