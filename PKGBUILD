# Maintainer: you

pkgname=joplin-desktop-bin
_upstream_pkgname=Joplin
pkgver=3.6.16
pkgrel=1
pkgdesc='Joplin desktop repackaged from the official upstream Debian release'
arch=('x86_64')
url='https://joplinapp.org/'
license=('AGPL-3.0-or-later')
depends=(
  'alsa-lib'
  'at-spi2-core'
  'cups'
  'gtk3'
  'libnotify'
  'libsecret'
  'libxss'
  'libxtst'
  'nss'
  'util-linux-libs'
  'xdg-utils'
)
optdepends=('libappindicator: tray icon support')
provides=('joplin-desktop')
conflicts=('joplin-desktop' 'joplin-bin' 'joplin-appimage' 'joplin-beta' 'joplin-beta-bin')
options=('!strip')
source_x86_64=(
  "${_upstream_pkgname}-${pkgver}.deb::https://github.com/laurent22/joplin/releases/download/v${pkgver}/${_upstream_pkgname}-${pkgver}.deb"
  "LICENSE::https://raw.githubusercontent.com/laurent22/joplin/v${pkgver}/LICENSE"
)
noextract_x86_64=("${_upstream_pkgname}-${pkgver}.deb")
sha256sums_x86_64=(
  '1b7f42d2ee978b1adf5cd95b8880b48769f42e3135076632392192022e300e7d'
  'f6b29267fe2c4251ed95cf9c846b47774a970c5960be9eb8a409f21ffd98425e'
)

package() {
  local _workdir="${srcdir}/deb-extract"

  rm -rf "${_workdir}"
  mkdir -p "${_workdir}"
  cd "${_workdir}"

  ar x "${srcdir}/${_upstream_pkgname}-${pkgver}.deb"
  bsdtar -xf data.tar.xz -C "${pkgdir}"

  install -dm755 "${pkgdir}/usr/bin"
  ln -sf /opt/Joplin/joplin "${pkgdir}/usr/bin/joplin-desktop"

  sed -i 's|Exec=/opt/Joplin/joplin|Exec=/usr/bin/joplin-desktop|' \
    "${pkgdir}/usr/share/applications/joplin.desktop"

  rm -f "${pkgdir}/opt/Joplin/resources/app-update.yml"

  install -Dm644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
