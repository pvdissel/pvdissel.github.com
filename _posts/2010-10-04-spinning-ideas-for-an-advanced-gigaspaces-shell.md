---
layout: post
title: Spinning ideas for an advanced Gigaspaces shell
tags:
- gigaspaces
- gshell
- ideas
- shell
- Technical
published: true
---
**Update 2011-05-28:**
> A colleague of mine did a create job getting a Gigaspaces Shell project started and working, thanks Jos :)
> Check it out on <https://code.google.com/p/gash/>

As the original GigaSpaces command-line scripts and maven OpenSpaces plugin are far to minimalistic to be usefull for *real* world use. <del>I'm thinking of creating something to fill the gap!</del>

So, spinning some ideas for an advanced GigaSpaces shell which is usable in *real* world situations. I'm thinking of features like:

- Usable as interactive AND non-interactive shell
- Platform independend; it should run on all common platforms (win32/64, linux, mac, solaris, etc.) without external dependencies to platform specific commands
- Unified configuration and usage; configuration and usage of the shell must be the same on every platform and between interactive and non-interactive mode; there's *only* one way to do something!
- Option to go in a graphic Interactive shell, something Midnight Commander like so you can walk through a tree with all lookupgroups, gsa, gsc, gsm, lus, pu's, etc. with the tree at the left side and detailed info of the selected item at the right
- Support remote deployments
> eg. move defined files over ssh (with possible gateway in between) to GS server or one that can connect to a lookupgroup(s). And deploy the files to specified lookupgroup(s) with specific properties/sla when needed.
Some requirements can be gathered from the example above (with some extra fantasy :)):
  - Deployable files must be definable
  - Definable files can come from all kind of resources, eg.:
    - Maven repo (configurable)
    - FTP?
    - HTTP(s)?
    - Local disc
    - ..?

- Definable files should be deployed in order of definement, as there often are dependencies between PU's. Undeploy should happen in reverse order.
- All kind of settings must be definable per deployable file, eg.:
  - deployment properties
  - sla
  - ...?

- Ssh needs support for tunneling/gateway, where the gateway as possibly different login credentials as the final ssh login. Usage of Public-Private-keys must be possible.
- Deployment config must be highly modular, eg.:
  - With a general config and possibility to override settings per deployment file
  - Possibility to create config groups and extend them

- Statistics, all supported statistics by the GS Admin API must be usable
- The interactive and non-interactive shell must be intuitive, eg.:
  - Has auto-completion support (eg. bash-like tab-tab auto-completion)
  - Has command history support (eg. bash-like up-down history)
  - Each command must have an usable and self-container help feature (eg. svn-like svn help {command} or svn {command} help)

- Must be able to work with multiple hosts (something [Capistrano](http://capify.org)/[Fabric](http://fabfile.org) like functionality)
- Must be configurable for multiple stages (eg. local, dev, integration, staging, preproduction, production)
- Support for quering spaces
- Show the full url's that webapps are bound to

So for short:
>All possible GS Admin API features must be usable

**Interesting links:**

- [JLine](http://jline.sourceforge.net/) - "a Java library for handling console input"
JLine is used by projects like Spring Roo and GroovyShell. So even tho it's last update was in 2008, it seems to be working very nicely
- [Maven Shell](http://github.com/sonatype/mvnsh/) - "Maven Shell is a CLI interface for Maven that enabled faster turn-around, and a more intelligent interaction with repositories and  projects."
This can be a good resource to take a look at the implementation
- [GS Elastic Service Manage (ESM)](http://www.gigaspaces.com/wiki/display/XAP71/The+Elastic+Service+Manager) and [ESM by Example](http://natishalom.typepad.com/nati_shaloms_blog/2010/06/wtf-is-elastic-data-grid-by-example.html) for creating elastic platform setups
- CLI Arguments with Apache Commons <http://www.baptiste-wicht.com/2010/10/compute-command-line-arguments-with-apache-commons-cli>
