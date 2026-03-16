# PKGBUILD acl-2.3.2

# Maintainer: Adalberto Pérez Pausa <appausa73@gmail.com>

pkgname=acl
pkgver=2.3.2
pkgrel=1
pkgdesc='Utilities for administering Access Control List'
arch=('x86_64')
url="https://savannah.nongnu.org/projects/acl"
license=('LGPL-2.1-or-later AND GPL-2.0-or-later')
depends=()
makedepends=()
source=("https://download.savannah.gnu.org/releases/$pkgname/$pkgname-$pkgver.tar.xz")
md5sums=('590765dee95907dbc3c856f7255bd669')

build(){
	cd "$srcdir/$pkgname-$pkgver"
	sed -i -e 's|/@pkg_name@|&-@pkg_version@|' include/builddefs.in	
	sed -i "s:| sed.*::g" test/{sbits-restore,cp,misc}.test
	sed -i -e "/TABS-1;/a if (x > (TABS-1)) x = (TABS-1);" \
        libacl/__acl_to_any_text.c
	./configure --prefix=/usr    \
                    --disable-static \
                    --libexecdir=/usr/lib
	make

}

package(){
	cd "$srcdir/$pkgname-$pkgver"
	make DESTDIR=$pkgdir install install-dev install-lib
	chmod -v 755 $pkgdir/usr/lib/libacl.so
	install -d $pkgdir/lib
	mv -v $pkgdir/usr/lib/libacl.so.* $pkgdir/lib
	ln -sfv ../../lib/$(readlink /usr/lib/libacl.so) $pkgdir/usr/lib/libacl.so
}
