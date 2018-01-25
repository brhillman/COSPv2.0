# Paths to source codes in COSP library
SRC_PATH     = ../src
HOOKS        = $(SRC_PATH)/hooks
SIM_PATH     = $(SRC_PATH)/simulator
RT_PATH      = $(SIM_PATH)/rttov
RS_PATH      = $(SIM_PATH)/quickbeam
CS_PATH      = $(SIM_PATH)/actsim
ISCCP_PATH   = $(SIM_PATH)/icarus-scops-4.1-bsd
MISR_PATH    = $(SIM_PATH)/MISR_simulator
MODIS_PATH   = $(SIM_PATH)/MODIS_simulator
PARASOL_PATH = $(SIM_PATH)/parasol
VPATH = $(SRC_PATH):$(HOOKS):$(SIM_PATH):$(RT_PATH):$(RS_PATH):$(CS_PATH):$(ISCCP_PATH):$(MISR_PATH):$(MODIS_PATH):$(PARASOL_PATH)

# Example subcolumn generaton and mapping to optical properties, following COSP 1.4
SUBCOLS = ../driver/src/subsample_and_optics
VPATH += $(SUBCOLS)/optics:$(SUBCOLS)/optics/quickbeam_optics:$(SUBCOLS)/subcol:$(SUBCOLS)/subcol/rng

# Standalone driver
# Replace directory with ../driver/cmor to exercise CMOR-compatible offline driver; then need to link with CMOR
DRIVER = ../driver/src
VPATH += $(DRIVER)

# Makefile.conf needs to define $(F90), $(F90FLAGS)
include Makefile.conf
NC_LIB = /Users/robert/Applications/ifort/lib
NC_INC = /Users/robert/Applications/ifort/include

F90FLAGS += -I$(NC_INC)
LDFLAGS += -L. -lcosp -lsubcol -L$(NC_LIB) -lnetcdff

# Rules
%.o: %.F90
	$(F90) $(F90FLAGS) -c $<

%.o: %.f90
	$(F90) $(F90FLAGS) -c $<

%: %.o
	$(F90) $(F90FLAGS) -o $@ $^ $(LDFLAGS) $(LIBS)

#
# The COSP library
#
COSP_OBJS = cosp.o cosp_config.o cosp_stats.o \
       cosp_constants.o cosp_errorHandling.o cosp_kinds.o \
       cosp_isccp_interface.o    icarus.o	\
			 cosp_misr_interface.o     MISR_simulator.o \
			 cosp_modis_interface.o    modis_simulator.o \
			 cosp_parasol_interface.o  parasol.o \
			 cosp_calipso_interface.o  lidar_simulator.o \
			 cosp_cloudsat_interface.o quickbeam.o \
			 cosp_rttov_interfaceSTUB.o cosp_rttovSTUB.o

LIBRARY = libcosp.a

$(LIBRARY): $(COSP_OBJS)
	ar -rvs $(LIBRARY) $(COSP_OBJS)

#
# Alternative target for library
#
lib: $(LIBRARY)

clean:
	rm -f *.optrpt *.mod *.o $(LIBRARY) fort.*

# Dependencies for libary
cosp.o                    : cosp_kinds.o cosp_modis_interface.o cosp_constants.o \
                            cosp_rttov_interfaceSTUB.o cosp_misr_interface.o cosp_isccp_interface.o \
                            cosp_calipso_interface.o cosp_cloudsat_interface.o cosp_stats.o         \
  			                    cosp_parasol_interface.o cosp_rttovSTUB.o cosp_rttov_interfaceSTUB.o    \
                            quickbeam.o MISR_simulator.o lidar_simulator.o parasol.o icarus.o
cosp_constants.o          : cosp_kinds.o
cosp_config.o             : cosp_kinds.o
cosp_errorHandling.o      : cosp_kinds.o
cosp_stats.o              : cosp_kinds.o cosp_config.o
cosp_isccp_interface.o    : cosp_kinds.o icarus.o
icarus.o	                : cosp_kinds.o cosp_constants.o  cosp_stats.o
cosp_misr_interface.o     : cosp_kinds.o
MISR_simulator.o          : cosp_kinds.o cosp_config.o cosp_stats.o
cosp_modis_interface.o    : cosp_kinds.o cosp_config.o modis_simulator.o
modis_simulator.o         : cosp_kinds.o cosp_config.o cosp_stats.o
cosp_parasol_interface.o  : cosp_kinds.o
parasol.o                 : cosp_kinds.o cosp_config.o cosp_constants.o
cosp_calipso_interface.o  : cosp_kinds.o lidar_simulator.o
lidar_simulator.o         : cosp_kinds.o cosp_config.o cosp_stats.o
cosp_cloudsat_interface.o : cosp_kinds.o cosp_config.o quickbeam.o
quickbeam.o               : cosp_kinds.o cosp_stats.o
cosp_rttov_interfaceSTUB.o: cosp_kinds.o cosp_config.o cosp_rttovSTUB.o
cosp_rttovSTUB.o          : cosp_kinds.o cosp_config.o cosp_constants.o

# Example subcolumn generaton and mapping to optical properties, following COSP 1.4
SUBCOL_OBJS = mo_rng.o scops.o prec_scops.o cosp_utils.o cosp_optics.o quickbeam_optics.o array_lib.o math_lib.o mrgrnk.o optics_lib.o
libsubcol.a: $(SUBCOL_OBJS) libcosp.a
	ar -rvs libsubcol.a $(SUBCOL_OBJS)

array_lib.o       : cosp_kinds.o
mrgrnk.o          : cosp_kinds.o
math_lib.o        : cosp_kinds.o cosp_errorHandling.o
optics_lib.o      : cosp_kinds.o cosp_errorHandling.o
quickbeam_optics.o: cosp_kinds.o cosp_errorHandling.o cosp_constants.o cosp_config.o mrgrnk.o array_lib.o optics_lib.o math_lib.o quickbeam.o
scops.o      : cosp_kinds.o               mo_rng.o
prec_scops.o : cosp_kinds.o cosp_config.o
cosp_utils.o : cosp_kinds.o cosp_config.o
cosp_optics.o: cosp_kinds.o cosp_config.o cosp_constants.o modis_simulator.o
mo_rng.o     : cosp_kinds.o

# Offline driver
cosp2_io.o    : cosp_kinds.o cosp_config.o cosp.o
cosp2_test.o  : cosp2_io.o libcosp.a libsubcol.a 
cosp2_test    : cosp2_test.o cosp2_io.o