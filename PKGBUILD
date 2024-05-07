#https://github.com/calamares/calamares/releases
#change prepare number too

pkgname=calamares-app
_pkgname=calamares
pkgver=3.3.6
pkgrel=07
pkgdesc='Distribution-independent installer framework'
arch=('x86_64')
license=(GPL)
url="https://github.com/calamares/calamares"
license=('LGPL')
provides=('calamares')
depends=(
	'boost-libs'
	'ckbcomp'
	'cryptsetup'
	'doxygen'
	'efibootmgr'
	'gptfdisk'
	'gtk-update-icon-cache'
	'hwinfo'
	'icu'
	'kconfig'
	'kcoreaddons'
	'kcrash'
	'ki18n'
	'kparts'
	'kpmcore'
	'kservice'
	'kwidgetsaddons'
	'libpwquality'
	'mkinitcpio-openswap'
	'polkit-qt6'
	'rsync'
	'qt6-declarative'
	'solid'
	'squashfs-tools'
	'yaml-cpp'
)
makedepends=(
	'boost'
	'cmake'
	'extra-cmake-modules'
	'git'
	'ninja'
	'python-jsonschema'
	'python-pyaml'
	'python-unidecode'
	'qt6-tools'
)
backup=('usr/share/calamares/modules/bootloader.conf'
        'usr/share/calamares/modules/displaymanager.conf'
        'usr/share/calamares/modules/initcpio.conf'
        'usr/share/calamares/modules/unpackfs.conf')

source=($pkgname::git+$url
	"xero-cal.desktop"
	"xero-cal-debug.desktop"
	"calamares_polkit")

sha256sums=('SKIP'
            'SKIP'
            'SKIP'
            'SKIP')

prepare() {

	cp -rv ../modules/* ${srcdir}/$pkgname/src/modules/

	sed -i -e 's/"Install configuration files" OFF/"Install configuration files" ON/' "$srcdir/$pkgname/CMakeLists.txt"
	sed -i -e "s/desired_size = 512 \* 1024 \* 1024  \# 512MiB/desired_size = 512 \* 1024 \* 1024 \* 4  \# 2048MiB/" "$srcdir/$pkgname/src/modules/fstab/main.py"

	cd $pkgname
	sed -i -e "s|CALAMARES_VERSION 3.3.6|CALAMARES_VERSION $pkgver-$pkgrel|g" CMakeLists.txt
}

build() {
	cd $pkgname

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
            summaryq \
            tracking \
            welcomeq \
            zfs \
            zfshostid"

    cmake --build build
}

package() {
	cd $pkgname/build
	DESTDIR="${pkgdir}" cmake --build . --target install
	install -Dm644 "$srcdir/xero-cal.desktop" "$pkgdir/usr/share/applications/xero-cal.desktop"
	install -Dm644 "$srcdir/xero-cal.desktop" "$pkgdir/etc/xdg/autostart/xero-cal.desktop"
	install -Dm644 "$srcdir/xero-cal-debug.desktop" "$pkgdir/usr/share/applications/xero-cal-debug.desktop"
	install -Dm755 "$srcdir/calamares_polkit" "$pkgdir/usr/bin/calamares_polkit"
	rm "$pkgdir/usr/share/applications/calamares.desktop"
}
