# allow to use Host cuda functions in C/C++
DEF_INCLUDES = @CUDA_INCLUDES@

if WANT_JANSSON
JANSSON_INCLUDES= -I$(top_srcdir)/compat/jansson
else
JANSSON_INCLUDES=
endif

EXTRA_DIST		= autogen.sh README.md README.txt LICENSE.txt \
			  uouminer.sln uouminer.vcxproj uouminer.vcxproj.filters \
			  compat/gettimeofday.c compat/getopt/getopt_long.c

SUBDIRS = compat

bin_PROGRAMS = uouminer

uouminer_SOURCES  	= elist.h miner.h compat.h \
			  compat/inttypes.h compat/stdbool.h compat/unistd.h \
			  compat/sys/time.h compat/getopt/getopt.h \
			  crc32.c hefty1.c \
			  uouminer.cpp pools.cpp util.cpp bench.cpp bignum.cpp \
			  api.cpp hashlog.cpp nvml.cpp stats.cpp sysinfos.cpp cuda.cpp \
			  nvsettings.cpp \
			  pipe/pipe.cu \
			  pipe/cuda_blake512.cu pipe/cuda_blake512.h \
			  pipe/cuda_combine.cu pipe/cuda_combine.h \
			  pipe/cuda_groestl512.cu pipe/cuda_groestl512.h \
			  pipe/cuda_hefty1.cu pipe/cuda_hefty1.h \
			  pipe/cuda_keccak512.cu pipe/cuda_keccak512.h \
			  pipe/cuda_sha256.cu pipe/cuda_sha256.h \
			  fuguecoin.cpp Algo256/cuda_fugue256.cu sph/fugue.c uint256.h \
			  groestlcoin.cpp cuda_groestlcoin.cu cuda_groestlcoin.h \
			  myriadgroestl.cpp cuda_myriadgroestl.cu \
			  cuda_checkhash.cu \
			  sph/blake.c sph/groestl.c sph/jh.c sph/keccak.c 

if HAVE_NVML
nvml_defs = -DUSE_WRAPNVML
nvml_libs = -ldl
endif

if HAVE_WINDOWS
uouminer_SOURCES += compat/winansi.c
endif

uouminer_LDFLAGS  = $(PTHREAD_FLAGS) @CUDA_LDFLAGS@
uouminer_LDADD    = @LIBCURL@ @JANSSON_LIBS@ @PTHREAD_LIBS@ @WS2_LIBS@ @CUDA_LIBS@ @OPENMP_CFLAGS@ @LIBS@ $(nvml_libs)
uouminer_CPPFLAGS = @LIBCURL_CPPFLAGS@ @OPENMP_CFLAGS@ $(CPPFLAGS) $(PTHREAD_FLAGS) -fno-strict-aliasing $(JANSSON_INCLUDES) $(DEF_INCLUDES) $(nvml_defs)

if HAVE_OSX
uouminer_CPPFLAGS += -I/usr/local/llvm/lib/clang/4.0.0/include
uouminer_LDFLAGS += -L/usr/local/llvm/lib
uouminer_LDADD += -lomp
endif

#uouminer_CPPFLAGS += -DUSE_LIBSODIUM
#uouminer_LDFLAGS += -Lequi/lib
#uouminer_LDADD += -lsodium
uouminer_LDADD += -lcuda

nvcc_ARCH :=
#nvcc_ARCH += -gencode=arch=compute_61,code=\"sm_61,compute_61\"
nvcc_ARCH += -gencode=arch=compute_52,code=\"sm_52,compute_52\"
nvcc_ARCH += -gencode=arch=compute_50,code=\"sm_50,compute_50\"
#nvcc_ARCH += -gencode=arch=compute_35,code=\"sm_35,compute_35\"
#nvcc_ARCH += -gencode=arch=compute_30,code=\"sm_30,compute_30\"

nvcc_FLAGS = $(nvcc_ARCH) @CUDA_INCLUDES@ -I. @CUDA_CFLAGS@
nvcc_FLAGS += $(JANSSON_INCLUDES) --ptxas-options="-v"

# we're now targeting all major compute architectures within one binary.
.cu.o:
	$(NVCC) $(nvcc_FLAGS) --maxrregcount=128 -o $@ -c $<

pipe/cuda_hefty1.o: pipe/cuda_hefty1.cu
	$(NVCC) $(nvcc_FLAGS) --maxrregcount=80 -o $@ -c $<


