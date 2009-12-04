# vim: ft=python expandtab

import re
import os
from site_init import GBuilder, Initialize

opts = Variables()
opts.Add(PathVariable('PREFIX', 'Installation prefix', os.path.expanduser('~/FOSS'), PathVariable.PathIsDirCreate))
opts.Add(BoolVariable('DEBUG', 'Build with Debugging information', 0))
opts.Add(PathVariable('PERL', 'Path to the executable perl', r'C:\Perl\bin\perl.exe', PathVariable.PathIsFile))

env = Environment(variables = opts,
                  ENV=os.environ, tools = ['default', GBuilder])

Initialize(env)

headers  = Split("jconfig.h jerror.h jmorecfg.h jpeglib.h")
env.Command('jconfig.h', 'jconfig.vc', 'copy $SOURCE $TARGET')
def modify_makefile(target, source, env):
    o = open(str(target[0]), 'w')
    i = open(str(source[0]), 'r')
    lib = re.compile('lib -out:')
    for line in i:
        if not lib.search(line):
            o.write(line)
        else:
            o.write('\t$(link) $(LDFLAGS) /DLL /out:libjpeg-7.dll /DEF:libjpeg-7.def /IMPLIB:libjpeg.lib $(LIBOBJECTS)\n')
    i.close()
    o.close()
env.Command('makefile.scons', 'makefile.vc', modify_makefile)
env.Command(['libjpeg-7.dll', 'libjpeg.lib'], 'makefile.scons', 'nmake -f $SOURCE')

env.Alias('install', env.Install('$PREFIX/include/', headers))
env.Alias('install', env.Install('$PREFIX/bin', 'libjpeg-7.dll'))
env.Alias('install', env.Install('$PREFIX/lib',  'libjpeg.lib'))
env.Alias('install', env.InstallAs('$PREFIX/lib/jpeg.lib',  'libjpeg.lib'))
