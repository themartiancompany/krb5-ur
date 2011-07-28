# Maintainer: Stéphane Gaudreault <stephane@archlinux.org>

pkgname=krb5
pkgver=1.9.1
pkgrel=3
pkgdesc="The Kerberos network authentication system"
arch=('i686' 'x86_64')
url="http://web.mit.edu/kerberos/"
license=('custom')
depends=('e2fsprogs' 'libldap' 'keyutils')
makedepends=('perl')
provides=('heimdal')
replaces=('heimdal')
conflicts=('heimdal')
backup=('etc/krb5.conf' 'var/lib/krb5kdc/kdc.conf')
source=(http://web.mit.edu/kerberos/dist/${pkgname}/1.9/${pkgname}-${pkgver}-signed.tar
        krb5-kadmind
        krb5-kdc
        krb5-1.9.1-config-script.patch)
sha1sums=('e23a1795a237521493da9cf3443ac8b98a90c066'
          '2aa229369079ed1bbb201a1ef72c47bf143f4dbe'
          '77d2312ecd8bf12a6e72cc8fd871a8ac93b23393'
          '8d1ec8bdb39fec230caace112d1a41ad792f7d97')
options=('!emptydirs')

build() {
   tar zxvf ${pkgname}-${pkgver}.tar.gz
   cd "${srcdir}/${pkgname}-${pkgver}/src"

   # - Make krb5-config suppress CFLAGS output when called with --libs
   #   cf https://bugzilla.redhat.com/show_bug.cgi?id=544391
   #      http://pkgs.fedoraproject.org/gitweb/?p=krb5.git;a=blob;f=krb5-1.7-buildconf.patch
   #
   # - Omit extra libraries because their interfaces are not exposed to applications
   #   by libkrb5, unless do_deps is set to 1, which indicates that the caller
   #   wants the whole list.
   #   cf http://pkgs.fedoraproject.org/gitweb/?p=krb5.git;a=blob;f=krb5-1.7-nodeplibs.patch
   patch -Np2 -i ${srcdir}/krb5-1.9.1-config-script.patch

   export CFLAGS+=" -fPIC -fno-strict-aliasing -fstack-protector-all"
   export CPPFLAGS+=" -I/usr/include/et"
   ./configure --prefix=/usr \
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
   make DESTDIR="${pkgdir}" EXAMPLEDIR="/usr/share/doc/${pkgname}/examples" install

   # Sample KDC config file
   install -dm 755 "${pkgdir}"/var/lib/krb5kdc
   install -pm 644 config-files/kdc.conf "${pkgdir}"/var/lib/krb5kdc/kdc.conf

   # Default configuration file
   install -dm 755 "${pkgdir}"/etc
   install -pm 644 config-files/krb5.conf "${pkgdir}"/etc/krb5.conf

   install -dm 755 "${pkgdir}"/etc/rc.d
   install -m 755 ../../krb5-kdc "${pkgdir}"/etc/rc.d
   install -m 755 ../../krb5-kadmind  "${pkgdir}"/etc/rc.d

   install -Dm644 "${srcdir}"/${pkgname}-${pkgver}/NOTICE "${pkgdir}"/usr/share/licenses/${pkgname}/LICENSE
}
