# 1. kernel time
*linux-kernel/init/main.c*

- start_kernel
	- reset_init
		- kernel_thread(kernel_init,...)
			- run_init_process(/sbin/init)
# 2. /sbin/init
*procd/initd/init.c*
## main
- early
	- **early_mounts**
		- mount: proc, sys, cgroup, dev, dev/pts
		- early_dev: mknod(/dev/null)
		- early_console
		- mount tmp
	- early_env: set PATH "/usr/sbin:/usr/bin/sbin:/bin"
- cmdline: get debug lvl
- watchdog_init(1)
- fork **run kmodloader** /etc/modules-boot.d/
- wait upto 120s for kmodloader to complete
- uloop_init
- preinit
	- fork run **/sbin/procd -h /etc/hotplug-preinit.json**
	- setenv PREINIT 1
	- fork run **/bin/sh [/etc/preinit](#/etc/preinit)**
- uloop_run
	- wait for child processes to change state (to complete)
	- run callback
		- /sbin/procd -h /etc/hotplug-preinit.json complete => almost do nothing
		- /bin/sh /etc/preinit complete => spawn_procd()

## /etc/preinit
### code flow
- boot_hook_init:
	- preinit_essential
	- preinit_main
	- failsafe
	- initramfs
	- preinit_mount_root
- boot_hook_add preinit_main scripts in /lib/preinit
- boot_hook_run preinit_essential
- boot_hook_run preinit_main

### hooks
#### preinit_essential
no function in this hook
#### preinit_main
- add
	- define_default_set_state
	- do_ramips
	- do_checksummuing_disable
	- ramips_set_preinit_iface
	- preinit_ip
	- pi_inidicate_preinit
	- do_sysinfo_generic
	- failsafe_wait
	- run_failsafe_hook
	- indicate_regular_preinit
	- initramfs_test
	- do_mount_root
	- run_init
#### failsafe
- add
	- 10_indicate_failsafe indicate_failsafe
	- 99_10_failsafe_logic failsafe_netlogin
	- 99_10_failsafe_logic failsafe_shell
- run
	- run_failsafe_hook
#### initramfs
no function in this hook
#### preinit_mount_root
no function in this hook

## spawn_procd
- kill procd plugd
- wait if system is upgrading
- set dbglvl, wdt_fd
- execvp /sbin/procd

# 3. procd
## main
- setsid
- uloop_init
- procd_signal
	- any signal send to procd may cause system reboot
- trigger_init
	- runqueue_init
- procd_state_next
	- state++ = STATE_EARLY
	- state_enter(STATE_EARLY)
- uloop_run
- uloop_done

## States
### STATE_EARLY
- watchdog_init(0)
- hotplug("/etc/hotplug.json")
- procd_coldplug()
#### procd_coldplug
- umount2(/dev) => mount(dev)
- fork udevtrigger
- wait udevtrigger to complete => STATE_UBUS
#### udevtrigger
- scan_subdir
	- device_list_insert
### STATE_UBUS
- watchdog_init(0)
- set_stdio("console")
- procd_connect_ubus(): wait 1s and call ubus_connect_cb
	- ubus_connect()
	- ubus_init_service()
	- ubus_init_system()
	- watch_ubus()
	- ubus_add_uloop()
	- procd_state_ubus_connect()
- service_init(): init avl
- service_start_early("ubus", ubus_cmd): start ubusd
### STATE_INIT

- procd_inittab
	- init init_action: 
::sysinit:/etc/init.d/rcS S boot
::shutdown:/etc/init.d/rcS K shutdown
::askconsole:/bin/ash --login
- procd_inittab_run("respawn")
- procd_inittab_run("askconsole")
- procd_inittab_run("askfirst")
- procd_inittab_run("sysinit")
	- runrc, arg = rcS S boot
		- runqueue_init()
		- \_rc(&q, "/etc/rc.d", "S", "\*", "boot")
			- add boot functions in rc.d to runqueue