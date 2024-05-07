#https://github.com/calamares/calamares/releases
#change prepare number too

pkgname=calamares-app
_pkgname=calamares
pkgver=3.3.6
pkgrel=07
pkgdesc='Distribution-independent installer framework'
arch=('i686' 'x86_64')
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

source=($pkgname::git+$url#commit=eb1e5a2
	"xero-cal.desktop"
	"calamares_polkit")

sha256sums=('b9be31fc297a18140e1493a3239c07f6f46c0af309fea1af72da393801efdb93'
            '434fae82a6d4ea39b9681e05f173395ddbe1a93fc46e43de79f33b7dbda286eb'
            'be188e6277023b3d26a53293b3a713293e41e3137c9fc6bee7eeaebc5102fc5b'
            '966c5f0834039dc6a7529e75f70417ba2510b1e643ffb49fd25855ce9dc244b4')

prepare() {

	cp -rv ../modules/* ${srcdir}/$pkgname/src/modules/

	sed -i -e 's/"Install configuration files" OFF/"Install configuration files" ON/' "$srcdir/$pkgname/CMakeLists.txt"
	sed -i -e "s/desired_size = 512 \* 1024 \* 1024  \# 512MiB/desired_size = 512 \* 1024 \* 1024 \* 4  \# 2048MiB/" "$srcdir/$pkgname/src/modules/fstab/main.py"

	cd $pkgname
	sed -i -e "s|CALAMARES_VERSION 3.3.6|CALAMARES_VERSION $pkgver-$pkgrel|g" CMakeLists.txt
	sed -i -e '/property real padding: 16;/d' "$srcdir/$pkgname/src/qml/calamares-qt5/slideshow/Presentation.qml"
	sed -i -e '/property real padding: 16;/d' "$srcdir/$pkgname/src/qml/calamares-qt6/slideshow/Presentation.qml"
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
	install -Dm644 "$srcdir/xero-cal.desktop" "$pkgdir/etc/xdg/autostart/xero-cal.desktop"
	install -Dm755 "$srcdir/calamares_polkit" "$pkgdir/usr/bin/calamares_polkit"
	rm "$pkgdir/usr/share/applications/calamares.desktop"
}
