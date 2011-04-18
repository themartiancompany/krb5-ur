# Maintainer: Stéphane Gaudreault <stephane@archlinux.org>

pkgname=krb5
pkgver=1.9
pkgrel=1
pkgdesc="The Kerberos network authentication system"
arch=('i686' 'x86_64')
url="http://web.mit.edu/kerberos/"
license=('custom')
depends=('e2fsprogs' 'libldap' 'keyutils')
makedepends=('perl')
provides=('heimdal')
replaces=('heimdal')
conflicts=('heimdal')
backup=('etc/krb5/krb5.conf' 'etc/krb5/kdc.conf')
source=(http://web.mit.edu/kerberos/dist/${pkgname}/${pkgver}/${pkgname}-${pkgver}-signed.tar
        kadmind.rc
        krb5-kdc.rc
        CVE-2010-4022.patch
        CVE-2011-0281.0282.0283.patch
        CVE-2011-0284.patch
        CVE-2011-0285.patch)
sha1sums=('a7ad1b4ed37bff4b9087f6c4561b2b222208d779'
          '640e3046c6558313d2be81cf2252afc8622892b0'
          '77d2312ecd8bf12a6e72cc8fd871a8ac93b23393'
          '79ece8b1c140deb2c01bfb64af575636b9bc7704'
          'fb2486168ce128cb1a2866bd0df8cd7c4bcd7824'
          '1c72390c5d629eee592e5cb0c2b600b376e2fdc5'
          'b6ae716616ecd5e92f32ec8203a1ab51b5726184')
options=('!emptydirs')

build() {
   tar zxvf ${pkgname}-${pkgver}.tar.gz
	cd "${srcdir}/${pkgname}-${pkgver}/src"

   patch -Np2 -i ../../CVE-2010-4022.patch
   patch -Np2 -i ../../CVE-2011-0281.0282.0283.patch
   patch -Np2 -i ../../CVE-2011-0284.patch
   patch -Np2 -i ../../CVE-2011-0285.patch

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
   make DESTDIR="${pkgdir}" EXAMPLEDIR="/usr/share/doc/${pkgname}/examples" install

   install -D -m 644 config-files/kdc.conf "${pkgdir}"/etc/krb5/kdc.conf
   install -D -m 644 config-files/krb5.conf "${pkgdir}"/etc/krb5/krb5.conf
   install -D -m 755 ../../krb5-kdc.rc "${pkgdir}"/etc/rc.d
   install -D -m 755 ../../kadmind.rc "${pkgdir}"/etc/rc.d

	install -Dm644 "${srcdir}"/${pkgname}-${pkgver}/NOTICE "${pkgdir}"/usr/share/licenses/${pkgname}/LICENSE
}
