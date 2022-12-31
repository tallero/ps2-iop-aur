# Maintainer: Pellegrino Prevete <pellegrinoprevete@gmail.com>

# shellcheck disable=SC2034
# _target="arm-none-eabi"
_module="iop"
_platform="ps2"
_bu="binutils-gdb"
_base="toolchain"
pkgbase="${_platform}-${_module}"
pkgname=("${pkgbase}-${_bu}"
         "${pkgbase}-gcc-stage1")
pkgver=v1.0
_bu_ver="v2.35.2"
_gcc_ver="v11.3.0"
pkgrel=1
_pkgdesc=("IOP compiler used in the creation of homebrew software "
          "for the Sony PlayStation® 2 videogame system.")
pkgdesc="${_pkgdesc[*]}"
arch=('x86_64')
license=('BSD')
_github="https://github.com/ps2dev"
_local="ssh://git@127.0.0.1:/home/git"
url="${_github}/${_platform}${_base}-${_module}"
checkdepends=('shellcheck')
makedepends=("libgmp-static"
             "mpfr-static"
             "libmpc-static"
             "gmp4-static"
             "gcc7"
             "zstd-static")
optdepends=()
_bu_branch="${_module}-${_bu_ver}"
_gcc_branch="${_module}-${_gcc_ver}"
_bu_commit="04d85a33e7dd7a10b937dca8855a526c4b92601a"
_gcc_commit="331453616ac96717cfef82d21c03573c8984f17d"
source=("${pkgbase}-${_bu}::git+${_github}/${_bu}#commit=${_bu_commit}"
        "${pkgbase}-gcc::git+${_github}/gcc#commit=${_gcc_commit}")
# source=("${pkgname}::git+${_local}/${_platform}-${_bu}#branch=${_bu_branch}")
#         "${pkgbase}-gcc::git+${_local}/${_platform}-gcc#commit=${_gcc_branch}")
sha256sums=('SKIP'
            'SKIP')

_n_cpu="$(getconf _NPROCESSORS_ONLN)"
_make_opts=(-j "${_n_cpu}")
_root="opt/ps2dev"
_usr="${_root}/${_module}"
_bin="${_usr}/bin"

build() {
  "build_${_platform}-${_module}-${_bu}"
  "build_${_platform}-${_module}-gcc-stage1"
}

# shellcheck disable=SC2154
build_ps2-iop-binutils-gdb() {
  local _target

  local _cflags=(-D_FORTIFY_SOURCE=0
                 -O2
                 -I/usr/include
                 -L/usr/lib
                 -Wno-implicit-function-declaration)

  local _ldflags=(${LDFLAGS}
                  -I/usr/include
                  -L/usr/lib
                  -lgmp
                  -lmpfr
                  -lmpc
                  -static
                  /usr/lib/libgmp4.a
                  /usr/lib/libgmpxx4.a
                  /usr/lib/libgmp.a
                  /usr/lib/libmpfr.a
                  /usr/lib/libmpc.a
                  -s)

  local _build_opts=(${_make_opts[@]}
                     CFLAGS="${_cflags[*]}"
                     CPPFLAGS="${_cflags[*]}"
                     LDFLAGS="${_ldflags[*]}")

  mkdir -p "${srcdir}/${_bu}-root"
  cd "${srcdir}/${pkgbase}-${_bu}"

  for _target in "mipsel-ps2-irx" "mipsel-ps2-elf"; do
    rm -rf "build-${_target}"
    mkdir -p "build-${_target}"
    cd "build-${_target}"
    local _configure_opts=(--prefix="/${_usr}"
                           --target="${_target}"
                           --disable-separate-code
                           --disable-sim
                           --with-gmp
                           --with-mpfr
                           --with-mpc
                           --disable-nls)

    LD_LIBRARY_PATH=/usr/lib \
    CC="/usr/bin/gcc" \
    CXX="/usr/bin/g++" \
    CFLAGS="${_cflags[*]}" \
    CXXFLAGS="${_cxxflags[*]}" \
    LDFLAGS="${_ldflags[*]}" \
    LIBS="${_libs[*]}" \
    "../configure" ${_configure_opts[@]}

    make "${_build_opts[@]}"
    make DESTDIR="${srcdir}/${_bu}-root" "${_make_opts[@]}" install
    
    cd ..
  done
}

# shellcheck disable=SC2154
package_ps2-iop-binutils-gdb() {
  local _target
  cd "${srcdir}/${pkgbase}-${_bu}"
  for _target in "mipsel-ps2-irx" "mipsel-ps2-elf"; do
    cd "build-${_target}"
    make DESTDIR="${pkgdir}" "${_make_opts[@]}" install-strip
    cd ..
  done
}

# shellcheck disable=SC2154
build_ps2-iop-gcc-stage1() {
  local _target
  local _tbu_bin
  local _bu_bin="${srcdir}/${_bu}-root/${_bin}"

  CFLAGS=""
  CXXFLAGS=""
  CPPFLAGS=""
  LDFLAGS=""
  export CFLAGS
  export CXXFLAGS
  export CPPFLAGS
  export LDFLAGS
  export PATH="${PATH}:${_bu_bin}"
  export LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:/usr/lib"

  local _cflags=(-D_FORTIFY_SOURCE=0
                 -O2
                 -Wno-implicit-function-declaration
                 -I/usr/include
                 -L/usr/lib
                 # -ldl
                 -static)

  local _ldflags=(-I/usr/include
                  # -rdynamic
                  -L/usr/lib
                  # -ldl
                  -Bstatic
                  -s)

  local _libs=(-L/usr/lib)
               # -ldl
               # /usr/lib/libmpc.so
               # /usr/lib/libmpfr.so
               # /usr/lib/libgmp.so)

  local _build_opts=(${_make_opts[@]}
                     CFLAGS="${_cflags[*]}"
                     CXXFLAGS="${_cflags[*]}"
                     CPPFLAGS="${_cflags[*]}"
                     LDFLAGS="${_ldflags[*]}"
                     LIBS="${_libs[*]}")

  cd "${srcdir}/${pkgbase}-gcc"

  for _target in "mipsel-ps2-irx" "mipsel-ps2-elf"; do
    _tbu_bin="${srcdir}/${_bu}-root/${_usr}/${_target}/bin"
    export PATH="${PATH}:${_tbu_bin}"
    rm -rf "build-${_target}"
    mkdir -p "build-${_target}"
    cd "build-${_target}"
    local _configure_opts=(--prefix="/${_usr}"
                           --target="${_target}"
                           --enable-languages="c"
                           --with-float=soft
                           --with-gmp
                           --with-mpfr
                           --with-mpc
                           --without-headers
                           --with-headers=no
                           --without-newlib
                           --without-cloog
                           --without-ppl
                           --disable-bootstrap
                           --disable-decimal-float
                           --disable-libada
                           --disable-libatomic
                           --disable-libffi
                           --disable-libgomp
                           --disable-libmudflap
                           --disable-libquadmath
                           --disable-libssp
                           --disable-libstdcxx-pch
                           --disable-multilib
                           --disable-shared
                           --disable-threads
                           --disable-target-libiberty
                           --disable-target-zlib
                           --disable-nls
                           --disable-tls)

    LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:/usr/lib" \
    CC="/usr/bin/gcc" \
    CXX="/usr/bin/g++" \
    CFLAGS="${_cflags[*]}" \
    CXXFLAGS="${_cxxflags[*]}" \
    LDFLAGS="${_ldflags[*]}" \
    LIBS="${_libs[*]}" \
    "../configure" ${_configure_opts[@]}

    LD_LIBRARY_PATH=/usr/lib \
    CC="/usr/bin/gcc" \
    CXX="/usr/bin/g++" \
    CFLAGS="${_cflags[*]}" \
    CXXFLAGS="${_cxxflags[*]}" \
    LDFLAGS="${_ldflags[*]}" \
    LIBS="${_libs[*]}" \
    make "${_build_opts[@]}" all
    
    cd ..
  done
}

# shellcheck disable=SC2154
package_ps2-iop-gcc-stage1() {
  local _target
  cd "${srcdir}/${pkgbase}-gcc"
  for _target in "mipsel-ps2-irx" "mipsel-ps2-elf"; do
    cd "build-${_target}"
    make DESTDIR="${pkgdir}" "${_make_opts}" install-strip
    cd ..
  done
}
