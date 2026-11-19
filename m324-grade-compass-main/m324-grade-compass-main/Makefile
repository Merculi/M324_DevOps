SONAR_HOST_URL ?= http://sonarqube:9000
SONAR_NETWORK ?= sonar-net
export SONAR_HOST_URL SONAR_TOKEN

ifeq ($(OS),Windows_NT)
MVN = mvn.cmd
DOCKER = docker.exe
linting: export MSYS2_ARG_CONV_EXCL = type=bind
else
MVN = mvn
DOCKER = docker
linting: SCANNER_USER = --user "$$(id -u):$$(id -g)" --env SONAR_USER_HOME=/tmp/.sonar
endif

.DEFAULT_GOAL := test
.PHONY: test clean coverage linting
test:
	$(MVN) -f backend/pom.xml test

clean:
	$(MVN) -f backend/pom.xml clean

coverage: test
	$(MVN) -f backend/pom.xml jacoco:report dependency:copy-dependencies -DoutputDirectory=target/dependency

linting: coverage
	$(DOCKER) run --rm --network "$(SONAR_NETWORK)" $(SCANNER_USER) \
		--env SONAR_HOST_URL --env SONAR_TOKEN \
		--mount "type=bind,source=.,target=/usr/src" \
		sonarsource/sonar-scanner-cli
