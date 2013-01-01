---
layout: post
title: Groovy scripting using multiple files
tags:
- groovy
- Technical
published: true
---
Today I completed the second evening of the free Groovy course at [@InterAccess](https://twitter.com/interaccess) given by [@Frans_van_Buul](https://twitter.com/Frans_van_Buul), very interesting to see how Groovy works with in combination with Java code. But I want to use Groovy scripts as replacements for Bash/Batch scripts, the only thing I couldn't find out until now, was how to work with multiple groovy files when using groovy in its scripting context. Finally found it and it couldn't be easier!

{% highlight groovy linenos=table title="main.groovy" %}
#!/usr/bin/env groovy
def sayer = new GhelloSayer()
println sayer.sayGHello()
println new Utils().gotTools()
{% endhighlight %}

{% highlight groovy linenos=table title="GhelloSayer.groovy" %}
class GhelloSayer {
    def sayGHello() {
        return 'Ghello!'
    }
}
{% endhighlight %}

{% highlight groovy linenos=table title="Utils.groovy" %}
def gotTools() {
    return 'Yes I haz'
}
{% endhighlight %}

If you look at these three groovy files, it's very easy to see what Groovy does:

- See if a .groovy file exists equal to the classname you want to instanciate, if so, it loads it
- Non-main groovy scripts should have filenames that always start with a capital first-character, as if it's a Java class source file. This way groovy will put the file in the classpath somehow and the file is loadable as if the filename is the classname and the groovy file content is the content of that class
- This also works with packages, packages just start from the directory of the main script

Magic@work, but this finally makes it possible for me to really replace Bash/Batch scripts with Groovy.

Btw. main.groovy can ofcourse be named anything you like!

/me happy
