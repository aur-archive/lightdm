#Mantainer: M0Rf30
#Contributor: ossfm
#Contributor: shosca
#Contributor: ondbalhaf
pkgname=lightdm
pkgver=1.5.1
pkgrel=1
pkgdesc="A lightweight display manager"
arch=('i686' 'x86_64')
url="https://launchpad.net/lightdm"
license=('GPL3' 'LGPL3')
source=("https://launchpad.net/lightdm/1.6/$pkgver/+download/$pkgname-$pkgver.tar.xz"
	lightdm.rc
	lightdm.service
	lightdm.tmpfiles
	xsession
	lightdm-autologin.pam
	lightdm.pam
	lightdm.rules
	lightdm-1.5.0-systemd_login1_power.patch
	lightdm-lock-screen-before-switch.patch)
depends=('dbus-glib' 'libxklavier')
options=(!libtool)
install=lightdm.install

optdepends=('xorg-server-xephyr: run lightdm in test mode'
	    'accountsservice: limit account shown by the greeter'
	    'lightdm-kde: Qt lightdm greeter'
	    'lightdm-gtk-greeter: You need this package to test the default greeter'
	    'lightdm-webkit-greeter-bzr: webkit lightdm greeter'
            'lightdm-crowd-greeter: 3d lightdm greeter'
	    'lightdm-pantheon-greeter: ElementaryOS greeter writtern in vala')

makedepends=('gobject-introspection' 'pkg-config' 'intltool' 'patch' 'itstool')

backup=(etc/apparmor.d/lightdm-guest-session
	etc/dbus-1/system.d/org.freedesktop.DisplayManager.conf
	etc/lightdm/keys.conf
	etc/lightdm/lightdm.conf
	etc/lightdm/users.conf
	etc/pam.d/lightdm)

build() {
  cd $srcdir/$pkgname-$pkgver

  patch -p1 -i ../lightdm-1.5.0-systemd_login1_power.patch
  patch -p1 -i ../lightdm-lock-screen-before-switch.patch

   ./configure --prefix=/usr \
     --sysconfdir=/etc --disable-static --libexecdir=/usr/lib/lightdm \
     --localstatedir=/var --with-greeter-user=lightdm \
     --with-greeter-session=lightdm-gtk-greeter --disable-tests
   make || return 1
}

package() {
  cd $srcdir/$pkgname-$pkgver
  make DESTDIR=$pkgdir install

# init services file 
  install -D -m755 ../lightdm.rc $pkgdir/etc/rc.d/lightdm
  install -D -m644 ../lightdm.service $pkgdir/usr/lib/systemd/system/lightdm.service
  install -D -m644 ../lightdm.tmpfiles $pkgdir/usr/lib/tmpfiles.d/lightdm.conf

# some tweaks  
  rm -rf $pkgdir/etc/init
  chmod +x ../xsession 
  install -D -m755 ../xsession $pkgdir/etc/lightdm
  sed -i -e "s|#run-directory=/var/run/lightdm|run-directory=/run/lightdm|g" $pkgdir/etc/lightdm/lightdm.conf
  sed -i -e "s|minimum-uid=500|minimum-uid=1000|g" $pkgdir/etc/lightdm/users.conf  
  sed -i -e "s|/usr/sbin/nologin|/sbin/nologin|g" $pkgdir/etc/lightdm/users.conf
  sed -i -e "s|#session-wrapper=lightdm-session|session-wrapper=/etc/lightdm/xsession|g" $pkgdir/etc/lightdm/lightdm.conf
  sed -i -e "s|#autologin-session=UNIMPLEMENTED|#autologin-session=UNIMPLEMENTED\n#pam-service=lightdm-autologin|g" $pkgdir/etc/lightdm/lightdm.conf
  install -d -m770 $pkgdir/run/lightdm

# Doing Autologin and security fixes
  cp ../lightdm-autologin.pam $pkgdir/etc/pam.d/lightdm-autologin
  cp ../lightdm.pam $pkgdir/etc/pam.d/lightdm
  sed 's#\[UserAccounts\]#\[UserList\]#g' -i $pkgdir/etc/lightdm/users.conf

  install -d -m700 $pkgdir/usr/share/polkit-1/rules.d/
  install -m644 ../lightdm.rules $pkgdir/usr/share/polkit-1/rules.d/lightdm.rules
}
md5sums=('e1b24757fdbd498af9e4af253f112754'
         '6699eb35f65ff498d1d05e6782f4f902'
         '737c6f27488517c6320bfc797954b400'
         'b1e1baf7351ff58c7b3b9b204472f6bb'
         '683bc8bc3f423157065dc6295f9fecef'
         '9e39da461e36f9d3fdd4447a80ebd878'
         'a5c60ec8739a698e4127b47ef417e517'
         '2a7326f4de1d949b8c96749b62cc5021'
         'da9ce685cd38df6b5891574aa3ecbb13'
         '43314fcf13397aaf2321a86d7ed9452c')
