# MongoDB 5 for Raspberry Pi Zero 2

Cross build MongoDB v5.3 for Raspberry Pi OS (64-bit, Bullseye) / 64-bit Arm Cortex-A53 CPU (like Broadcom BCM2710A1). Build will be performed on **Debian 11 (Bullseye) x64**.


## Download source

Clone [Mongo Git repository](https://github.com/mongodb/mongo.git) and checkout branch v5.3.

## Install libraries

    apt install gcc gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu g++-aarch64-linux-gnu make
    apt install python3-pip
	dpkg --add-architecture arm64
	apt update
	apt install libssl-dev:arm64 libcurl4-openssl-dev:arm64
	apt install crossbuild-essential-arm64 dpkg-cross build-essential
	apt install python-dev-is-python3 libssl-dev liblzma-dev
	
> Some could possibly be omitted


## Install compile requirements

	python3 -m pip install -r etc/pip/compile-requirements.txt

## Compile

Run an optimized `install-core` (includes `mongod` and `mongos`) build for the CPU of Rpi zero 2.

	python3 buildscripts/scons.py --disable-warnings-as-errors CC=/usr/bin/aarch64-linux-gnu-gcc-10 CXX=/usr/bin/aarch64-linux-gnu-g++-10 CCFLAGS="-march=armv8-a+crc -mtune=cortex-a53" install-core
> Results in ~31GB of files of which ~8GB are the binaries

> **Do not run with args** `--install-mode=hygienic --separate-debug install-core{,-debug}` since it will fail, because used `objcopy` defaults to `/usr/bin/objcopy`. 
> 
## Strip debug symbols
Having an arm64 architecture, stripping debug will be done using `aarch64-linux-gnu-objcopy`. After build is **done**, you can strip the binaries from their debug symbols using:

    aarch64-linux-gnu-objcopy --strip-debug mongo
	aarch64-linux-gnu-objcopy --strip-debug mongod
	aarch64-linux-gnu-objcopy --strip-debug mongos

Another possible way could be to change the objcopy location in the $PATH enviroment variable and try with the above args (`install-mode` etc).
	
> Stripped binaries (`mongo`, `mongod`, `mongos`) are located in `build/install/bin` and their total size is ~250MB.

## Useful links

### Mongo official
[MongoDB building docs](https://github.com/mongodb/mongo/blob/master/docs/building.md)

[MongoDB install binaries](https://www.mongodb.com/docs/guides/server/install/)

### Mongo forums
[Add MongoDB 4.2 ARM64 builds for Raspberry Pi OS 64 bit (Debian Buster)](https://www.mongodb.com/community/forums/t/add-mongodb-4-2-arm64-builds-for-raspberry-pi-os-64-bit-debian-buster/5046/5)

[Huge binary size of core binaries (mongod, mongo)](https://www.mongodb.com/community/forums/t/huge-binary-size-of-core-binaries-mongod-mongo/149507)

