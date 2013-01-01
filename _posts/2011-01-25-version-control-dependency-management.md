---
layout: post
title: Version Control != Dependency Management
tags:
- ant
- apache
- dependency management
- ify
- make
- maven
- pear
- pear2
- phing
- php
- pom
- pyrus
- svn
- Technical
- vcs
published: true
---
*This article was originally featured in the [August of 2010 issue](http://www.phparch.com/magazine/2010-2/august/) of [phpArchitect](http://www.phparch.com).*
> Are you using [svn:externals](http://svnbook.red-bean.com/en/1.5/svn-book.html#svn.advanced.externals), [git-submodules](http://book.git-scm.com/5_submodules.html) or something similar with your Version Control System (VCS) of choice for connecting third-party libraries to your PHP projects? This article explores ways to handle dependencies in PHP projects, just like projects in other programming languages have done for ages.

## Dependencies

Many projects have some kind of external dependency, e.g. libraries, language extensions, system tools or other applications. All these dependencies need to be available for the project to work. In PHP, dependencies like language extensions can be checked to be loaded with calls to the "extension_loaded" or "get_loaded_extensions" functions. The availability of system tools and other application can be checked by calling the command with for example the PHP [file_exists](http://php.net/file_exists) function in combination with [is_executable](http://php.net/is_executable). Availability of a required library can be checked by asking for it through the [class_exists](http://php.net/class_exists) function when using auto-loading or simply [require_once](http://php.net/require_once) the library and wait for it to fail.

For language extensions, system tools and applications, their availability should be checked. These are dependencies that should not be included in the project package, because they are often platform depended and there is a fair chance that these dependencies are already available on the system running the project. The previous paragraph explained ways of checking them. These checks can be easily executed from an install/release script and let the install fail when something is wrong.

But how about library dependencies? The distributed project package should already contain the required libraries or at least fetch them automatically. Fetching libraries automatically on install sounds good because then the project package for distribution is smaller, saving storage and bandwidth. But it makes your customers, during the installation of the project, depended on the availability of the required libraries from possibly 3rd parties. Think about it. If the current version of your project is still used 5 years from now, can you guarantee that the 3rd parties still provide that specific library version?
On the other hand, when the project is extendable with plugins and modules, fetching the those only when the customer chooses to use them is a fair solution. But the distributed project package should include all the default plugins and modules and all extensions should be fetched from your repository, so you have control over the availability.

## svn:externals

How and when are required libraries included in your project?
Many PHP projects, that use Subversion (*svn*) as their Version Control System (VCS) of choice, use the [svn:externals](http://svnbook.red-bean.com/en/1.5/svn-book.html#svn.advanced.externals) functionality to bind libraries to their project. It's an easy solution; with every *svn checkout* or *svn update* the external libraries are also checked-out and updated into the local working copy. Meaning, it's possible to directly edit and commit on the external libraries. And svn gives much freedom:

- Add an external pointing to the latest (HEAD) revision
- Add an external pointing to a specific revision
- Add an external from an external repository
- Set the externals property anywhere in the project and including that external somewhere else in the project structure


This means that there is no central location to configure the externals. Everyone working on the project should just know where it is set or search through the svn properties of all the project directories. Dependency management becomes magic "that just works", or does it?

Ever tried to branch or tag a project that uses svn:externals for dependency management? First branching/tagging all the externals, then updating the svn:externals to those branched/tagged externals and finally create the branch/tag for the project. That's not workable.

Branching/tagging can be made easier by applying the convention that externals should always point to tags. It's easier branching/tagging because the externals will then always be pointing to a stable tag version. But svn still offers commit permission on the external that is pointing to a tag. Specifying the revision that the external should point to is the safest way to work with externals, this way the external will always be exact at that revision.

So what's wrong with using a VCS for dependency management?

- No overview of external configuration, they can be defined and placed anywhere in the project
- Externals must be controlled in the same type of VCS. There's no way to add a git repository as external in svn, or a simple .tar.bz2-file
- Committing on externals is possible

## How do other programming language handle dependencies

The most common lines of an "INSTALL" file of Linux C/C++ projects are

    $ ./configure
    $ make
    $ sudo make install

Ever tried that on a big project? Did "make" error-out the first time it ran? And the second time? Telling it missed a specific library and therefore could not continue? Dependencies! The project could not be compiled without having the correct dependencies available.

Java build tools like [Apache Maven](http://maven.apache.org) and [Apache Ant](http://ant.apache.org) with [Apache Ivy](http://ant.apache.org/ivy/) went a step further. Maven defines the project in a [Project Object Model](http://maven.apache.org/pom.html) (pom.xml) file, which describes the software project being built. Containing it's dependencies on other projects/modules and external libraries, a list of the involved developers, links to the version control repository, issue tracker and more. Maven has a central repository which can be used to automatically fetch the project dependencies from, when they exist. Everyone can create their own Maven repository that can be used as a proxy to the central repository. This means the Java project can be build and all needed dependencies are automatically fetched from the central repository or any other repository that is provided in the Maven global settings or project POM configuration. Apache Ant with Apache Ivy works somewhat the same.
This all leads to the fact that with a build tool like Maven, there is a single configuration file per project for defining dependencies. That's easy and clear for everyone.

The C/C++/Java languages have the advantage to be compiled languages. If a required dependency is missing, the project won't compile. PHP does not have this advantage. It will run any code until it fails. Therefore PHP projects need some checking for the availability of dependencies.

## So, how to apply this on PHP projects?

There is nothing fully equivalent to Maven for PHP except Maven itself with a PHP plug-in. This is what the "Maven for PHP" project does. It's probably also possible to use Apache Ant with Ivy or other build tools from other programming languages to build PHP projects with. But this has the disadvantage that the build tool is written in a different language than PHP, most probably has no support for PHP specific tools by default and wanting to extend it forces to use the language that the build tool is written in. This makes using a non-native PHP build tool a bit harder.

But there are native PHP tools that provide parts of the features that build tools like Apache Maven provides.
We have [Phing](http://phing.info), a build tool based on Apache Ant, which has many tasks by default for executing filesystem related commands. Optionally it also contains tasks for many commonly used tools and release methods in the PHP world, like; DbDeploy, FtpDeploy, JsMin, PearPackage, PharPackage, PhpCodeSniffer, PHPUnit and much more. It's also very easy to extend, just create a PHP class which extends from the Phing Task class and implement a few functions. It can be made as complex as needed, but it's all in PHP, your language of choice.
For the repository part of a Maven like native PHP tool, we have PEAR for PHP4 and 5 packages and PEAR2 for PHP 5.3.1+ packages. PEAR and PEAR2 are "a framework, packaging and distribution system for reusable PHP Components". There is a PEAR and PEAR2 framework specific repository, "Channel" in PEAR terms, at <http://pear.php.net> and <http://pear2.php.net> respectively. PEAR2 is the next-generation version of PEAR using the new PHP 5+ language features where possible, like namespaces and supports the Phar packaging format. PEAR2 also has a new installer, for installing packages and dependencies, called [Pyrus](http://pear2.php.net). Pyrus is an easy to use installer which can easily be used standalone without installation from the command-line or be integrated into a project. Just like with Maven, it's possible to create your own PEAR channel as a repository for projects and dependencies.

All this completes the combined feature list of Phing, PEAR/PEAR2 and Pyrus equal to that of Maven.

One drawback is that Phing does not (yet) has a task for installing dependencies from Pear/Pear2/Pyrus, but tasks doing just that can easily be written. When you do, please contribute it to the Phing project. The same is true for Version Control Systems other than CVS and Subversion, and everything else you want support for.

## To conclude

For more information on referenced tools and a direct link to the "Extending Phing" documentation of Phing see the related url's list. I also want to hint you on the article "Going Industrial" by Stéphen Périn, which was published in the phpArchitect issue of Januari 2010, for more interesting insights and hints on what tools and practices could be used to improve you PHP development life cycle.

## Requirements

- PHP:
  - 5\.0\.2\+ (for Phing)
  - 5\.2\+ (for Phar)
  - 5\.3\.1\+ (for Pyrus)

## Related URLs

- Phing - <http://www.phing.info>
- PEAR - <http://pear.php.net>
- PEAR2 - <http://pear2.php.net>
- PHAR - <http://php.net/phar>
- PHP Make - <http://www.pake-project.com>
- Wikipedia 'make' - <http://en.wikipedia.org/wiki/Make_%28software%29>
- Apache Maven - <http://maven.apache.org>
- Apache Ant - <http://ant.apache.org>
- Apache Ivy - <http://ant.apache.org/ivy/>
- Extending Phing - <http://phing.info/docs/guide/stable/chapters/ExtendingPhing.html>
