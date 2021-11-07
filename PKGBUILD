# Maintainer: Lukas Fleischer <lfleischer@archlinux.org>
# Contributor: Paul Mattal <paul@archlinux.org>

pkgname=lirc-irdroid
pkgver=0.8.7
pkgrel=1
pkgdesc="Linux Infrared Remote Control utils (irdroid version)"
arch=('i686' 'x86_64' 'armv7h' 'aarch64')
url="http://www.lirc.org/"
license=('GPL')
depends=('alsa-lib' 'libx11' 'libftdi-compat' 'libirman')
# makedepends=('help2man' 'alsa-lib' 'libx11' 'libftdi-compat' 'libirman' 'libxslt' 'python')
# optdepends=('python: for lirc-setup, irdb-get and pronto2lirc')
provides=('lirc' 'lirc-utils')
conflicts=('lirc' 'lirc-utils')
replaces=('lirc' 'lirc-utils')
backup=('etc/lirc/lirc_options.conf' 'etc/lirc/lircd.conf' 'etc/lirc/lircmd.conf')
install=lirc.install
source=("lirc.tar.gz::http://www.irdroid.com/wp-content/plugins/download-monitor/download.php?id=16"
        lirc.logrotate
        lirc.tmpfiles
        lircd.service)
md5sums=('64548f86d6ecf8a2456e511e8aa14984'
         '3deb02604b37811d41816e9b4385fcc3'
         'febf25c154a7d36f01159e84f26c2d9a'
         '64e7e7d4a6befe57c6e42777fdf0a36f')

build() {
  cd "${srcdir}/irtoy"
  
  chmod +x ./configure

  # Fix some C stuff that causes linking to fail; line numbers will likely change
  sed -i '182s/inline //' ./daemons/transmit.c
  sed -i '417s/inline //' ./daemons/receive.c
  sed -i '256s/inline //' ./daemons/lircd.c
  sed -i '198s/inline //' ./tools/lircrcd.c

  # Oh my god this is the worst C I've read this year.
  sed -i '3030s/manidx/"%s", manidx/' ./doc/man2html.c

  # Override a default
  sed -i '102s/"\/dev\/ttyACM0"/"\/dev\/lirc0"/' ./daemons/hw_usbirtoy.c
  sed -i '68s/"\/dev\/ttyACM0"/"\/dev\/lirc0"/' ./setup.sh
  sed -i '219s/"\/dev\/ttyACM0"/"\/dev\/lirc0"/' ./setup.sh
  sed -i '5s/"\/dev\/ttyACM0"/"\/dev\/lirc0"/' ./configure.sh
  sed -i '4s/"\/dev\/ttyACM0"/"\/dev\/lirc0"/' ./.setup.config
  

  ./configure --prefix=/usr --sbindir=/usr/bin --sysconfdir=/etc --localstatedir=/var \
    --with-transmitter --with-driver=usb_irtoy --enable-sandboxed
  
  sed -i '4s/"\/dev\/ttyACM0"/"\/dev\/lirc0"/' ./.setup.config # idk paranoia?
  
  make
}

package() {
  cd "${srcdir}/irtoy"

  # dirtyfix: For some reason the "make install" fails if this directory does
  # not exist (it tries to remove it)
  mkdir -p "${pkgdir}/var/run/lirc"

  make DESTDIR="${pkgdir}" install

  install -Dm644 "${srcdir}"/lirc.tmpfiles "${pkgdir}"/usr/lib/tmpfiles.d/lirc.conf
  install -Dm644 "${srcdir}"/lirc.logrotate "${pkgdir}"/etc/logrotate.d/lirc
  install -Dm644 "${srcdir}"/lircd.service "${pkgdir}"/usr/lib/systemd/system/lircd.service

  rmdir "${pkgdir}"/var/{run/lirc/,run/,}
}
