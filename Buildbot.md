This page provides information about the buildbot system that automates
the Project Ara firmware builds.

####About Buildbot 
Buildbot is an open-source framework for automating software build, 
test, and release processes. Currently, buildbot is only used for
building firmware images. The build is performed whenever changes 
are committed to the github repository.

Buildbot main website: http://buildbot.net/

Buildbot documentation: http://docs.buildbot.net/current/index.html

####Buildbot for Project Ara Firmware

There are two machines involved, a master and a slave. The master
contains the buildbot configuration files, monitors the github repository
for changes, commands the slave to perform builds, 
serves the buildbot web interface, and sends out status emails 
to the firmware mailing list.  

Under control of the master, the slave fetches the code from github, 
performs the builds, and reports the status back to master.  Upon completion of a successful build, the master uploads the image files from the slave, and makes them available from the web interface.

####Buildbot web interface
The web interface is served by master here: http://ara-buildbot.leaflabs.com/

##### Brief tour of the Buildbot web interface

The home page is basically a jump page with links to several sub-pages.
Perhaps the most useful is the waterfall display. 

Here's a picture of the waterfall. Click the image for a larger version.
<a href="https://github.com/projectara/Firmware-wiki/wiki/images/waterfall.jpg"><img src="https://github.com/projectara/Firmware-wiki/wiki/images/waterfall.jpg" width="640">
