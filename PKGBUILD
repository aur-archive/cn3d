# Maintainer: leepesjee <lpeschier at xs4all dot nl>
pkgname=cn3d
pkgver=4.3
pkgrel=3
_ncbiver=12_0_0

pkgdesc="A visualization tool for biomolecular structures, sequences, and sequence alignments."
arch=('i686' 'x86_64')
license=('custom')
url=http://www.ncbi.nlm.nih.gov/Structure/CN3D/cn3d.shtml
depends=('gtk2' 'wxwidgets-ncbi' 'libpng' 'libjpeg-turbo' 'libtiff' 'desktop-file-utils' 'glu')
optdepends=('xdg-utils: launch a web-browser for on-line help')
install=$pkgname.install

source=(ftp://ftp.ncbi.nlm.nih.gov/toolbox/ncbi_tools++/ARCHIVE/12_0_0/ncbi_cxx--$_ncbiver.tar.gz
        cn3d_png.patch
        cn3d.lst
        cn3d.desktop
        ncbi.license)

md5sums=('eb90379fb6a35dd9e5c928b49c4d8a74'
         '5d564a47a5ed758e6c0c8ead510af813'
         '36ac838f449ed1939b5ae6cad8453484'
         'e8bf6ba0d335e27c312bc39ff7b485b9'
         '8cdf51908efacc8a8b055593fa063d09')

prepare() {
  patch -Np2 -i ../cn3d_png.patch
  cd ncbi_cxx--$_ncbiver
  # the data dir needs to be hardcoded (search routines don't seem to work properly)
  sed -i 's|wxString(GetDataDir().c_str()) + "bstdt.val"|"/usr/share/cn3d/data/bstdt.val"|' ./src/app/cn3d/cn3d_app.cpp  
  sed -i 's|wxString(GetProgramDir().c_str()) + "cn3d_commands.htb"|wxString("/usr/share/cn3d/cn3d_commands.htb")|' ./src/app/cn3d/structure_window.cpp
  # use a more appropriate name for user config data
  sed -i 's|"Cn3D_User"|".cn3d"|' ./src/app/cn3d/cn3d_tools.cpp
  # use xdg-open to launch the on-line help
  sed -i "s|netscape -noraise -remote 'openURL(<URL>,new-window)' \|\| netscape '<URL>'|xdg-open '<URL>'|" ./src/app/cn3d/cn3d_tools.cpp
}

build() {
  # configure using the cn3d.lst target list file
  cd ncbi_cxx--$_ncbiver
  ./configure --with-wxwidgets=/opt/ncbi/wxwidgets-ncbi --without-debug --with-projects=../../cn3d.lst
  # make the project
  cd ./GCC*Release64/build && make all_p
  # for reasons unclear to me the app needs a separate build line (this wasn't in previous versions):
  cd ./app/cn3d && make LDFLAGS="-lGL -lGLU -lX11 -ldl"
}
  
package() {
  install -dv -m755 $pkgdir/usr/bin
  install -m755 $srcdir/ncbi_cxx*/GCC*Release64/bin/$pkgname $pkgdir/usr/bin
  install -dv -m755 $pkgdir/usr/share/cn3d/data
  install -m644 $srcdir/ncbi_cxx*/src/app/$pkgname/data/* $pkgdir/usr/share/cn3d/data
  install -m644 $srcdir/ncbi_cxx*/src/app/$pkgname/HelpBlocks/Cn3D_Commands.htb $pkgdir/usr/share/cn3d/cn3d_commands.htb

# Install license
  install -dv -m755 $pkgdir/usr/share/licenses/$pkgname
  install -m644 ncbi.license $pkgdir/usr/share/licenses/$pkgname

# install icon
  install -m755 -d $pkgdir/usr/share/pixmaps
  install -m644 $srcdir/ncbi_cxx*/src/app/$pkgname/cn3d42App.xpm $pkgdir/usr/share/pixmaps
  install -m755 -d $pkgdir/usr/share/applications
  install -m644 $pkgname.desktop $pkgdir/usr/share/applications/
}
