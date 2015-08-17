# Contributor: JokerBoy <jokerboy at punctweb dot ro>
# Contributor: Ionut Biru <ibiru@archlinux.org>

pkgname=virtualbox-modules-x32
pkgver=4.1.20
pkgrel=1
pkgdesc='Linux-x32 kernel modules for VirtualBox'
arch=('i686' 'x86_64')
url='http://virtualbox.org'
license=('GPL')
depends=('linux-x32>=3.5' 'linux-x32<3.6' "virtualbox=${pkgver}")
makedepends=('libstdc++5' 'bin86' 'dev86' 'iasl' 'libxslt' 'libxml2' 'libpng' 'libidl2' 'xalan-c' 'sdl' 'linux-x32-headers')
[[ "$CARCH" == "x86_64" ]] && makedepends=("${makedepends[@]}" 'gcc-multilib' 'lib32-glibc')
provides=('virtualbox-modules')
source=("http://download.virtualbox.org/virtualbox/${pkgver}/VirtualBox-${pkgver}.tar.bz2"
        'LocalConfig.kmk')
md5sums=('5784c6dda8a9d53f8f21b2d41eba903c'
         '4c88bd122677a35f68abd76eb01b378b')
install=virtualbox-modules-x32.install

_extramodules=extramodules-3.5-x32
_kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

export KERN_DIR="/usr/lib/modules/${_kernver}/build"
export KERN_INCL="/usr/src/linux-${_kernver}/include/"

build() {
    cd "$srcdir/VirtualBox-${pkgver}"
    cp "$srcdir/LocalConfig.kmk" .
    ./configure \
        --with-linux=/usr/src/linux-${_kernver} \
        --disable-java \
        --disable-docs \
        --disable-xpcom \
        --disable-python \
        --disable-sdl-ttf \
        --disable-alsa \
        --disable-pulse \
        --disable-dbus  \
        --disable-opengl \
        --build-headless \
        --nofatal
    source ./env.sh
    kmk all
    make -C "$srcdir/VirtualBox-${pkgver}/out/linux.$BUILD_PLATFORM_ARCH/release/bin/src"
}

package(){
  source "$srcdir/VirtualBox-${pkgver}/env.sh"
  cd "$srcdir/VirtualBox-${pkgver}/out/linux.$BUILD_PLATFORM_ARCH/release/bin/src"
  install -D -m644 vboxdrv.ko \
    "$pkgdir/usr/lib/modules/${_extramodules}/vboxdrv.ko"
  install -D -m644 vboxnetadp.ko \
    "$pkgdir/usr/lib/modules/${_extramodules}/vboxnetadp.ko"
  install -D -m644 vboxnetflt.ko \
    "$pkgdir/usr/lib/modules/${_extramodules}/vboxnetflt.ko"
  install -D -m644 vboxpci.ko \
    "$pkgdir/usr/lib/modules/${_extramodules}/vboxpci.ko"
  find "${pkgdir}" -name '*.ko' -exec gzip -9 {} \;
  sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='${_extramodules}'/" "$startdir/virtualbox-modules-x32.install"
}
