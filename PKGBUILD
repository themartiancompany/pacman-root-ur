# vim: set ts=2 sw=2 et:
# Maintainer: Dan McGee <dan@archlinux.org>
# Maintainer: Dave Reisner <dreisner@archlinux.org>

pkgname=pacman
pkgver=4.0.3
pkgrel=1
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base')
depends=('bash' 'glibc>=2.15' 'libarchive>=3.0.2' 'curl>=7.19.4'
         'gpgme' 'pacman-mirrorlist')
makedepends=('asciidoc')
optdepends=('fakeroot: for makepkg usage as normal user')
backup=(etc/pacman.conf etc/makepkg.conf)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz{,.sig}
        pacman.conf
        pacman.conf.x86_64
        makepkg.conf)
md5sums=('387965c7125e60e5f0b9ff3b427fe0f9'
         '1a70392526c8768470da678b31905a6e'
         '4605b3490d4fd1e5c6e20db17da9ded6'
         'a0edf98ad1845a4c7d902a86638d5d2d'
         'db051afbd12993b7743ccd4d58668499')

build() {
  cd $srcdir/$pkgname-$pkgver

  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc
  make
}

check() {
  make -C "$pkgname-$pkgver" check
}

package() {
  cd $srcdir/$pkgname-$pkgver
  make DESTDIR=$pkgdir install

  # install Arch specific stuff
  mkdir -p $pkgdir/etc
  case "$CARCH" in
    i686)
      install -m644 $srcdir/pacman.conf $pkgdir/etc/pacman.conf
      mycarch="i686"
      mychost="i686-pc-linux-gnu"
      myflags="-march=i686 "
      ;;
    x86_64)
      install -m644 $srcdir/pacman.conf.x86_64 $pkgdir/etc/pacman.conf
      mycarch="x86_64"
      mychost="x86_64-unknown-linux-gnu"
      myflags="-march=x86-64 "
      ;;
  esac
  install -m644 $srcdir/makepkg.conf $pkgdir/etc/
  # set things correctly in the default conf file
  sed -i $pkgdir/etc/makepkg.conf \
    -e "s|@CARCH[@]|$mycarch|g" \
    -e "s|@CHOST[@]|$mychost|g" \
    -e "s|@CARCHFLAGS[@]|$myflags|g"

  # install completion files
  install -Dm644 contrib/bash_completion "$pkgdir/usr/share/bash-completion/completions/pacman"
  for f in makepkg pacman-key; do
    ln -s pacman "$pkgdir/usr/share/bash-completion/completions/$f"
  done

  install -Dm644 contrib/zsh_completion $pkgdir/usr/share/zsh/site-functions/_pacman
}
