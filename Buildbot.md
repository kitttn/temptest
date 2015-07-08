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

#####Brief tour of the Buildbot web interface

The home page is basically a jump page with links to several sub-pages. Perhaps the most useful of these is the waterfall display. 

######Waterfall display

Here's a picture of the waterfall display. Click the image for a larger version.

<a href="https://github.com/projectara/Firmware-wiki/wiki/images/waterfall.jpg"><img src="https://github.com/projectara/Firmware-wiki/wiki/images/waterfall.jpg" width="640">

In the upper right of the waterfall page, there's a help link that describes a bunch of options you can use to customize how the waterfall page is displayed. You can mess with that at your leisure, but for now, let's talk about the default waterfall display.

The first column at the left is the timeline, showing the local time of the master when events happened. The time zone is shown at the top of the column. Latest events are shown at the top, and events eventually scroll off the bottom as they are displaced.

The next column over is "changes". This column shows the changes on github, with the contributor's name. You can click these items for more information on each change.

The row to the right of "changes" contains the names of builds, known as "builders" in Buildbot parlance. The firmware image builders are named "ara-*". There are two other builds, fwbuild and defconfigs. 

Fwbuild is the top-level builder that triggers all other builders whenever git repository changes are detected. Defconfigs is triggered first. It does a 'make olddefconfig' on all the existing defconfigs (nuttx image build configuration files) under nuttx/configs/ara. If the defconfigs build generates new defconfig files, it stages, commits, and pushes back to the git repository.  If the defconfigs builder does not generate new defconfig files, the stage/commit/push steps are skipped.  In the case where defconfigs are pushed to master, fwbuild does not trigger the remaining builders, which would be redundant since the defconfigs push to the repository will trigger a new build cycle when that change is detected. 

Each of the firmware image builders performs three steps: update, build, and upload. In the update step, the code on the slave is updated from the git repository. In the build step, the build script 'build_ara_image.sh' is invoked to build the specified image. In the upload step, the image files -- nuttx, nuttx.bin, and System.map -- are uploaded to the master from the slave. Click on the [image files] link to open a page where you can download these files.

Just above the row containing the build names, the builder status is shown. Normally this will be idle. 

Across the top is a row showing build names and the status of the most recent build for each.

######Grid and T-Grid display

This page lists each of the build names down the left side, and in a column for each change set detected, the status of the build. The T-Grid shows the same information, transposed about the diagonal.

######Console display

Console view shows a list of change sets down the left side, and allows a developer to quickly see the status of each builder for the build including his or her change. A green box means that the change didn't break any of the builds. A red box means the change broke the build in some way.  A yellow box means the build is in progress.

######Builders display


