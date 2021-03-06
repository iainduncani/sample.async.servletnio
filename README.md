# Java EE7: Non-blocking Servlet IO

Java EE7 added support for Non-blocking Servlet IO (NIO). This sample contains a few variations
to illustrate how to use Non-blocking IO in EE7 applications.

* [Building with maven](#building-with-maven)
* [Downloading WAS Liberty](#downloading-was-liberty)
* [Starting the server](#starting-the-server)

## Building with maven

This sample can be build using [Apache Maven](http://maven.apache.org/).

```bash
$ mvn install
```
 In addition to publishing the war to the local maven repository, the built war file is copied into the apps directory of the server configuration located in the async-servlet-nio-wlpcfg directory:

```text
async-servlet-nio-wlpcfg
 +- servers
     +- servletNioSample                         <-- specific server configuration
        +- server.xml                            <-- server configuration
        +- apps                                  <-- directory for applications
           +- async-servlet-nio-application.war  <-- sample application
        +- logs                                  <-- created if/when you run the server locally
        +- workarea                              <-- created if/when you run the server locally
```


## Downloading WAS Liberty

There are lots of ways to get your hands on WAS Liberty, but two of them follow. Note that you will need a version of Liberty that has support for Servlet 3.1 for this sample. (Java EE 7 Web Profile will do).

### Direct download

To download just the WAS Liberty runtime, go to the [wasdev.net Downloads page][wasdev], and choose between the [latest version of the runtime][wasdev-latest] or the [latest beta][wasdev-beta].

There are a few options to choose from (especially for the beta drivers): choose the one that is most appropriate.
* There are convenience archives for downloading pre-defined content groupings,
* You can add additional features from the repository using the [installUtility][installUtility] or the liberty-maven-plugin.

[wasdev]: https://developer.ibm.com/wasdev/downloads/
[wasdev-latest]: https://developer.ibm.com/wasdev/downloads/liberty-profile-using-non-eclipse-environments/
[wasdev-beta]: https://developer.ibm.com/wasdev/downloads/liberty-profile-beta/
[installUtility]: http://www-01.ibm.com/support/knowledgecenter/#!/was_beta_liberty/com.ibm.websphere.wlp.nd.multiplatform.doc/ae/rwlp_command_installutility.html


### Eclipse / WDT

The WebSphere Development Tools (WDT) for Eclipse can be used to control the server (start/stop/dump/etc.), it also supports incremental publishing with minimal restarts, working with a debugger to step through your applications, etc.

WDT also provides:

* content-assist for server configuration (a nice to have: server configuration is minimal, but the tools can help you find what you need and identify finger-checks, etc.)
* automatic incremental publish of applications so that you can write and test your changes locally without having to go through a build/publish cycle or restart the server (which is not that big a deal given the server restarts lickety-split, but less is more!).

Installing WDT on Eclipse is as simple as a drag-and-drop, but the process is explained [on wasdev.net] [wasdev-wdt].

#### Clone Git Repo

If the sample git repository hasn't been cloned yet, WDT has git tools integrated into the IDE:
1.	Open the Git repositories view  
* Window -> Show View -> Other
* Type "git" in the filter box, and select "Git Repositories"
2.	Copy Git repo url by finding the textbox under " HTTPS clone URL" at the top of this page, and select "Copy to clipboard"
3.	In the Git repositories view, select the hyperlink "Clone a Git repository"
4.	The git repo url should already be filled in.  Select "Next "-> "Next" -> "Finish"
5.	The "sample.async.websockets [master]" repo should appear in the view

#### Import Maven projects into WDT

1.	In the Git Repository view, expand the websocket repo to see the "Working Directory" folder
2.	Right-click on this folder, and select "Copy path to Clipboard"
3.	Select menu "File" -> "Import" -> Maven -> "Existing Maven Projects"
4.	In the Root Directory textbox, Paste in the repository directory.
5.	Select Browse... button and select Finish (confirm it finds 3 pom.xml files)
6.	This will create 3 projects in Eclipse: async-servlet-nio, async-servlet-nio-application, and async-servlet-nio-wlpcfg


For the purposes of this sample, we will create the Liberty server (step 3 in the wasdev.net instructions) a little differently:

1. Create a Runtime Environment in Eclipse
	1. Open the 'Runtime Explorer' view:
		* *Window -> Show View -> Other*
		* type `runtime` in the filter box to find the view (it's under the Server heading).
	2. Right-click in the view, and select *New -> Runtime Environment*
	3. Give the Runtime environment a name, e.g. `wlp-2015.6.0.0` if you're using the June 2015 beta.
	4. Either:
	    * Select an existing installation (perhaps what you downloaded earlier, if you followed those instructions), or
	    * select *Install from an archive or a repository* to download a new Liberty archive.
	5. Follow the prompts (and possibly choose additional features to install) until you *Finish* creating the Runtime Environment
2. Add the User directory from the maven project
	1. *Right-click -> Edit* the Runtime Environment created above
	2. Click the `Advanced Options...` link
    3. If the `async-servlet-nio-wlpcfg` directory is not listed as a User Directory, we need to add it:
        1. Click New
        2. Select the async-servlet-nio-wlpcfg project
        3. Select Finish, OK, Finish
    4. You should see the user directory listed under the Runtime Environment in the Runtime Explorer view.
3. Create a Server to represent our application and maven configuration in Eclipse
	1. From the Runtime Explorer view, *Right-click* on the async-websocket-wlpcfg user directory associated with the target runtime, and select *New Server*.
	2. The resulting dialog should be pre-populated with the websocketSample Liberty profile server.
	3. Click Finish
	
#### Running application from WDT

1.	Select the "async-servlet-nio-application" project
2.	Right-click -> "Run As..." -> "Run On Server"
3.	Select the WebShere Application Server under localhost folder, and select "Finish"
4.	Confirm web browser opens on "http://localhost:9082/servlet-nio/ " with hyperlinks to run samples
	

*Note:* This step is trying something experimental, so please let us know if you like it!

[wasdev-wdt]: https://developer.ibm.com/wasdev/downloads/liberty-profile-using-eclipse/


## Starting the server

There are a few options for starting the server, three of which are below. Once the server has been started, go to [http://localhost:9082/servlet-nio/](http://localhost:9082/servlet-nio/) to view the sample.

### Running on the command line

Based on the server directory generated above, running the build application is easy:

```bash
$ export WLP_USER_DIR=/path/to/sample.async.servlet.nio/async-servlet-nio-wlpcfg
$ /path/to/wlp/bin/server run servletNioSample
```

* `run` runs the server in the foreground.
* `start` runs the server in the background. Look in the logs directory for console.log to see what's going on, e.g.

```bash
$ tail -f ${WLP_USER_DIR}/servers/servletNioSample/logs/console.log
```


*Note/Option:* The maven target will clean server output (logs and workarea, etc) from the async-servlet-nio-wlpcfg directory, however, if you wanted to maintain strict separation between what is checked into async-servlet-nio-wlpcfg and what is generated by a running server, you could also specify the WLP_OUTPUT_DIR environment variable, e.g. into the maven target directory.

```bash
$ export WLP_OUTPUT_DIR=${WLP_USER_DIR}/target
```


### Running with maven

The liberty-maven-plugin can also control and manipulate the server for use in automated builds to support continuous integration.

* TODO: more here. I know, you're impatient. Stop gnashing your teeth at me.
* Gnash, Gnash, Gnash.


### Using WDT




## Tips

* If you use bash, consider trying the [command line tools](https://github.com/WASdev/util.bash.completion), which provide tab-completion for the server and other commands.


## Notice

© Copyright IBM Corporation 2015.

## License

```text
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
````

