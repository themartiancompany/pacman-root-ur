# Maintainer: Aaron Griffin <aaron@archlinux.org>
# Maintainer: Dan McGee <dan@archlinux.org>

pkgname=pacman
pkgver=3.4.0
pkgrel=2
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base')
depends=('bash' 'libarchive>=2.7.1' 'libfetch>=2.25' 'pacman-mirrorlist')
optdepends=('fakeroot: for makepkg usage as normal user')
backup=(etc/pacman.conf etc/makepkg.conf)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz
        pacman.conf
        pacman.conf.x86_64
        makepkg.conf
        0001-makepkg-fallback-to-sane-defaults-for-library-stripp.patch)
md5sums=('50ad71be1faaad84842c576e239d1bb5'
         'eda9cbdb47f85fabda2e7e63801e3e16'
         '9ba146b4fa7bcb3cc18204b06d1f6157'
         'aef317285c7d16ac495b0e53deeb948d'
         'f8c4a3cc7702a7a70d177659441495c5')
sha256sums=('cd80e206ee653ce337555c73b7064088e672e9341245317fe09dc52d06bff3c3'
            'cc5b197d50739ecd5e757b95d19ba5936782f8b588260e3ec063f2ae383ff6a1'
            '816655f858cf5d4950a284ed6ca5fc52ab3abc81f6618d3a8b3644f4f2d03242'
            '3a60e1f895c90c8e74f5ca389fa05fb3328745e9873c5452b8cd1b2e68bee418'
            '99f1d108f930b134cfb4c1ca8c86cd282fe9efb69de39cd747218f3d448dda44')

# keep an upgrade path for older installations
PKGEXT='.pkg.tar.gz'

build() {
  cd $srcdir/$pkgname-$pkgver
  patch -Np1 < $srcdir/0001-makepkg-fallback-to-sane-defaults-for-library-stripp.patch
  ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var --enable-doc
  make || return 1
}

package() {
  cd $srcdir/$pkgname-$pkgver
  make DESTDIR=$pkgdir install || return 1

  # install Arch specific stuff
  mkdir -p $pkgdir/etc
  case "$CARCH" in
    i686)
      install -m644 $srcdir/pacman.conf $pkgdir/etc/pacman.conf
      ;;
    x86_64)
      install -m644 $srcdir/pacman.conf.x86_64 $pkgdir/etc/pacman.conf
      ;;
  esac
  install -m644 $srcdir/makepkg.conf $pkgdir/etc/
  # set things correctly in the default conf file
  case "$CARCH" in
    i686)
      mycarch="i686"
      mychost="i686-pc-linux-gnu"
      myflags="-march=i686 "
      ;;
    x86_64)
      mycarch="x86_64"
      mychost="x86_64-unknown-linux-gnu"
      myflags="-march=x86-64 "
      ;;
  esac
  sed -i $pkgdir/etc/makepkg.conf \
    -e "s|@CARCH[@]|$mycarch|g" \
    -e "s|@CHOST[@]|$mychost|g" \
    -e "s|@CARCHFLAGS[@]|$myflags|g"

  # install completion files
  mkdir -p $pkgdir/etc/bash_completion.d/
  install -m644 contrib/bash_completion $pkgdir/etc/bash_completion.d/pacman
  mkdir -p $pkgdir/usr/share/zsh/site-functions/
  install -m644 contrib/zsh_completion $pkgdir/usr/share/zsh/site-functions/_pacman
}

# vim: set ts=2 sw=2 et:
