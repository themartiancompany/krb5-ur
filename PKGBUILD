# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>

_system_verto="true"
_system_etss="true"
_os="$( \
  uname \
    -o)"
[[ "${_os}" == "Android" ]] && \
  _system_verto="false" && \
  _system_etss="false"
_pkg=krb5
pkgbase="${_pkg}"
pkgname=(
  "${pkgbase}"
)
_pkgvermajor=1.21
pkgver=1.21.2
pkgrel=2
pkgdesc='The Kerberos network authentication system'
url='https://web.mit.edu/kerberos'
arch=(
  'x86_64'
  'arm'
  'aarch64'
  'i686'
)
license=(
  'custom'
)
depends=(
  'glibc'
  'e2fsprogs'
  'libldap'
  'keyutils'
  'libverto-module-base'
  libkeyutils.so
  libss.so
  libcom_err.so
)
[[ "${_os}" == "Android" ]] && \
  depends+=(
    'libresolv-wrapper'
  )

[[ "${_system_verto}" == "true" ]] && \
  depends+=(
    'libverto-module-base'
    'libverto.so'
  )
makedepends=(
  'perl'
)
provides=(
  "libgssapi_${_pkg}.so"
  libgssrpc.so
  libk5crypto.so
  libkadm5clnt_mit.so
  libkadm5srv_mit.so
  libkdb5.so
  libkdb_ldap.so
  libkrad.so
  "lib${_pkg}.so"
  "lib${_pkg}support.so"
)
backup=(
  "etc/${_pkg}.conf"
  "var/lib/${_pkg}kdc/kdc.conf"
)
options=(
  '!emptydirs'
)
source=(
  "${url}/dist/${_pkg}/${_pkgvermajor}/${_pkg}-${pkgver}.tar.gz"{"",".asc"}
  "${_pkg}-kadmind.service"
  "${_pkg}-kdc.service"
  "${_pkg}-kpropd.service"
  "${_pkg}-kpropd@.service"
  "${_pkg}-kpropd.socket"
)
sha512sums=(
  '4e09296b412383d53872661718dbfaa90201e0d85f69db48e57a8d4bd73c95a90c7ec7b6f0f325f6bc967f8d203b256b071c0191facf080aca0e2caec5d0ac49'
  'SKIP'
  'ae1fa980e8e30a83dfef7fe233be70a9ec530ebaffc344a0e7eba61e7de4c800421b45cf203f1e526cc8351754038d6539184b30aa049a567e2a9e80f0d39841'
  'b137ff0154f9af4891e9e78cec692af47ecfd92ca9ce6e00b40ef137d942ba04e8caf483fc5d464b9559ad4a5c4e75ec57b6eab7fc35181115ca1606c0b316c1'
  'b57bbe55c19c92a04cd3e8ad569adcc5aedd4246075b2e6dbcc363e523853332a3e7650be85098b91e495799f8b728ea550495ab303f46ccd9298a2260120b2f'
  'a31ed42e988fed892dd3f1ca1942c3cd4bf391d894c9cebe5e91f52797392261e129727590ae06e2f727bb7b0f8d73a7aa35e8b983d6bad77f22a099091ee944'
  '4e7ae175425e0787a1d5ff959471a88bf5af4cd6e213dc6d4048902fab7547c1186a082370b523f9549f5096acfab1fb03b4839e42bd80dc539130ae4bb3ea55'
)
[[ "${_os}" == "Android" ]] && \
  source+=(
    "lib-${_pkg}-os-dnsglue.c.patch"
    "netbsd_getpass.c"
    "plugins-kdb-db2.patch"
  ) && \
  sha512sums+=(
    "a222aec81c9b4230caa68bc0f9731dbaa01de8a7b63804def6a7f5253c5ef03f65f372cf147dad9c49d69b17c922558442b117ca3a4deec728a76654c29a0d53"
    "1459ae4a9d084864ca0f6d7f92fe8db66cbf14a31c1116ea5c58b087c4bc5c6aa04ec484e66e7424047c0a3d48c461e0ec3844486ce00fc23249e3630c6eaf0d"
    "e936b9c2b5f11998a0e205826fb2b1958b1d52208cb9f1558a3dbdbf7d251905d0563e8c5698f7ec8b215afef7a59acd6b12f88bc4d649f82901582615d6e7f2"
  )
validpgpkeys=(
  # Tom Yu <tlyu@mit.edu>
  '2C732B1C0DBEF678AB3AF606A32F17FD0055C305'  
  # Greg Hudson <ghudson@mit.edu>
  'C4493CB739F4A89F9852CBC20CBA08575F8372DF'
  )

_bin="$( \
  dirname \
    "$( \
      command \
        -v \
	"cc" \
	"gcc" | \
      head \
        -n \
	1)")"

_usr="$( \
  dirname \
    "${_bin}")"

prepare() {
  cd \
    "${pkgname}-${pkgver}"
  # FS#25384
  sed \
    -i \
    "/KRB5ROOT=/s/\/local//" \
    "src/util/ac_check_${_pkg}.m4"
  sed \
    "s%/usr/local%${_usr}%g" \
    -i \
    "src/config-files/kdc.conf"
  sed \
    -i \
    "s%= 88%= 1088%g" \
    "src/config-files/kdc.conf"
  if [[ "${_os}" == "Android" ]]; then
  	cp \
      "${srcdir}/netbsd_getpass.c" \
      "src/clients/kpasswd"
    cd \
      "src"
    patch \
      -p1 < \
      "${srcdir}/lib-${_pkg}-os-dnsglue.c.patch"
     # patch \
     #  -p1 < \
     #  "${srcdir}/plugins-kdb-db2.patch"
  fi
}

build() {
  local \
    _configure_opts=() \
    _verto="with" \
    _cppflags=() \
    _cflags=() \
    _ldflags=()
  _cppflags=(
    "${CPPFLAGS}"
  )
  _cflags=(
    "${CFLAGS}"
    "-fPIC"
    "-fno-strict-aliasing"
    "-fstack-protector-all"
  )
  _ldflags=(
    "${LDFLAGS}"
  )
  _configure_opts=(
    --prefix=/usr
    --sbindir=/usr/bin
    --sysconfdir=/etc
    --localstatedir=/var/lib
    --enable-shared
    --disable-rpath
    --without-tcl
    --enable-dns-for-realm
    --with-ldap
  )
  [[ "${_system_verto}" == "false" ]] && \
    _verto+="out"
   _configure_opts+=(
    --"${_verto}-system-verto"
  )
  [[ "${_system_etss}" == "true" ]] && \
    _configure_opts+=(
      --with-system-et
      --with-system-ss
    ) && \
    _cppflags+=(
      "-I/usr/include/et"
    )
  [[ "${_os}" == "Android" ]] && \
    _configure_opts+=(
      --disable-thread-support
    ) && \
    _cflags+=(
      -D_PASSWORD_LEN=PASS_MAX
    ) && \
   _ldflags+=(
      -lresolv_wrapper
   )
  cd \
    ${pkgname}-${pkgver}/src
  CPPFLAGS="${_cppflags[*]}" \
  CFLAGS="${_cflags[*]}" \
  LDFLAGS="${_ldflags[*]}" \
  ./configure \
    "${_configure_opts[@]}"
  CPPFLAGS="${_cppflags[*]}" \
  CFLAGS="${_cflags[*]}" \
  LDFLAGS="${_ldflags[*]}" \
  make
}

package() {
   cd \
     "${pkgname}-${pkgver}/src"
   make \
     DESTDIR="${pkgdir}" \
     PREFIX="/usr" \
     EXAMPLEDIR=/usr/share/doc/${pkgname}/examples \
     install

   # Fix FS#29889
   install \
     -m644 \
     plugins/kdb/ldap/libkdb_ldap/kerberos.{ldif,schema} \
     "${pkgdir}/usr/share/doc/${pkgname}/examples"

   install \
     -Dpm 644 \
     "config-files/${_pkg}.conf" \
     -t \
     "${pkgdir}/etc"
   install \
     -Dpm 644 \
     "config-files/kdc.conf" \
     -t \
     "${pkgdir}/var/lib/${_pkg}kdc"
   install \
     -Dm 644 \
     "util/ac_check_${_pkg}.m4" \
     -t \
     "${pkgdir}/usr/share/aclocal"
   install \
     -Dm 644 \
     "${srcdir}"/${pkgname}-${pkgver}/NOTICE \
     "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
   # systemd stuff
   install \
     -Dm 644 \
     "${srcdir}/${_pkg}-"{kadmind.service,kdc.service,kpropd.service,kpropd@.service,kpropd.socket} \
      -t \
      "${pkgdir}/usr/lib/systemd/system"
}

# vim: ts=2 sw=2 et:

