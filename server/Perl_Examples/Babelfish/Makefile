SHELL := /bin/bash
# add to PATH and PERL5LIB for running `carton`
PATH := $(PATH):$(HOME)/perl5/bin
PERL5LIB ?= $(HOME)/perl5/lib/perl5
GITDIR := $(shell readlink -f ../../..)
WORKINGDIR := $(PWD)
CARTON := $(shell PATH=$(PATH) which carton)
CPANM := $(shell PATH=$(PATH) which cpanm)
GCC := $(shell PATH=$(PATH) which gcc)
# Set the Application ENV
DEBUG ?= 0
SAVE_BLANK_CONVERSATIONS ?= 1
TOP_P ?= 0.6
TEMPERATURE ?= 0.6
# in $HOME/.netrc, put the credentials for signalwire in, all on one line, as:
# machine signalwire.com
# login SPACE_NAME=spacename:FROM_NUMBER=+15555555555:DOMAIN_NAME=signalwire.com
# password AUTH_TOKEN=authtoken account ACCOUNT_SID=accountsid
SIGNALWIRE := signalwire.com
FIELDS := $$1" "$$2" "$$3" "$$5" "$$7
NETRC := machine $(SIGNALWIRE) login password account
PRINT := print $$4":"$$6":"$$8
CONFIG := $(shell awk '$(FIELDS) == "$(NETRC)" {$(PRINT)}' ~/.netrc)
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
/etc/sudoers.d/mount: mount
	sudo cp $< $@
/app/.env: /app
	# can't check for presence of /etc/sudoers.d/mount due to permissions
	# however, if you get prompted for a password here, it's not there.
	if ! sudo umount $(@D); then \
	 if [ -e /app/Makefile ]; then \
	  echo 'tell $(USER) to fix sudoers.d/mount' >&2; false; \
	 else \
	  echo '/app already not mounted' >&2; \
	 fi; \
	fi
	sudo mount --bind $(WORKINGDIR) $(@D)
.env: .FORCE
	echo '$(CONFIG)' | awk '{gsub(":","\n"); print}' > $@
deps:
	# NOTE: Occasionally the C module will fail to install.
	# Re-running seems to resolve the issue.
	# So, if application fails, `make REINSTALL=1`
	if [ -z "$(GCC)" ]; then \
	 sudo apt install libssl-dev gcc g++ zlib1g-dev; \
	fi
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
ssh:
	# if this fails, you need to add the IP address of your development
	# system to /etc/hosts or to ~/.ssh/config
	$@ signalwire-dev
.FORCE:
