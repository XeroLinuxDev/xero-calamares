# Maintainer: DarkXero

pkgname=xero-calamares
_pkgname=calamares
pkgver=3.3.6
pkgrel=01
pkgdesc='Distribution-independent installer framework'
arch=('x86_64')
license=(GPL)
url="https://github.com/calamares/calamares"
license=('LGPL')
depends=('boost-libs' 'ckbcomp' 'cryptsetup' 'doxygen' 'efibootmgr' 'gptfdisk' 'gtk-update-icon-cache' 'hwinfo' 'icu' 'kconfig' 'kcoreaddons' 'kcrash' 'ki18n' 'kparts' 'kpmcore' 'kservice' 'kwidgetsaddons' 'libpwquality' 'mkinitcpio-openswap' 'polkit-qt6' 'rsync' 'qt6-declarative' 'solid' 'squashfs-tools' 'yaml-cpp')
makedepends=('boost' 'cmake' 'extra-cmake-modules' 'git' 'ninja' 'python-jsonschema' 'python-pyaml' 'python-unidecode' 'qt6-tools')

source=("calamares::https://github.com/erikdubois/calamares/archive/refs/tags/$pkgver-$pkgrel.tar.gz"
        "calamares_polkit")

prepare() {

	cp -rv ../modules/* ${srcdir}/$_pkgname-${pkgver}-${pkgrel}/src/modules/


	# patches here
	sed -i -e 's/"Install configuration files" OFF/"Install configuration files" ON/' "$srcdir/${_pkgname}-${pkgver}-${pkgrel}/CMakeLists.txt"
	sed -i -e "s/desired_size = 512 \* 1024 \* 1024  \# 512MiB/desired_size = 512 \* 1024 \* 1024 \* 4  \# 2048MiB/" "$srcdir/${_pkgname}-${pkgver}-${pkgrel}/src/modules/fstab/main.py"

	# change version
	cd ${_pkgname}-${pkgver}-${pkgrel}
	sed -i -e "s|CALAMARES_VERSION 3.3.5|CALAMARES_VERSION $pkgver-$pkgrel|g" CMakeLists.txt
	sed -i -e '/property real padding: 16;/d' "$srcdir/${_pkgname}-${pkgver}-${pkgrel}/src/qml/calamares-qt5/slideshow/Presentation.qml"
	sed -i -e '/property real padding: 16;/d' "$srcdir/${_pkgname}-${pkgver}-${pkgrel}/src/qml/calamares-qt6/slideshow/Presentation.qml"

}

build() {
        cd $_pkgname-$pkgver-${pkgrel}

   cmake -S . -Bbuild \
        -GNinja \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_INSTALL_LIBDIR=lib \
        -DWITH_APPSTREAM=OFF \
        -DWITH_PYBIND11=OFF \
        -DWITH_QT6=ON \
        -DSKIP_MODULES="dracut \
            dracutlukscfg \
            dummycpp \
            dummyprocess \
            dummypython \
            dummypythonqt \
            initramfs \
            initramfscfg \
            interactiveterminal \
            keyboardq \
            license \
            localeq \
            oemid \
            packagechooserq \
            partitionq \
            services-openrc \
            services-systemd \
            summaryq \
            tracking \
            usersq \
            welcomeq \
            zfs \
            zfshostid"

        cmake --build build
}

package() {
    install -Dm755 "../calamares_polkit" "$pkgdir/usr/bin/calamares_polkit"
	DESTDIR="${pkgdir}" cmake --build . --target install
	install -Dm644 "$srcdir/xerolinux.desktop" "$pkgdir/usr/share/applications/xerolinux.desktop"
	install -Dm644 "$srcdir/xerolinux-debug.desktop" "$pkgdir/usr/share/applications/xerolinux-debug.desktop"
	install -Dm755 "$srcdir/calamares_polkit" "$pkgdir/usr/bin/calamares_polkit"
	rm "$pkgdir/usr/share/applications/calamares.desktop"
}
sha256sums=('72089a695e64858156b9ef9bc9207079c5afc4f31752ff24456c4a2146261375'
            'e61245ff7e4c3af6f05a9fe9a3fcf47f2780d9aa88c11eab02a35fac446cf1e1')
