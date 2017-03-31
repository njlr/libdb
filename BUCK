def merge_dicts(x, y):
  z = x.copy()
  z.update(y)
  return z

headers = [
  'clib_port.h',
  'db_config.h',
  'db_cxx.h',
  'db_int.h',
  'db.h',
]

genrule(
  name = 'configure',
  out = 'out',
  srcs = glob([
    'dist/*.in',
    'dist/*.sh',
    'dist/buildpkg',
    'dist/config.*',
    'dist/configure',
    'dist/install-sh',
    'dist/Makefile.in',
    'lang/**/*.in',
    'lang/**/*.h',
    'lang/**/*.cpp',
    'lang/**/*.c',
    'src/**/*.h',
    'src/**/*.c',
    'src/**/*.in',
    'test/**/*.awk',
    'test/**/*.cpp',
    'test/**/*.c',
    'test/**/*.in',
    'test/**/*.tcl',
    'test/**/*.txt',
  ]),
  cmd = 'cp -r $SRCDIR $OUT && ' + \
        'cd $OUT && ' + \
        './dist/configure --disable-replication --enable-cxx --enable-stl ',
)

unix_sources = [
  'src/mutex/mut_pthread.c',
]

windows_sources = [
  'src/repmgr/repmgr_windows.c',
  'src/mutex/mut_win32.c',
]

platform_sources = unix_sources + windows_sources

unix_flags = [
  '-DHAVE_MUTEX_PTHREADS',
]

def extract(x):
  genrule(
    name = x,
    out = x,
    cmd = 'cp $(location :configure)/' + x + ' $OUT',
  )
  return ':' + x

cxx_library(
  name = 'libdb',
  header_namespace = '',
  exported_headers = merge_dicts(subdir_glob([
    ('src', '**/*.h'),
  ]),
  dict({ (x, extract(x)) for x in headers })),
  srcs = glob([
    'src/**/*.c',
  ],
  excludes = platform_sources + glob([
    'src/clib/**/*.c',
    'src/repmgr/**/*.c',
    'src/os/os_addrinfo.c',
  ])),
  platform_srcs = [
    ('default', unix_sources),
    ('^macos.*', unix_sources),
    ('^linux.*', unix_sources),
    ('^windows.*', windows_sources),
  ],
  compiler_flags = [
    '-DHAVE_MUTEX_HYBRID=1',
  ],
  platform_compiler_flags = [
    ('default', unix_flags),
    ('^macos.*', unix_flags),
    ('^linux.*', unix_flags),
  ],
  visibility = [
    'PUBLIC',
  ],
)
