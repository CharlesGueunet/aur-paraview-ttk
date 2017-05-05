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
# you should consider adding -j<nb threads>
# in your /etc/makepkg.conf to allow parallel build

# TODO extern VTK

pkgbase=paraview
pkgname=('paraview' 'vtk' 'ttk')
_paraview_ver=5.3.0
_ttk_ver=0.9.1
pkgver=${_paraview_ver}
pkgrel=1
pkgdesc='Parallel Visualization Application using the Topology ToolKit (TTK)'
arch=('i686' 'x86_64')

depends=('qt5-tools' 'qt5-x11extras'  'qt5-xmlpatterns'
         'openmpi' 'python-matplotlib' 'python-numpy' 'ffmpeg' 'boost' 'glew' 'openssl-1.0'
         'expat' 'freetype2' 'libjpeg' 'libxml2' 'libtheora' 'libpng' 'libtiff' 'zlib'
         )
makedepends=('cmake' 'mesa' 'gcc-fortran' 'git')
provides=('paraview' 'vtk' 'ttk')

install=ttk.install

source=("paraview::http://paraview.org/files/v${_paraview_ver:0:3}/ParaView-v${_paraview_ver}.tar.gz"
        "ttk::https://topology-tool-kit.github.io/stuff/ttk-${_ttk_ver}.tar.gz"
        'paraview-desktop.patch')
sha1sums=('c8a31039b189e63b20618bbfa91e89555ce62b6d'
          'ea0b1eb37a3e326cee6de8e694a64168c1f91134'
          'd7da23daca34cd015294c4d2f702cdc4a81f0853')

prepare() {
    pvpath=${srcdir}/ParaView-v${_paraview_ver}
    ttkpath=${srcdir}/ttk-${_ttk_ver}
    # patch paraview desktop
    cd "$pvpath"
    patch -p1 -i ../paraview-desktop.patch

    # patch paraview with ttk
    cd "$ttkpath/paraview/patch"
    ./patch-paraview-${_paraview_ver}.sh $pvpath
}

package_paraview() {
    pkgdesc='Parallel Visualization Application with VTK'
    url='http://www.paraview.org/'
    license=('custom')
    # BSD but certain code or section of code within Paraview
    # repository have a special licenses:
    # http://www.paraview.org/paraview-license/

    mkdir -p "${srcdir}/build-paraview"
    cd "${srcdir}/build-paraview"

    # flags to enable system libs
    # add PROTOBUF when https://gitlab.kitware.com/paraview/paraview/issues/13656 gets fixed
    local VTK_USE_SYSTEM_LIB=""
    for lib in EXPAT FREETYPE GLEW HDF5 JPEG LIBXML2 OGGTHEORA PNG TIFF ZLIB; do
        VTK_USE_SYSTEM_LIB+="-DVTK_USE_SYSTEM_${lib}:BOOL=ON "
    done

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
        -DPARAVIEW_USE_VISITBRIDGE:BOOL=ON \
        -DVTK_PYTHON_VERSION=3 \
        -DVTK_QT_VERSION=5 \
        -DVTK_RENDERING_BACKEND:STRING=OpenGL2 \
        -DVTK_SMP_IMPLEMENTATION_TYPE:STRING=OpenMP \
        ${VTK_USE_SYSTEM_LIB} \
        ../ParaView-v${_paraview_ver}

    make DESTDIR="${pkgdir}" install

    # Install license
    install -Dm644 "${srcdir}/ParaView-v${_paraview_ver}/License_v1.2.txt" "${pkgdir}/usr/share/licenses/paraview/LICENSE"

    # Remove IceT man pages to avoid conflicts
    # rm -- "${pkgdir}/usr/share/man/man3/icet"*.3
}

package_vtk() {
    pkgdesc='the Visual ToolKit: Scientific Visualization Library in C++.'
    url='http://www.vtk.org/'
    license=('bsd')

    mkdir -p "${srcdir}/build-vtk"
    cd "${srcdir}/build-vtk"

    # flags to enable system libs
    # add PROTOBUF when https://gitlab.kitware.com/paraview/paraview/issues/13656 gets fixed
    local VTK_USE_SYSTEM_LIB=""
    for lib in EXPAT FREETYPE GLEW HDF5 JPEG LIBXML2 OGGTHEORA PNG TIFF ZLIB; do
        VTK_USE_SYSTEM_LIB+="-DVTK_USE_SYSTEM_${lib}:BOOL=ON "
    done

    cmake \
        -DBUILD_DOCUMENTATION:BOOL=OFF \
        -DBUILD_EXAMPLES:BOOL=ON \
        -DBUILD_SHARED_LIBS:BOOL=ON \
        -DBUILD_TESTING:BOOL=OFF \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_C_COMPILER=mpicc \
        -DCMAKE_CXX_COMPILER=mpicxx \
        -DCMAKE_INSTALL_PREFIX:PATH=/usr/lib \
        -DCMAKE_VERBOSE_MAKEFILE:BOOL=OFF \
        -DVTK_PYTHON_VERSION=3 \
        -DVTK_QT_VERSION=5 \
        -DVTK_RENDERING_BACKEND:STRING=OpenGL2 \
        -DVTK_SMP_IMPLEMENTATION_TYPE:STRING=OpenMP \
        ${VTK_USE_SYSTEM_LIB} \
        ../ParaView-v${_paraview_ver}/VTK

    # Direct install
    make DESTDIR="${pkgdir}" install
}

package_ttk() {
    pkgdesc='the Topology ToolKit: plugins suite for Paraview and VTK.'
    depends=('vtk' 'paraview')
    url='https://topology-tool-kit.github.io/'
    license=('bsd')

    mkdir -p "${srcdir}/build-ttk"
    cd "${srcdir}/build-ttk"

    # plugins
    ttk_suffix="/usr/lib/ttk"
    plugins_suffix="${ttk_suffix}/plugins"
    plugins_path="${pkgdir}${plugins_suffix}"

    cmake \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_C_COMPILER=mpicc \
        -DCMAKE_CXX_COMPILER=mpicxx \
        -DCMAKE_VERBOSE_MAKEFILE:BOOL=OFF \
        -DCMAKE_INSTALL_PREFIX:PATH=${ttk_suffix} \
        -DParaView_DIR="${srcdir}/build-paraview" \
        -DVTK_DIR="${srcdir}/build-vtk" \
        -DwithVTK=1 \
        -DwithVTKGUI=1 \
        -DwithVTKCMD=1 \
        ../ttk-${_ttk_ver}

    make DESTDIR="${plugins_path}" install

    # add plugins location for paraview
    ttk_plugins="/lib/paraview-${_paraview_ver%\.[0-9]}/plugins"
    env_path="${pkgdir}/etc/profile.d"
    mkdir -p ${env_path}
    echo "export PV_PLUGIN_PATH='${plugins_suffix}${ttk_plugins}'" > "${env_path}/ttk.sh"
}
