artic_default:
	@echo 'To build kernel for artik710: `make artik710`' >&2
	@echo 'Otherwise, `make -f Makefile`' >&2
artik710 atk710:
	sudo apt-get install gcc-aarch64-linux-gnu
	sudo apt-get install android-tools-fsutils || \
	 sudo apt-get install android-sdk-libsparse-utils
	$(MAKE) ARCH=arm64 artik710_raptor_defconfig
	$(MAKE) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image -j4
	$(MAKE) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- dtbs
	mkdir usr/modules
	$(MAKE) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- modules -j4
	$(MAKE) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- \
	 modules_install INSTALL_MOD_PATH=usr/modules INSTALL_MOD_STRIP=1
	make_ext4fs -b 4096 -L modules \
	 -l 32M usr/modules.img \
	 usr/modules/lib/modules/
	rm -rf usr/modules
%:
	$(MAKE) -f Makefile $@
