MYNAME = nnet_test
include common.mak

ifndef SELECT_PRESET_ID
USE_PRESET=FALSE
endif
MYDIR = nnet_test
IMAGE = $(DIR_IMG)/nnet_test$(EXE)
ITEMS = \
	fp/nnet \

nnet_test$(OEXT): nnet_test.c

MYOBJD = nnet_test$(OEXT)
MYOBJS_WORKLOAD = nnet_test$(OEXT)

ifdef PRECISION
ifeq ($(PRECISION),DP)
MAKEFLAGS+= WORKLOAD_DEFINES=-DUSE_FP64
endif
ifeq ($(PRECISION),SP)
MAKEFLAGS+= WORKLOAD_DEFINES=-DUSE_FP32
endif
KBUILD=$(addsuffix /$(PRECISION),$(ITEMS))
KMPATH=../
else
KBUILD=$(ITEMS)
endif
MYDIRS = $(addprefix $(DIR_BENCH)/,$(KBUILD))
S_DIRS = $(addprefix $(TOPDIR)benchmarks,$(ITEMS))
MYOBJD += $(addsuffix /done.build,$(MYDIRS))
MYOBJS = $(MYOBJS_WORKLOAD) $(addsuffix /*$(OEXT),$(MYDIRS))
TOPME = $(TOPDIR)workloads/$(MYDIR)
vpath %.c $(TOPME) $(S_DIRS)
vpath %$(LIBTYPE) $(TOPME) $(MYDIRS)
INC_DIRS += $(TOPDIR)mith/al/include 

ifdef SELECT_PRESET
ifneq ($(USE_PRESET),FALSE)
ITEMS+=$(SELECT_PRESET) 
preset_defines=$(foreach item,$(ITEMS),$(TOPDIR)benchmarks/$(item)/preset.mak )
-include $(preset_defines)
MYOBJS = $(MYOBJS_WORKLOAD) $(KERNEL_OBJS)
endif
endif

include $(TOPME)/$(MYNAME).opt

.PHONY: dirs
dirs: $(MYDIRS)

.PHONY: build
build: done.build $(DO_DATA_COPY)

done.build: $(IMAGE)
	@echo " $(DATESTAMP) $(MYNAME)" >> $(LOG_PROGRESS)
	@echo "  wld-flags: $(BENCHCFLAGS)" >> $(LOG_PROGRESS)
	touch done.build

$(IMAGE): $(MYDIRS) $(MYOBJD) $(LIB_TH)
	$(LD) $(LINKER_FLAGS) $(EXEOUT)$(IMAGE_NAME) $(MYOBJS_NAME) $(BENCH_LIBS) $(LIB_TH_NAME) $(LINKER_LAST)
	
$(MYDIRS):
	$(MDIR) $@

$(DIR_BENCH)/%/done.build: $(FORCE_REBUILD)
	cd $(DIR_BENCH)/$* && $(MAKE) -f $(TOPDIR)benchmarks/$*/$(KMPATH)Makefile build

