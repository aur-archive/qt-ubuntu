# Maintainer: Xiao-Long Chen <chenxiaolong@cxl.epac.to>
# Original Maintainer: Andrea Scarpino <andrea@archlinux.org>
# Contributor: Pierre Schmitz <pierre@archlinux.de>

# Cannot build if qt-ubuntu is installed
if $(pacman -Q qtwebkit &>/dev/null) && [ "x${IGNORE_NO_QTWEBKIT}" == 'x' ] && [ "x${UPDATE_SCRIPT}" == 'x' ]; then
  echo "" >&2
  error "qtwebkit must be temporarily removed before building."
  msg "Please run:"
  echo "" >&2
  echo "  pacman -Rdd qtwebkit" >&2
  echo "" >&2
  msg "and reinstalled after the build."
  echo "" >&2
  echo "(If for some reason, you need to override this message, run:)" >&2
  echo "" >&2
  echo "  IGNORE_NO_QTWEBKIT=yes makepkg" >&2
  exit 1
fi

# AUR fix
pkgname=qt-ubuntu
pkgdesc='A cross-platform application and UI framework'

pkgbase=qt-ubuntu
true && pkgname=('qt-ubuntu' 'qt-private-headers-ubuntu')
_ubuntu_rel=0ubuntu1
_actual_ver=4.8.4
_extra_ver=+dfsg
pkgver=${_actual_ver}.dfsg.${_ubuntu_rel}
pkgrel=100
arch=('i686' 'x86_64')
url='http://qt-project.org/'
license=('GPL3' 'LGPL')
makedepends=('libtiff' 'libpng' 'libmng' 'sqlite3' 'ca-certificates' 'dbus' 'fontconfig' 'libgl' 'libsm' 'libxrandr' 'libxv' 'libxi' 'alsa-lib' 'xdg-utils' 'hicolor-icon-theme' 'desktop-file-utils' 'mesa' 'postgresql-libs' 'mysql' 'unixodbc' 'cups' 'gtk2' 'libfbclient')
options=('!libtool')
_pkgfqn="${pkgbase%-*}-everywhere-opensource-src-${_actual_ver}"
source=("http://releases.qt-project.org/qt4/source/${_pkgfqn}.tar.gz"
        "https://launchpad.net/ubuntu/+archive/primary/+files/qt4-x11_${_actual_ver}${_extra_ver}-${_ubuntu_rel}.debian.tar.gz")
sha512sums=('472972b63b5cad83b4c4656b23e95b6d4923cc31a518138e25d5921982b14abc835883356c2b779a7972cb77cf73fe25b46630be7c6a86a917b364186aa17496'
            '84954b2dba277772a9801b4a5c8343c9360cafcbfdfe05de549a92da7b8b233fce786588cc0c7f927359f4742fec5a6a7cbaacc3efbc88661e5b1181d30763a9')

build() {
  cd "${srcdir}/${_pkgfqn}"

  # Apply Arch Linux patches
    # (FS#28381) (KDEBUG#180051)
      # Arch Linux: improve-cups-support.patch
      #  == Ubuntu: kubuntu_37_cups_settings.diff
    # (FS#29469)
      # Arch Linux: fix-crash-in-assistant.patch
      #  == Ubuntu: QTBUG-25324_assistant_segfault_on_start_with_gcc_4.7.patch

  # Apply Ubuntu patches
    # Disable patches (longest command you've ever seen? :D) (can't use
    # punctuation for comments or editors will completely screw up the
    # syntax highlighting)
      sed -i \
      `# Patch for adding '-qt4' to the end of the filenames of binaries` \
        -e '/01_debian_append_qt4_suffix.diff/d'                          \
      `# Multiarch patch for Debian/Ubuntu`                               \
        -e '/qt-multiarch-plugin-path-compat.diff/d'                      \
      `# Debian architecture detection {fails with:`                      \
      `# "Qt has not been ported to this architecture"}`                  \
        -e '/07_trust_dpkg-arch_over_uname-m.diff/d'                      \
      `# ARM v6 entry for previous patch`                                 \
        -e '/94_armv6_uname_entry.diff/d'                                 \
      `# Disable Qt webkit {exists in separate package}`                  \
        -e '/16_hide_std_symbols_on_qtwebkit.diff/d'                      \
        -e '/18_enable_qt3support_qtwebkit_debug_info.diff/d'             \
        -e '/30_webkit_unaligned_access.diff/d'                           \
        -e '/96_webkit_no_gc_sections.diff/d'                             \
        -e '/kubuntu_17_enable_qtwebkit_for_qtassistant.diff/d'           \
        -e '/kubuntu_34_no_gold_check.diff/d'                             \
      `# Fix FTBFS in buildd`                                             \
        -e '/11_build_translations.diff/d'                                \
      `# Disable enabling qvfb {does that make sense? :D} - it requires`  \
      `# the built-in Qt webkit`                                          \
        -e '/20_install_qvfb.diff/d'                                      \
      `# Debug packages arent used in Arch Linux - remove patch to`       \
      `# default to keeping debug symbols`                                \
        -e '/12_add_nostrip_for_debug_packages.diff/d'                    \
      `# Unsupported CPU architectures`                                   \
        -e '/add_missing_method_for_QBasicAtomicPointer_on_s390.patch/d'  \
        -e '/powerpc_designer_gstabs.diff/d'                              \
        -e '/kfreebsd_monotonic_clock.diff/d'                             \
        -e '/sh.diff/d'                                                   \
        -e '/powerpcspe.diff/d'                                           \
        -e '/99_hppa_bug561203_decrease_failure_rate.diff/d'              \
        -e '/92_armel_gcc43_valist_compat.diff/d'                         \
        -e '/80_hurd_max_path.diff/d'                                     \
        -e '/71_hppa_unaligned_access_fix_458133.diff/d'                  \
        -e '/70_hppa_ldcw_fix.diff/d'                                     \
        -e '/51_kfreebsd_strnstr_build_fix.diff/d'                        \
        -e '/50_kfreebsd_Q_OS.diff/d'                                     \
        -e '/41_disable_opengl_visibility.diff/d'                         \
        -e '/40_alpha_ice.diff/d'                                         \
        -e '/s390x_jscore.diff/d'                                         \
      `# Demos arent built on Arch Linux`                                 \
        -e '/buildable_appchooser_states_demos.patch/d'                   \
      `# This is not Debian or Ubuntu`                                    \
        -e '/08_configure_quilt_compat.diff/d'                            \
      `# Do not default to ibus`                                          \
        -e '/kubuntu_10_ibus_input_method.diff/d'                         \
      `# Tests arent built`                                               \
        -e '/10_config_tests_fixes.diff/d'                                \
      `# Not needed for rolling release distros`                          \
        -e '/23_permit_plugins_built_with_future_qt.diff/d'               \
      `# Do not hide stuff from the user`                                 \
        -e '/no_libicu_message.diff/d'                                    \
      \
      "${srcdir}/debian/patches/series"

  for i in $(cat "${srcdir}/debian/patches/series" | grep -v '#'); do
    msg "Applying patch: ${i}"
    patch -p1 -i "${srcdir}/debian/patches/${i}"
  done

  export QT4DIR="${srcdir}/${_pkgfqn}"
  export LD_LIBRARY_PATH="${QT4DIR}/lib:${LD_LIBRARY_PATH}"

  sed -i "s|-O2|${CXXFLAGS}|" mkspecs/common/{g++,gcc}-base.conf
  sed -i "/^QMAKE_LFLAGS_RPATH/s| -Wl,-rpath,||g" mkspecs/common/gcc-base-unix.conf
  sed -i "/^QMAKE_LFLAGS\s/s|+=|+= ${LDFLAGS}|g" mkspecs/common/gcc-base.conf

  # From debian/rules:
  # Remove include directory. Then ./configure will take care of calling
  # syncqt and regenerating it.
  rm -rvf include

  ./configure \
    `# License` \
    -confirm-license \
    -opensource \
    `# Paths and prefixes` \
    -datadir /usr/share/qt \
    -demosdir /usr/share/doc/qt/demos \
    -docdir /usr/share/doc/qt \
    -examplesdir /usr/share/doc/qt/examples \
    -importdir /usr/lib/qt/imports \
    -plugindir /usr/lib/qt/plugins \
    -prefix /usr \
    -sysconfdir /etc/xdg \
    -translationdir /usr/share/qt/translations \
    `# Enable features` \
    -plugin-sql-ibase \
    -plugin-sql-mysql \
    -plugin-sql-odbc \
    -plugin-sql-psql \
    -plugin-sql-sqlite \
    `# Disable features` \
    -no-openvg \
    -no-phonon \
    -no-phonon-backend \
    -no-webkit \
    `# Configuration options` \
    -graphicssystem raster \
    -optimized-qmake \
    -reduce-relocations \
    -system-sqlite \
    `# Build options` \
    -dbus-linked \
    -no-rpath \
    -nomake demos \
    -nomake docs \
    -nomake examples \
    -openssl-linked \
    -verbose

  make ${MAKEFLAGS}
}

package_qt-ubuntu() {
  pkgdesc='A cross-platform application and UI framework'
  depends=('libtiff' 'libpng' 'libmng' 'sqlite3' 'ca-certificates' 'glib2' 'dbus' 'fontconfig' 'libgl' 'libsm' 'libxrandr' 'libxv' 'libxi' 'alsa-lib' 'xdg-utils' 'hicolor-icon-theme' 'desktop-file-utils')
  conflicts=('qt')
  provides=("qt=${_actual_ver}")
  optdepends=('postgresql-libs: PostgreSQL driver'
              'libmysqlclient: MySQL driver'
              'unixodbc: ODBC driver'
              'libfbclient: Firebird/iBase driver'
              'libxinerama: Xinerama support'
              'libxcursor: Xcursor support'
              'libxfixes: Xfixes support'
              'icu: Unicode support')
  install='qt.install'

  cd "${srcdir}/${_pkgfqn}"
  make INSTALL_ROOT="${pkgdir}" install

  ### Tnstall desktop files and icons ###
  install -d -m755 "${pkgdir}/usr/share/applications/"

  # Qt Designer
  sed 's/-qt4//g' < "${srcdir}/debian/desktop/designer-qt4.desktop" \
                  > "${pkgdir}/usr/share/applications/designer.desktop"
  install -p -D -m644 tools/designer/src/designer/images/designer.png \
                    "${pkgdir}/usr/share/pixmaps/designer.png"
  # Qt Assistant
  sed 's/-qt4//g' < "${srcdir}/debian/desktop/assistant-qt4.desktop" \
                  > "${pkgdir}/usr/share/applications/assistant.desktop"
  install -p -D -m644 tools/assistant/tools/assistant/images/assistant-128.png \
                    "${pkgdir}/usr/share/pixmaps/assistant.png"
  # Qt Linguist
  sed 's/-qt4//g' < "${srcdir}/debian/desktop/linguist-qt4.desktop" \
                  > "${pkgdir}/usr/share/applications/linguist.desktop"
  install -p -D -m644 tools/linguist/linguist/images/icons/linguist-128-32.png \
                    "${pkgdir}/usr/share/pixmaps/linguist.png"
  # Qt Config
  sed 's/-qt4//g' < "${srcdir}/debian/desktop/qtconfig-qt4.desktop" \
                  > "${pkgdir}/usr/share/applications/qtconfig.desktop"
  install -p -D -m644 src/gui/dialogs/images/qtlogo-64.png \
                    "${pkgdir}/usr/share/pixmaps/qtconfig.png"

  ### Install manpages from Debian ###
  install -d -m755 "${pkgdir}/usr/share/man/man1/"
  for i in "${srcdir}"/debian/manpages/*.1; do
    FILENAME="${i##*/}"
    FILENAME="${FILENAME/-qt4/}"
    sed -e 's/-qt4//g' -e 's/-QT4//g' \
      < "${i}" \
      > "${pkgdir}/usr/share/man/man1/${FILENAME}"
  done

  ### Install license files ###
  install -D -m644 LGPL_EXCEPTION.txt \
    "${pkgdir}/usr/share/licenses/qt/LGPL_EXCEPTION.txt"

  ### Fix path in pkgconfig files ###
  find "${pkgdir}/usr/lib/pkgconfig" -type f -name '*.pc' -exec \
    perl -pi -e "s, -L${srcdir}/?\S+,,g" {} \;

  ### Fix path in prl files ###
  find "${pkgdir}/usr/lib" -type f -name '*.prl' -exec \
    sed -i -e '/^QMAKE_PRL_BUILD_DIR/d;s/\(QMAKE_PRL_LIBS =\).*/\1/' {} \;
}

package_qt-private-headers-ubuntu(){
  pkgdesc="Qt private headers for development"
  depends=("qt-ubuntu=${pkgver}")
  conflicts=('qt-private-headers')
  provides=("qt-private-headers=${_actual_ver}")

  install -d -m755 "${pkgdir}"/usr/include/{QtCore,QtDeclarative,QtGui,QtScript}
  install -d -m755 "${pkgdir}"/usr/src/{corelib,declarative,gui,script}
    
  for i in QtCore QtDeclarative QtGui QtScript; do
    cp -r "${srcdir}/${_pkgfqn}/include/${i}/private/" \
          "${pkgdir}/usr/include/${i}/"
  done

  for i in corelib declarative gui script; do
    cp -r "${srcdir}/${_pkgfqn}/src/${i}" "${pkgdir}/usr/src/"
  done
}

# vim:set ts=2 sw=2 et:
