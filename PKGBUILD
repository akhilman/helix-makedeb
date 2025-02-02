# Maintainer: Ildar Akhmetgaleev <akhilmak@gmail.com>
# vim: set sw=2 expandtab:
# Based on Arch's PKGBUILD https://gitlab.archlinux.org/archlinux/packaging/packages/helix/-/blob/main/PKGBUILD?ref_type=heads

pkgname=helix
pkgver=25.01.1
pkgrel=1
pkgdesc='A post-modern modal text editor.'
arch=('amd64')
conflicts=('helix-bin')
license=('MPL-2.0')
url='https://helix-editor.com/'
depends=('libgcc-s1' 'libc6')
optdepends=('hicolor-icon-theme')
makedepends=('git')

source=("$pkgname-$pkgver.tar.gz::https://github.com/helix-editor/helix/archive/$pkgver.tar.gz"
        'rustup-init.sh::https://sh.rustup.rs')
b2sums=('b2f10bf6047877852c122a1146d0cdb57656a4a83c135a71389ad2105196ca8577afb91c935f1af57d16ca00cc4d595bcba33688b64faabf53e1c6cc5690dab0'
        '51fadcba5e3eb75cada1cadd82e9134b2ac2b3c13b2bce3be5322136a5db4cccf1dc82d658a04f175c1bc8160fb7413bf8f2886fc8a7abfd48a5db4eab68e38b')

prepare() {
  export RUSTUP_HOME=$srcdir/rustup
  export CARGO_HOME=$srcdir/cargo
  sh ./rustup-init.sh -qy --default-toolchain none
  . $CARGO_HOME/env

  cd "$pkgname-$pkgver"
  # NOTE: we are renaming hx to helix so there is no conflict with hex (providing hx)
  sed -i "s|hx|helix|g" contrib/completion/hx.*
  sed -i 's|hx|helix|g' contrib/Helix.desktop
  cargo fetch --locked --target "$(rustc -vV | sed -n 's/host: //p')"
}

build() {
  cd "$pkgname-$pkgver"
  cargo build --frozen --release
}

check() {
  cd "$pkgname-$pkgver"
  cargo test --frozen
}

package() {
  cd "$pkgname-$pkgver"
  install -Dm 755 "target/release/hx" "$pkgdir/usr/lib/$pkgname/hx"
  install -vdm 755 "$pkgdir/usr/bin"
  ln -sv /usr/lib/$pkgname/hx "$pkgdir/usr/bin/$pkgname"
  install -Dm 644 README.md -t "$pkgdir/usr/share/doc/$pkgname"

  local runtime_dir="$pkgdir/usr/lib/$pkgname/runtime"
  mkdir -p "$runtime_dir/grammars"
  cp -r "runtime/queries" "$runtime_dir"
  cp -r "runtime/themes" "$runtime_dir"
  find "runtime/grammars" -type f -name '*.so' -exec \
  install -Dm 755 {} -t "$runtime_dir/grammars" \;
  install -Dm 644 runtime/tutor -t "$runtime_dir"

  install -Dm 644 "contrib/completion/hx.bash" "$pkgdir/usr/share/bash-completion/completions/$pkgname"
  install -Dm 644 "contrib/completion/hx.fish" "$pkgdir/usr/share/fish/vendor_completions.d/$pkgname.fish"
  install -Dm 644 "contrib/completion/hx.zsh" "$pkgdir/usr/share/zsh/site-functions/_$pkgname"
  install -Dm 644 "contrib/Helix.desktop" "$pkgdir/usr/share/applications/$pkgname.desktop"
  install -Dm 644 "contrib/$pkgname.png" -t "$pkgdir/usr/share/icons/hicolor/256x256/apps"
}
