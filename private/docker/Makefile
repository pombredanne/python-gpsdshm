
# Docker tag is the branch with 'master' replaced with 'latest'
#
BRANCH := $(subst /,_,$(shell git branch | awk '$$1 ~ /^\*/ { print $$2 }'))
DOCKERTAG := $(subst master,latest,$(BRANCH))

all: help

help:
	@echo "make build           - Build Docker image"
	@echo "make test            - Start Docker image and run tests (mock or gpsfake)"
	@echo "make gpsfake         - Start Docker image and run tests (gpsfake)"
	@echo "make run             - Start Docker image"
	@echo "make clean_failed    - Delete failed Docker containers"
	@echo "make clean_dangling  - Delete dangling Docker images"

test: test_3.0 \
      test_3.1 \
      test_3.2 \
      test_3.3 \
      test_3.4 \
      test_3.5 \
      test_3.6 \
      test_3.7 \
      test_3.8 \
      test_3.9 \
      test_3.10 \
      test_3.11 \
      test_3.12 \
      test_3.13 \
      test_3.14 \
      test_3.15 \
      test_3.16

gpsfake: gpsfake_3.0 \
         gpsfake_3.1 \
         gpsfake_3.2 \
         gpsfake_3.3 \
         gpsfake_3.4 \
         gpsfake_3.5 \
         gpsfake_3.6 \
         gpsfake_3.7 \
         gpsfake_3.8 \
         gpsfake_3.9 \
         gpsfake_3.10 \
         gpsfake_3.11 \
         gpsfake_3.13 \
         gpsfake_3.14 \
         gpsfake_3.15 \
         gpsfake_3.16

# gpsfake doesn't seem to work well with gpsd-3.12
#         gpsfake_3.12 \


build: Dockerfile
	docker build --build-arg http_proxy=$(http_proxy) \
	             --build-arg https_proxy=$(https_proxy) \
                     -t mjuenema/python-gpsdshm:$(DOCKERTAG) .

test_3.%: build
	docker run -it --rm -e "RELEASE=$(subst test_,,$@)" \
		-v `pwd`/workdir/cache:/home/developer/.cache \
		-v `pwd`/workdir/ccache:/home/developer/.ccache \
		-v `pwd`/../..:/home/developer/src \
		mjuenema/python-gpsdshm:$(DOCKERTAG) /run_tests.sh

gpsfake_3.%: build
	EL_RELEASE=$(subst gpsfake_,,$@) EL_DOCKERTAG=$(DOCKERTAG) ./expect-lite gpsfake.elt

run:
	docker run -it --rm \
		-v `pwd`/workdir/cache:/home/developer/.cache \
		-v `pwd`/workdir/ccache:/home/developer/.ccache \
		-v `pwd`/../..:/home/developer/src \
		mjuenema/python-gpsdshm:$(DOCKERTAG) /bin/bash
	

.PHONY: Dockerfile
Dockerfile:
       

clean_dangling:
	[ "`docker images -q -f dangling=true`" != "" ] && \
		docker rmi `docker images -q -f dangling=true`

clean_failed:
	[ "`docker ps -a | egrep 'Exited \([^0]' | cut -c 1-16`" != "" ] && \
		docker rm `docker ps -a | egrep 'Exited \([^0]' | cut -c 1-16`	
	
