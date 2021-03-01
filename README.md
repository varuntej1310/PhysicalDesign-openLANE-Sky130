# Advanced Physical Design using OPENLANE/SKY130
VLSI System Design workshop on Physical Design Flow from RTL to GDSII using OPENLANE and SKY130 PDK.
# Chip Design
Each chip has its core, the core considered here is of RISC-V based SoC. RISC-V is an instruction set architecture that helps to communicate with the computer. A netlist describes the connectivity of an electronic design. The CORE is the section of the chip where the fundamental logic of the design is placed. A DIE consists of CORE is a small semiconductor material on which the circuit is fabricated.

<img width="617" alt="Chip design" src="https://user-images.githubusercontent.com/72096419/105984549-4b679900-60c0-11eb-91ff-c1f901c42d2c.png">

### Foundry IP's
PLLs, DAC, ADC and SRAMs are known as Foundry IP's. They are manually designed.
### Macros
Macros are digital blocks which are made up of digital logic.

# OpenLANE ASIC Flow
Using openlane, we can produce a GDSII from a chip RTL.

<img width="532" alt="openlane flow" src="https://user-images.githubusercontent.com/72096419/105984775-9b466000-60c0-11eb-9cf9-a217bd37b9a1.png">

Openlane will automate the entire RTL to GDSII flow based on several components including openRaod, yosys, Magic, Netgen, Fault and custom methodology scripts for design exploration and optimization.

## PDK
Process Design Kit is a collection of files used to model a fabrication process for the EDA tools used to design an IC. It acts as an interface between the CAD designers and the foundry.

## Design Stages and Tools
### 1) Synthesis
  * yosys - Performs RTL synthesis
  * abc - Performs technology mapping
  * OpenSTA - Pefroms static timing analysis on the resulting netlist to generate timing reports
### 2) Floorplan and PDN
  * init_fp - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
  * ioplacer - Places the macro input and output ports
  * pdn - Generates the power distribution network
  * tapcell - Inserts welltap and decap cells in the floorplan
### 3) Placement
  * RePLace - Performs global placement
  * Resizer - Performs optional optimizations on the design
  * OpenPhySyn - Performs timing optimizations on the design
  * OpenDP - Perfroms detailed placement to legalize the globally placed components
### 4) CTS
  * TritonCTS - Synthesizes the clock distribution network (the clock tre
### 5) Routing 
  * FastRoute - Performs global routing to generate a guide file for the detailed router
  * TritonRoute - Performs detailed routing
  * SPEF-Extractor - Performs SPEF extraction
### 6) GDSII Generation
  * Magic - Streams out the final GDSII layout file from the routed def
### 7) Checks
  * Magic - Performs DRC Checks & Antenna Checks
  * Netgen - Performs LVS Checks
  
# DAY 1
 
 <img width="612" alt="1" src="https://user-images.githubusercontent.com/72096419/106121150-3d2b8280-617d-11eb-9933-5b2b3e5e83d8.png">
 
The ./flow.tcl -interactive command runs the OpenLane in interactive mode.

## Design Preparation Step 

<img width="503" alt="2" src="https://user-images.githubusercontent.com/72096419/106121803-f8541b80-617d-11eb-8b11-c498ff787a0e.png">

Openlane has many built-in designs and we design for picorv32a. We will be performing the synthesis first.
The mergeLEF.py indicates the merging of the two LEF files into one. 
The config.tcl file shows the default parameters taken by run.

## Synthesis

After the preparation is done as shown above, the next command is,
     % run_synthesis 

This will run the yosys synthesis as well as the abc.

<img width="956" alt="3" src="https://user-images.githubusercontent.com/72096419/106122095-4f59f080-617e-11eb-8aff-4ccaa5c23dc8.png">


Chip area for module '\picorv32a': 237049.849600 

The challenge of day 1 is to find the flop ratio which is the number of D flip flops to total number of cells 

The total number of cells are 17323 

The D flip flops are 1634 

Therefore the flop ratio is 0.094 

# Day 2

## Floorplan
The arrangement of IP's in a chip is referred as floorplanning.
Utilization factor is the area occupied by netlist to total area of the core.
Aspect ratio is the height of the core divided by the width of the core.

The command to run floorplan in openlane is,
    % run_floorplan

<img width="506" alt="1" src="https://user-images.githubusercontent.com/72096419/106123552-10c53580-6180-11eb-8d2a-a2cc69281f85.png">

<img width="505" alt="3 deffile" src="https://user-images.githubusercontent.com/72096419/106123567-1589e980-6180-11eb-946a-2ddd3f62793a.png">

### Floorplan in Magic

<img width="619" alt="magicT cmd 4" src="https://user-images.githubusercontent.com/72096419/106123933-8a5d2380-6180-11eb-9300-cba35c86cccd.png">

<img width="615" alt="5" src="https://user-images.githubusercontent.com/72096419/106123948-8f21d780-6180-11eb-8fd4-30bbae3edd95.png">

We can see the standard cell placement in the left corner:

<img width="602" alt="standard cell placement left corner 6" src="https://user-images.githubusercontent.com/72096419/106124177-cf815580-6180-11eb-8ede-fc8abf7262fa.png">

## Placement
It happens in two stages:
* Global Placement
* Detailed Placement
The command to run placement in openlane is, 
    % run_placement
    
 <img width="514" alt="placement 7" src="https://user-images.githubusercontent.com/72096419/106124614-474f8000-6181-11eb-86b6-bc469eb2154a.png">
 
### Placement in Magic

<img width="448" alt="8 magic placement" src="https://user-images.githubusercontent.com/72096419/106124626-4b7b9d80-6181-11eb-86c3-87d58452cce5.png">

## Cell Design Flow

1) INPUTS - 
     PDK's: DRC and LVS rules, SPICE models, library and user-defined specs.
2) Design Steps -
     Circuit Design, Layout Design, Characterization.
3) Outputs - 
     CDL( Circuit Description Language), GDSII, LEF, extracted SPICE netlist, timing, noise, power.libs function.

### Decoupling Capacitors:
They are placed local to preplaced cells during floorplanning. The capacitor will charge upto the power supply voltage over time and it will work as a charge reservoir. Therefore, it decouples the circuit from the mail supply and acts like the power supply to the circuit.

# DAY 3
## 16 Mask CMOS Process
* Substrate Selection 
* Create an active region for transistors 
* Nwell & Pwell formation 
* Creating Gate terminal 
* Lightly Doped Drain (LDD) formation 
* Source and Drain formation 
* Contacts & local interconnect Creation 
* Higher Level metal layer formation 

Cloning the Git Repository : https://github.com/nickson-jose/vsdstdcelldesign

<img width="513" alt="cloning the git repo 1" src="https://user-images.githubusercontent.com/72096419/106129741-88965e80-6186-11eb-8b8a-a5fb54dd50fd.png">

Copying sky130A.tech file 

<img width="504" alt="copying tech file 2" src="https://user-images.githubusercontent.com/72096419/106165376-138c4e80-61b1-11eb-8212-955daf87d107.png">

<img width="504" alt="3" src="https://user-images.githubusercontent.com/72096419/106165411-1d15b680-61b1-11eb-9b4b-6befa055cd0c.png">

## CMOS Inverter in Magic

<img width="505" alt="cmd to view in magic 4" src="https://user-images.githubusercontent.com/72096419/106165900-99a89500-61b1-11eb-99e1-11455b7b1a46.png">

<img width="525" alt="inverter in magic" src="https://user-images.githubusercontent.com/72096419/106165920-9f05df80-61b1-11eb-80db-e3e8465fda51.png">

Magic performs continuous DRC checks and the errors are appeared in the tkcon window.

## PEX Extraction
Output the .ext file into spice file.

<img width="505" alt="spice extraction 6" src="https://user-images.githubusercontent.com/72096419/106166844-96fa6f80-61b2-11eb-949b-701c7b0aec27.png">

<img width="504" alt="7" src="https://user-images.githubusercontent.com/72096419/106166871-9cf05080-61b2-11eb-9fa8-3ec84d85dec8.png">

Run the simulation with ngspice and the output vs time plot can be viewed.

<img width="296" alt="8 output vs time" src="https://user-images.githubusercontent.com/72096419/106167423-2e5fc280-61b3-11eb-8e64-c987f572572b.png">

# Day 4
## LEF Files:
1) Technology LEF - contains layer information, via information and restricted DRC rules.
2) Cell LEF - contains abstract information of the standard cells
### Converting grid info to track info

<img width="165" alt="gridinfototrackinfo 1" src="https://user-images.githubusercontent.com/72096419/106169548-80094c80-61b5-11eb-9cde-4c0204552d8a.png">

### LEF file from standrad cell inverter

<img width="357" alt="lef from std cell 2" src="https://user-images.githubusercontent.com/72096419/106170632-a5e32100-61b6-11eb-82f7-68a92d19fe74.png">

Overwrite the previous run to include the new cells.
Slack violations can be fixed by STA (Static Timing Analysis) with openSTA.

## Clock Tree Synthesis
The command to run CTS is % run_cts. 
After CTS, new clock buffers will be inserted into the design.

<img width="775" alt="cts 3" src="https://user-images.githubusercontent.com/72096419/106174460-12f8b580-61bb-11eb-84f9-728dc3e1ca2b.png">

Layout after CTS in magic:

<img width="518" alt="cts in magic 4" src="https://user-images.githubusercontent.com/72096419/106174476-17bd6980-61bb-11eb-86f8-1755452c434b.png">

openLANE has openROAD which performs the timing analysis by creating a database file.

# DAY 5
## Power Disrtibution Network
The command used to run PDN is % gen_pdn.

<img width="536" alt="power planning 1" src="https://user-images.githubusercontent.com/72096419/106177041-075abe00-61be-11eb-8c61-7c82f5bd7fc2.png">

## Routing
The command used to run routing is % run_routing.
TritonRoute is used to perform the routing.
Routing has two stages:
1) Global Routing
2) Detailed Routing

<img width="408" alt="route 2" src="https://user-images.githubusercontent.com/72096419/106177478-8e0f9b00-61be-11eb-81bd-4030d0d6ae61.png">

## SPEF
SPEF(Standard Paracitic Extraction Form) file will generate from DEF and LEF outside of openlane after routing.

# Acknowledgements:
* Kunal Ghosh, Co-founder (VSD Corp. Pvt. Ltd)
* Nickson P Jose, Teaching Assistant (VSD Corp. Pvt. Ltd)







