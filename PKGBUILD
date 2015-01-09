# Maintainer1: Dave Higham <pepedog@archlinuxarm.org>
# Maintainer2: Kevin Mihelich <kevin@archlinuxarm.org>

# Cubox i.MX6 kernel and headers
#  - note: any other kernel packages should include headers for that march
#  - there will be no v7 kernel26 package, each march will be tagged individually

buildarch=4

pkgbase=linux-imx6-utilite-dt
pkgname=('linux-imx6-utilite-dt' 'linux-headers-imx6-utilite-dt')
# pkgname=linux-custom       # Build kernel with a different name
_commit=4f4c534c8e6eac1f7e5d5e91ed8c76f3537bd553
_srcname=linux-imx6-3.14-${_commit}
_kernelname=${pkgname#linux}
_basekernel=3.14
pkgver=${_basekernel}.27
pkgrel=1
cryptodev_commit=6aa62a2c320b04f55fdfe0ed015c3d9b48997239
arch=('arm')
url="http://www.kernel.org/"
license=('GPL2')
makedepends=('xmlto' 'docbook-xsl' 'kmod' 'inetutils' 'bc' 'uboot-mkimage' 'git' 'lzop')
options=('!strip')
source=("https://github.com/pepedog/linux-imx6-3.14/archive/${_commit}.tar.gz"
        "cryptodev.tar.gz::https://github.com/cryptodev-linux/cryptodev-linux/archive/${cryptodev_commit}.tar.gz"
        "git://git.code.sf.net/p/aufs/aufs3-standalone#branch=aufs${pkgver:0:4}.21+"
        '0001-Bluetooth-allocate-static-minor-for-vhci.patch'
        'config'
        '001_cec.patch'
        '002_cec.patch'
        '003_cec.patch')

md5sums=('b6912b4425acc4d32e0881cb64c206b3'
         'ddf7876487c876f6676ef0e050e9d204'
         'SKIP'
         '1b276abe16d14e133f3f28d9c9e6bd68'
         'f0f19aca7ae4ec88a495d622da3047a4'
         '8bf79a580704e8dab806f58043720a90'
         '6391a74bf1d451b74df6f189a25cf642'
         'a70798b63a0e7c3fb50a57ea1815d353')

prepare() {
  cd "${srcdir}/${_srcname}"


msg2 "Fix hci_vhci no minor warning"
  patch -Np1 -i ${srcdir}/0001-Bluetooth-allocate-static-minor-for-vhci.patch

msg2 "Copying aufs3 patches into the kernel source tree"
  cp -ru "${srcdir}/aufs3-standalone/Documentation" "${srcdir}/${_srcname}"
  cp -ru "${srcdir}/aufs3-standalone/fs" "${srcdir}/${_srcname}"
  cp -ru "${srcdir}/aufs3-standalone/include/uapi/linux/aufs_type.h" "${srcdir}/${_srcname}/include/linux/"
  cp -ru "${srcdir}/aufs3-standalone/include/uapi/linux/aufs_type.h" "${srcdir}/${_srcname}/include/uapi/linux/"

  #Copy Utilite dts
#  cp -ruv "${srcdir}/utilite-dtc/Makefile" "${srcdir}/${_srcname}/arch/arm/boot/dts/"
#  cp -ruv "${srcdir}/utilite-dtc/imx6q-cm.dtsi" "${srcdir}/${_srcname}/arch/arm/boot/dts/"
#  cp -ruv "${srcdir}/utilite-dtc/imx6q-sb-fx6x.dtsi" "${srcdir}/${_srcname}/arch/arm/boot/dts/"
#  cp -ruv "${srcdir}/utilite-dtc/imx6qdl-cm.dtsi" "${srcdir}/${_srcname}/arch/arm/boot/dts/"
#  cp -ruv "${srcdir}/utilite-dtc/imx6q-cm-fx6.dts" "${srcdir}/${_srcname}/arch/arm/boot/dts/"
#  cp -ruv "${srcdir}/utilite-dtc/imx6q-sb-fx6.dtsi" "${srcdir}/${_srcname}/arch/arm/boot/dts/"
#  cp -ruv "${srcdir}/utilite-dtc/imx6q-sbc-fx6.dts" "${srcdir}/${_srcname}/arch/arm/boot/dts/"
#  cp -ruv "${srcdir}/utilite-dtc/imx6q-cm-fx6.dtsi" "${srcdir}/${_srcname}/arch/arm/boot/dts/"
#  cp -ruv "${srcdir}/utilite-dtc/imx6q-sb-fx6m.dtsi" "${srcdir}/${_srcname}/arch/arm/boot/dts/"
#  cp -ruv "${srcdir}/utilite-dtc/imx6q-sbc-fx6m.dts" "${srcdir}/${_srcname}/arch/arm/boot/dts/"

msg2 "Applying aufs3 patches"
  patch -Np1 -i ../aufs3-standalone/aufs3-kbuild.patch
  patch -Np1 -i ../aufs3-standalone/aufs3-base.patch
  patch -Np1 -i ../aufs3-standalone/aufs3-mmap.patch
  patch -Np1 -i ../aufs3-standalone/aufs3-standalone.patch

#msg2 "CEC patches"
  patch -Np1 -i ${srcdir}/001_cec.patch
  patch -Np1 -i ${srcdir}/002_cec.patch
  patch -Np1 -i ${srcdir}/003_cec.patch

  cat "${srcdir}/config" > ./.config


  # add pkgrel to extraversion
  sed -ri "s|^(EXTRAVERSION =)(.*)|\1 \2-${pkgrel}|" Makefile

  # don't run depmod on 'make install'. We'll do this ourselves in packaging
  sed -i '2iexit 0' scripts/depmod.sh

}

build() {
  cd "${srcdir}/${_srcname}"
  LDFLAGS=""

  # get kernel version
  make prepare

  # load configuration
  # Configure the kernel. Replace the line below with one of your choice.
  #make menuconfig # CLI menu for configuration
  #make nconfig # new CLI menu for configuration
  #make xconfig # X-based configuration
  #make oldconfig # using old config from previous kernel version
  # ... or manually edit .config

  # Copy back our configuration (use with new kernel version)
  #cp ./.config ../${_basekernel}.config

  ####################
  # stop here
  # this is useful to configure the kernel
  #msg "Stopping build"
  #return 1
  ####################

  #yes "" | make config

  # build!
  make ${MAKEFLAGS} zImage modules imx6q-cm-fx6.dtb imx6q-sbc-fx6.dtb imx6q-sbc-fx6m.dtb imx6q-wandboard.dtb imx6q-cubox-i.dtb imx6dl-cubox-i.dtb imx6dl-hummingboard.dtb imx6q-hummingboard.dtb

  msg "Building cryptodev module"
  cd "${srcdir}/cryptodev-linux-${cryptodev_commit}"
  make KERNEL_DIR="${srcdir}/${_srcname}"
}

package_linux-imx6-utilite-dt() {
  pkgdesc="The Linux Kernel and modules - i.MX6 processors for all utilite-i"
  depends=('coreutils' 'linux-firmware' 'module-init-tools>=3.16' 'mkinitcpio>=0.7')
  optdepends=('crda: to set the correct wireless channels of your country')
  provides=('kernel26' "linux=${pkgver}" 'aufs_friendly' 'cryptodev_friendly')
  conflicts=('linux-trimslice' 'linux-omap')
  backup=("etc/mkinitcpio.d/${pkgname}.preset")
  install=${pkgname}.install

  cd "${srcdir}/${_srcname}"

  KARCH=arm

  # get kernel version
  _kernver="$(make kernelrelease)"

  mkdir -p "${pkgdir}"/{lib/modules,lib/firmware,boot}
  make INSTALL_MOD_PATH="${pkgdir}" modules_install
  cp arch/$KARCH/boot/zImage "${pkgdir}/boot/zImage"
  cp arch/$KARCH/boot/dts/*.dtb "${pkgdir}/boot"


  # set correct depmod command for install
  sed \
    -e  "s/KERNEL_NAME=.*/KERNEL_NAME=${_kernelname}/g" \
    -e  "s/KERNEL_VERSION=.*/KERNEL_VERSION=${_kernver}/g" \
    -i "${startdir}/${pkgname}.install"

  # remove build and source links
  rm -f "${pkgdir}"/lib/modules/${_kernver}/{source,build}
  # remove the firmware
  rm -rf "${pkgdir}/lib/firmware"
  # gzip -9 all modules to save 100MB of space
  find "${pkgdir}" -name '*.ko' |xargs -P 2 -n 1 gzip -9
  # make room for external modules
  ln -s "../extramodules-${_basekernel}-${_kernelname:-ARCH}" "${pkgdir}/lib/modules/${_kernver}/extramodules"
  # add real version for building modules and running depmod from post_install/upgrade
  mkdir -p "${pkgdir}/lib/modules/extramodules-${_basekernel}-${_kernelname:-ARCH}"
  echo "${_kernver}" > "${pkgdir}/lib/modules/extramodules-${_basekernel}-${_kernelname:-ARCH}/version"

  # install cryptodev module
  cd "${srcdir}/cryptodev-linux-${cryptodev_commit}"
  make -C "${srcdir}/${_srcname}" INSTALL_MOD_PATH="${pkgdir}" SUBDIRS=`pwd` modules_install

  cd "${srcdir}/${_srcname}"

  # Now we call depmod...
  depmod -b "$pkgdir" -F System.map "$_kernver"

  # move module tree /lib -> /usr/lib
  mkdir -p "${pkgdir}/usr"
  mv "$pkgdir/lib" "$pkgdir/usr"
}

package_linux-headers-imx6-utilite-dt() {
  pkgdesc="Header files and scripts for building modules for linux kernel - i.MX6 utilite-i"
  provides=("linux-headers=${pkgver}" "linux-headers-imx6-fsl=${pkgver}")
  conflicts=('linux-headers-omap' 'linux-headers-trimslice')

  install -dm755 "${pkgdir}/usr/lib/modules/${_kernver}"

  cd "${pkgdir}/usr/lib/modules/${_kernver}"
  ln -sf ../../../src/linux-${_kernver} build

  cd "${srcdir}/${_srcname}"

  install -D -m644 Makefile \
    "${pkgdir}/usr/src/linux-${_kernver}/Makefile"
  install -D -m644 kernel/Makefile \
    "${pkgdir}/usr/src/linux-${_kernver}/kernel/Makefile"
  install -D -m644 .config \
    "${pkgdir}/usr/src/linux-${_kernver}/.config"

  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/include"

  for i in acpi asm-generic config crypto drm generated linux math-emu \
    media net pcmcia scsi sound trace uapi video xen; do
    cp -a include/${i} "${pkgdir}/usr/src/linux-${_kernver}/include/"
  done

  # copy arch includes for external modules
  mkdir -p ${pkgdir}/usr/src/linux-${_kernver}/arch/$KARCH
  cp -a arch/$KARCH/include ${pkgdir}/usr/src/linux-${_kernver}/arch/$KARCH/
  mkdir -p ${pkgdir}/usr/src/linux-${_kernver}/arch/$KARCH/mach-imx
  cp -a arch/$KARCH/mach-imx/*.h ${pkgdir}/usr/src/linux-${_kernver}/arch/$KARCH/mach-imx/

  # copy files necessary for later builds, like nvidia and vmware
  cp Module.symvers "${pkgdir}/usr/src/linux-${_kernver}"
  cp -a scripts "${pkgdir}/usr/src/linux-${_kernver}"

  # fix permissions on scripts dir
  chmod og-w -R "${pkgdir}/usr/src/linux-${_kernver}/scripts"
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/.tmp_versions"

  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/arch/${KARCH}/kernel"

  cp arch/${KARCH}/Makefile "${pkgdir}/usr/src/linux-${_kernver}/arch/${KARCH}/"

  if [ "${CARCH}" = "i686" ]; then
    cp arch/${KARCH}/Makefile_32.cpu "${pkgdir}/usr/src/linux-${_kernver}/arch/${KARCH}/"
  fi

  cp arch/${KARCH}/kernel/asm-offsets.s "${pkgdir}/usr/src/linux-${_kernver}/arch/${KARCH}/kernel/"

  # add headers for lirc package
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/video"

  cp drivers/video/*.h  "${pkgdir}/usr/src/linux-${_kernver}/drivers/video/"

  for i in cpia2 pwc; do
    mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/usb/${i}"
    cp -a drivers/media/usb/${i}/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/usb/${i}"
  done

  # add docbook makefile
  install -D -m644 Documentation/DocBook/Makefile \
    "${pkgdir}/usr/src/linux-${_kernver}/Documentation/DocBook/Makefile"

  # add dm headers
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/md"
  cp drivers/md/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/md"

  # add inotify.h
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/include/linux"
  cp include/linux/inotify.h "${pkgdir}/usr/src/linux-${_kernver}/include/linux/"

  # add wireless headers
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/net/mac80211/"
  cp net/mac80211/*.h "${pkgdir}/usr/src/linux-${_kernver}/net/mac80211/"

  # add dvb headers for external modules
  # in reference to:
  # http://bugs.archlinux.org/task/9912
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb-core"
  cp drivers/media/dvb-core/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb-core/"
  # and...
  # http://bugs.archlinux.org/task/11194
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/include/config/dvb/"
  cp include/config/dvb/*.h "${pkgdir}/usr/src/linux-${_kernver}/include/config/dvb/"

  # add dvb headers
  # in reference to:
  # http://bugs.archlinux.org/task/20402
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/usb/dvb-usb"
  cp drivers/media/usb/dvb-usb/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/usb/dvb-usb/"
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb-frontends"
  cp drivers/media/dvb-frontends/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/dvb-frontends/"
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/tuners"
  cp drivers/media/tuners/*.h "${pkgdir}/usr/src/linux-${_kernver}/drivers/media/tuners/"

  # add xfs and shmem for aufs building
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/fs/xfs"
  mkdir -p "${pkgdir}/usr/src/linux-${_kernver}/mm"
  cp fs/xfs/xfs_sb.h "${pkgdir}/usr/src/linux-${_kernver}/fs/xfs/xfs_sb.h"

  #make uapi headers, some of them are needed for vpu/ipu usage
  mkdir -p "${srcdir}/headers"
  make headers_install ARCH=$KARCH INSTALL_HDR_PATH="${srcdir}/headers"


  # copy freescale specific headers to /usr/include/linux
  mkdir -p "${pkgdir}/usr/include/linux"
  for f in "$srcdir/headers/include/linux"/mxc*.h "$srcdir/headers/include/linux/ipu.h"; do
    cp "$f" "${pkgdir}/usr/include/linux"
  done

  # copy in Kconfig files
  for i in `find . -name "Kconfig*"`; do
    mkdir -p "${pkgdir}"/usr/src/linux-${_kernver}/`echo ${i} | sed 's|/Kconfig.*||'`
    cp ${i} "${pkgdir}/usr/src/linux-${_kernver}/${i}"
  done

  chown -R root.root "${pkgdir}/usr/src/linux-${_kernver}"
  find "${pkgdir}/usr/src/linux-${_kernver}" -type d -exec chmod 755 {} \;

  # strip scripts directory
  find "${pkgdir}/usr/src/linux-${_kernver}/scripts" -type f -perm -u+w 2>/dev/null | while read binary ; do
    case "$(file -bi "${binary}")" in
      *application/x-sharedlib*) # Libraries (.so)
        /usr/bin/strip ${STRIP_SHARED} "${binary}";;
      *application/x-archive*) # Libraries (.a)
        /usr/bin/strip ${STRIP_STATIC} "${binary}";;
      *application/x-executable*) # Binaries
        /usr/bin/strip ${STRIP_BINARIES} "${binary}";;
    esac
  done

  # remove unneeded architectures
  rm -rf "${pkgdir}"/usr/src/linux-${_kernver}/arch/{alpha,arm26,avr32,blackfin,cris,frv,h8300,ia64,m32r,m68k,m68knommu,mips,microblaze,mn10300,parisc,powerpc,ppc,s390,sh,sh64,sparc,sparc64,um,v850,x86,xtensa}

  # install cryptodev header
  cd "${srcdir}/cryptodev-linux-${cryptodev_commit}"
  install -D crypto/cryptodev.h "${pkgdir}/usr/src/linux-${_kernver}/crypto/cryptodev.h"
}

