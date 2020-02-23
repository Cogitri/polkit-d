# Based on https://github.com/gtkd-developers/GlibD/blob/master/meson.build

project(
    'polkit-d',
    'd',
    license: 'GPL-3.0-or-later',
    meson_version: '>=0.50',
    version: '0.1.0',
)

project_soversion = 0

pkgc = import('pkgconfig')

source_root = meson.current_source_dir()
build_root = meson.current_build_dir()
gen_dir = 'generated'

glibd_dep = dependency('glibd-2.0')
polkit_gobject_dep = dependency('polkit-gobject-1')

gir_to_d_prog = find_program('girtod')
gir_wrap_dir = join_paths(source_root, 'src')
gir_d_intf_dir = join_paths(build_root, gen_dir)

message('Generating D interfaces from GIR...')
girtod_gen = run_command(gir_to_d_prog,
                         '-i', gir_wrap_dir,
                         '-o', gir_d_intf_dir,
                         '--print-files', 'relative,' + source_root,
			 '-g', get_option('gir-dir'))
if girtod_gen.returncode() != 0
    error('Unable to build D intefaces from GIR:\n' + girtod_gen.stderr())
endif

gir_bind_dir = include_directories(gen_dir)
# Enlist D GIR interface sources
gir_binding_sources = girtod_gen.stdout().strip().split('\n')

polkitd = library('polkit-gobjectd-1',
    [gir_binding_sources],
    include_directories: [gir_bind_dir],
    dependencies: [glibd_dep, polkit_gobject_dep],
    install: true,
    soversion: project_soversion,
    version: meson.project_version())

install_subdir(join_paths(build_root, gen_dir, 'polkit'),    install_dir: 'include/d/polkit-gobjectd-1/polkit-gobjectd-1')

pkgc.generate(polkitd,
    name: 'polkit-gobjectd-1',
    subdirs: 'd/polkit-gobjectd-1',
    version: meson.project_version(),
    requires: [polkit_gobject_dep],
    description: 'D bindings for Polkit-GObject.')

