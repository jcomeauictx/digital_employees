SHELL := /bin/bash
GITDIR := $(shell readlink -f ../../..)
WORKINGDIR := $(PWD)
CARTON := $(shell which carton)
CPANM := $(shell which cpanm)
# Set the Application ENV
export DEBUG := 0
#export FROM_NUMBER=${FROM_NUMBER}
export SAVE_BLANK_CONVERSATIONS := 1
#export API_VERSION=api/relay/rest
export TOP_P := 0.6
export TEMPERATURE = 0.6

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
