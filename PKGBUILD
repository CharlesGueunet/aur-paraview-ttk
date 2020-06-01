# Maintainer : Charles Gueunet <Azrael>
# Contributor:  Oliver Goethel <deezy>
# Contributor: eolianoe eolianoe <eolianoe [at] gmail [DoT] com>
# Contributor: George Eleftheriou <eleftg>
# Contributor: Mathias Anselmann <mathias.anselmann@gmail.com>
# Contributor: Stéphane Gaudreault <stephane@archlinux.org>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: Michele Mocciola <mickele>
# Contributor: Simon Zilliken <simon____AT____zilliken____DOT____name>
# Contributor: chuckdaniels

# This package can take a long time to build.
# you should consider adding MAKEFLAGS='-j<nb threads>'
# in your /etc/makepkg.conf to allow parallel build

pkgname=('paraview-ttk')
_paraview_ver=5.8.0
pkgver=${_paraview_ver}
pkgrel=1
pkgdesc='Parallel Visualization Application patched for the Topology ToolKit (TTK)'
arch=('i686' 'x86_64')
url='http://www.paraview.org/'
license=('custom')
# BSD but certain code or section of code within Paraview
# repository have a special licenses:
# http://www.paraview.org/paraview-license/

depends=(
'boost' 'boost-libs' 'cgns' 'double-conversion' 'expat' 'ffmpeg' 'freetype2' 'eigen' 'gdal'
'glew' 'hdf5' 'intel-tbb' 'jsoncpp' 'libjpeg' 'libjsoncpp.so' 'libpng'
'libtiff' 'libxml2' 'netcdf' 'ospray' 'protobuf'
'python-mpi4py' 'python-numpy' 'qt5-svg' 'qt5-tools' 'qt5-x11extras'
'utf8cpp' 'zlib'
)
makedepends=('cmake' 'mesa' 'gcc-fortran' 'git')
conflict=('paraview', 'paraview-git')

source=("paraview-src::http://paraview.org/files/v${_paraview_ver:0:3}/ParaView-v${_paraview_ver}.tar.gz"
        "ttk-patch.tgz")
md5sums=('8151ed24eeeae5bc8ba038bb544eb409'
         '4c1e951ef170239ccdeee8a14ddbf92b')

prepare() {
    mkdir -p build

    pvpath=${srcdir}/ParaView-v${_paraview_ver}
    ttkpath=${srcdir}/ttk-patch

    # extract patch
    tar -xvzf ttk-patch.tgz

    # patch paraview with ttk
    cd "paraview/patch"
    ./patch-paraview-${_paraview_ver}.sh $pvpath
}

package() {


    msg "Build paraview..."

    mkdir -p "${srcdir}/build-paraview"
    cd "${srcdir}/build-paraview"

    msg2 "Configure"

    cmake ../ParaView-v${_paraview_ver} \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DPARAVIEW_ENABLE_FFMPEG=ON \
        -DPARAVIEW_ENABLE_GDAL=ON \
        -DPARAVIEW_USE_PYTHON=ON \
        -DPARAVIEW_USE_RAYTRACING=ON \
        -DPARAVIEW_BUILD_WITH_EXTERNAL=ON \
        -DVTK_SMP_IMPLEMENTATION_TYPE=TBB \
        -DPARAVIEW_USE_VTKM=OFF

    msg2 "Compilation"

    make ${MAKEFLAGS}

    msg2 "Install"

    DESTDIR="${pkgdir}" make install
    # Install license
    install -Dm644 "${srcdir}"/ParaView-v${_paraview_ver}/License_v1.2.txt "${pkgdir}"/usr/share/licenses/ParaView-v${_paraview_ver}/LICENSE

    msg "Build ttk..."
}

