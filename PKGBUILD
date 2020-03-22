# Maintainer: Doug Newgard <scimmia at archlinux dot org>
# Contributor: Maciej Sieczka <msieczka at sieczka dot org>
#              Sylvain POULAIN <sylvain dot poulain at giscan dot com>

pkgname=grass
pkgver=7.8.2
pkgrel=1
_shortver=${pkgver%.*}; _shortver=${_shortver/./}
pkgdesc='Geospatial data management and analysis, image processing, graphics/maps production, spatial modeling and visualization'
arch=('i686' 'x86_64')
url='http://grass.osgeo.org/'
license=('GPL')
depends=('blas' 'cairo' 'fftw' 'fontconfig' 'freetype2' 'gcc-libs' 'gdal' 'geos' 'glibc' 'glu' 'lapack' 'libpng'
         'libtiff' 'libx11' 'libgl' 'netcdf' 'pdal' 'proj' 'python-gdal' 'python-matplotlib' 'python-numpy'
         'python-pillow' 'readline' 'unixodbc' 'subversion' 'wxgtk' 'wxpython' 'zstd' 'zlib')
makedepends=('libxt')
optdepends=('postgresql: PostgreSQL database interface'
            'sqlite: SQLite database interface')
source=("http://grass.osgeo.org/grass$_shortver/source/$pkgname-$pkgver.tar.gz")
md5sums=('352acc82e0651b1f44628c865c89ba82')

build() {
  cd $pkgname-$pkgver

  # Ancient autoconf used upstream can't handle CPPFLAGS correctly, so set CPP to ignore warnings
  CPP="gcc -E -w" \
  ./configure \
    --prefix=/opt/$pkgname \
    --with-blas \
    --with-bzlib \
    --with-freetype-includes=/usr/include/freetype2 \
    --with-lapack \
    --with-wxwidgets \
    --with-readline \
    --with-pthread \
    --with-netcdf \
    --with-nls \
    --with-geos \
    --with-postgres \
    --with-openmp \
    --with-zstd \
    --with-pdal

  make
}

package() {
  cd $pkgname-$pkgver

  make exec_prefix="$pkgdir/usr" INST_DIR="$pkgdir/opt/$pkgname" install

  # Install linker config file, needed for qgis to find grass
  install -d "$pkgdir/etc/ld.so.conf.d/"
  echo "/opt/$pkgname/lib" > "$pkgdir/etc/ld.so.conf.d/$pkgname.conf"

  cd "$pkgdir/opt/$pkgname"

  # Fix for 3rd party python scripts
  # ln -s ../../../usr/bin/python2 bin/python

  # Put freedesktop.org files in correct location
  mv share "$pkgdir/usr"

  # Fix some paths that get hard coded by make install
  sed -i "s|$pkgdir||g" demolocation/.grassrc$_shortver \
                        include/Make/{Platform,Grass}.make \
                        etc/fontcap \
                        "$pkgdir/usr/bin/grass$_shortver"
}
