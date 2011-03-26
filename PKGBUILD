# Maintainer: Stéphane Gaudreault <stephane@archlinux.org>

pkgname=krb5
pkgver=1.9
pkgrel=1
pkgdesc="The Kerberos network authentication system"
arch=('i686' 'x86_64')
url="http://web.mit.edu/kerberos/"
license=('custom')
depends=('e2fsprogs' 'openssl' 'libldap')
makedepends=('perl')
provides=('heimdal')
replaces=('heimdal')
conflicts=('heimdal')
source=(http://web.mit.edu/kerberos/dist/${pkgname}/${pkgver}/${pkgname}-${pkgver}-signed.tar)
sha1sums=('a7ad1b4ed37bff4b9087f6c4561b2b222208d779')

build() {
   tar zxvf ${pkgname}-${pkgver}.tar.gz
	cd "${srcdir}/${pkgname}-${pkgver}/src"

   export CFLAGS+=" -fPIC -fno-strict-aliasing -fstack-protector-all"
	export CPPFLAGS+=" -I/usr/include/et"
	./configure --prefix=/usr \
               --sysconfdir=/etc/krb5 \
               --mandir=/usr/share/man \
               --localstatedir=/var/lib \
               --enable-shared \
               --with-system-et \
               --with-system-ss \
               --disable-rpath \
               --without-tcl \
               --enable-dns-for-realm \
               --with-ldap

   make
}

package() {
	cd "${srcdir}/${pkgname}-${pkgver}/src"
   make DESTDIR="${pkgdir}" EXAMPLEDIR="${pkgdir}/usr/share/doc/${pkgname}/examples" install

	install -Dm644 "${srcdir}"/${pkgname}-${pkgver}/NOTICE "${pkgdir}"/usr/share/licenses/${pkgname}/LICENSE
}
