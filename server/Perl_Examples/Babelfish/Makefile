SHELL := /bin/bash
# add to PATH and PERL5LIB for running `carton`
PATH := $(PATH):$(HOME)/perl5/bin
PERL5LIB ?= $(HOME)/perl5/lib/perl5
GITDIR := $(shell readlink -f ../../..)
WORKINGDIR := $(PWD)
CARTON := $(shell PATH=$(PATH) which carton)
CPANM := $(shell PATH=$(PATH) which cpanm)
# Set the Application ENV
DEBUG ?= 0
SAVE_BLANK_CONVERSATIONS ?= 1
TOP_P ?= 0.6
TEMPERATURE ?= 0.6
ifneq ($(SHOWENV),)
	export
else
	export PERL5LIB DEBUG SAVE_BLANK_CONVERSATIONS TOP_P TEMPERATURE
endif

run: /app/.env deps
	source $< && NGROK_HOST=$${NGROK_URL:8} \
	RELAY_URL="https://$${NGROK_HOST}/swml" \
	carton exec perl $(WORKINGDIR)/app.pl
/app:
	sudo mkdir $@
/app/.env: /app
	sudo umount $(@D) || true
	sudo mount --bind $(WORKINGDIR) $(@D)
.env: env.example
	@echo 'Must `cp env.example .env` and edit it with correct parameters`'
deps:
	# NOTE: Occasionally the C module will fail to install.
	# Re-running seems to resolve the issue.
	# So, if application fails, `make REINSTALL=1`
	if [ -z "$(CPANM)" ]; then sudo apt install cpanminus; fi
	if [ -z "$(CARTON)" -o "$(REINSTALL)" ]; then \
		echo -n 'Installing Perl Dependencies. '; \
		echo 'This may take a few minutes...'; \
		cpanm --installdeps ${WORKINGDIR}; \
		cpanm --installdeps ${WORKINGDIR}; \
		cpanm Carton; \
		carton install; \
		echo 'Installation of dependencies complete.'; \
	fi
env:
ifneq ($(SHOWENV),)
	$@
else
	$(MAKE) SHOWENV=1 $@
endif
