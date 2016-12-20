# ![logo](https://raw.githubusercontent.com/sagemathinc/smc/master/src/webapp-lib/favicon-48.png) SageMathCloud (SMC)

#### _A collaborative web-based interface to Sage, IPython, LaTeX and the Terminal_

## Copyright/License

This version of the code is released under the 2-clause BSD license.
The copyright is (c) 2016, University of Washington.

## NSF grant support

This work was supported by National Science Foundation Grants DMS-1161226 and DMS-1147802.

## Website

   * [SageMathCloud](https://cloud.sagemath.com)
   * [Github](https://github.com/sagemathinc/smc)
   * [Developer mailing list](https://groups.google.com/forum/#!forum/sage-cloud-devel)

## Development/install

   * `git clone https://github.com/sagemathinc/smc` -- copy repo
   * `cd smc/src`
   * `npm run install-all` -- build
   * `npm test` -- run test suite (expected failures if your clock is not UTC)
   * `install.py all --compute --web` -- build and install some parts system-wide for development use
   * See `INSTALL.md` for more details.
   * Docker: https://github.com/sagemathinc/smc/tree/master/src/dev/docker

## Contributors

### Active contributors

   * William Stein, SageMath Inc and University of Washington -- founder; everything
   * Harald Schilly, Vienna, Austria -- everything
   * Tim Clemans -- fontend work
   * John Jeng -- frontend work
   * Hal Snyder -- backend Python-related work
   * Simon Luu -- frontend work (chat)
   * Todd Zimmerman -- screencasts
   * Greg Bard -- updating the FAQ; documentation

### Past contributors

   * Jon Lee, University of Washington -- frontend work, history viewer
   * Rob Beezer, University of Puget Sound -- design, maintenance
   * Nicholas Ruhland, University of Washington -- frontend work, tab reordering and resizing
   * Keith Clawson -- hardware/infrastructure
   * Andy Huchala, University of Washington -- frontend work, bug finding


## Build Status: Testing and Coverage

We test SMC via [Travis CI](https://travis-ci.org).
Here are the results:

* [master](https://github.com/sagemathinc/smc/):
  [![Build Status](https://travis-ci.org/sagemathinc/smc.svg?branch=master)](https://travis-ci.org/sagemathinc/smc)
  [![Coverage Status](https://coveralls.io/repos/sagemathinc/smc/badge.svg)](https://coveralls.io/r/sagemathinc/smc)

DevOps note: The relevant files are:

* .travis.yml - to tell travis-ci what to do (two modes: client and server)
* smc/*/test/mocha.opts - defaults for running mocha
* smc/package.json - the "scripts" section (overwrite mocha reporter, only call `coveralls` when on travis-ci, etc.)

## ARCHITECTURE

  * Client       -- javascript client library that runs in web browser
  * Load balancer/ssl -- HAproxy
  * Database     -- RethinkDB
  * Compute      -- VM's running TCP servers (e.g., sage, console, projects, python3, R, etc.)
  * Hub          -- written in Node.js; primus server; connects with *everything* -- compute servers, database, other hubs, and clients.
  * Storage      -- Snapshots of project data
  * HTTP server  -- Nginx
  * admin.py     -- Python program that uses the paramiko library to start/stop everything
  * The Cloud   -- Google Compute Engine

### Architectural Diagram
<pre>

   Client    Client    Client   Client  ...
     /|\
      |
   https://cloud.sagemath.com (primus)
      |
      |
     \|/
 HAproxy (load balancing...)HAproxy                  Admin     (monitor and control system)
 /|\       /|\      /|\      /|\
  |         |        |        |
  |http1.1  |        |        |
  |         |        |        |
 \|/       \|/      \|/      \|/
 Hub<----> Hub<---->Hub<---> Hub  <-----------> RethinkDB <--> RethinkDB  <--> RethinkDB ...
           /|\      /|\      /|\
            |        |        |
   ---------|        |        | (tcp)
   |                 |        |
   |                 |        |
  \|/               \|/      \|/
 Compute<-------->Compute<-->Compute <--- rsync replication  to Storage Server, which has BTRFS snapshots

</pre>





