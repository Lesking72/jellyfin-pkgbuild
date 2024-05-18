# Maintainer: Les King <bGVza2luZzcyQGdtYWlsLmNvbQo=>
# Contributor: George Rawlinson <Z3Jhd2xpbnNvbkBhcmNobGludXgub3JnCg==>
# Contributor: Luca Weiss <bHVjYUB6M250dS54eXoK>
# Contributor: Maxime Gauduin <YWx1Y3J5ZEBhcmNobGludXgub3JnCg==>
# Contributor: Daniel Seymour <ZGFubnlzZWVsZXNzQGdtYWlsLmNvbQo=>

pkgbase=jellyfin
pkgname=(jellyfin-web jellyfin-server)
pkgver=10.9.2
pkgrel=1
arch=('x86_64')
_dotnet_runtime=linux-x64
url='https://jellyfin.org'
license=('GPL2')
makedepends=('dotnet-sdk>=8' 'npm')
source=("jellyfin-${pkgver}.tar.gz::https://github.com/jellyfin/jellyfin/archive/v${pkgver}.tar.gz"
        "jellyfin-web-${pkgver}.tar.gz::https://github.com/jellyfin/jellyfin-web/archive/v${pkgver}.tar.gz"
        'jellyfin.conf'
        'jellyfin.service'
        'jellyfin.sysusers'
        'jellyfin.tmpfiles')
sha512sums=('d921ac194b4e6f8040e9b7509d7adcc3765d4cfbae71daa10d7d646c863c393abbc87a491b5384533197478fe8b0401111a0f78586c55502abcf124079733345'
            'd1efa372ebe92ee2973bd8799c70f3195dd61f273fc125b03c6741c42aab0a528b54f32937dd4da61d14792541712b810cee457fde674d31b039c330b3201573'
            'c4aacfcf7966543e53c249a21e0135e3517fa3b2028d920328965d56d052619b51b49cf2c36c3f64ecef07ef6e93c40bb1c322fefb274b204aed1e70ee05e54a'
            '5fb8240ef2a553f76f50d8af44c8eba055c981417b517f187dbc950fa86592f1ca0814b8174f7f0bc8f827ba5020ce912651b81d4bf5d43d78f00a8a018e1fa4'
            '6fc2638e6ec4b1ee0240e17815c91107b694e5fde72c1bc7956c83067bbeacb632de899b86837e47a0ec04288131b15c20746373b45e0669c8976069a55d627a'
            '3e12ec3d3fcb15975d5f86bc3ce3363ae89b0e9e0b2580c29fc8a612c0220a74a067138b15c48ae27bb3c5777eca33055f10651949678a1ee7bd094293f6abb6')

build(){
  # Build jellyfin-web
  cd jellyfin-web-$pkgver

  npm ci --no-audit
  npm run build:production

  # Build jellyfin-server
  cd ../jellyfin-$pkgver

  # Disable dotnet telemetry
  export DOTNET_CLI_TELEMETRY_OPTOUT=1

  # Force dotnet to use 8.x when multiple SDKs are installed
  dotnet new globaljson --sdk-version 8.0.0 --roll-forward latestMinor --force

  dotnet publish --configuration Release Jellyfin.Server --output "$PWD"/publish --runtime "$_dotnet_runtime" -p:DebugSymbols=false -p:DebugType=none
  # Changed this to follow official pkg behavior because:
  # makepkg complains that the symbols contain $srcdir
  # No point having every possible linux-* runtime when only one gets used. Removes more bickering and saves 41MiB.
  # build and then publish doesn't look necessary.
}

package_jellyfin-server() {
  pkgdesc="Jellyfin server component"
  depends=('dotnet-runtime>=8' 'aspnet-runtime>=8' 'jellyfin-ffmpeg' 'sqlite')
  optdepends=('jellyfin-web: Jellyfin web client')
  backup=('etc/jellyfin/logging.json' 'etc/jellyfin/jellyfin.env')

  mkdir -p "$pkgdir"/usr/lib
  mkdir -p "$pkgdir"/usr/bin
  mkdir -p -m 750 "$pkgdir"/etc/jellyfin

  cp -dr --no-preserve='ownership' jellyfin-$pkgver/publish "$pkgdir"/usr/lib/jellyfin

  ln -sf /usr/lib/jellyfin/jellyfin "$pkgdir"/usr/bin/jellyfin
  chmod 755 "$pkgdir"/usr/lib/jellyfin/jellyfin

  install -Dm 644 jellyfin.service -t "$pkgdir"/usr/lib/systemd/system/
  install -Dm 644 jellyfin.sysusers "$pkgdir"/usr/lib/sysusers.d/jellyfin.conf
  install -Dm 644 jellyfin.tmpfiles "$pkgdir"/usr/lib/tmpfiles.d/jellyfin.conf
  install -Dm 644 jellyfin.conf "$pkgdir"/etc/jellyfin/jellyfin.env
  install -Dm 640 jellyfin-$pkgver/Jellyfin.Server/Resources/Configuration/logging.json -t "$pkgdir"/etc/jellyfin/
}

package_jellyfin-web() {
  pkgdesc="Jellyfin web client"
  mkdir -p "$pkgdir"/usr/lib/jellyfin
  cp -r jellyfin-web-$pkgver/dist "$pkgdir"/usr/lib/jellyfin/jellyfin-web
}
