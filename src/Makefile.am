OBJS_COMMON = \
Volumes.o \
Crc.o\
Endian.o 

OBJ_MAIN = \
Core.o \
Charset.o \
Utils.o \
Main.o

OBJS_CRYPTO =

CFLAGS = -g -I./Common/ -I./Crypto/ -I./Cuda/ -I./Main/ -I./ -I/usr/local/cuda/include/ 
LFLAGS = -lm -O3 

if DARWIN
CFLAGS += -DTC_MACOSX
else
CFLAGS += 
#-DTC_UNIX
endif

if ENABLE_CPU
OBJS_CRYPTO += Rmd160.o CpuAes.o Sha1.o Sha2.o Whirlpool.o Serpent.o Twofish.o
OBJS_COMMON += Pkcs5.o CpuCore.o Crypto.o
COMPILER=$(CC)
else
OBJ_CUDA=Cuda.o
CFLAGS += -D_GPU_
COMPILER=$(NVCC)
# CUDA code generation flags
GENCODE_SM10= -gencode arch=compute_10,code=sm_10
GENCODE_SM20= -gencode arch=compute_20,code=sm_20
GENCODE_SM30= -gencode arch=compute_30,code=sm_30 -gencode arch=compute_35,code=sm_35
GENCODE_FLAGS=  $(GENCODE_SM10) $(GENCODE_SM20) $(GENCODE_SM30)
CFLAGS += $(GENCODE_FLAGS) -ftz=true -use_fast_math
endif



all: truecrack

truecrack:  $(OBJ_CUDA) $(OBJ_MAIN) $(OBJS_COMMON) $(OBJS_CRYPTO) 
	$(COMPILER) -o $@ $(OBJ_CUDA) $(OBJS_COMMON) $(OBJ_MAIN) $(OBJS_CRYPTO) $(CFLAGS) $(LFLAGS)

$(OBJ_CUDA): 
	cat Cuda/Core.cu > Cuda.cu 
	cat Cuda/Pkcs5.cu >> Cuda.cu
	cat Cuda/Aes.cu >> Cuda.cu
	cat Cuda/Serpent.cu >> Cuda.cu
	cat Cuda/Twofish.cu >> Cuda.cu
	cat Cuda/Crypto.cu >> Cuda.cu
	cat Cuda/Xts.cu >> Cuda.cu
	cat Cuda/Rmd160.alternative.cu >> Cuda.cu
	cat Cuda/Sha2.cu >> Cuda.cu
	cat Cuda/Whirlpool.cu >> Cuda.cu
	
	$(COMPILER) -c Cuda.cu $(CFLAGS) --opencc-options -OPT:Olimit=0 $< -o $@

$(OBJS_COMMON): %.o: Common/%.c
	$(COMPILER) -c $(CFLAGS) $< -o $@

$(OBJS_CRYPTO): %.o: Crypto/%.c
	$(COMPILER) -c $(CFLAGS) $< -o $@

$(OBJ_MAIN): %.o: Main/%.c
	$(COMPILER) -c $(CFLAGS) $< -o $@


clean:
	rm -rf truecrack
	rm -rf *.o *.cu


bin_PROGRAMS=truecrack
truecrack_SOURCES=

truecrack_CFLAGS  = $(CUDA_CFLAGS)
truecrack_LDADD  = $(CUDA_LIBS)
truecrack_LDFLAGS = 
