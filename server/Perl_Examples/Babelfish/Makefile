PATH := $(PATH):$(HOME)/perl5/bin
PERL5LIB := $(HOME)/perl5/lib/perl5
export
all: start_babelfish.sh .FORCE
	bash -x $< 2>&1 | tee $(HOME)/babelfish.log
.FORCE:
