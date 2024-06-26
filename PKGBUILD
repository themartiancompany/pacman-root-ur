# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: Morten Linderud <foxboron@archlinux.org>

_os="$( \
  uname \
    -o)"
_prefix="/usr"
if [[ "${_os}" == "Android" ]]; then 
  _prefix="/opt"
fi
_pkgname=pacman
pkgname="${_pkgname}-root"
pkgver=6.1.0
pkgrel=3
pkgdesc="A library-based package manager with dependency support"
arch=(
  'x86_64'
  'arm'
  'aarch64'
  'mips'
  'powerpc'
)
url="https://www.archlinux.org/pacman/"
license=(
  'GPL-2.0-or-later'
)
depends=(
  'bash'
  'glibc'
  'libarchive'
  'curl'
  'gpgme'
  'pacman-mirrorlist'
  'gettext'
  'gawk'
  'coreutils'
  'gnupg'
  'grep'
)
makedepends=(
  'meson'
  'asciidoc'
  'doxygen'
)
checkdepends=(
  'python'
  'fakechroot'
)
optdepends=(
  'perl-locale-gettext: translation support in makepkg-template'
)
provides=(
  'libalpm.so'
)
backup=(
  opt/etc/pacman.conf
  opt/etc/makepkg.conf
)
options=(
  'strip'
)
validpgpkeys=(
  # Allan McRae <allan@archlinux.org>
  '6645B0A8C7005E78DB1D7864F99FFE0FEAE999BD'
  # Andrew Gregory (pacman) <andrew@archlinux.org>
  'B8151B117037781095514CA7BBDFFC92306B1121'
)
source=(
  https://gitlab.archlinux.org/pacman/pacman/-/releases/v$pkgver/downloads/pacman-$pkgver.tar.xz{,.sig}
  revertme-makepkg-remove-libdepends-and-libprovides.patch::https://gitlab.archlinux.org/pacman/pacman/-/commit/354a300cd26bb1c7e6551473596be5ecced921de.patch
  "${_pkgname}-fix-msg-unknown-key.patch::https://gitlab.archlinux.org/pacman/pacman/-/commit/6bb95c8856437513ee0ab19226bc090d6fd0fb06.patch"
  "${_pkgname}-man-gitlab.patch::https://gitlab.archlinux.org/pacman/pacman/-/commit/95f148c2222db608a0d72d5c5577d0c71e7fa199.patch"
  "${_pkgname}-make-aligned-titles.patch::https://gitlab.archlinux.org/pacman/pacman/-/commit/5e0496260b7d3f9c9fcf2b1c4899e4dbcc20ff03.patch"
  "${_pkgname}-repo-add-parseopts.patch::https://gitlab.archlinux.org/pacman/pacman/-/commit/0571ee82bff0edbd5ffac2228d4e6ac510b9008e.patch"
  "${_pkgname}-drop-result-warn.patch::https://gitlab.archlinux.org/pacman/pacman/-/commit/111eed0251238a9d3f90e76d62f2ac01aeccce48.patch"
  "${_pkgname}-fix-debugedit.patch::https://gitlab.archlinux.org/pacman/pacman/-/commit/bae9594ac1806ce30f2af1de27c49bb101a00d44.patch"
  pacman.conf
  makepkg.conf
)
sha256sums=(
  '5a60ac6e6bf995ba6140c7d038c34448df1f3daa4ae7141d2cad88eeb5f1f9d9'
  'SKIP'
  'b3bce9d662e189e8e49013b818f255d08494a57e13fc264625f852f087d3def2'
  '94c987046c2ff232fa0d395cddc11644840d767806711e04ef34f876a9baf217'
  '0774d7035e34661f74b673d4b0a94be877bdc0158a555b873ec6bd4e2c936377'
  '7bb64910265ce2590f593cdfd302076e49f67a68f8cc792a9aaac572d36fc842'
  '2bbfe40539513ff5775aaf900644c8985ef618f5df9af856b9d571e2501365b0'
  '160515b741aadc876a67f213029f5f62a51ff072ea4aaeb687bbe614035bf72f'
  '1f4e4cc54332e60c9da2bdabf9a80dc11db466535f1a0be298cbf654f0723721'
  '656c4d4cb8cb12adbf178fc8cb2fd25f8c285d6572bbdbb24d865d00e0d5a85a'
  '2465d495cb275dce434eb3bfe4d293a223e301b968c14861aea42bc7c60404ef')

prepare() {
  cd \
    "${_pkgname}-${pkgver}"
  # handle patches
  local \
    -a patches
  patches=(
    $(printf \
        '%s\n' \
        "${source[@]}" | \
        grep \
          '.patch'))
  patches=(
    "${patches[@]%%::*}"
  )
  patches=(
    "${patches[@]##*/}"
  )
  if (( ${#patches[@]} != 0 )); then
    for patch in "${patches[@]}"; do
      if [[ $patch =~ revertme-* ]]; then
        msg2 "Reverting patch $patch..."
        patch -RNp1 < "../$patch"
      else
        msg2 "Applying patch $patch..."
        patch -Np1 < "../$patch"
      fi
    done
  fi
}

build() {
  local \
    _cflags=() \
    _meson_opts=()
  cd \
    "${_pkgname}-$pkgver"
  _cflags=(
    "${CFLAGS}"
    -DLINE_MAX=2048
  )
  _meson_opts=(
    # TODO:
    #   Find a way to have this
    #   simply installed near the non-root
    #   pacman
    --prefix=/opt
    --buildtype=plain
    -Dbuildstatic=false
    -Di18n=false
    -Ddoc=disabled
    -Ddoxygen=disabled
    -Dscriptlet-shell="/data/data/com.termux/files/usr/bin/bash"
    -Dldconfig="/data/data/com.termux/files/usr/bin/ldconfig"
  )
  export \
    CFLAGS="${_cflags[*]}"
  CFLAGS="${_cflags[*]}" \
  meson \
    "${_meson_opts[@]}" \
    build
  CFLAGS="${_cflags[*]}" \
  meson \
    compile \
    -C \
      build
}

check() {
  cd \
    "${_pkgname}-$pkgver"
  meson \
    test \
    -C \
      build
}

package() {
  local \
    _units=() \
    _unit \
    _wantsdir
  _wantsdir="${pkgdir}${_prefix}/lib/systemd/system/sockets.target.wants"
  _units=(
    dirmngr
    gpg-agent
    gpg-agent-{browser,extra,ssh}
    keyboxd
  )
  cd \
    "${_pkgname}-$pkgver"
  DESTDIR="${pkgdir}" \
  meson \
    install \
    -C \
      build
  # install Arch specific stuff
  install \
    -dm755 \
    "${pkgdir}${_prefix}/etc"
  install \
    -m644 \
    "${srcdir}/pacman.conf" \
    "${pkgdir}${_prefix}/etc"
  install \
    -m644 \
    "${srcdir}/makepkg.conf" \
    "${pkgdir}${_prefix}/etc"
  install \
    -dm755 \
    "${_wantsdir}"
  for _unit in "${_units[@]}"; do
    ln \
      -s \
      "../${_unit}@.socket" \
      "${_wantsdir}/${_unit}@etc-pacman.d-gnupg.socket"
  done
}

# vim: set ts=2 sw=2 et:
