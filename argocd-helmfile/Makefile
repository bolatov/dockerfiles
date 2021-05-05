.PHONY: build
build:
	docker build --no-cache -t bolatov/`basename $$PWD` .;
	@version=$$(docker inspect -f {{.Config.Labels.version}} bolatov/`basename $$PWD`); \
		if [ -n "$$version" ]; then \
			docker tag bolatov/`basename $$PWD`:latest bolatov/`basename $$PWD`:$$version; \
		fi

.PHONY: check
check:
	@version=$$(docker inspect -f {{.Config.Labels.version}} bolatov/`basename $$PWD`); \
		if [ -z "$$version" ]; then \
			echo "\033[91mError: version is not defined in Dockerfile.\033[0m"; \
			exit 1; \
		fi;
	@echo "\033[92mno problem.\033[0m";

.PHONY: test
test:
	docker-compose -f docker-compose.test.yml up --build --no-start sut
	docker cp $(shell pwd)/goss `basename $$PWD`:/goss
	docker-compose -f docker-compose.test.yml up --no-recreate --exit-code-from sut sut

.PHONY: push
push:
	@version=$$(docker inspect -f {{.Config.Labels.version}} bolatov/`basename $$PWD`:latest); \
		if docker inspect --format='{{index .RepoDigests 0}}' bolatov/$$(basename $$PWD):$$version >/dev/null 2>&1; then \
			echo "no changes"; \
		else \
			docker push bolatov/`basename $$PWD`; \
		fi
