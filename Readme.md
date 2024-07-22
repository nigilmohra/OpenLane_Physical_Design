# Physical Design using OpenLANE SKY130
This repository contains steps to use OpenLane with the SKY130nm PDK for GDSII generation, explaining the complete top (RTL) to bottom (GDSII) flow. The flow has been carried out in the [Ubuntu](https://ubuntu.com/download/desktop) Linux Distribution. The examples present in most repositories on GitHub use the built-in `picorv32a` as an example to illustrate the complete flow. In this [OpenLane_Physical_Design](https://github.com/nigilmohra/OpenLane_Physical_Design/tree/main) the flow is explained along with how to generate an RTL design, add it to the OpenLane tools, and run the complete flow.

**SKY130** is the hardware industry's first open-source Process Design Kit (PDK) released by **SkyWater Technology Foundry** in collaboration with **Google**. This provides worldwide access to the IP functions and open-source ASICs. For more details refer [here](https://github.com/google/skywater-pdk).

# Open-Source Tools
| Name of Tool                                                       | Application                                                                                                                                                                                 |
| ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Yosys](https://github.com/YosysHQ/yosys)                          | Synthesis of RTL Design                                                                                                                                                                     |
| [ABC](https://people.eecs.berkeley.edu/~alanmi/abc/abc.htm)        | Mapping of Netlist                                                                                                                                                                          |
| [OpenSTA](https://github.com/The-OpenROAD-Project/OpenSTA)         | Static Time Analysis                                                                                                                                                                        |
| [OpenROAD](https://github.com/The-OpenROAD-Project/OpenROAD)       | Floorplanning, Placement, Clock Tree Synthesis (CTS), Optimization and Routing                                                                                                              |
| [TritonRoute](https://github.com/The-OpenROAD-Project/TritonRoute) | Detailed Routing                                                                                                                                                                            |
| [Magic VLSI](http://opencircuitdesign.com/magic/)                  | Layout Tool                                                                                                                                                                                 |
| [NGSPICE](https://github.com/imr/ngspice)                          | SPIECE Extraction and Simulation                                                                                                                                                            |
| [SPEF](https://github.com/HanyMoussa/SPEF_EXTRACTOR)               | Generation of Standard Parasitic Extraction Format ([SPEF](https://www.physicaldesign4u.com/2020/05/standard-parasitic-extraction-format.html)) File from Design Exchange Format (DEF) File |

# Installation
The above list of tools is required for various tasks in physical VLSI design. Each tool in itself has a number of system requirements and requires various supporting tools to be installed. Installing each tool one after the other is efficient. This is made easy by using some custom scripts that setup the required tools and environments for them in just a few easy steps. To install the required tools, the below link can be referred to.
| Page                                                                            | Installed Software                                               |
| ------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| [OpenLANE Ubuntu Installation](https://openlane.readthedocs.io/en/latest/getting_started/installation/installation_ubuntu.html) | Complete list of softwares from the above table |

# Introduction: Starting with the ASIC Flow
## Materials and Courses
For more in-depth study of the **VLSI Physical Design** the following NPTEL course are useful: 
1. [VLSI Physical Design with Timing Analysis by Prof. Bishnu Prasad Das (IITR)](https://www.youtube.com/playlist?list=PLLy_2iUCG87Bny6CcGkCanvlHuXwr4-_W)
2. [An Introduction to Electronic System Packaging by Prof. G V Mahesh (IISc)](https://youtube.com/playlist?list=PLD50A0FB75B98EDA3&feature=shared) 
To understand how the computer hardware and software interact with ISA as the bridge, the following lecture series are very useful:
1. [Computer Organization by Prof. S Raman (IITM)](https://www.youtube.com/playlist?list=PL1A5A6AE8AFC187B7)
2. [High Performance Computer Architecture by Prof. Ajit Pal (IITKh)](https://www.youtube.com/playlist?list=PLbMVogVj5nJQmNqgs7GLBE-HhMi0GQOPW)
3. To understand the evolution of RISC-V ISA the following webpage materials are useful: [Specifications – RISC-V](https://riscv.org/technical/specifications/)

## OpenLANE
### Process Design Kit (PDK) 
Process Design Kit (PDK) is an essential collection of files and data provided by semiconductor foundries to enable Integrated Circuit (IC) designers to create designs that can be manufactured using a specific semiconductor fabrication process. The PDK encapsulates all the process-specific information and rules needed for designing and verifying circuits, ensuring that the designs are compatible with the manufacturing technology of the foundry.
| Components of PDK            |                                                                                                                                                                                                                                                             |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Design Rules                 | **Design Rule Manual (DRM)** specifies the geometric constraints and layout guidelines that must be followed to ensure manufacturability and yield. Examples include minimum width and spacing for metal layers, via sizes, and layer overlap requirements. |
| Device Models                | **SPICE Models** provide electrical characteristics of the transistors, resistors, capacitors, and other passive devices for circuit simulation.                                                                                                            |
|                              | **Model Parameters** include temperature variations, process corners (e.g. fast, typical, slow), and other process variations.                                                                                                                              |
| Standard Cell Libraries      | **Cell Libraries** include pre-designed and characterized standard cells (e.g., logic gates, flip-flops) with timing, power, and area information.                                                                                                          |
| Layout and Schematic Views   | **GDSII/OASIS Files** provide the geometric representation of the standard cells and other components for layout purposes.                                                                                                                                  |
|                              | **Schematic Symbols** represent the devices and cells in circuit schematics.                                                                                                                                                                                |
| Verification Runsets         | **Design Rule Checking (DRC) Runsets** automates the rules to verify that the layout adheres to the design rules.                                                                                                                                           |
|                              | **Layout Versus Schematic (LVS) Runsets** ensure the layout matches the schematic design.                                                                                                                                                                   |
|                              | **Parasitic Extraction (PEX) Runsets** extracts parasitic capacitance and resistance from the layout for post-layout simulation.                                                                                                                            |
| IP Blocks                    | **Analog and Digital IPs** are pre-designed and verified intellectual property blocks such as ADCs, DACs, PLLs, memory blocks that can be integrated into the design.                                                                                       |
| Process Specific Information | **Material Properties** contains information on dielectric constants, resistivities, and thermal properties of materials used in the process.                                                                                                               |
|                              | **Manufacturing Specifications** contains details on the photolithography, etching, doping and deposition process.                                                                                                                                          |

*Google and SkyWater technology foundry in collaboration have released a completely open-source Process Design Kit (PDK). The current release is a 130nm process node. The PDK is available here* [SkyWater Open Source PDK](https://github.com/google/skywater-pdk). 

#### OpenLANE ASIC Flow
 
|  ![[The OpenLane Flow.png]]   |
| :---------------------------: |
| *Figure 1. The OpenLane Flow* |

OpenLANE is an automated RTL to GDSII flow which includes various open-source components such as OpenROAD, Yosys, Magic, Fault, Netgen and SPEF-Extractor.
1. It facilitates to add custom design exploration and optimization scripts.
2. There are two modes of operations of OpenLANE
	1. Autonomous
		It figures the flow and push the bottom flow and wait for time based on design size and get the final GDS report.
	2. Interactive
		It performs the flow one at a time.
OpenLANE contains many sub-states. The detailed flow of the stages and sub-stages are given below.
