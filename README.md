# framebuffer-vncserver

VNC server for Linux framebuffer devices.

[![Build Status](https://travis-ci.org/ponty/framebuffer-vncserver.svg?branch=master)](https://travis-ci.org/ponty/framebuffer-vncserver)

The goal is to access remote embedded Linux systems without X.
Implemented features: remote display, touchscreen, keyboard, rotation
Not implemented: file transfer, ..

Working configurations:
without rotation:
- [ ]  1 bit/pixel
- [x]  8 bit/pixel
- [x]  16 bit/pixel
- [x]  24 bit/pixel
- [x]  32 bit/pixel

with rotation:
- [ ]  1 bit/pixel
- [ ]  8 bit/pixel
- [x]  16 bit/pixel
- [ ]  24 bit/pixel
- [ ]  32 bit/pixel

The code is based on a LibVNC example for Android:
https://github.com/LibVNC/libvncserver/blob/master/examples/androidvncserver.c

### build

Dependency:

	sudo apt-get install libvncserver-dev

There are 2 options: CMake or qmake

Using cmake:

	mkdir -p build && cd build
	cmake ..
	make
	
Using qmake:

	mkdir -p build && cd build
	qmake ../framebuffer-vncserver.pro
	make

 

### command-line help 

	# ./framebuffer-vncserver -h
		framebuffer-vncserver [-f device] [-p port] [-t touchscreen] [-k keyboard] [-r rotation] [-h]
		-p port: VNC port, default is 5900
		-f device: framebuffer device node, default is /dev/fb0
		-k device: keyboard device node (example: /dev/input/event0)
		-t device: touchscreen device node (example:/dev/input/event2)
		-r degrees: framebuffer rotation, default is 0
		-h: print this help

## Run on startup as service

To run at startup as a service using systemd, edit the file `fbvnc.service` make sure the path and command line arguments are correct and then run:

```shell
sudo cp fbvnc.service /etc/systemd/system/
sudo systemctl enable fbvnc.service
sudo systemctl start fbvcn.service
```

## Vfb test

Linux Virtual Frame Buffer kernel object (vfb.ko) is used for this test.
https://cateee.net/lkddb/web-lkddb/FB_VIRTUAL.html

Local computer:
	
	vagrant up
	vncviewer localhost &
	vagrant ssh

Inside vagrant box:

	sudo su

	# build framebuffer-vncserver
	cd /home/vagrant/buildc/;make

	# set resolution, color depth
    fbset -g 640 480 640 480 16

	# restart framebuffer-vncserver
	killall framebuffer-vncserver;/home/vagrant/buildc/framebuffer-vncserver -t /dev/input/ms -k /dev/input/kbd &

	# set test pattern or ..
	fb-test

	# draw random rectangles or ..
	rect
	
	# display a GUI or ...
	qmlscene -platform linuxfb -plugin evdevmouse:/dev/input/ms:abs -plugin evdevkeyboard:/dev/input/kbd:grab=1

Automatic test, generates patterns with different resolutions and color depth:
	
	pip3 install fabric vncdotool python-vagrant entrypoint2
	python3 vfb.py
