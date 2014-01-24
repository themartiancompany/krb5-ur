# Maintainer: Stéphane Gaudreault <stephane@archlinux.org>

pkgname=krb5
pkgver=1.12.1
pkgrel=1
pkgdesc="The Kerberos network authentication system"
arch=('i686' 'x86_64')
url="http://web.mit.edu/kerberos/"
license=('custom')
depends=('e2fsprogs' 'libldap' 'keyutils')
makedepends=('perl')
backup=('etc/krb5.conf' 'var/lib/krb5kdc/kdc.conf')
options=('!emptydirs')
source=(http://web.mit.edu/kerberos/dist/${pkgname}/${pkgver%.*}/${pkgname}-${pkgver}-signed.tar
        krb5-config_LDFLAGS.patch
        krb5-kadmind.service
        krb5-kdc.service
        krb5-kpropd.service
        krb5-kpropd@.service
        krb5-kpropd.socket)
sha1sums=('d59e8dc0fc9e1890e109cd033756539984e3d3fe'
          '09e478cddfb9d46d2981dd25ef96b8c3fd91e1aa'
          '59bbc7e686cbb4bcefddf0f134d928d7bd5e7722'
          '2ef2476a8673b3b702e829d8f451c839c2273b02'
          '74d66aefd291f22dd80799f0437cc03d83083ed5'
          '6787c6ce2783b3f980c423e2dd4abf5236af670b'
          'f3677d30dbbd7106c581379c2c6ebb1bf7738912')

prepare() {
  tar -xf ${pkgname}-${pkgver}.tar.gz
  # the signature and source are bundled together, so signature check needs to be done here
  local statusfile=$(mktemp)
  (( SKIPPGPCHECK )) || gpg --quiet --batch --status-file "$statusfile" --verify krb5-${pkgver}.tar.gz.asc krb5-${pkgver}.tar.gz 2> /dev/null
  rm "$statusfile"

  cd ${pkgname}-${pkgver}/src
  # cf https://bugs.gentoo.org/show_bug.cgi?id=448778
  (cd build-tools;  patch -Np2 -i "${srcdir}"/krb5-config_LDFLAGS.patch; cd ..)

   # FS#25384
   sed -i "/KRB5ROOT=/s/\/local//" util/ac_check_krb5.m4
}

build() {
   cd ${pkgname}-${pkgver}/src
   export CFLAGS+=" -fPIC -fno-strict-aliasing -fstack-protector-all"
   export CPPFLAGS+=" -I/usr/include/et"
   ./configure --prefix=/usr \
               --sbindir=/usr/bin \
               --sysconfdir=/etc \
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
   cd ${pkgname}-${pkgver}/src
   make DESTDIR="${pkgdir}" EXAMPLEDIR=/usr/share/doc/${pkgname}/examples install

   # Fix FS#29889
   install -m 644 plugins/kdb/ldap/libkdb_ldap/kerberos.{ldif,schema} "${pkgdir}"/usr/share/doc/${pkgname}/examples

   # Sample KDC config file
   install -dm 755 "${pkgdir}"/var/lib/krb5kdc
   install -pm 644 config-files/kdc.conf "${pkgdir}"/var/lib/krb5kdc/kdc.conf

   # Default configuration file
   install -dm 755 "${pkgdir}"/etc
   install -pm 644 config-files/krb5.conf "${pkgdir}"/etc/krb5.conf

   install -dm 755 "${pkgdir}"/usr/share/aclocal
   install -m 644 util/ac_check_krb5.m4 "${pkgdir}"/usr/share/aclocal

   install -Dm644 "${srcdir}"/${pkgname}-${pkgver}/NOTICE "${pkgdir}"/usr/share/licenses/${pkgname}/LICENSE

   # systemd stuff
   install -dm 755 "${pkgdir}"/usr/lib/systemd/system
   install -m 644 ../../krb5-{kadmind.service,kdc.service,kpropd.service,kpropd@.service,kpropd.socket} \
      "${pkgdir}"/usr/lib/systemd/system
}
