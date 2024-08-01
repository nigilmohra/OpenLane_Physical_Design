# Physical Design using OpenLANE SKY130 (Under Construction)
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

# Common Issue
I had similar error message when trying to use Yosis. Fixed by running command (as root) `xhost +local:*` before the docker run command, then once done with docker, ran the command `xhost -local:*` again to disable. Note that you will also have to run docker container as root and include `--volume="$HOME/.Xauthority:/root/.Xauthority:rw"`  in the docker run.
