CC = gcc

CFLAGS = -g -pg -I../include -O2 -Wall
LDFLAGS = -I../include -pthread

ND=nicksrc
ED=eigensrc
NLIB = $(ND)/libnick.a
MATHLIB = -lm

LAPACK = -llapack
ATLAS_LAPACK = -L/usr/lib64/atlas -llapack -lf77blas -lcblas -latlas

# ----- OS X fix
UNAME := $(shell uname)
ifeq ($(UNAME), Darwin)
LAPACK = -framework Accelerate
ATLAS_LAPACK = -framework Accelerate
CFLAGS = -g -pg -I../include -arch x86_64 -O2 -Wall
LDFLAGS = -arch x86_64 -I../include -pthread
else
ifeq ($(UNAME), MINGW32_NT-6.2)
LAPACK = -Wl,-Bstatic -L. lapack/liblapack.a -L. lapack/librefblas.a
ATLAS_LAPACK = -Wl,-Bstatic -L. lapack/liblapack.a -L. lapack/librefblas.a
LDFLAGS = -pg -I../include -static-libgcc
endif
endif

EXE = convertf mergeit smartpca smartrel pcatoy smarteigenstrat twstats eigenstrat eigenstratQTL pca

TWTAB = \"$(PWD)/smarttables/twtable\"

NLIBOBJS = $(ND)/gauss.o $(ND)/gds.o $(ND)/getpars.o $(ND)/linsubs.o $(ND)/sortit.o $(ND)/statsubs.o \
        $(ND)/strsubs.o $(ND)/vsubs.o $(ND)/xsearch.o


# ----- default 
%:%.o
	$(CC) $(LDFLAGS) $^ -o $@ $(MATHLIB)

%.o: %.c
	$(CC) -c $(CFLAGS) -o $@ $<


.PHONY: all clean clobber install



# ----- define TW table in statsubs
ifeq ($(UNAME), MINGW32_NT-6.2)
$(ND)/statsubs.o: $(ND)/statsubs.c
	$(CC) -c $(CFLAGS) -DTWTAB="\"smarttables/twtable\"" -o $@ $<
else
$(ND)/statsubs.o: $(ND)/statsubs.c
	$(CC) -c $(CFLAGS) -DTWTAB=$(TWTAB) -o $@ $<
endif


# ----- build nicksrc/libnick.a
$(NLIB):	$(NLIBOBJS)
	ar -r $(NLIB) $(NLIBOBJS)




# ----- Windows-specific EIGENSOFT build
ifeq ($(UNAME), MINGW32_NT-6.2)
convertf:	convertf.o mcio.o egsubs.o admutils.o h2d.o $(ED)/exclude.o $(NLIB)
	$(CC) $(LDFLAGS) $^ -o $@ $(MATHLIB)

mergeit:	mergeit.o mcio.o admutils.o $(NLIB)
	$(CC) $(LDFLAGS) $^ -o $@ $(MATHLIB) 

pcatoy: 	eigensrc/pcatoy.o eigensrc/eigsubs.o eigensrc/eigx.o $(NLIB)
	gfortran -O2 -o $@ $(LDFLAGS) $^ $(LAPACK) $(MATHLIB)

smartpca:	$(ED)/smartpca.o twsubs.o mcio.o qpsubs.o admutils.o egsubs.o regsubs.o \
		$(ED)/eigsubs.o  \
		$(ED)/exclude.o $(ED)/smartsubs.o $(ED)/eigx.o $(NLIB)
	gfortran -O2 -o $@ $(LDFLAGS) $^ $(LAPACK) $(MATHLIB)

smartrel:	$(ED)/smartrel.o twsubs.o mcio.o qpsubs.o admutils.o egsubs.o regsubs.o \
		$(ED)/eigsubs.o $(ED)/eigx.o $(ED)/smartsubs.o $(NLIB)    
	gfortran -O2 -o $@ $(LDFLAGS) $^ $(LAPACK) $(MATHLIB)

smarteigenstrat:	$(ED)/smarteigenstrat.o mcio.o admutils.o $(NLIB)
	$(CC) $(LDFLAGS) -o $@ $(LAPACK) $^ $(MATHLIB)

twstats:	$(ED)/twstats.o $(NLIB) 
	$(CC) $(LDFLAGS) $^ -o $@  $(MATHLIB)

eigenstrat:	$(ED)/eigenstrat.o 
	$(CC) $(LDFLAGS) $^ -o $@  $(MATHLIB)

eigenstratQTL:	$(ED)/eigenstratQTL.o 
	$(CC) $(LDFLAGS) $^ -o $@  $(MATHLIB)

pca:	$(ED)/eigsubs.o pca.o eigx.o $(NLIB)
	gfortran -O2 -o $@ $(LDFLAGS) $^ $(LAPACK) $(MATHLIB)
else
# ----- OS X/Linux EIGENSOFT build
convertf:	convertf.o mcio.o egsubs.o admutils.o h2d.o $(ED)/exclude.o $(NLIB)
	$(CC) $(LDFLAGS) $^ -o $@ $(MATHLIB)

mergeit:	mergeit.o mcio.o admutils.o $(NLIB)
	$(CC) $(LDFLAGS) $^ -o $@ $(MATHLIB) 

pcatoy: 	eigensrc/pcatoy.o eigensrc/eigsubs.o eigensrc/eigx.o $(NLIB)
	$(CC) $(LDFLAGS) -o $@ $(LAPACK) $^ $(MATHLIB)

smartpca:	$(ED)/smartpca.o twsubs.o mcio.o qpsubs.o admutils.o egsubs.o regsubs.o \
		$(ED)/eigsubs.o  \
		$(ED)/exclude.o $(ED)/smartsubs.o $(ED)/eigx.o $(NLIB)
	$(CC) $(LDFLAGS) -o $@ $(LAPACK) $^ $(MATHLIB)

smartrel:	$(ED)/smartrel.o twsubs.o mcio.o qpsubs.o admutils.o egsubs.o regsubs.o \
		$(ED)/eigsubs.o $(ED)/eigx.o $(ED)/smartsubs.o $(NLIB)    
	$(CC) $(LDFLAGS) -o $@ $(LAPACK) $^ $(MATHLIB)

smarteigenstrat:	$(ED)/smarteigenstrat.o mcio.o admutils.o $(NLIB)
	$(CC) $(LDFLAGS) -o $@ $(LAPACK) $^ $(MATHLIB)

twstats:	$(ED)/twstats.o $(NLIB) 
	$(CC) $(LDFLAGS) $^ -o $@  $(MATHLIB)

eigenstrat:	$(ED)/eigenstrat.o 
	$(CC) $(LDFLAGS) $^ -o $@  $(MATHLIB)

eigenstratQTL:	$(ED)/eigenstratQTL.o 
	$(CC) $(LDFLAGS) $^ -o $@  $(MATHLIB)

pca:	$(ED)/eigsubs.o pca.o eigx.o $(NLIB)
	$(CC) $(LDFLAGS) -o $@ $(LAPACK) $^ $(MATHLIB)
endif


# ----- phony targets
all:    $(EXE)

install:  all
	mv $(EXE) ../bin

clobber:
	rm -f *.o core core.* $(ND)/*.o $(ND)/*.a $(ED)/*.o
	rm -f $(EXE)
	cd ../bin/ ; rm -f $(EXE) ; cd ../src

clean:
	rm -f *.o core core.* *.o
