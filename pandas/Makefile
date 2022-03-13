VERSION := $(shell grep pandas environment.yml | awk -F'=' '{print $$NF}')
NOTEBOOKS ?= ${PWD}/examples
IMG_REPO ?= bitsofparag/pandas

lock:
	@conda-lock -f environment.yml -p osx-64 -p linux-64

build:
	@docker build \
		--build-arg VERSION=${VERSION} \
		--build-arg ENV_NAME=$$(head -1 environment.yml | cut -d' ' -f2) \
		--target build \
		-t ${IMG_REPO}-build:${VERSION} .

dist:
	@docker build \
		--build-arg VERSION=${VERSION} \
		--build-arg ENV_NAME=$$(head -1 environment.yml | cut -d' ' -f2) \
		--target dist \
		-t ${IMG_REPO}:${VERSION} .

jupyter:
	@docker build \
		--build-arg VERSION=${VERSION} \
		--build-arg ENV_NAME=$$(head -1 environment.yml | cut -d' ' -f2) \
		--target jupyter \
		-t ${IMG_REPO}:${VERSION}-jupyter .

run-jupyter:
	@docker run -it --rm -p 10000:8888 \
		--name pandas-notebook \
		-v "${NOTEBOOKS}":/usr/local/src ${IMG_REPO}:jupyter

publish-versions:
	@echo "Publishing pandas image version ${VERSION}..."
	@docker image push ${IMG_REPO}:${VERSION}
	@docker image push ${IMG_REPO}:${VERSION}-jupyter

publish-latest:
	@echo "Publishing pandas image latest tag..."
	@docker image tag ${IMG_REPO}:latest ${IMG_REPO}:${VERSION}
	@docker image push ${IMG_REPO}:latest
	@docker image tag ${IMG_REPO}:latest-jupyter ${IMG_REPO}:${VERSION}-jupyter
	@docker image push ${IMG_REPO}:latest-jupyter


clean:
	rm -rf *.lock*
	-docker rmi $$(docker images -f "dangling=true" -q)
	-docker rmi $$(docker images | grep 'pandas-' | awk '{ print $$1,$$2 }' | tr ' ' ':' | xargs -I {}) 2> /dev/null
	-docker rmi $$(docker images | grep '${IMG_REPO}' | awk '{ print $$1,$$2 }' | tr ' ' ':' | xargs -I {}) 2> /dev/null

all: clean lock build jupyter dist
.PHONY: all clean lock build dist jupyter run-jupyter publish-versions publish-latest
