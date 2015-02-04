# vim:set ft=sh:
# Maintainer: BlackIkeEagle < ike DOT devolder AT gmail DOT com >
# Contributor: DonVla <donvla@users.sourceforge.net>
# Contributor: Ulf Winkelvos <ulf [at] winkelvos [dot] de>
# Contributor: Ralf Barth <archlinux dot org at haggy dot org>
# Contributor: B & monty - Thanks for your hints :)
# Contributor: marzoul
# Contributor: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Contributor: Brad Fanella <bradfanella@archlinux.us>
# Contributor: [vEX] <niechift.dot.vex.at.gmail.dot.com>
# Contributor: Zeqadious <zeqadious.at.gmail.dot.com>
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Maxime Gauduin <alucryd@gmail.com>
#
# Original credits go to Edgar Hucek <gimli at dark-green dot com>
# for his xbmc-vdpau-vdr PKGBUILD at https://archvdr.svn.sourceforge.net/svnroot/archvdr/trunk/archvdr/xbmc-vdpau-vdr/PKGBUILD

pkgbase=kodi-git
pkgname=('kodi-git' 'kodi-eventclients-git')
_gitname='xbmc'
pkgver=20150203.1fd79f4
pkgrel=1
arch=('i686' 'x86_64')
url="http://kodi.tv"
license=('GPL2')
makedepends=(
  'afpfs-ng' 'bluez-libs' 'boost' 'cmake' 'curl' 'cwiid' 'doxygen' 'git' 'glew'
  'gperf' 'hicolor-icon-theme' 'jasper' 'java-runtime' 'libaacs' 'libass'
  'libbluray' 'libcdio' 'libcec' 'libgl' 'libmariadbclient' 'libmicrohttpd'
  'libmodplug' 'libmpeg2' 'libnfs' 'libplist' 'libpulse' 'libssh' 'libva'
  'libvdpau' 'libxrandr' 'libxslt' 'lzo' 'nasm' 'nss-mdns' 'python2-pillow'
  'python2-pybluez' 'python2-simplejson' 'rtmpdump' 'sdl2' 'sdl_image'
  'shairplay' 'smbclient' 'swig' 'taglib' 'tinyxml' 'unzip' 'upower' 'yajl' 'zip'
)
source=(
	"$_gitname::git://github.com/xbmc/xbmc.git"
)
sha256sums=(
	'SKIP'
)

_prefix='/usr'

pkgver() {
	cd "$srcdir/$_gitname"
	git log -1 --date=short --format="%cd.%h" | tr -d '-'
}

prepare() {
	cd ${_gitname}

	find -type f -name *.py -exec sed 's|^#!.*python$|#!/usr/bin/python2|' -i "{}" +
	sed 's|^#!.*python$|#!/usr/bin/python2|' -i tools/depends/native/rpl-native/rpl
	sed 's/python/python2/' -i tools/Linux/kodi.sh.in
}

build() {
	cd ${_gitname}

	# Bootstrapping
	MAKEFLAGS=-j1 ./bootstrap

	# Configuring XBMC
	export PYTHON_VERSION=2  # external python v2
	./configure --prefix=$_prefix --exec-prefix=$_prefix \
		--enable-debug \
		--disable-optimizations \
		--enable-libbluray \
		--enable-external-libraries \
		--with-lirc-device=/run/lirc/lircd

	# Now (finally) build
	make
}

package_kodi-git() {
	pkgdesc="A software media player and entertainment hub for digital media"

	# depends expected for kodi plugins:
	# 'python2-pillow' 'python2-pybluez' 'python2-simplejson'
	# depends expeced in FEH.py
	# 'mesa-demos' 'xorg-xdpyinfo'
	depends=(
		'python2-pillow' 'python2-pybluez' 'python2-simplejson'
		'mesa-demos' 'xorg-xdpyinfo'
		'bluez-libs' 'fribidi' 'glew' 'hicolor-icon-theme' 'libcdio'
		'libjpeg-turbo' 'libmariadbclient' 'libmicrohttpd' 'libpulse' 'libssh'
		'libva' 'libxrandr' 'libxslt' 'lzo' 'sdl2' 'smbclient' 'taglib' 'tinyxml'
		'yajl'
	)
	optdepends=(
		'gdb: for meaningful backtraces in case of trouble - STRONGLY RECOMMENDED'
		'afpfs-ng: Apple shares support'
		'bluez: Blutooth support'
		'libnfs: NFS shares support'
		'libplist: AirPlay support'
		'libcec: Pulse-Eight USB-CEC adapter support'
		'lirc: Remote controller support'
		'pulseaudio: PulseAudio support'
		'shairplay: AirPlay support'
		'udisks: Automount external drives'
		'unrar: Archives support'
		'unzip: Archives support'
		'upower: Display battery level'
	)
	install="kodi-git.install"
	provides=('xbmc' 'kodi')
	conflicts=('xbmc' 'kodi')
	replaces=('xbmc-svn' 'xbmc-git')

	cd ${_gitname}
	# Running make install
	make DESTDIR="$pkgdir" install

	# Licenses
	install -dm755 ${pkgdir}${_prefix}/share/licenses/${pkgname}
	for licensef in LICENSE.GPL copying.txt; do
		mv ${pkgdir}${_prefix}/share/doc/kodi/${licensef} \
			${pkgdir}${_prefix}/share/licenses/${pkgname}
	done
}

package_kodi-eventclients-git() {
	pkgdesc="Kodi Event Clients (master branch)"
	conflicts=('kodi-eventclients')

	depends=('cwiid')

	cd ${_gitname}

	make DESTDIR="$pkgdir" eventclients WII_EXTRA_OPTS=-DCWIID_OLD

	install -dm755 "$pkgdir/usr/lib/python2.7/kodi"
	mv "$pkgdir/kodi"/* "$pkgdir/usr/lib/python2.7/kodi"
	rmdir "$pkgdir/kodi"
}
