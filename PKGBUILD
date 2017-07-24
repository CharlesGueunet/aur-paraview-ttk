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
_paraview_ver=5.4.0
pkgver=${_paraview_ver}
pkgrel=1
pkgdesc='Parallel Visualization Application patched for the Topology ToolKit (TTK)'
arch=('i686' 'x86_64')
url='http://www.paraview.org/'
license=('custom')
# BSD but certain code or section of code within Paraview
# repository have a special licenses:
# http://www.paraview.org/paraview-license/

depends=('qt5-tools' 'qt5-x11extras'  'qt5-xmlpatterns'
         'openmpi' 'python-matplotlib' 'python-numpy' 'ffmpeg' 'boost' 'glew' 'openssl-1.0'
         'expat' 'freetype2' 'libjpeg' 'libxml2' 'libtheora' 'libpng' 'libtiff' 'zlib'
         )
makedepends=('cmake' 'mesa' 'gcc-fortran' 'git')

source=("paraview-src::http://paraview.org/files/v${_paraview_ver:0:3}/ParaView-v${_paraview_ver}.tar.gz"
        "ttk-patch.tgz")
sha1sums=('d1bc9112d76f603d3232069b4ea9c507c4e1b1a7'
          '085b3a94ffd19b80f15936d174cc0bf0e2d5f0f8')

prepare() {
    pvpath=${srcdir}/ParaView-v${_paraview_ver}
    ttkpath=${srcdir}/ttk-patch

    # extract patch
    tar -xvzf ttk-patch.tgz

    # patch paraview with ttk
    cd "$ttkpath/paraview/patch"
    ./patch-paraview-${_paraview_ver}.sh $pvpath
}

package() {

    mkdir -p "${srcdir}/build-paraview"
    cd "${srcdir}/build-paraview"

    # flags to enable system libs
    # add PROTOBUF when https://gitlab.kitware.com/paraview/paraview/issues/13656 gets fixed
    local VTK_USE_SYSTEM_LIB=""
    for lib in EXPAT FREETYPE GLEW HDF5 JPEG LIBXML2 OGGTHEORA PNG TIFF ZLIB; do
        VTK_USE_SYSTEM_LIB+="-DVTK_USE_SYSTEM_${lib}:BOOL=ON "
    done

    msg "Build paraview..."
    msg2 "Configuration"

    cmake \
        -DBUILD_DOCUMENTATION:BOOL=OFF \
        -DBUILD_EXAMPLES:BOOL=ON \
        -DBUILD_SHARED_LIBS:BOOL=ON \
        -DBUILD_TESTING:BOOL=OFF \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_C_COMPILER=mpicc \
        -DCMAKE_CXX_COMPILER=mpicxx \
        -DCMAKE_INSTALL_PREFIX:PATH=/usr \
        -DCMAKE_VERBOSE_MAKEFILE:BOOL=OFF \
        -DPARAVIEW_ENABLE_FFMPEG:BOOL=ON \
        -DPARAVIEW_ENABLE_MATPLOTLIB:BOOL=ON \
        -DPARAVIEW_ENABLE_PYTHON:BOOL=ON \
        -DPARAVIEW_INSTALL_DEVELOPMENT_FILES:BOOL=ON \
        -DPARAVIEW_QT_VERSION=5 \
        -DPARAVIEW_USE_MPI:BOOL=ON \
        -DVTK_PYTHON_VERSION=3 \
        -DVTK_QT_VERSION=5 \
        -DVTK_RENDERING_BACKEND:STRING=OpenGL2 \
        -DVTK_SMP_IMPLEMENTATION_TYPE:STRING=OpenMP \
        ${VTK_USE_SYSTEM_LIB} \
        ../ParaView-v${_paraview_ver}

    msg2 "Compilation"

    make DESTDIR="${pkgdir}" install

    msg2 "Finishing..."

    # Install license
    install -Dm644 "${srcdir}/ParaView-v${_paraview_ver}/License_v1.2.txt" "${pkgdir}/usr/share/licenses/paraview/LICENSE"

    # Remove IceT man pages to avoid conflicts
    if [ -d ${pkgdir}/usr/share/man/man3 ]; then
        rm -- "${pkgdir}/usr/share/man/man3/icet"*.3
    fi
}

