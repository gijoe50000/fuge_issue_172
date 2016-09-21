![fuge-logo][logo]

# fuge
[![npm version][npm-badge]][npm-url]
[![npm downloads][npm-downloads-badge]][npm-url]
[![Build Status][travis-badge]][travis-url]
[![Win Status][win-badge]][win-url]
[![Gitter][gitter-badge]][gitter-url]

Fuge provides a generation and execution environment for microservice development on Node.js. It aims to help developers working on microservice systems in several ways:

- By providing a service execution environment
- By reducing the friction between processes and containers during development

Full documentation is available at [fuge.io](http://fuge.io/).

If you're using this module, and need help, you can:

- Post a [github issue][],
- Ask on the [Gitter][gitter-url].

## Install
To install, use npm to install globally.

```
npm install -g fuge
```

## Usage
In order to run your system via fuge, you need the following key files,

* A `docker-compose.yaml` file that serves as the main configuration reference for the system
* A `fuge-config.json` file that contains fuge specific settings and overrides not supported by docker-compose

Each service in the system should have a Dockerfile or an entry in the overrides section of `fuge-config.json`. Once added
follow the commands below to configure, start, stop, and build your system.

### Docker
Fuge is fully compatible the the v1 docker-compose format. This means the fuge docker-compose file can be run entirely using docker-compose. If you have Docker installed you can try this out by running,

```
cd ./fuge_system_folder
docker-compose up
```

Docker will build a set of containers and start them up for you using the docker-compose yaml file.

__NOTE:__ currently fuge only supports the docker-compose v1 format.

#### Proxy
In addition to running Docker containers, Fuge will proxy connections to the Docker virtual machine on a mac and windows environment. Running the proxy command will show you how fuge is proxying connections to the Docker containers running as part of your system.

When using Docker on linux or via Docker for Mac/Win ensure `proxy` is set to `none`. Proxy is only required when you are using docker-toolbox or specifically working with proxied connections.

### Running Infrastructure
Let's say that we want one of our services to connect a redis server. We could go ahead and install redis to our development system. Another approach however is to use Docker. If you have Docker installed then try the following:

Edit the system yaml file (let's name it `compose-dev.yml`) and add the following:

```
redis:
  image: redis
  ports:
    - 6379:6379
```

Ensure that the redis container is on your system by running:

```
docker pull redis
```

Start the system up in the fuge shell again by running:

```
fuge shell ./fuge/compose-dev.yml
```

Starting fuge will now start the redis container as well as your micro-services.

### Sample config
```
module.exports = {

  // run docker containers. If false containers with image attribute will not be run
  runDocker: true,

  // proxy settings - one of the options: docker | process | all | none
  // if you are running docker natively (docker for mac/win/linux) proxy
  // must be set to 'none'. Set to docker to use with docker-toolbox.
  proxy: 'none',

  // if true tail running process to the shell by default
  tail: false,

  // if true monitor running processes for changes by default
  monitor: true,

  // exclude these patterns from the monitor
  exclude: /node_modules|\.git|\.log/mgi,

  // override section. Allows the default build, run and debug commands
  // to be overriden on a service by service basis. These commands are
  // normally generated by inspecting the Dockerfile for a service
  overrides: {
    service1: { build: 'sh build.sh' }
  }
}
```

## Terminal Commands

Fuge commands have the following format:

`fuge <command> <options>`

The current supported commands are:

* `fuge build` - build a system by executing the RUN commands in each services Dockerfile
* `fuge pull <compose-file>` - update a system by attempting a git pull against each service
* `fuge clone <Github repo>` - clone a Github repo, supports all valid repo name formats
* `fuge generate <Github repo>` - alias for `fuge clone`. Will be deprecated.
* `fuge run <compose-file>` - run a system
* `fuge preview <compose-file>` - preview a run command for a system
* `fuge shell <compose-file>` - start an interactive shell for a system
* `fuge version` - display the fuge version
* `fuge help` - show help

## Fuge Shell Commands
Fuge provides a simple shell to manage microservice execution. To start the shell run:

```
fuge shell ./fuge/compose-dev.yml
```

The fuge shell supports the following commands:

* `help` - display a list of supported commands
* `ps` - list status of managed processes and containers
* `proxy` - list proxy and port forwarding status
* `info [process name]` - show information on a specific process
* `stop [process]` - stop a process and any associated watcher
* `stop all` - stop all processes and watchers
* `start [process]` - start a process
* `start all` - start all stopped processes
* `debug [process]` - start a process in debug mode and launch node-debug (experimental)
* `profile [process]` - start a process via [0x][], when the process is stopped a flamegraph will be generated in `/services`.
* `watch [process] | all` - turn on watching for a process or for all processes
* `unwatch [process] | all` - turn off watching for a process or for all processes
* `tail [process] | all` - tail output for a process or for all processes
* `untail [process] | all` - end tail output for a specific processes or for all processes
* `grep 'search string' [process]` - searches a process' log or all processes' logs
* `exit` - terminate all managed processes and exit

## Contributing
The [apparatus team][] encourage open participation. If you feel you can help in any way, be it with
documentation, examples, extra testing, or new features please get in touch.

## License
Copyright the apparatus team 2016, Licensed under [MIT][].

[logo]: http://fuge.io/files/fuge-logo.png

[npm-badge]: https://badge.fury.io/js/fuge.svg
[npm-url]: https://badge.fury.io/js/fuge
[npm-downloads-badge]: https://img.shields.io/npm/dm/fuge.svg?maxAge=2592000
[win-badge]: https://ci.appveyor.com/api/projects/status/github/apparatus/fuge?branch=master&svg=true
[win-url]: https://ci.appveyor.com/project/mcdonnelldean/fuge
[travis-badge]: https://travis-ci.org/apparatus/fuge.svg?branch=master
[travis-url]: https://travis-ci.org/apparatus/fuge
[gitter-badge]: https://badges.gitter.im/Join%20Chat.svg
[gitter-url]: https://gitter.im/apparatus

[MIT]: ./LICENSE
[github issue]: https://github.com/apparatus/fuge-runner/issues/new
[apparatus team]: https://github.com/apparatus

[0x]: https://www.npmjs.com/package/0x
