# Maintainer: Daniel Hillenbrand <codeworkx [at] bbqlinux [dot] org>
# Contributor: Philipp 'TamCore' B. <philipp [at] tamcore [dot] eu>
# Contributor: Jakub Schmidtke <sjakub-at-gmail-dot-com>
# Contributor: Christoph Brill <egore911-at-gmail-dot-com>

pkgname=android-studio
pkgver=0.2.5
pkgrel=4
_build=130.737825
_patch=130.782403
pkgdesc="A new Android development environment based on IntelliJ IDEA."
arch=(i686 x86_64)
url="http://developer.android.com/sdk/installing/studio.html"
license=('APACHE')
depends=('python' 'fontconfig' 'mesa' 'libxrender' 'android-sdk' 'android-sdk-platform-tools')
makedepends=('unzip' 'xorg-server-xvfb' 'ttf-dejavu')
optdepends=('android-sdk-build-tools')
options=('!strip')
install=${pkgname}.install
source=(http://dl.google.com/android/studio/android-studio-bundle-${_build}-linux.tgz
        ${pkgname}.desktop)
test ! -z ${_patch} && source+=("http://dl.google.com/android/studio/patches/AI-${_build}-${_patch}-patch-unix.jar")
noextract=(AI-${_build}-${_patch}-patch-unix.jar)
md5sums=('dcd13922f7cf577e3c852b224205d843'
         '0f77280fd3769b0e04ff60dad547ce5e'
         '7bc8896c8203f3fd2a47532d3a73b554')

[ $CARCH = "x86_64" ] && depends=('python' 'lib32-fontconfig' 'lib32-mesa' 'lib32-libxrender' 'android-sdk')

prepare() {
    echo "Performing update..."

    if [ ! -z "${_patch}" ]
    then
        xvfb-run java -classpath "${srcdir}/AI-${_build}-${_patch}-patch-unix.jar" com.intellij.updater.Runner install "${srcdir}/${pkgname}" || ret="${?}"

        if [ "${ret}" != "42" ]; then
            echo "Update process failed with code: ${ret}"
            exit 1
        fi
    fi

    cd "${srcdir}/${pkgname}"

    # remove pause after open jdk warning
    sed -i 's/read IGNORE//' bin/studio.sh

    # extract the application icon
    unzip -o lib/resources.jar artwork/icon_AS_128.png

    # enable anti aliasing
    for f in bin/*.vmoptions; do
      echo "-Dawt.useSystemAAFontSettings=lcd" >> ${f}
      echo "-Dswing.aatext=true" >> ${f}
    done
}

package() {
    cd "${srcdir}/${pkgname}"

    # application stuff
    mkdir -p "${pkgdir}/opt/${pkgname}"
    cp -a bin lib plugins "${pkgdir}/opt/${pkgname}"

    mkdir -p "${pkgdir}/usr/bin"
    ln -s /opt/android-studio/bin/studio.sh "${pkgdir}/usr/bin/android-studio"

    # starter stuff
    install -Dm655 artwork/icon_AS_128.png "${pkgdir}/usr/share/pixmaps/${pkgname}.png"
    install -Dm655 "${srcdir}/${pkgname}.desktop" "${pkgdir}/usr/share/applications/${pkgname}.desktop"

    # Fix permissions
    chmod -R ugo+rX "${pkgdir}/opt"
    chmod g+rwX "${pkgdir}/opt/${pkgname}"
    chown -R android "${pkgdir}/opt/${pkgname}"
    chgrp -R users "${pkgdir}/opt/${pkgname}"
}
