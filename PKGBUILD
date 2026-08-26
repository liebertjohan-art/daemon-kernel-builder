# Maintainer: Akash <liebertjohan-art>
# Kernel: Linux with BORE CPU scheduler, optimized for AMD Llano (K10.5) Dual-Core APUs

pkgbase=linux-daemon-llano
pkgver=6.12.10
pkgrel=1
pkgdesc='Linux Kernel tuned with BORE Scheduler & AMD K10.5 (-march=amdfam10) optimization for Daemon'
url='https://github.com/liebertjohan-art/daemon-kernel-builder'
arch=('x86_64')
license=('GPL-2.0-only')
makedepends=(
  'bc'
  'bison'
  'flex'
  'git'
  'kmod'
  'libelf'
  'openssl'
  'pahole'
  'perl'
  'python'
  'tar'
  'xz'
  'zstd'
)
options=('!strip')
_srcname="linux-${pkgver}"

source=(
  "https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-${pkgver}.tar.xz"
  "git+https://github.com/firelzrd/bore-scheduler.git"
  "config"
  "linux-daemon-llano.preset"
)
sha256sums=('SKIP' 'SKIP' 'SKIP' 'SKIP')

prepare() {
  cd "${srcdir}/${_srcname}"

  echo "Setting version..."
  echo "-daemon-llano" > localversion.10-pkgname

  echo "Applying BORE CPU Scheduler patch..."
  BORE_PATCH=$(find "${srcdir}/bore-scheduler" -name "*6.12.10*.patch" | head -n 1)
  if [ -z "$BORE_PATCH" ]; then
    BORE_PATCH=$(find "${srcdir}/bore-scheduler" -name "*.patch" | head -n 1)
  fi

  if [ -n "$BORE_PATCH" ]; then
    echo "Applying patch: $BORE_PATCH"
    patch -Np1 -i "$BORE_PATCH" || true
  fi

  echo "Configuring tailored AMD Llano K10 kernel options..."
  cp "${srcdir}/config" .config

  # Force compiler optimization flags in Makefile
  sed -i 's/-march=x86-64/-march=amdfam10 -O2/g' Makefile || true

  make olddefconfig
}

build() {
  cd "${srcdir}/${_srcname}"
  make -j$(nproc) all
}

package_linux-daemon-llano() {
  pkgdesc="The Linux kernel and modules (BORE + AMD Llano K10 tuned)"
  depends=('coreutils' 'kmod' 'initramfs')
  optdepends=('wireless-regdb: to set the correct wireless channels of your country'
              'linux-firmware: firmware images needed for some devices')
  provides=('VIRTUALBOX-GUEST-MODULES' 'WIREGUARD-MODULE')
  replaces=()

  cd "${srcdir}/${_srcname}"

  local modulesdir="${pkgdir}/usr/lib/modules/${pkgver}-daemon-llano"

  echo "Installing boot image..."
  install -Dm644 "$(make -s image_name)" "${modulesdir}/vmlinuz"

  # Used by mkinitcpio to name the kernel
  echo "${pkgver}-daemon-llano" | install -Dm644 /dev/stdin "${modulesdir}/pkgbase"

  echo "Installing modules..."
  make INSTALL_MOD_PATH="${pkgdir}/usr" INSTALL_MOD_STRIP=1 \
    DEPMOD=/doesnt/exist modules_install

  # remove build and source links
  rm -f "${modulesdir}"/{source,build}
}

package_linux-daemon-llano-headers() {
  pkgdesc="Headers and scripts for building modules for the linux-daemon-llano kernel"
  depends=('pahole')

  cd "${srcdir}/${_srcname}"
  local builddir="${pkgdir}/usr/lib/modules/${pkgver}-daemon-llano/build"

  echo "Installing build files..."
  install -Dt "${builddir}" -m644 .config Makefile Module.symvers System.map \
    localversion.* version vmlinux
  install -Dt "${builddir}/kernel" -m644 kernel/Makefile
  install -Dt "${builddir}/arch/x86" -m644 arch/x86/Makefile*
  install -Dt "${builddir}/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

  cp -t "${builddir}" -a include scripts

  echo "Installing arch headers..."
  cp -t "${builddir}/arch/x86" -a arch/x86/include
}

pkgname=("linux-daemon-llano" "linux-daemon-llano-headers")
