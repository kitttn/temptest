Table of Contents
Table of Contents
What is this?
Basics: who’s who, scheduling lingo
Spiral 1
Spiral 2
Spiral 3
What to Read Next
Important but not covered well here (or elsewhere?)
Glossary
What is this?
This is a “getting started” doc for doing software development for Project Ara. Hopefully you know this already, but the goal of Project Ara is to produce a modular smartphone platform, where almost all of the hardware normally bound to a particular phone is hot-pluggable and replaceable.

It’s mostly Marti’s opinions (Marti works for LeafLabs, and is the Project Ara firmware lead; see below for a cast of characters), and shouldn’t be considered Google policy.
Basics: who’s who, scheduling, lingo
Project Ara is run by Regina Dugan, and is part of Google’s ATAP (Advanced  Technology and Projects) R&D department. Greg Kroah-Hartman (greg@kroah.com) is the software lead for Project Ara.

ATAP projects have fixed duration (Project Ara is scheduled to launch in 2015), and have a small staff within Google that manages the project. Everything else (like software) is done by external contractors (like you). ATAP  and Project Ara were formerly part of Motorola, but moved to Google when Lenovo bought Motorola. The goal of an ATAP project is to do something wildly ambitious and then produce a Demo at Convincing Scale (D@CS) that convinces a product team within Google to take the idea and run with it. The Project Ara D@CS is a limited launch within a small target market. In January, it was announced that Puerto Rico will be the launch site. The success of this launch will be determined by both the market’s reaction and the characteristics of the module developer ecosystem that results.

Logistically, Project Ara is organized into spirals. What goes into each spiral has changed a bit over time, but basically, each spiral takes at least a few months, and has a set of main deliverables. The companies of contractors are organized into workstreams. Each non-Google person working on Project Ara is sometimes called a “performer” (which covers engineering, management, industrial design, etc.).
Spiral 1
In theory, spiral 1 lasted from August 2013 through Google I/O on June 26, 2014 , but in reality overlapped with spiral 2 into early 2015.

The spiral 1 deliverables were an architecture for a modular cell phone, development boards for module developer prize challenges, and a first prototype. The spiral 1 software team were performers working under the umbrella of NK Labs, as part of the Electrical Engineering workstream. During this time, we decided on UniPro as our network architecture, electropermanent magnets (EPMs) to attach modules, and a noncontact media converter for UniPro’s usual M-PHY (i.e., data transfer over the air gap between the module and the endoskeleton).

The main documentation output from spiral 1 are the documents, schematics, and reference sources in the Module Developer’s Kit (MDK). The spiral 1 MDK version was v0.11; it is now obsolete. At launch, the MDK will contain everything a module vendor needs to know in order to make modules and get them certified for sale. Later MDK releases have included updates as requirements and design decisions changed.

During spiral 1, Toshiba signed on to produce UniPro bridge and switch ASICs, since none of those existed. In the interim, the spiral prototype was full of FPGAs with UniPro IP on them, which tunneled existing native protocols to save driver development time on the Android side. This let the AP running Android pretend it was connected directly to each peripheral (e.g., SDIO went into one FPGA on the AP module, through an FPGA UniPro switch, and SDIO came out of another FPGA on the WiFi module side). This allowed using existing device drivers (mostly) unchanged.
Spiral 2
Spiral 2 started immediately after Spiral 1, and is still ongoing (as of February 2015). The original deliverables are a booting phone that makes a phone call and battery hotswap, and uses the first engineering samples (ES1) of the Toshiba ASICs. These have changed; at time of writing (2015-02-18), the ES1 demo deliverables are being revisited.

As of Spiral 2, we now have:
A Google Drive (log in with your @projectara.com Google Account), where all files are kept.
A JIRA instance for tracking work. A usage guide for the software team is available as a section within the project-wide JIRA usage guide.
Google Hangouts for video chat meetings. These are called “IPTs” (in-person teleconferences) sometimes.
A software-related IRC channel for quick questions, etc.

During spiral 2, software development began with HAPS-62 FPGA boards, as shown in the  block diagram of slide 1, here. The Virtex 6 FPGAs on the HAPS-62 board provided emulation for the Toshiba AP and GP bridge ASICs. (We will also use HAPS-62 boards to develop the boot ROM for the Toshiba ES3 bridge ASICs, so the platform is still in limited use, mostly at the LeafLabs Boston site.)

When ES1 ASICs were available, software development continued using a “Big Dev Board” (BDB), containing a UniPro switch surrounded by bridge ASICs. There are several different BDBs out there. The BDBs with ES1 ASICs on them are “BDB1s;” there were two revisions, BDB1A and BDB1B.

Simultaneously to BDB development, software was written for form factor (FF) modules on an endoskeleton. (Due to some hardware issues, we weren’t able to fully test everything on BDBs in time for demos.)

Engineering in spiral 2 is done by a variety of companies; here are some of them:

BayLibre: also firmware for bridge ASICs, as a subcontractor to LeafLabs. The PoC is Benoit Cousson (bcousson@baylibre.com).
LeafLabs: firmware in general. Marti at LeafLabs (mbolivar@leaflabs.com) is the firmware lead for Project Ara, and has been involved since the beginning of the project.
Linaro: Linaro is doing the brunt of the Android and kernel development (Opersys is the other company involved in those areas). The main PoC at Linaro is George Grey (george.grey@linaro.org), Glen Valante (glen.valante@linaro.org) is the Linaro Project Manager for Ara.
Linux Solutions: Greg Kroah-Hartman (greg@kroah.com) has been working on the Greybus specification and kernel portions of the Greybus core, before being conned^Wpersuaded to be the software lead for the whole team.
Oxford Systems: Olin Sibert (olin@oxford.com) has been involved with the UniPro working group at MIPI, and is developing the ES3 ASIC boot ROM.
Opersys: Karim Yaghmour (karim.yaghmour@opersys.com) is working on Android and literally wrote the book on how to design an Android system.
Quanta: Quanta is doing all of the board layout and fab. The engineering lead at Quanta is Dexter Yeh (Dexter.Yeh@quantatw.com); the PM is Philip Chen (Philip.Chen@quantatw.com).
Toshiba: Toshiba is producing UniPro bridge ASICs. The documentation for these is in the UniPro Network workstream directory.
X5: Derek Linden, in concert with Quanta, is designing the antenna for the phone and WiFi. The antennas are designed by a genetic algorithm using custom software developed by X5.
Spiral 3
Work on Spiral 3 started in January 2015. Spiral 3 is based around the ES3 Toshiba ASICs, which are still (as of February 2015) in development.

The goals for spiral 3 are to reach market pilot.
What to Read Next
The Software Project sections in the Project Ara JIRA Usage Guide. These describe how we use JIRA to track issues and schedule work.
The Project Ara Software Architecture doc is the main reference for decisions we’ve made about software design. You should read that next, though it’s currently (8/29) incomplete or obsolete in some places and rough in others, as we are still in the design phase.
Project Ara Module Developer’s Kit, v0.11 (MDK). BEWARE: the software sections in v0.11 are largely obsolete. New decisions get made over email and during meetings, and are written into the Software Architecture document as they are finalized. However, the industrial and mechanical design descriptions (and definitions) in the v0.11 MDK are still essentially valid, so those parts of MDK are a good next things to read.
The MIPI Specs folder contains various MIPI specifications. The most relevant one for the software team in general is UniPro v1.6. There are a couple of other protocols built on top of UniPro you should be aware of:
CSI-3 for cameras (its predecessor, CSI-2, is not based on UniPro, but we still use it for display and camera data in the Toshiba bridge ASICs to support existing chipsets)
UFS (JEDEC login-wall; your @projectara.com address can get you an account)
DSI-2 is a UniPro-based display protocol that we are not thinking about, as it didn’t get much traction.
The Greybus Application Protocol doc is a work in progress for how modules and the supervisory controller on the endoskeleton will communicate with one another. You should look at this after the Software Architecture doc, or at least its introduction.

Important but not covered well here (or elsewhere?)
Device drivers:
The Greybus Specification is part of the MDK, and describes protocols we are using to write device drivers for UniPro-based devices. The kernel implementation is on GitHub.
The long term goal is to use “device class drivers.” This will allow a single driver within the kernel to support a variety of devices. For example, all battery modules would be supported by a single battery device class driver in the kernel.
There are also “bridged PHY drivers” which allow access to the various peripherals available on the Toshiba bridges (like UART, SPI, SDIO, etc.). There are two reasons for bridged PHY drivers.
First, to allow use of existing in-kernel drivers for particular devices.
Second, to allow userspace apps to access these PHYs for “weird and wonderful” modules that don’t fit into a device class. For example, a pollution sensor that uses SPI could be supported entirely in userspace with a custom app that uses the SPI bridged PHY driver in the kernel.
Android Build
Info on Android Build  -  here
Glossary
 is a (forever incomplete) collection of terms you will run into during your day-to-day. It should probably go into the MDK. This

Feel free to add more, but keep the list in alphabetical order!

AP: Application Processor.  Where Linux runs, along with the rest of the Android userspace code.
ATAP: Advanced Technology and Projects. The Google R&D department that Project Ara is part of.
Bridge ASICs: Custom ASIC developed for Project Ara by Toshiba, which allow devices speaking existing protocols (like SoCs, baseband modules, displays, etc.) to communicate through the UniPro network. The bridge ASICs do some of this protocol conversion in firmware, and some in hardware. The allocation between which protocols are handled by firmware versus software is subject to change between ES1 and ES2.
Bundle: Within an Interface there are CPorts, and each CPort connects to the AP, or another Interface, using a Protocol to define the data stream.  One, or more than one, CPorts together can make up a Bundle to provide a unified way to communicate between an Interface and the AP.
CPort: A UniPro term.  It is a single “address” on a Module.
Connection: A logical communication path between two CPorts.  A connection is defined by two (Device, CPort) tuples.  These are UniPro Device and CPort numbers associated with the AP and with a device on another module.
D@CS: Demo at Convincing Scale. The Project Ara launch.
Endoskeleton (endo for short): the metal chassis that modules plug in to.
EPM: electropermanent magnet. With a current pulse, it transitions from a weak magnet to a strong one. This is how modules attach to the endoskeleton.
ESx: Engineering Sample x, e.g. for the Toshiba ASICs (e.g. ES1 is the first engineering sample, which we will get first silicon for during Spiral 2).
GP Bridge: Microprocessor in a module that talks to the Unipro ASIC in the module as well as the hardware on the module that does not talk Unipro.
IA: Information Assurance, a newspeak term for what most of the world calls “computer security”. IA in Ara includes hardware security mechanisms (e.g., in the UniPro ASICs) as well as firmware/software.
Interface block: the pins (spiral 2) and combination of pins/noncontact data transfer interfaces (spiral 3) that form the interface between the PCBs inside a module and inside the endoskeleton.  There can be more than one interface block per module.
IPT: In-person teleconference. Basically, a video chat on Google Hangouts.
MDK: Module Developer’s Kit. The specification for module developers that we periodically update and release as our design progresses.
Module: A physical device that can be slid into the Endoskeleton.
Operation: represents an RPC-like interaction using a Connection between the AP and an Interface.  See the Greybus Application Protocol for more information about Operations.
Protocol: defines the stream of bytes that pass on a single Connection in a specified form to talk to between the two ends of the connection.
SVC: supervisory controller. A microcontroller in the endoskeleton that administers the power bus, controls the EPMs, controls the switch, and communicates with the AP to administer the network.
UniPro: the on-board network protocol used to send information between modules.
Wake/Detect: out-of-band (with respect to UniPro) signals used to convey hotplug and wake from sleep between modules and the endoskeleton.
Workstream: A logical topic of work in the project. Roughly, these break down as software, electrical, mechanical, industrial design, and radio, though the boundaries are somewhat porous.
