artic_default:
	@echo 'To build kernel for artik710: `make atk710`' >&2
	@echo 'Otherwise, `make -f Makefile`' >&2
atk710: atk710_kernel atk710_modules
	@echo 'To install, first add an entry in /etc/hosts for atk710' >&2
	@echo 'For example: 192.168.11.77 atk710' >&2
	@echo 'Then, with the dev board running: `make atk710_install`' >&2
atk710_kernel:
	sudo apt-get install gcc-aarch64-linux-gnu
	sudo apt-get install android-tools-fsutils || \
	 sudo apt-get install android-sdk-libsparse-utils
	$(MAKE) ARCH=arm64 artik710_raptor_defconfig
	$(MAKE) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image -j4
	$(MAKE) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- dtbs
atk710_modules:
	mkdir -p usr/modules
	$(MAKE) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- modules -j4
	$(MAKE) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- \
	 modules_install INSTALL_MOD_PATH=usr/modules INSTALL_MOD_STRIP=1
	#make_ext4fs -b 4096 -L modules \
	# -l 32M usr/modules.img \
	# usr/modules/lib/modules/
	#
	# make_ext4fs is from an old package called, on Debian,
	# android_tools_fsutils. It takes the following arguments:
	# make_ext4fs [ -l <len> ] [ -j <journal size> ] [ -b <block_size> ]
	#  [ -g <blocks per group> ] [ -i <inodes> ] [ -I <inode size> ]
	#  [ -L <label> ] [ -f ] [ -a <android mountpoint> ]
	#  [ -S file_contexts ]
	#  [ -z | -s ] [ -w ] [ -c ] [ -J ] [ -v ]
	#  <filename> [<directory>]
	#
	# Instead we use mkfs.ext4 from e2fsprogs, modifying the args
	# accordingly; specifically, since we are specifying 4096 as block
	# size, we need 8192 total blocks to make 32M.
	mkfs.ext4 -b 4096 -L modules -d usr/modules/lib/modules \
	 usr/modules.img 8192
	rm -rf usr/modules
atk710_install:
	scp arch/arm64/boot/Image root@atk710:/root
	scp arch/arm64/boot/dts/nexell/*.dtb root@atk710:/root
	scp usr/modules.img root@atk710:/root
	ssh root@atk710 mount -o remount,rw /boot
	ssh root@atk710 cp /root/Image /boot
	ssh root@atk710 cp /root/*.dtb /boot
	ssh root@atk710 dd if=/root/modules.img of=/dev/mmcblk0p2
	ssh root@atk710 sync
	ssh root@atk710 reboot
%:
	$(MAKE) -f Makefile $@
