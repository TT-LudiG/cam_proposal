# Camera-Based Image Recognition System Proposal

## Proposal Synopsis

This is a proposal to design, install and operate a camera-based human/motor-vehicle recognition, recording and alert-generating system.

The following sections will describe the proposed solution as it pertains to:

1. [System Overview](#system_overview)
2. [Hardware Requirements](#hardware_requirements)
3. [Hardware Recommendations](#hardware_recommendations)
4. [Software Requirements](#software_requirements)
5. [Software Recommendations](#software_recommendations)
6. [Installation Process](#installation_process)
7. [System Design](#system_design)
	1. [Physical Layout](#sd_physical_layout)
	2. [Extensiblity](#sd_extensibility)
	3. [Scalability](#sd_scalability)
	4. [Concerns](#sd_concerns)
8. [Estimated Hardware Costs](#estimated_hardware_costs)
9. [Proposal Conclusion](#proposal_conclusion)

<a name="system_overview"></a>
## System Overview

The system will comprise of simple IP cameras installed on locations with native internet access, likely residential properties, and connected to the on-site internal network via ethernet cables. A pre-programmed, compact computer will be installed on-site, alongside the cameras, for the purpose of reading the video feed from the cameras and running an image-recognition algorithm to identify frames that contain persons and/or cars, and send these frames via the on-site internet to a remote server. This computer will also be connected to the internal on-site network via an Ethernet cable.
The remote server will collect all sent image frames (containing persons and/or vehicles) and store them. The server will then generate an alert message in an appropriate form (instant messenger, SMS, email, etc.) corresponding to the received frame, and send it to the appropriate recipients.

<a name="hardware_requirements"></a>
## Hardware Requirements

The system will require the following pieces of hardware:

* **On-site:**
    * Simple IP cameras supporting PoE and infrared nightvision (meaning no unnecessary onboard intelligence and able to supply power via the Ethernet cable, one-to-many per installation site)
    * Compact, cheap computer running a Unix-based operating system (for running the intelligence required to be local to the cameras, including image recognition, only one per installation site)
    * Ethernet cables (one per camera, one for the computer, one for an Ethernet switch, length to be determined during installation)
    * Ethernet switch supporting PoE (for connecting the Ethernet cables to the on-site router and supplying power via those cables, requiring only one free Ethernet port on the router)
    * Metal pole/fixture, coated with anti-rust spray/paint (for fixing the cameras in position, likely one per camera, depending on the installation site)
    * PVC pipes (for protecting Ethernet cables laid on-site that are exposed to the elements, or underground, to be determined during installation)
 
* **Remote:**
    * Server machine, with data storage capacity (for receiving image frames sent by the on-site computers, storing them, generating alerts, facilitating debugging and maintenance and providing the opportunity for functionality expansion; can be accomplished with a virtual machine)

<a name="hardware_recommendations"></a>
## Hardware Recommendations

The following devices are recommended as viable solutions to the hardware requirements outlined above, with motivation:

* **On-site:**
    * **Simple IP cameras supporting PoE:** Hikvision AcuSense 4MP Bullet Cameras, of appropriate focal length. Reliable, accessable and affordable, if you forgo the more extravagant models that come bundled with intelligent features, such as image recognition (unnecessary in our case, our intelligence will be provided by the on-site computer).
    * **Compact, cheap computer running a Unix-based operating system:** The Raspberry Pi 4 Model B is reliable, accessable and affordable. It has the necessary capability to run an image recognition algorithm to process the video feeds coming from a few cameras in real time without lagging noticibly, local storage capacity will be provided via 32GB MicroSD cards.

* **Remote:**
    * **Server machine, with data storage capacity:** DigitalOcean Droplets are affordable, scalable and relatively simple virtual machines. All DigitalOcean data storage is SSD-based, meaning data retrieval times are minimised for any functionality added in the future that would need said data.

<a name="software_requirements"></a>
## Software Requirements

The system will require the following software functionality, described briefly:

* **On-site:**
    * Functionality to read RTSP video feeds from on-site cameras (via the internal network), and pass image frames along to the image recognition algorithm.
    * Image recognition algorithm, to identify persons and cars in provided image frames and pass said frames along to the networking component.
	* Networking component to send received image frames to the remote server, via internet packets.
 
* **Remote:**
    * Functionality to listen for and receive image frames from on-site client machines via internet packets, reassemble said frames and store them locally in a database.
    * Functionality to parse the local database for newly added image frames, and send alerts containg said frames via appropriate mediums (instant messenger, SMS, email, etc.), referencing contact lists maintained in the local database.

<a name="software_recommendations"></a>
## Software Recommendations

The following 3rd-party applications are recommended as solutions to the software requirements outlined above, with motivation:

* **On-site:**
	* **Image recognition algorithm:** The DeepStack AI Server, run in the Docker deployment environment, is an open-source machine learning algorithmic suite that has an image recognition algorithm, trained for identifying persons and cars, freely available.

<a name="installation_process"></a>
## Installation Process

_(Note that henceforth the Raspberry Pi will be substituted for all mentions of the on-site computer)._

The following tasks will be performed during the on-site installation process:

1. Identifying exactly where all requested cameras must be positioned, to supply the desired area coverage.
2. Install metal poles/fixtures in identified positions, fixing them to objects likes walls and trees as necessary/desired, to be discussed with the property owner.
3. Fix cameras to metal poles/fixtures, adjusting for the desired field of view.
4. Lay Ethernet cables from each of the cameras to the on-site router, running through PVC piping fixed to walls with brackets or buried in trenches as necessary/desired, to be discussed with the property owner.
5. Connect cables to the on-site network via an Ethernet switch placed in the vicinity of the on-site router.
6. Place the Raspberry Pi computer, with SD card, in the vicinity of the on-site router, supplying power as necessary and connecting it to the internal network via the installed Ethernet switch.

<a name="system_design"></a>
## System Design

<a name="sd_physical_layout"></a>
### Physical Layout

The following diagram is intended to provide a look as to what the physical infrastructure, that is to be installed at each site, will look like:

![Physical Layout Diagram](https://drive.google.com/uc?export=view&id=1xe-w7Uk8pZsLqEbjdqd_LWtVK2SNxc4C)

**Green-coloured items denote hardware that will be installed amongst the preexisting, black-coloured infrastructure.**  
**Solid lines between items represent physical Ethernet cables, whereas dotted lines represent internet connections of unspecified nature.**

<a name="sd_extensibility"></a>
### Extensiblity

The system design, as described in previous sections, lends itself well to extension and expansion. Additional cameras can be added by simply plugging them into the installed Ethernet switch. Specialised types of cameras, such as cameras that can reliably discern and detect license plate values on cars, can be added in a similar fashion, with updates to the local Pis being, at most, as complicated as simply swapping out the existing SD cards for updated ones that support the new functionality.
A VPN can be established via the remote server that can expedite the update process, providing remote access to the Pis that circumvent the requirement for physical access to the machines in order to perform both updates and maintenance/debugging, should the need arise. Said VPN could be extended to provide remote access to the on-site cameras as well by routing via the Pi, sidestepping the requirement to log into the on-site router in order to open ports on the outside for purposes of exposing the cameras to remote login, again simplifying updating, debugging and other potential maintenance tasks.
The remote server provides a platform to host potential data-dependant solutions that might be of interest to clients, from simple solutions that expose the collected data on the server to a client via easy-to-use web interfaces, to complex solutions that interpret the data in meaningful ways to provide clients with useful information regarding observed patterns of human and motor-vehicle movement caught on camera. The scalability of virtual machines ensures that additional server functionality will always be possible, provided the additional cost is acceptable.

<a name="sd_scalability"></a>
### Scalability

The system design is also well-suited for scaling with additional cameras being installed and additional properties being included into the system. All intensive machine learning work is performed locally per-site, so as long as the number of cameras per each individual site (a site being defined as containing a single Pi) is kept relatively low, new sites with new cameras can be added to the system without any noticeable performance overhead on the remote server, or the system itself.
The number of cameras possible on a single site with a single Pi are restricted only by the performance of the Pi in running the image recognition algorithm. The networking overhead of adding additional cameras is negligible, since the Pi filters out all irrelevant image frames and only frames that contain people or cars are sent over the internet to the remote server. Thus, performance of the on-site internet connection should mostly unaffected by the inclusion of the system's traffic, regardless of how many cameras are added on the site.

<a name="sd_concerns"></a>
### Concerns

The concerns addressed here are not so much concerns about the core system design, as they are concerns about some of the functionality one might feasibly choose to add to the system. By default, the system only runs image recognition with the intent to discern human figures and cars that appear in the video feed from the cameras, and does not attempt facial recognition or license plate recognition for vehicles. There are 2 reasons for this:

1. License plate recognition requires a particular type of camera that is more costly, cannot reliably discern human from car (thus necessitating a camera that can) and have difficulty operating in the dark (due to the manner in which infrared light reacts to the plates). Cameras that can perform facial recognition are not nearly as particular, however they still have higher resolution requirements than cameras that only need discern humans from cars and may require camera focal lengths that are not optimal for that task.

2. More significantly, the utility of identifying and recording faces and license plates out in public space is situational: Identifying people and cars that pass by a house, or on a street, will likely not assist in you dealing with the police should a burglary/theft occur and the perpetrators escape, unless the footage that indentifies the guilty persons also shows clearly their involvement with the act in question. Or, simply put, correlation does not equal causation in the eyes of the law, and the primary utility of cameras that identify individuals would be to ensure that those individuals are caught by the police and, potentially, to allow the recovery of stolen goods. They provide reactive capability after a crime is committed, as opposed to the proactive capability of cameras that send alerts when any human or vehicle is detected in a certain vicinity, ideally allowing for appropriate response to either interrupt the crime in progress, or prevent it from happening at all.

That being said, there is an advantage in building a database of faces and license plates that pass through your area: The data can either be parsed by specialised pattern-recognition algorithms, or simply exposed to clients via a web interface, in order to provide information to said clients that they might deem valuable. As long as these databases remain private, no legal trouble would be warranted. License plates could be checked against other privately-owned databases to attempt to identify owners, if access to such databases could be acquired. Ultimately, it is up to the client to decide whether or not facial and/or license plate recognition would be worth the additional cost, time and effort.

<a name="estimated_hardware_costs"></a>
## Estimated Hardware Costs

The following table provides a cost estimate for each of the most significant hardware items:

| Unit | Approx. Price Per Unit (Incl. VAT)|
| --- | --- |
| Hikvision AcuSense 4MP Bullet Camera 4mm | R2000 |
| Hikvision AcuSense 4MP Bullet Camera 2.8mm | R1900 |
| Raspberry Pi 4 Model B 4GB | R1100 |
| Raspberry Pi 4 Model B 2GB | R800 |
| Ethernet Switch w/ PoE | R700 |
| MicroSD Card 32GB | R120 |

The average site setup would include:
* 2 x Hikvision cameras @ **approx. R1950 each**
* 1 x Raspberry Pi @ **approx. R950**
* 1 x PoE Switch @ **approx. R700**
* 1 x MicroSD Card @ **approx. R120**

Which would total **R5670** per site, plus **approx. R200-R300** for all supplementary material, and not including the installation fee.

<a name="proposal_conclusion"></a>
## Proposal Conclusion

With all that said and done, it bears emphasising that this is merely a proposal. The details mentioned here are very much subject to change, depending on the requirements you, the client, are set to have. This is just a first step. As such, I await your response.

_By Ludwig Gryffenberg_
