allaux = files('pgemraux.c')
imrsrc = files('pigemr.c', 'pigemr2.c', 'pitrmr.c', 'pitrmr2.c')

mrsrc = []

if arith.contains('s')
  mrsrc += files('psgemr.c', 'psgemr2.c', 'pstrmr.c', 'pstrmr2.c')
endif

if arith.contains('c')
  mrsrc += files('pcgemr.c', 'pcgemr2.c', 'pctrmr.c', 'pctrmr2.c')
endif

if arith.contains('d')
  mrsrc += files('pdgemr.c', 'pdgemr2.c', 'pdtrmr.c', 'pdtrmr2.c')
endif

if arith.contains('z')
  mrsrc += files('pzgemr.c', 'pzgemr2.c', 'pztrmr.c', 'pztrmr2.c')
endif

redist = [allaux, imrsrc, mrsrc]
