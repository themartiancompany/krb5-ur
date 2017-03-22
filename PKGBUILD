# Maintainer: Florian Pritz <flo@xinu.at>
# Contributor: Stéphane Gaudreault <stephane@archlinux.org>

_pkgbasename=krb5
pkgname=lib32-$_pkgbasename
pkgver=1.15
pkgrel=1
pkgdesc="The Kerberos network authentication system (32-bit)"
arch=('x86_64')
url="https://web.mit.edu/kerberos/"
license=('custom')
depends=('lib32-e2fsprogs' 'lib32-libldap' 'lib32-keyutils' "$_pkgbasename")
makedepends=('perl' 'gcc-multilib')
source=(https://web.mit.edu/kerberos/dist/krb5/${pkgver}/${_pkgbasename}-${pkgver}.tar.gz{,.asc}
        krb5-config_LDFLAGS.patch)
sha1sums=('07fb763082c21e9e597b39aac6413302e3aa7c97'
          'SKIP'
          'f125824ed37f31e6fd2fdb6a437be8ff1c3700ab')
options=('!emptydirs')
validpgpkeys=('2C732B1C0DBEF678AB3AF606A32F17FD0055C305') # Tom Yu <tlyu@mit.edu>

prepare() {
   tar zxvf ${_pkgbasename}-${pkgver}.tar.gz
   cd "${srcdir}/${_pkgbasename}-${pkgver}"

   # cf https://bugs.gentoo.org/show_bug.cgi?id=448778
   patch -p1 -i "${srcdir}"/krb5-config_LDFLAGS.patch
}

build() {
   cd "${srcdir}/${_pkgbasename}-${pkgver}/src"

   export CC="gcc -m32"
   export CXX="g++ -m32"
   export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"

   export CFLAGS+=" -fPIC -fno-strict-aliasing -fstack-protector-all"
   export CPPFLAGS+=" -I/usr/include/et"
   ./configure --prefix=/usr \
               --sysconfdir=/etc \
               --localstatedir=/var/lib \
               --libdir=/usr/lib32 \
               --enable-shared \
               --with-system-et \
               --with-system-ss \
               --disable-rpath \
               --without-tcl \
               --enable-dns-for-realm \
               --with-ldap \
               --without-system-verto

   make
}

#check() {
   # We can't do this in the build directory.

   # only works if the hostname is set properly/resolves to something. whatever...
   #cd "${srcdir}/${_pkgbasename}-${pkgver}"
   #make -C src check
#}

package() {
   cd "${srcdir}/${_pkgbasename}-${pkgver}/src"
   make DESTDIR="${pkgdir}" install

   rm -rf "${pkgdir}"/usr/{include,share,bin,sbin}
   mkdir -p "$pkgdir/usr/share/licenses"
   ln -s $_pkgbasename "$pkgdir/usr/share/licenses/$pkgname"
}
