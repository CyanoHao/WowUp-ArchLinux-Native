# Maintainer: Cyano Hao <c@cyano.cn>

_pkgname=WowUp
pkgname=${_pkgname,,}-native
_pkgver=2.7.1
pkgver=${_pkgver/-/.}
pkgrel=1
pkgdesc='WowUp the World of Warcraft addon updater (system Electron)'
arch=('x86_64')
# _electronver=$(</usr/lib/electron/version)
_electronver=18.2.0 # 18.2.1 missing from npmjs.com

url='https://github.com/WowUp/WowUp'
license=('GPL3')
depends=(
    'electron'
)
makedepends=(
    'git'
    'nodejs-lts-gallium' # may fail with latest nodejs, use lts
    'npm'
    'asar'
    'imagemagick'
)
source=(
    "git+$url.git?v${_pkgver}"
    wowup-native.desktop
    run_wowup-native.sh
)
sha256sums=('SKIP'
            '76ebf12e022e15075a6a3824731a8288acbc6a4e1f69f6bd0fa3591d6f658656'
            '96b62f48ab60f289a375b93eef8ccbd67be818e1043f450da706894b2c958356')

prepare() {
    # set legacy peer deps in .npmrc file to dependency conflict since npm 7
    echo "legacy-peer-deps=true" >>"$_pkgname/wowup-electron/.npmrc"
}

build() {
    cd "$_pkgname/wowup-electron"

    # Angular may ask for sharing anonymous usage data during `npm install`.
    # Say “no” to it.
    # npm install electron@$_electronver <<<"N"

    # or use miorrors
    export ELECTRON_MIRROR="https://npmmirror.com/mirrors/electron/"
    npm --registry https://registry.npmmirror.com/ install electron@$_electronver <<<"N"

    npm run build:prod
    ./node_modules/.bin/electron-builder --linux dir -c.electronDist=/usr/lib/electron -c.electronVersion=$_electronver
}

package() {
    install -DTm755 run_wowup-native.sh "$pkgdir/usr/bin/$pkgname"
    install -Dm644 wowup-native.desktop -t "$pkgdir/usr/share/applications/"

    _dest="$pkgdir/usr/lib/$pkgname"
    asar e "$srcdir/$_pkgname/wowup-electron/release/linux-unpacked/resources/app.asar" "$_dest"
    rm -r "$_dest"/node_modules/{@angular,@microsoft}

    cd "$srcdir/$_pkgname/wowup-electron/"
    install -Dm644 assets/wowup_logo_512np.png "$pkgdir/usr/share/icons/hicolor/512x512/apps/$pkgname.png"
    for size in 16 24 32 48 64 72 128 256; do
        target="$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps"
        mkdir -p $target
        convert assets/wowup_logo_512np.png -resize ${size}x${size} "$target/$pkgname.png"
    done
}
