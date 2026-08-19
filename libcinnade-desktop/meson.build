dbus_xrandr_built_sources = gnome.gdbus_codegen('meta-dbus-xrandr',
  'xrandr.xml',
  namespace: 'MetaDBus',
  interface_prefix: 'org.cinnade.MythicWM'
)

dbus_idle_built_sources = gnome.gdbus_codegen('meta-dbus-idle-monitor',
  'idle-monitor.xml',
  namespace: 'MetaDBus',
  interface_prefix: 'org.cinnade.MythicWM',
  object_manager: true
)

libcinnade_desktop_gir_sources = [
  'display-name.c',
  'edid-parse.c',
  'gnome-bg-crossfade.c',
  'gnome-bg.c',
  'gnome-desktop-thumbnail.c',
  'gnome-desktop-utils.c',
  'gnome-gettext-portable.c',
  'gnome-idle-monitor.c',
  'gnome-languages.c',
  'gnome-pnp-ids.c',
  'gnome-rr-config.c',
  'gnome-rr-labeler.c',
  'gnome-rr-output-info.c',
  'gnome-rr.c',
  'gnome-systemd.c',
  'gnome-thumbnail-pixbuf-utils.c',
  'gnome-wall-clock.c',
  'gnome-xkb-info.c',
]

libcinnade_desktop_misc_headers = [
  'gnome-datetime-source.h',
  'gnome-rr-private.h',
  'edid.h',
  'private.h'
]

libcinnade_desktop_sources = [
  libcinnade_desktop_gir_sources,
  libcinnade_desktop_misc_headers,
  'gnome-datetime-source.c',
]

cdesktop_headers = [
  'cdesktop-enums.h',
]
libcinnade_desktop_headers = [
  'gnome-bg-crossfade.h',
  'gnome-bg.h',
  'gnome-desktop-thumbnail.h',
  'gnome-desktop-utils.h',
  'gnome-idle-monitor.h',
  'gnome-languages.h',
  'gnome-pnp-ids.h',
  'gnome-rr-config.h',
  'gnome-rr-labeler.h',
  'gnome-rr.h',
  'gnome-systemd.h',
  'gnome-wall-clock.h',
  'gnome-xkb-info.h',
]

libcinnade_desktop = shared_library('cinnade-desktop',
  [
    libcinnade_desktop_sources,
    dbus_idle_built_sources,
    dbus_xrandr_built_sources
  ],
  include_directories: [ rootInclude ],
  c_args: [
    '-DG_LOG_DOMAIN="CinnadeDesktop"',
    '-DXKB_BASE="@0@"'.format(xkb_base),
  ],
  dependencies: cinnade_deps,
  install: true,
  version: LT_VERSION,
)

install_headers(
  libcinnade_desktop_headers + cdesktop_headers,
  subdir: join_paths('cinnade-desktop', 'libcinnade-desktop')
)

pkgconfig.generate(
  name: 'cinnade-desktop',
  description: 'Utility library for loading .desktop files',
  version: meson.project_version(),
  requires: [ 'gtk+-3.0', ],
  requires_private: [ 'xkbfile', ],
  libraries: libcinnade_desktop,
  subdirs: join_paths('cinnade-desktop'),
  variables: [ 'exec_prefix=${prefix}'],
)

cdesktop_gir = gnome.generate_gir(libcinnade_desktop,
  sources: 'cdesktop-enums.h',
  namespace: 'CDesktopEnums',
  nsversion: '3.0',
  identifier_prefix: 'CDesktop',
  symbol_prefix: 'c_desktop',
  extra_args: [
    '-DGNOME_DESKTOP_USE_UNSTABLE_API',
  ],
  install: true,
)

gnome.generate_gir(libcinnade_desktop,
  sources: libcinnade_desktop_gir_sources + libcinnade_desktop_headers,
  namespace: 'CinnadeDesktop',
  nsversion: '3.0',
  export_packages: 'cinnade-desktop',
  identifier_prefix: 'Gnome',
  symbol_prefix: 'gnome_',
  includes: ['GObject-2.0', 'Gtk-3.0', cdesktop_gir[0], ],
  extra_args: [
    '-DGNOME_DESKTOP_USE_UNSTABLE_API',
  ],
  install: true,
)

# FIXME
# https://github.com/mesonbuild/meson/issues/1687
custom_target('gsettings-enums',
  input : libcinnade_desktop_headers + libcinnade_desktop_misc_headers + cdesktop_headers,
  output: 'org.cinnade.desktop.enums.xml',
  capture: true,
  command: ['glib-mkenums',
    '--comments', '<!-- @comment@ -->',
    '--fhead', '<schemalist>',
    '--vhead', '  <@type@ id=\'org.cinnade.desktop.@EnumName@\'>',
    '--vprod', '    <value nick=\'@valuenick@\' value=\'@valuenum@\'/>',
    '--vtail', '  </@type@>',
    '--ftail', '</schemalist>',
    '@INPUT@'
  ],
  install: true,
  install_dir: join_paths(get_option('datadir'), 'glib-2.0', 'schemas'),
)
