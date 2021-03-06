project('SCALAPACK',
  ['c', 'fortran'],
  version: '2.1.0',
  meson_version: '>= 0.50',
  default_options : ['default_library=static', 'buildtype=release', 'warning_level=0'])

cargs = '-DAdd_'
arith = get_option('arith')

os = host_machine.system()
cc = meson.get_compiler('c')
fc = meson.get_compiler('fortran')

quiet_f = fc.first_supported_argument(['-w', '-nowarn', '/nowarn'])
add_project_arguments(quiet_f, language: 'fortran')
quiet_c = cc.first_supported_argument(['-w', '-w0', '/W0'])
add_project_arguments(quiet_c, language: 'fortran')

# == MPI
mpiexec = find_program('mpiexec', required: false, disabler: true)
mpif = dependency('mpi', language : 'fortran')
if mpif.found() and not fc.links('use mpi; end', name: 'Fortran MPI', dependencies: mpif)
  error('MPI Fortran not found, and Scalapack uses MPI throughout')
endif

mpic_code = '''
#include <mpi.h>
int main(int argc, char *argv[]) { MPI_Init(&argc, &argv); MPI_Finalize(); return 0;}
'''
mpic = dependency('mpi', language : 'c')
if mpic.found() and not cc.links(mpic_code, dependencies: mpic, name: 'C MPI')
  error('MPI C not found, and Scalapack uses MPI throughout')
endif

# == BLACS

subdir('BLACS/SRC')
if not (meson.is_subproject() or get_option('CI'))
  blacs_test_exe = executable('blacs_test', 'tests/blacs_helloworld.f90',
    link_with: blacs_lib,
    dependencies: mpif,
    link_language: 'fortran')

  test('BLACS basic', blacs_test_exe,
    timeout: 15,
    suite: 'blacs')
endif

# == LAPACK

lapack = dependency('lapack', required: false, disabler: true)
if lapack.found() and not fc.links('print *,disnan(0.); end', dependencies: lapack, name: 'LAPACK')
  lapack = disabler()
endif
if not lapack.found()
  lapack_proj = subproject('lapack')
  lapack = declare_dependency(link_with: lapack_proj.get_variable('lapack'))
endif

# == SCALAPACK
subdir('TOOLS')  # tools files
subdir('PBLAS/SRC')  # pblas files
subdir('REDIST/SRC')  # redist files
subdir('SRC') # src files

subdir('TOOLS/LAPACK')

# this is the only thing installed: libscalapack.{a,so,dylib,lib}
scalapack_lib = library('scalapack',
  sources: [tools, pblas, redist, src, extra_lapack],
  link_with: blacs_lib,
  dependencies: [lapack, mpic, mpif],
  c_args : cargs,
  install: true)

if meson.is_subproject()
  subdir_done()
endif

# --- generate pkg-config
pkg = import('pkgconfig')
pkg.generate(scalapack_lib,
  url: 'https://github.com/scivision/scalapack',
  subdirs: 'scalapack',
  description: 'Netlib SCALAPACK')

# --- Scalapack test
if arith.contains('d')
scalapack_test_exe = executable('example1', 'tests/test_scalapack_d.f90',
  link_with : scalapack_lib,
  dependencies : [mpif, lapack],
  link_language: 'fortran')
test('scalapack basic', scalapack_test_exe,
  timeout: 60, suite: 'scalapack')
endif