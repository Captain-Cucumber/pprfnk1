
Debian
====================
This directory contains files used to package pprfnkd/pprfnk-qt
for Debian-based Linux systems. If you compile pprfnkd/pprfnk-qt yourself, there are some useful files here.

## pprfnk: URI support ##


pprfnk-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install pprfnk-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your pprfnk-qt binary to `/usr/bin`
and the `../../share/pixmaps/pprfnk128.png` to `/usr/share/pixmaps`

pprfnk-qt.protocol (KDE)

