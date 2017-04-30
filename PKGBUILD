# Maintainer: Erik Fleischer <erik [at] erlenweg [dot] de>
# Contributor: Cian Mc Govern <cian [at] cianmcgovern [dot] com>
# Contributor: Roland Singer <roland [at] manjaro [dot] org>
# Contributor: TheBenj <thebenj88 [at] gmail [dot] com>
# Contributor: Philipp 'TamCore' B. <philipp [at] tamcore [dot] eu>

pkgname=crossover
pkgver=16.2.0
pkgrel=3
_pkgdebrel=1
pkgdesc="Run Windows Programs on Linux"
arch=('i686' 'x86_64')
url="http://www.codeweavers.com"
license=('custom:CrossOver Linux License Grant')
makedepends=('tar')
install=${pkgname}.install
replaces=('crossover-games' 'crossover-pro' 'crossover-standard')

source=("http://media.codeweavers.com/pub/${pkgname}/cxlinux/demo/${pkgname}_${pkgver}-${_pkgdebrel}.deb")
sha256sums=('8a440555c8cc047b9c58784924aab4e7c36a3c8925940663af4ae68db5ef4cf9')


# dependency handling wine-style
depends=(
  python             # Crossover invokes '/usr/bin/python'
  python2            # and then uses that to start itself in a python2 interpreter.
  pygtk
  python2-dbus
  fontconfig      lib32-fontconfig
  lcms2           lib32-lcms2
  libxml2         lib32-libxml2
  libxcursor      lib32-libxcursor
  libxrandr       lib32-libxrandr
  libxdamage      lib32-libxdamage
  libxi           lib32-libxi
  gettext         lib32-gettext
  freetype2       lib32-freetype2
  glu             lib32-glu
  libsm           lib32-libsm
  gcc-libs        lib32-gcc-libs
  libpcap         lib32-libpcap
  desktop-file-utils
)

optdepends=(
  giflib                lib32-giflib
  libpng                lib32-libpng
  libldap               lib32-libldap
  gnutls                lib32-gnutls
  mpg123                lib32-mpg123
  openal                lib32-openal
  v4l-utils             lib32-v4l-utils
  libpulse              lib32-libpulse
  alsa-plugins          lib32-alsa-plugins
  alsa-lib              lib32-alsa-lib
  libjpeg-turbo         lib32-libjpeg-turbo
  libxcomposite         lib32-libxcomposite
  libxinerama           lib32-libxinerama
  ncurses               lib32-ncurses
  opencl-icd-loader     lib32-opencl-icd-loader
  libxslt               lib32-libxslt
  gst-plugins-base-libs lib32-gst-plugins-base-libs
  libexif               lib32-libexif
  libgphoto2
  cups
  samba           
  dosbox
  sane
)

# dependency stripping wine-style
if [[ $CARCH == i686 ]]; then
  # Strip lib32 etc. on i686
  depends=(${depends[@]/*32-*/})
  optdepends=(${optdepends[@]/*32-*/})
fi

package() {
    cd ${srcdir}

    ar -p crossover_${pkgver}-${_pkgdebrel}.deb data.tar.xz | tar Jxf - -C "${pkgdir}" || return 1
    if [[ "${CARCH}" = 'i686' ]]; then
        rm -f ${pkgdir}/opt/cxoffice/lib/nsplugin-linux64.so
    fi

    #remove symlink and create real directory
    rm ${pkgdir}/opt/cxoffice/doc 
    mkdir ${pkgdir}/opt/cxoffice/doc 

    mv ${pkgdir}/usr/share/doc/crossover/* ${pkgdir}/opt/cxoffice/doc

    rm -r "${pkgdir}/usr"
    sed -e 's!;;"MenuRoot" = ""!"MenuRoot" = "Windows Games"!' \
        -e 's!;;"MenuStrip" = ""!"MenuStrip" = "1"!' \
        -i ${pkgdir}/opt/cxoffice/share/crossover/bottle_data/cxbottle.conf

    mkdir -p ${pkgdir}/usr/bin
    ln -s /opt/cxoffice/bin/crossover ${pkgdir}/usr/bin/crossover
    ln -s /opt/cxoffice/bin/cxsetup ${pkgdir}/usr/bin/cxsetup

    mkdir -p ${pkgdir}/etc/profile.d
    echo '[ -d /opt/cxoffice/bin ] && export PATH="${PATH}:/opt/cxoffice/bin"' > ${pkgdir}/etc/profile.d/cxoffice.sh
    echo '[ -d /opt/cxoffice/bin ] && setenv PATH ${PATH}:/opt/cxoffice/bin' > ${pkgdir}/etc/profile.d/cxoffice.csh
    chmod 755 ${pkgdir}/etc/profile.d/cxoffice.sh ${pkgdir}/etc/profile.d/cxoffice.csh

    # Fix Auto update error
    install -m 644 -D ${pkgdir}/opt/cxoffice/share/crossover/data/cxoffice.conf ${pkgdir}/opt/cxoffice/etc/cxoffice.conf
    sed -e 's!;;"PrivateShortcutDirs" = ""!"PrivateShortcutDirs" = "${HOME}/bin:${CX_ROOT}/bin"!' \
        -e 's!;;"PrivateLinuxNSPluginDirs" = ""!"PrivateLinuxNSPluginDirs" = "${MOZ_PLUGIN_PATH}"!' \
        -e 's!;;"PrivateLinux64NSPluginDirs" = ""!"PrivateLinux64NSPluginDirs" = "${MOZ_PLUGIN_PATH}"!' \
        -e 's!;;"ProductPackage" = ""!"ProductPackage" = "Converted from .deb to pacman."!' \
        -i ${pkgdir}/opt/cxoffice/etc/cxoffice.conf

    # place license in correct directory
    install -D -m644 ${pkgdir}/opt/cxoffice/doc/license.txt.gz ${pkgdir}/usr/share/licenses/${pkgname}/license.txt.gz
}
