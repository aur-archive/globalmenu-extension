# Contributor: Tom Kuther <gimpel@sonnenkinder.org>

pkgname=globalmenu-extension
pkgver=20130216
pkgrel=2
pkgdesc="Global dbusmenu/appmenu extension for Firefox/Thunderbird"
url="https://launchpad.net/globalmenu-extension"
arch=('i686' 'x86_64')
license=('LGPL')
depends=('libdbusmenu' 'libdbusmenu-gtk2')
makedepends=('bzr' 'autoconf2.13' 'python2' 'yasm' 'zip' 'unzip' 'xulrunner' 'libidl2')
optdepends=('firefox' 'thunderbird')
options=()
conflicts=('globalmenu-extension-firefox' 'globalmenu-extension-thunderbird')
replaces=('globalmenu-extension-firefox' 'globalmenu-extension-thunderbird')
install=
source=('disable_webgl_deps.patch')
md5sums=('baaef8791e47640a347a1188f8081755')

# _bzrroot=lp:globalmenu-extension/3.5
_bzrroot=lp:~extension-hackers/globalmenu-extension/3.6
# _bzrroot=lp:globalmenu-extension
_bzrname=globalmenu-extension

build() {
	cd "$srcdir"
	msg "Connecting to BZR server...."

	if [ -d $_bzrname ] ; then
		cd $_bzrname && bzr update
		msg "The local files are updated."
	else
		bzr branch $_bzrroot $_bzrname
	fi
	msg "BZR checkout done"

	msg "Starting make..."
	rm -rf "$srcdir/${_bzrname}-build"
	cp -r "$srcdir/${_bzrname}" "$srcdir/${_bzrname}-build"
	cd "$srcdir/${_bzrname}-build"

	patch -p0 < "${srcdir}/disable_webgl_deps.patch"

	autoconf-2.13
	sed -i 's/^ \t/\t/g' config/rules.mk
	sed -i '/^XPIDL_COMPILE[[:space:]]*=/s@\$(LIBXUL_DIST)/@&sdk/@' config/config.mk

	./configure --with-libxul-sdk=`pkg-config --variable=sdkdir libxul` \
		--with-system-libxul \
		--with-system-nspr \
		--with-system-nss \
		--enable-application=extensions \
		--enable-extensions=globalmenu \
		--disable-tests \
		--disable-necko-wifi \
		--disable-webm \
		--disable-crashreporter \
		--disable-ogg \
		--disable-alsa
	make || return 1
}

package() {
	cd "$srcdir/${_bzrname}-build"
	emid=$(sed -n '/.*<em:id>\(.*\)<\/em:id>.*/{s//\1/p;q}' dist/xpi-stage/globalmenu/install.rdf)
	install -d ${pkgdir}/usr/lib/firefox/extensions/${emid}
	#install -d ${pkgdir}/usr/lib/thunderbird/extensions
	unzip -d ${pkgdir}/usr/lib/firefox/extensions/${emid} dist/xpi-stage/globalmenu.xpi
	#cd ${pkgdir}/usr/lib/thunderbird/extensions && ln -s ../../firefox/extensions/${emid} .
}

# vim:syntax=sh
