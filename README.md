Heroku Buildpack: Nailgun
=========================

Install [Nailgun](http://www.martiansoftware.com/nailgun/) on Heroku.


Nailgun
-------
[Nailgun](http://www.martiansoftware.com/nailgun/) has the aim to overcome the long startup times of Java Virtual Machines (JVM) when launching Java programs.

For using Nailgun, you first launch a JVM that executes the **Nailgun server**.

For executing your Java program, you use the **Nailgun client**. This is a program that you supply with the name of the Java class you want to run and that then orders the server to execute this class on the already running server JVM. Thus, no new JVM needs to be started up, and the startup delay is dispensed with.

More information on the [Nailgun website](http://www.martiansoftware.com/nailgun/) and on the [GitHub repo](https://github.com/martylamb/nailgun).



Usage
-----

Use it stand-alone:

    $ heroku config:set BUILDPACK_URL=https://github.com/weibeld/heroku-buildpack-nailgun.git

    $ git push heroku master

Or in combination with other buildpacks (e.g. Ruby) with [heroku-buildpack-multi](https://github.com/ddollar/heroku-buildpack-multi):

    $ heroku config:set BUILDPACK_URL=https://github.com/ddollar/heroku-buildpack-multi.git

    # Create file .buildpacks in root directory of your application
    $ cat .buildpacks
    https://github.com/heroku/heroku-buildpack-ruby.git
    https://github.com/weibeld/heroku-buildpack-nailgun.git

    $ git push heroku master



Nailgun Usage
-------------

After the build, Nailgun is installed in the following form:

* `/path/nailgun` The Nailgun installation directory (added to `PATH`)
* `/path/nailgun/server.jar` The Nailgun server
* `/path/nailgun/ng` The Nailgun client (binary executable)

-----

### Starting the server

    java -classpath /path/nailgun/server.jar com.martiansoftware.nailgun.NGServer &

Note: `com.martiansoftware.nailgun.NGServer` is the main class of `server.jar`

-----

### Executing a Java class

First, add all the required classpaths to the server JVM.

    ng ng-cp /class/path /another/class/path

Then, execute the class via the Nailgun client.

    ng full.class.name.MyClass <arg1> <arg2> ...

That's it! The class will run on the server JVM, and not on an own new one.

-----

### Executing a JAR file

First, add the JAR file to the classpath of the server JVM.

    ng ng-cp /path/to/MyApp.jar

Then, find out which is the main class of the JAR file. This can be done, for example, by unpacking the JAR file (`jar xf MyApp.jar`) and checking the `META-INF/MANIFEST.MF` file.

Execute the JAR's main class via the Nailgun client.

    ng full.class.name.MainClass <arg1> <arg2> ...

where `<arg1>`, `<arg2>` etc. are the arguments that are usually given to the JAR file.

That's it! The JAR file will be executed on the server JVM, and not on an own new one.

-----

### Stopping the server

    ng ng-stop



Starting Server at Dyno Startup
-------------------------------

If the server is to be permanently running, the server startup command can be added as a [.profile.d](https://devcenter.heroku.com/articles/profiled) script. The .profile.d scripts are sourced at dyno startup.

    # In root directory of application
    $ mkdir -p .profile.d

    # Create file .profile.d/foo.sh containing the start server command
    $ cat .profile.d/foo.sh
    java -classpath /path/nailgun/server.jar com.martiansoftware.nailgun.NGServer &



License
-------

This buildpack is licensed under the MIT License. See [LICENSE.md](LICENSE.md) file.
