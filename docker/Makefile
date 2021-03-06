.PHONY: all build deps kill run-base run-local run stop

USER := $(shell whoami)
DOCKER_IP := $(shell ip addr show docker0 | grep -Po 'inet \K[\d.]+')

all: build

build:
	docker build -no-cache -rm -t $(USER)/glcd .

deps:
	docker pull nsqio/nsqlookupd
	docker pull nsqio/nsqd
	docker pull dockerfile/mongodb
	docker pull cantsin/glcd

run-base:
	docker run --name lookupd -d -p 4160:4160 -p 4161:4161 nsqio/nsqlookupd
	docker run --name nsqd -d -p 4150:4150 -p 4151:4151 nsqio/nsqd \
	    --broadcast-address=$(DOCKER_IP) \
	    --lookupd-tcp-address=$(DOCKER_IP):4160
	docker run --name mongodb -p 27017:27017 -p 28017:28017 -d dockerfile/mongodb

run-local: run-base
	docker run --name glcd -v $(shell pwd)/../glcd.config.default:/root/glcd.config -d $(USER)/glcd

run: run-base
	docker run --name glcd -v $(shell pwd)/../glcd.config.default:/root/glcd.config -d cantsin/glcd

stop:
	docker stop $(shell docker ps -a -q)

kill:
	docker rm $(shell docker ps -a -q)
