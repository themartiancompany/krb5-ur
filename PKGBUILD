# Maintainer: Stéphane Gaudreault <stephane@archlinux.org>

pkgname=krb5
pkgver=1.11.2
pkgrel=1
pkgdesc="The Kerberos network authentication system"
arch=('i686' 'x86_64')
url="http://web.mit.edu/kerberos/"
license=('custom')
depends=('e2fsprogs' 'libldap' 'keyutils')
makedepends=('perl')
backup=('etc/krb5.conf' 'var/lib/krb5kdc/kdc.conf')
source=(http://web.mit.edu/kerberos/dist/${pkgname}/1.11/${pkgname}-${pkgver}-signed.tar
        krb5-config_LDFLAGS.patch
        krb5-kadmind
        krb5-kadmind.service
        krb5-kdc
        krb5-kdc.service
        krb5-kpropd
        krb5-kpropd.service
        krb5-kpropd@.service
        krb5-kpropd.socket)
sha1sums=('3863f7bdb2d8fc3e50484fb566124373c4b0a250'
          '09e478cddfb9d46d2981dd25ef96b8c3fd91e1aa'
          '2aa229369079ed1bbb201a1ef72c47bf143f4dbe'
          'a2a01e7077d9e89cda3457ea0e216debb3dc353c'
          '77d2312ecd8bf12a6e72cc8fd871a8ac93b23393'
          'f5e4fa073e11b0fcb4e3098a5d58a4f791ec841e'
          '7f402078fa65bb9ff1beb6cbbbb017450df78560'
          '614401dd4ac18e310153240bb26eb32ff1e8cf5b'
          '023a8164f8ee7066ac814486a68bc605e79f6101'
          'f3677d30dbbd7106c581379c2c6ebb1bf7738912')
options=('!emptydirs')

build() {
   tar zxvf ${pkgname}-${pkgver}.tar.gz
   cd "${srcdir}/${pkgname}-${pkgver}/src"

   # cf https://bugs.gentoo.org/show_bug.cgi?id=448778
   patch -Np2 -i "${srcdir}"/krb5-config_LDFLAGS.patch

   rm lib/krb5/krb/deltat.c

   # FS#25384
   sed -i "/KRB5ROOT=/s/\/local//" util/ac_check_krb5.m4

   export CFLAGS+=" -fPIC -fno-strict-aliasing -fstack-protector-all"
   export CPPFLAGS+=" -I/usr/include/et"
   ./configure --prefix=/usr \
               --sysconfdir=/etc \
               --mandir=/usr/share/man \
               --localstatedir=/var/lib \
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

package() {
   cd "${srcdir}/${pkgname}-${pkgver}/src"
   make DESTDIR="${pkgdir}" EXAMPLEDIR=/usr/share/doc/${pkgname}/examples install

   # Fix FS#29889
   install -m 644 plugins/kdb/ldap/libkdb_ldap/kerberos.{ldif,schema} "${pkgdir}"/usr/share/doc/${pkgname}/examples

   # Sample KDC config file
   install -dm 755 "${pkgdir}"/var/lib/krb5kdc
   install -pm 644 config-files/kdc.conf "${pkgdir}"/var/lib/krb5kdc/kdc.conf

   # Default configuration file
   install -dm 755 "${pkgdir}"/etc
   install -pm 644 config-files/krb5.conf "${pkgdir}"/etc/krb5.conf

   install -dm 755 "${pkgdir}"/etc/rc.d
   install -m 755 ../../krb5-{kdc,kadmind,kpropd} "${pkgdir}"/etc/rc.d

   install -dm 755 "${pkgdir}"/usr/share/aclocal
   install -m 644 util/ac_check_krb5.m4 "${pkgdir}"/usr/share/aclocal

   install -Dm644 "${srcdir}"/${pkgname}-${pkgver}/NOTICE "${pkgdir}"/usr/share/licenses/${pkgname}/LICENSE

   # systemd stuff
   install -dm 755 "${pkgdir}"/usr/lib/systemd/system
   install -m 644 ../../krb5-{kadmind.service,kdc.service,kpropd.service,kpropd@.service,kpropd.socket} \
      "${pkgdir}"/usr/lib/systemd/system
}
