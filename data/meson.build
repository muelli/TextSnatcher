# Build and install rasterized PNG icons from the scalable SVG source

icon_sizes = ['16', '24', '32', '48', '64', '128']

# Detect a converter program (prefer rsvg-convert, then inkscape, then ImageMagick 'magick')
rsvg_convert = find_program('rsvg-convert', required: false)
inkscape_prog = find_program('inkscape', required: false)
magick_prog  = find_program('magick', required: false)  # ImageMagick 7 'magick' front-end

if not rsvg_convert.found() and not inkscape_prog.found() and not magick_prog.found()
  error('Need one of: rsvg-convert, inkscape, or ImageMagick (magick) to render PNG icons.')
endif

project_name = meson.project_name()

foreach s : icon_sizes
  svg_path = join_paths('icons', s, project_name + '.svg')
  normal_png   = project_name + '-' + s + '.png'
  hidpi_png    = project_name + '-' + s + '@2.png'

  # Compute HiDPI (double) size
  s_int    = s.to_int()
  hidpi_int = s_int * 2
  hidpi_str = hidpi_int.to_string()

  # Select command for normal size
  if rsvg_convert.found()
    normal_cmd = [rsvg_convert, '-w', s, '-h', s, '-f', 'png', '-o', '@OUTPUT@', '@INPUT@']
    hidpi_cmd  = [rsvg_convert, '-w', hidpi_str, '-h', hidpi_str, '-f', 'png', '-o', '@OUTPUT@', '@INPUT@']
  elif inkscape_prog.found()
    # Inkscape exports: inkscape input.svg --export-type=png --export-filename=out.png -w WIDTH -h HEIGHT
    normal_cmd = [inkscape_prog, '@INPUT@', '--export-type=png', '--export-filename=@OUTPUT@', '-w', s, '-h', s]
    hidpi_cmd  = [inkscape_prog, '@INPUT@', '--export-type=png', '--export-filename=@OUTPUT@', '-w', hidpi_str, '-h', hidpi_str]
  else
    # ImageMagick: magick convert input.svg -resize WxH output.png
    normal_cmd = [magick_prog, 'convert', '@INPUT@', '-resize', s + 'x' + s, '@OUTPUT@']
    hidpi_cmd  = [magick_prog, 'convert', '@INPUT@', '-resize', hidpi_str + 'x' + hidpi_str, '@OUTPUT@']
  endif

  normal_target = custom_target(
    'icon-' + s,
    input: svg_path,
    output: normal_png,
    command: normal_cmd,
    build_by_default: true,
    install: true,
    install_dir: join_paths(get_option('datadir'), 'icons', 'hicolor', s + 'x' + s, 'apps')
  )

  hidpi_target = custom_target(
    'icon-' + s + '-hidpi',
    input: svg_path,
    output: hidpi_png,
    command: hidpi_cmd,
    build_by_default: true,
    install: true,
    install_dir: join_paths(get_option('datadir'), 'icons', 'hicolor', s + 'x' + s + '@2', 'apps')
  )
endforeach

scalable_svg = join_paths('icons', 'scalable', project_name + '.svg')
install_data(scalable_svg, install_dir: join_paths(get_option('datadir'), 'icons', 'hicolor', 'scalable', 'apps'))

# Translate and install our .desktop file so the Applications Menu will see it
i18n.merge_file(
  input: project_name + '.desktop.in',
  output: project_name + '.desktop',
  po_dir: join_paths(meson.source_root(), 'po', 'extra'),
  type: 'desktop',
  install: true,
  install_dir: join_paths(get_option('datadir'), 'applications')
)

# Translate and install our .appdata.xml file so AppCenter will see it
i18n.merge_file(
  input: project_name + '.appdata.xml.in',
  output: project_name + '.appdata.xml',
  po_dir: join_paths(meson.source_root(), 'po', 'extra'),
  install: true,
  install_dir: join_paths(get_option('datadir'), 'metainfo')
)
