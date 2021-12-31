# Maintainer: Vladislav Nepogodin <nepogodin.vlad@gmail.com>

pkgname=contour-git
pkgver=0.2.1.r2091.0d4a358
pkgrel=1
pkgdesc="Modern C++ Terminal Emulator"
arch=(x86_64 aarch64)
url="https://github.com/contour-terminal/contour"
license=('Apache-2.0')
depends=('fontconfig')
makedepends=('cmake' 'extra-cmake-modules' 'git' 'microsoft-gsl' 'fmt' 'ninja' 'qt5-base' 'harfbuzz' 'fontconfig' 'catch2' 'range-v3' 'yaml-cpp')
source=("${pkgname}::git+https://github.com/contour-terminal/contour.git")
sha512sums=('SKIP')
provides=('contour')
conflicts=('contour')
options=(!strip)

pkgver() {
  cd "${srcdir}/${pkgname}"
  _pkgver="$(cat Changelog.md | grep '^###' | sed 's/### //g' | sed 's/ (.*//g' | head -1)"

  printf "${_pkgver}.r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
  _cpuCount=$(grep -c -w ^processor /proc/cpuinfo)

  CFLAGS=${CFLAGS/-Wp,-D_GLIBCXX_ASSERTIONS}
  CXXFLAGS=${CXXFLAGS/-Wp,-D_GLIBCXX_ASSERTIONS}

  # Fetch embedded dependencies
  PREPARE_ONLY_EMBEDS=ON "./${pkgname}/scripts/install-deps.sh"

  cmake -S"${pkgname}" -Bbuild \
        -GNinja \
        -DCMAKE_BUILD_TYPE=Release \
        -DYAML_BUILD_SHARED_LIBS=OFF -DYAML_CPP_BUILD_CONTRIB=OFF \
        -DYAML_CPP_BUILD_TESTS=OFF -DYAML_CPP_BUILD_TOOLS=OFF \
        -DYAML_CPP_INSTALL=OFF \
        -DCMAKE_INSTALL_PREFIX=/usr
  cmake --build build --parallel $_cpuCount
}

check() {
  cd "${srcdir}/build"
  ./src/contour/contour version
  ./src/crispy/crispy_test
  ./src/terminal/terminal_test
}

package() {
  cd "${srcdir}/build"
  DESTDIR="${pkgdir}" cmake --build . --target install

  cd "${srcdir}/${pkgname}"
  install -Dm644 LICENSE.txt "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}

# vim:set sw=2 sts=2 et:
