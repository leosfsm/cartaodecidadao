# Maintainer: Bruno Silva <brunofernandes at ua dot pt>
# Co-Maintainer: Carlos Silva <r3pek@r3pek.org>

pkgname=cartaodecidadao
_pkgname=autenticacao.gov
pkgver=3.1.0
pkgrel=2
pkgdesc="Portuguese Citizen Card Application"
arch=('i686' 'x86_64')
url="http://www.cartaodecidadao.pt/"
license=('GPL2' 'LGPL3' 'custom:EUPL')
depends=('qt5-base'
         'qt5-tools'
         'qt5-quickcontrols'
				 'qt5-quickcontrols2'
         'qt5-graphicaleffects'
         'pcsclite>=1.5.0'
         'openssl-1.0'
         'ccid'
         'libzip'
         'poppler-qt5'
				 'libxerces-c-3.1'
				 'libcurl-compat'
         'xml-security-c')
makedepends=('swig' 'qconf' 'git' 'xml-security-c' 'jdk11-openjdk')
optdepends=('autenticacao-gov-pt: Necessário para autenticações online'
            'cartaodecidadao-pki: PKI que confirma a validade dos certificados dos CC'
            'ecce-gov-pt-certificates: Certificados da ECCE quem assina dos certificados contidos em cartaodecidadao-pki')
conflicts=('classpath' 'cartaodecidadao-bin')

source=('git+https://github.com/amagovpt/autenticacao.gov/#branch=openssl-migration' "00-fix_certs_path.patch" "01-fix_pteiddialogsQTsrv_path.patch")

sha512sums=('SKIP' 'd906f37508c02bc286498237d93db172ba15c1fcd9ec177c2cb72549fc4972ef12b54c41f40d2f192c3b5826409adf5c57882b94a1a20d559e9682265566b6ba' 'e13b2b0817a46d49eef67693c5753fee47eb9db1ef0fa9cb79afe09631be6fe15a94086891cc12758e80e0b4d9c3088d0d861c1ca11d2362b43bfad6eeb3961e')

prepare(){
	# Temporary Fix in order to compile with archlinux java handling neededs sudo and conflicts with GNU classpath
	sudo archlinux-java set java-11-openjdk
	sudo ln -sf /usr/lib/jvm/default/include/jni.h /usr/include/jni.h
	sudo ln -sf /usr/lib/jvm/default/include/linux/jni_md.h /usr/include/jni_md.h
    patch -p1 < ${srcdir}/00-fix_certs_path.patch
 	patch -p1 < ${srcdir}/01-fix_pteiddialogsQTsrv_path.patch
}

build() {
	cd ${srcdir}/${_pkgname}/pteid-mw-pt/_src/eidmw
		qmake pteid-mw.pro
		make
}

package() {
	cd ${srcdir}/${_pkgname}/pteid-mw-pt/_src/eidmw

	# Fix upstream bug not creating path
	mkdir -p ${pkgdir}/usr/local/lib/


	# Install programs and libraries
	make INSTALL_ROOT="$pkgdir" DESTDIR=$pkgdir PREFIX=/usr install
	
	# Fix library path from debian to Arch Linux
    mv ${pkgdir}/usr/local/* ${pkgdir}/usr/
    mkdir -p ${pkgdir}/usr/share/cartaodecidadao/
    mkdir -p ${pkgdir}/usr/share/cartaodecidadao/certs_test
    mv ${pkgdir}/usr/share/certs/ ${pkgdir}/usr/share/cartaodecidadao/
    rm -rf ${pkgdir}/usr/local/

	# Install desktop files
	install -Dm644 ${srcdir}/${_pkgname}/pteid-mw-pt/_src/eidmw/debian/pteid-mw-gui.desktop ${pkgdir}/usr/share/applications/pteid-mw-gui.desktop

	# Install image files
	install -Dm644 ${srcdir}/${_pkgname}/pteid-mw-pt/_src/eidmw/debian/pteid-signature.png ${pkgdir}/usr/share/pixmaps/pteid-signature.png
	install -Dm644 ${srcdir}/${_pkgname}/pteid-mw-pt/_src/eidmw/debian/pteid-scalable.svg ${pkgdir}/usr/share/icons/hicolor/scalable/apps/pteid-scalable.svg
}
