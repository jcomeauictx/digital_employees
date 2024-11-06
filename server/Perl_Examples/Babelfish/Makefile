SHELL := /bin/bash
GITDIR := $(shell readlink -f ../../..)
WORKINGDIR := $(PWD)
CARTON := $(shell which carton)
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
/app/.env:
	sudo umount $(@D) || true
	sudo mount --bind $(WORKINGDIR) $@
.env: env.example
	@echo 'Must `cp env.example .env` and edit it with correct parameters`'
deps:
	# NOTE: Occasionally the C module will fail to install.
	# Re-running seems to resolve the issue, so we do it twice to be sure.
	if [ -z "$(CARTON)" ]; then \
		echo -n 'Installing Perl Dependencies. '; \
		echo 'This may take a few minutes...'; \
		cpanm --installdeps ${WORKINGDIR} > /dev/null 2>&1; \
		cpanm --installdeps ${WORKINGDIR} > /dev/null 2>&1; \
		cpanm Carton > /dev/null 2>&1; \
		carton install; \
		echo 'Installation of dependencies complete.'; \
	fi
