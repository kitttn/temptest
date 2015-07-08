This page provides information about the buildbot system that automates
the Project Ara firmware builds.

####About Buildbot 
Buildbot is an open-source framework for automating software build, 
test, and release processes. Currently, buildbot is only used for
building firmware images. The build is performed whenever changes 
are committed to the github repository.

Buildbot main website:

http://buildbot.net/

Buildbot documentation:

http://docs.buildbot.net/current/index.html

####Buildbot for Project Ara Firmware

There are two machines involved, a master and a slave. The master
contains the buildbot configuration files, controls the slave, 
serves the buildbot web interface, and sends out status emails 
to the firmware mailing list.  

The slave, under control of the master, fetches the code from github, performs the builds, and reports the status to master.  Upon completion of a successful build,
the master uploads the image files and makes them available from the web interface.


