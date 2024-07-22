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
 
|  ![The OpenLane Flow](https://github.com/user-attachments/assets/b03e0754-2080-40a5-a2a4-89e5f48ca378)|
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

##### Synthesis
| Tool      | Application                                               |
| --------- | --------------------------------------------------------- |
| `Yosys`   | RTL Synthesis                                             |
| `ABC`     | Technology Mapping                                        |
| `OpenSTA` | Static Time Analysis - generates timing and power reports |
##### Floorplan and Power Distribution Network (PDN) 
| Tool       | Application                                                                                                    |
| ---------- | -------------------------------------------------------------------------------------------------------------- |
| `init_fp`  | Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing) |
| `ioplacer` | Places the macro input and output ports                                                                        |
| `pdn`      | Generates the power distribution network                                                                       |
| `tapcell`  | Insert welltap and decap cells in the floorplan                                                                |
##### Placement
| Tool         | Application                                                            |
| ------------ | ---------------------------------------------------------------------- |
| `RePLace`    | Performs global placement                                              |
| `Resizer`    | Performs optional optimizations on the design                          |
| `OpenPhySyn` | Performs timing optimization on the design                             |
| `OpenDP`     | Performs detailed placement to legalize the globally placed components |
##### Clock Tree Synthesis (CTS)
| Tool        | Application                                |
| ----------- | ------------------------------------------ |
| `TritonCTS` | Synthesizes the clock distribution network |
##### Routing
| Tool             | Application                                                              |
| ---------------- | ------------------------------------------------------------------------ |
| `FastRoute`      | Performs global routing to generate a guide file for the detailed router |
| `TritonRoute`    | Performs detailed routing                                                |
| `SPEF-Extractor` | Performs SPEF extraction                                                 |
##### GDSII Generation
| Tool    | Application                                                  |
| ------- | ------------------------------------------------------------ |
| `Magic` | Streams out the final GDSII layout file from the routed file |
##### Checks 
| Tool     | Application                                   |
| -------- | --------------------------------------------- |
| `Magic`  | Performs design rule check and antenna checks |
| `Netgen` | Performs layout versus schematic checks       |

# Floor Planning (PnR) and Introduction to Library Cells
## Floor Planning

### Utilization Factor and Aspect Ratio
**Utilization Factor** is a measure of how efficiently the available area is being used by the standard cells and pre-placed cells. It is defined as the ratio of the area occupied by the cells to the total available area. **Aspect Ratio** is the ratio of the height to the width of the area occupied.
### Pre-Placed Cells
Pre-placed cells in VLSI physical design refer to critical or frequently-used components that are strategically placed in specific locations on the chip layout before the general placement of standard cells. This strategic placement helps to optimize performance, minimize routing complexity, and ensure the reliability of the integrated circuit.
### Decoupling Capacitors
In VLSI physical design, decoupling capacitors play a critical role in maintaining signal integrity and ensuring reliable circuit operation. These capacitors are strategically placed to mitigate noise and voltage fluctuations caused by switching activities in the circuit.

When pre-placed cells in a VLSI design switch states, they cause sudden changes in current demand. This rapid switching can induce noise and create voltage drops or spikes, potentially pushing the output into undefined regions outside the acceptable noise margins. To combat this, decoupling capacitors are used to stabilize the power supply voltage. The functions of decoupling capacitor include charge storage, noise reduction and voltage stabilization, ensuring that the circuit operates reliable within its noise margins, even under the stress of high frequency switching.
### Power Planning
In VLSI Physical Design (PD), effective power planning is essential for maintaining signal integrity and overall circuit performance. When multiple logic blocks are connect through a shared bus and powered by a single voltage source, voltage drop and **ground bounce** can occur. These issues arise because the capacitors in the circuit, which charge and discharge during operation, can create significant voltage variations if they share a common ground or power source without individual decoupling capacitors.

**To address these challenges, each logic block should be connected to dedicated V<sub>DD</sub> and V<sub>SS</sub> lines**. This approach minimizes voltage drops and ground bounce by ensuring that the power supply and ground return paths are independent for each block. Dedicated power connections help maintain stable voltage level, reduce noise, and ensure that the circuit operates reliably within its noise margins. Effective power planning is crucial for optimizing power distribution and minimizing the adverse effects of simultaneous switching noise in VLSI circuits. 
## Placement
### Pin Placement
Pin placement in VLSI physical design is a crucial step that directly impacts the overall performance, manufacturability, and reliability of the integrated circuit (IC). Pin placement refers to the strategic positioning of Input/Output (I/O) pins, power pins, and ground pins on the chip layout. Effective pin placement can minimize signal delay, reduce power consumption, and enhance signal integrity.
1. **Peripheral Pin Placement** : Pins are placed along the periphery of the chip. Common in traditional wire bonding packaging and standard cell-based design.
2. **Area Pin Placement** : Pins are distributed across the entire area of the chip, not just on the edges. Used in flip-chip packaging and advanced VLSI designs.
3. **Hierarchical Pin Placement** : Pins are placed based on hierarchical design approach, where the design is divided into blocks or modules, and pins are placed accordingly.
4. **IO Cluster Pin Placement** : Similar types of IO pins are grouped together in clusters. Used to facilitate specific design requirements, such as minimizing crosstalk for high-speed signals.
5. **Mixed Pin Placement** : A combination of peripheral and area pin placement. Used to balance the advantages and disadvantages of both peripheral and area pin placement techniques. 
### Placement Optimization
Placement optimization in VLSI physical design involves determining the optimal positions of various cells (standard cells, macros, I/O pads) within the chip layout. The goal is to minimize critical metrics such as wire length, delay, power consumption, and area, while ensuring that design rules and constraints are met. Effective placement can significantly enhance the performance and manufacturability of the chip.

| Objective of Placement Optimization |                                                                                               |
| ----------------------------------- | --------------------------------------------------------------------------------------------- |
| Minimize Wire Length                | Reduce the total wire length and help reduce signal delay, power consumption, and congestion. |
| Optimize Timing                     | Ensures critical path are minimized to meet timing constraints.                               |
| Reduce Congestion                   | Avoiding routing congestion to ensure signal integrity and manufacturability.                 |
| Minimize Power Consumption          | Reduced dynamic and leakage power by optimizing the placement.                                |
| Enhance Thermal Distribution        | Ensuring uniform heat distribution to prevent hotspot.                                        |
| Satisfy Design Rules                | Ensuring compliance with design rules set by fabrication technology.                          |

| Placement Optimization Algorithms | Description                                                                                                                                     | Algorithm                                                                                                                                                   |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Partitioning Based Placement      | The design is recursively divided into smaller partitions, and cells are placed within these partitions.                                        | **Kernighan-Lin (KL) Algorithm** iteratively improves the partitioning by swapping pairs of cells to minimize cut size.                                     |
|                                   |                                                                                                                                                 | **Fiduccia-Mattheyses (FM) Algorithm** is an efficient heuristic that improves upon KL algorithm by making single-cell moves instead of swaps.              |
| Simulated Annealing               | A probabilistic technique that explores the solution space by accepting worse solution with a certain probability to escape local minima.       | The algorithm starts with initial placement and iteratively makes random perturbations. The acceptance probability of a worse solution decreases over time. |
| Quadratic Placement               | Models the placement problem as a quadratic optimization problem, minimizing a quadratic objective function representing the total wire length. | **Gordon's Method** solves a system of linear equations to find the optimal position of cells.                                                              |
|                                   |                                                                                                                                                 | **Kraftwerk2** is an improved version that iteratively refines the placement to handle fixed cells and congestion.                                          |
| Analytical Placement              | Uses mathematical models to derive an optimal placement.                                                                                        | **Forced-Directed Placement** models nets as springs and used force equilibrium to find cell positions.                                                     |
|                                   |                                                                                                                                                 | **Numerical Solvers** solves non-linear equations to minimize a global cost function representing wire length and congestion.                               |
| Min-Cut Placement                 | Divides the circuit into smaller regions to minimize the number of interconnections (cuts) between regions.                                     | **Recursive Bisection** continuously divides the design into two regions to minimize cuts, placing cells in the resulting regions.                          |
| Modern Approaches                 |                                                                                                                                                 | **Machine Learning Based Placement** technique uses ML to predict optimal placement solution based on historical data and design characteristics.           |
|                                   |                                                                                                                                                 | **Genetic Algorithms** uses evolutionary principles to explore placement solution space by combining and mutating placement solutions.                      |

## Cell Design Flow
Cell design flow is a systematic process for creating a standard cells, which are the building blocks used to design larger integrated circuits. Standard cells include logic gates, flip-flops, multiplexers, and other fundamental components. The cell design flow ensures that these cells meet specific design criteria for functionality, performance, power, and area, and are manufacturable.
1. **Specification and Requirements** defines the functionality, performance targets, power consumption, area constraints, and other design specifications for the cell. Determines the process technology to be used.
2. **Circuit Design** contains **Schematic Design**, **Transistor Level Design** and **Simulation**. 
	1. In Schematic Design the schematic representation of the cell, shows the transistors connection for a particular logic function. 
	2. In Transistor-Level Design appropriate transistor sizes and types, power and performance specification are provided.
	3. In Simulation, pre-layout simulation is carried out using tools like SPICE to verify functionality and performance under different conditions.
3. **Layout Design** contains **Layout Creation**, **Design Rule Checking** and **Layout Versus Schematic (LVS)**.
	1. In Layout Creation, the schematic is translated to a physical layout, with transistor arrangement, interconnections, and other components within the area constraints. 
	2. Design Rule Checking (DRC) ensures the layout adheres to the foundry's design rules for manufacturing.
	3. Layout Versus Schematic verifies that the layout matches the schematic in terms of connectivity and functionality.
4. **Parasitic Extraction** extracts parasitic elements like capacitance, resistance and inductance from the layout to create more accurate model of the cell's electrical behavior.
5. **Post-Layout Simulation** performs simulation with extracted parasites to ensure that the cell meets performance and power specifications in its final layout form.
6. **Characterization** generates timing, power, and noise models for use in larger designs, These models include **Timing and Delay Models**. **Dynamic and Static Power Models** and **Noise Models**.
7. **Library Preparation** creates the necessary library files, including **Liberty Files**`.lib` that contains the timing, power, and noise models. **LEF Files** `lef` describes the cell's physical layout, including pin position and routing blockages.  **GDSII/OASIS Files** provide the final geometric representation of the cell for manufacturing.
8. **Verification and Validation** includes **Functional Verification**, **Physical Verification** and **Sign-Off Checks**.
9. **Release and Documentation** finalizes the cell and associated documentations. 
10. **Library Integration** is the final procedure that integrated the cell into standard cell libraries used by the design tools for PnR, Synthesis and Timing Analysis.
