# Maintainer: Aaron Griffin <aaron@archlinux.org>
# Maintainer: Dan McGee <dan@archlinux.org>

pkgname=pacman
pkgver=3.3.2
pkgrel=1
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base')
depends=('bash' 'libarchive>=2.7.1' 'libfetch>=2.25' 'pacman-mirrorlist')
optdepends=('fakeroot: for makepkg usage as normal user'
            'python: for rankmirrors script usage')
backup=(etc/pacman.conf etc/makepkg.conf)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz
        pacman.conf
        makepkg.conf)
md5sums=('023dd4abc3996f2084b12f9a02a088d6'
         'abe70dabacee7036368c7afeb686eb10'
         '52c7bc651b107d73f9902755b7594458')

build() {
  cd $srcdir/$pkgname-$pkgver
  
  ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var
  make || return 1
}

package() {
  cd $srcdir/$pkgname-$pkgver
  make DESTDIR=$pkgdir install || return 1

  # install Arch specific stuff
  mkdir -p $pkgdir/etc
  install -m644 $srcdir/pacman.conf $pkgdir/etc/
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
