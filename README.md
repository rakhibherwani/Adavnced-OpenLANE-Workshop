## Introduction to OpenLANE

OpenLANE is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, Fault, OpenPhySyn, CVC, SPEF-Extractor, CU-GR, Klayout and custom methodology scripts for design exploration and optimization. The flow performs full ASIC implementation steps from RTL all the way down to GDSII - this capability will be released in the coming weeks with completed SoC design examples that have been sent to SkyWater for fabrication.
 
### Inputs of ASIC Design Flow are:
The inputs to the ASIC design flow are:
- Process Design Rules: DRC, LVS, PEX
- Device Models (SPICE)
- Digital Standard Cell Libraries
- I/O Libraries


##### Process Design Kit
Process Design Kit (PDK) is the interface between the CAD designers and the foundry. The PDK is a collection of files used to model a fabrication process for the EDA tools used in designing an IC. PDK’s are traditionally closed-source and hence are the limiting factor to open-source Digital ASIC Design. Google and Skywater have broken this stigma and published the world’s first open-source PDK on June 30th, 2020. This breakthrough has been a catalyst for open-source EDA tools. This workshop focuses on using the open-source RTL2GDS EDA tool, OpenLANE, in conjunction with the Skywater 130nm PDK to perform the full RTL2GDS flow as shown below:
 
## Day-1 Inception of Open Source EDA tools


##### Skywater PDK Files

The Skywater PDK files we are working with are described under $PDK_ROOT. There are three subdirectories needed for the workshop:
 
1.	Skywater-pdk – Contains all the foundry provided PDK related files
2.	Open_pdks – Contains scripts that are used to bridge the gap between closed-source and open-source PDK to EDA tool compatibility
3.	Sky130A – The open-source compatible PDK files
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-1/1.png)
 
 
##### Design Folder
All designs run within OpenLANE are extracted from the openlane/designs folder:
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-1/2.png) 
 

##### Invoking OpenLane



![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-1/3.png)  
 
 
 
•	./flow.tcl is the script which runs the OpenLANE flow
•	OpenLANE can be run interactively or in autonomous mode
•	To run interactively, use the -interactive option with the ./flow.tcl script
 
 
##### Package Importing
Different software dependencies are needed to run OpenLANE. To import these into the OpenLANE tool we need to run:




![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-1/4.png)  
 
 
 
##### Prepare Design
 
Prep is used to make file structure for our design. To set this up do:

![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-1/5.png) 
 
 
 
 
### Synthesis
 
Synthesis with OpenLANE
To run Synthesis in OpenLANE:
 run_synthesis
 
 
Printing Statics obtained after synthesis contains summary of cells in the design after optimization in the synthesis. Chip area can also be noted.


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-1/6.png) 
 
 
 
##### Chip Area


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-1/7.png) 
 
 
##### Slack Report of min and max paths of the clock


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-1/8.png)  
 
 
 
 
##### Skywater supports:
 
hd: High Density Library
hdll: High Density Low Leakage Library
hvl: High Voltage Library
hs: High Speed Library
ms: Medium Speed Library
lp: Low Power Library
Those Libraries are offered at only three corners: Slow, Fast and Typical
 
 
 
## DAY-2 Chip Floorplanning and Standard Cells
In Floorplanning we typically set the:
 
Die Area
Core Area
Core Utilization
Aspect Ratio
Place Macros
Power distribution network (Normally done here but done later in OpenLANE)
Place input and output pins
 
 
Aspect Ratio and Utilization Factor
Two key descriptions of a floorplan are utilization and aspect ratio. The amount of area of the die core the standard cells are taking up is called utilization. Normally we go for 50-70% utilization to, or utilization factor of 0.5-0.7. Keeping within this range allows for optimization of placement and realizable routing of a system. Aspect ratio can specify the shape of your chip by the height of the core area divided by the width of the core area. An aspect ratio of 1 discribes the chip as a square.
 
Preplaced Cells
Preplaced cells, or MACRO’s, are important to enable hierarchical PnR flow. Preplaced cells enable VLSI engineers to granularize a larger design. In floorplanning we define locations and blockages for preplaced cells. Blockages are needed to ensure no standard cells are mapped where the placeplaced cells are located.
 
Decoupling Capacitors
Decoupling capacitors are placed local to preplaced cells during Floorplanning. Voltage drops associated with interconnect wires can heavily affect our noise margin or put it into an indeterminate state. Decoupling capacitor is a big capacitor located next to the macros to fix this problem. The capacitor will charge up to the power supply voltage over time and it will work as a charge reservoir when a transition is needed by the circuit instead of the charge coming from the power supply. Therefore it “decouples” the circuit from the main supply. The capacitor acts like the power supply.
 
Power Planning
Power planning during the Floorplanning phase is essential to lower noise in digital circuits attributed to voltage droop and ground bounce. Coupling capacitance is formed between interconnect wires and the substrate which needs to be charged or discharged to represent either logic 1 or logic 0. When a transition occurs on a net, charge associated with coupling capacitors may be dumped to ground. If there are not enough ground taps charge will accumulate at the tap and the ground line will act like a large resistor, raising the ground voltage and lowering our noise margin. To bypass this problem a robust PDN with many power strap taps are needed to lower the resistance associated with the PDN.
 
Pin Placement
Pin placement is an essential part of floorplanning to minimize buffering and improve power consumption and timing delays. The goal of pin placement is to use the connectivity information of the HDL netlist to determine where along the I/O ring a specific pin should be placed. In many cases, optimal pin placement will be accompanied with less buffering and therefore less power consumption. After pin placement is formed we need to place logical cell blockages along the I/O ring to discriminate between the core area and I/O area.
 
 
As with all other stages, the floorplanning will be run according to configuration settings in the design specific config.tcl file. 
Precendency
Sky130a config file > config file > default floorplan config file
 
In floorplan, we fix the core utilization ratio, aspect ratio and provide decap cells, welltap cells. Later, we fix the I/O pins position. The floorplan was done using run_floorplan command and employed the Magic tool to view the layout by importing the def file generated.
 
The output the the floorplanning phase is a DEF file which describes core area and placement of standard cell SITES:
 
To Open default  floorplan config file


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/9.png) 
 
 
 
Contents of Default floorplan config file


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/10.png)  
 
 
 
To Open sky 130 Config File


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/11.png) 
 
 
 
 
Contents of Sky130a Config file


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/12.png)
 
 
 
 
 
Sky130a config file


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/13.png)


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/14.png)
 
 
 
 
 
 
Floorplanning with OpenLANE
To run floorplan in OpenLANE:
 
 run_floorplan
 
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/15.png)
 
Floor plan

![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/16.png)
 
 
 
Therefore, we can see output of Floorplan is def file:


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/17.png)


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/18.png)
 
 
 
 
Viewing Floorplan in Magic: 
 
To view our floorplan in Magic we need to provide three files as input:
 
Magic technology file (sky130A.tech)
Def file of floorplan
Merged LEF file


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/19.png)
 
 Layout after floorplan stage in Magic tool


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/20.png)
 
 
 
Pins and decap cells


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/21.png)
 
 
 
Welltap cells arranged in checker board fashion


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/22.png) 
 
 
 Location of Std cells in the floorplan
 
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/23.png)
 
 
Run_placement
 
Placement
The next step in the Digital ASIC design flow after floorplanning is placement. The synthesized netlist has been mapped to standard cells and floorplanning phase has determined the standard cells rows, enabling placement. OpenLANE does placement in two stages:
1.	Global Placement - Optimized but not legal placement. Optimization works to reduce wirelength by reducing half parameter wirelength
2.	Detailed Placement - Legalizes placement of cells into standard cell rows while adhering to global placement
To do placement in OpenLANE:
 
%run_placement
 
For placement to converge the overflow value needs to be converging to 0. At the end of placement cell legalization will be reported:


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/24.png)
 
Viewing Placement in Magic
To view placement in Magic the command mirrors viewing floorplanning:
Layout of the design after Placement stage in Magic tool
 
![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/25.png)


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/26.png)
 
Standard Cell Design Flow
Cell design is done in 3 parts:
1.	Inputs - PDKs (Process design kits), DRC & LVS rules, SPICE models, library & user-defined specs.
2.	Design Steps - Design steps of cell design involves Circuit Design, Layout Design, Characterization. The software GUNA used for characterization. The characterization can be classified as Timing characterization, Power characterization and Noise characterization.
3.	Outputs - Outputs of the Design are CDL (Circuit Description Language), GDSII, LEF, extracted Spice netlist (.cir), timing, noise, power.libs, function.
Standard Cell Characterization
Standard Cell Libraries consist of cells with different functionality/drive strengths. These cells need to be characterized by liberty files to be used by synthesis tools to determine optimal circuit arrangement. The open-source software GUNA is used for characterization.
Characterization is a well-defined flow consisting of the following steps:
1.	Link Model File of CMOS containing property definitions
2.	Specify process corner(s) for the cell to be characterized
3.	Specify cell delay and slew thresholds percentages
4.	Specify timing and power tables
5.	Read the parasitic extracted netlist
6.	Apply input or stimulus
7.	Provide necessary simulation commands
 

![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/27.png) 
 
 
We generally know that there are two types of placements
•	Timing Driven
•	Placement Driven
We can set the same in OpenLANE as well. Below is the config file in which the environment variable named PL_TIME_DRIVEN is set as 0 which indicates FALSE. Hence, the placement performed above is Congestion Driven Placement by default.
Timing Driven is set to FALSE
 
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-2/28.png)
 
 The interesting thing to notice is that the PG routing is not done yet. Usually (in ASIC flow), we perform this during Floorplan stage itself. But in the OpenLANE flow, PG routing happens after the CTS and before Routing.
 
 
## Day-3 [Design library cell using Magic Layout and ngspice characterization]
OpenLANE has the benefit of allowing changes to internal switches of the ASIC design flow on the fly. This allows users to experiment with floorplanning and placement without having to reinvoke the tool.
 
Spice Simulations
To simulate standard cells spice deck wrappers will need to be created around our model files.
SPICE deck will comprise of:
 
Model include statements
Component connectivity, including substrate taps
Output load capacitance
Component values
Node names
Simulation commands
 
To plot the output waveform of the spice deck we will use ngspice. The steps to run the simulation on ngpice are as follows:
 
Source the .cir spice deck file
Run the spice file by: run
Run: setplot → allows you to view any plots possible from the simulations specified in the spice deck
Select the simulation desired by entering the simulation name in the terminal
Run: display to see nodes available for plotting
Run: plot  vs  to obtain output waveform
 
 
Switching Threshold of a CMOS Inverter
CMOS cells have three modes of operation:
 
Cutoff - No inversion
Triode - Inversion but no pinchoff in channel
Saturation - Inversion and pinchoff in channel
 
The voltages at which the switch between the modes of operation happens is dependent on the threshold voltage of the device(s). Threshold voltage is a function of the W/L ratio of a device, therefore varying the W/L ratio will vary the output waveform of CMOS devices.
To enable efficient description of the varying waveforms a single parameter called switching threshold is used. Switching threshold is defined at the intersection of Vin = Vout. A perfectly symmetrical device will have a switching threshold such that Vin = Vout = VDD/2.
 
16 Mask CMOS Process Steps
 
Substrate Selection : Selection of base layer on which other regions will be formed.
Create an active region for transistors : SiO2 and Si3N2 deposited. Pockets created using photoresist and lithography.
Nwell & Pwell formation : Pwell uses boron and nwell uses phosphorus. Drive in diffusion by placing it in a high temperature furnace.
Creating Gate terminal : For desired threshold value NA (doping Concentration) and Cox to be set.
Lightly Doped Drain (LDD) formation : LDD done to avoid hot electron effect and short channel effect.
Source and Drain formation : Forming the source and drain.
Contacts & local interconnect Creation : SiO2 removed using HF etch. Titanium deposited using sputtering.
Higher Level metal layer formation : Upper layers of metals deposited.
 
Cloning the GitHub repo for the inverter


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/29.png)
 
 
 
 
 
After invoking Magic
Layout of Inverter
 
 
![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/30.png)
 
 
 
Magic Key Features:
 
•	Color Palette - Defines layers and associated colors Continuous DRC
•	Device Inference - Automatic recognition of NMOS and PMOS devices
 
Device Inference
Select the specific layer/device by hovering over the object and pressing, s, iteratively, until you traverse the hierarchy to the specified object:
 
 
NMOS Transistor: press 's' , write 'what' on magic terminal:
 
Run the what command in the tkcon window:
 
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/31.png)
 
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/32.png)
 
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/33.png)
 
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/34.png)
 
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/35.png)
 
 
 ![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/36.png)
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
To check the DRC engine, we manually create the DRC error. If we could observe the starting imported layout, we get DRC errors as 0. Now, we delete the N-well and create the error. The interesting thing when compared with traditional verifications tools is the Dynamic DRC engine which means that DRC engine runs in background w.r.t changes in the layout.
 
 
 
 
DRC Errors
DRC errors in magic will be highlighted with white dotted lines:


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/37.png) 
 
 
 
To know the complete info about the DRC error, after selecting the drc error, shift to tkcon window of MAGIC tool and see the info as shown below.
Error info in the tkcon window


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/38.png)
 
 
 
Library Characterization
Basically, by library characterization, we mean to identify 4 parameters. Rise transition delay, fall transition delay, rise cell delay and fall cell delay We consider a basic inverter layout to identify the parameters. After importing the mag file and the skylane130A tech file, we extract the spice netlist
 
PEX Extraction with Magic
To extract the parasitic spice file for the associated layout one needs to create an extraction file:


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/39.png)
 
 
 
 
As we can see sky130_inv.ext file


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/40.png)
 
 
After generating the extracted file we need to output the .ext file to a spice file: 


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/41.png)
 
 
We can see sky130_inv.spice file


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/42.png)
 
Extracted Spice netlist
 
 
Spice Wrapper for simulation


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/43.png)
 
 
After extracting it, we invoke the NGSPice tool to perform the transient analysis and will find  Rise transition delay, fall transition delay, rise cell delay and fall cell delay 
Invoking NGSpice tool
To run the simulation with ngspice, invoke the ngspice tool with the spice file as input:

![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/44.png)

 
![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/45.png)


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/46.png)


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/47.png)


![alt text](https://github.com/rakhibherwani/Adavnced-OpenLANE-Workshop/blob/master/Day-3/48.png)
 
 
 
 
 
 
 
 
 
 
 
 
Transiant Analysis
 
The plot can be viewed by plotting the output vs time while sweeping the input:
plot y vs time a
 
 
 
 
 
 
20% of the maximum output value at the output rising edge: 
 
look at the terminal window:
 
80% of the maximum output value at the output rising edge & by looking at the terminal window:
 
Prop
 
 
 
 
•	from the previous two values we can calculate tr(rising time): 6.24547 - 6.18169= 0.06378ns
•	And by the same criteria we can calculate tf(falling time)
•	And the prpgation delay which is defined as (the time taken by the output to reach 50% of its maximum value - the time taken b y the input to reach it's maximum value)
 
 
## Day 4 Layout Timing Analysis and CTS
 
Place and routing (PnR) is performed using an abstract view of the GDS files generated by Magic. The abstract information will include metal and pin information. The PnR tool will use the abstract view information, formally defined as LEF information, to perform interconnect routing in conjunction to routing guides generated from the PnR flow.
An Introduction to LEF Files
•	Technology LEF - Contains layer information, via information, and restricted DRC rules
•	Cell LEF - Abstract information of standard cells
 
Grids in the layout
 
 
 
 
 
 
 
 
To understand this, we first need to convert the grids size in the MAGIC tool window to the track pitch size. For that, first we observe the values in the tracks.info file under the skylane130 files which is under Skylane130a PDK.
Tracks info file
 
 
 
 
Here the first value defines the offset followed by the pitch width. We have these values for both X and Y axis. We transform the default grid size in the MAGIC tool to the tracks.info defined values. We use the command grid x y xorigin yorigin where x is horizontal pitch value, y is vertical pitch value, xorigin is horizontal offset value and yorigin is vertical offset value. After implementing this command, our grid structure looks like below.
 
 
 
 
 
 
 
 
Tracks are used during the routing stage, routes can go over the tracks, or metal traces can go over the tracks. What the file is saying is that for the li1 layer the x or horizontal track is at an offset of 0.23 and a pitch of 0.46. The offset is the distance from the origin to the routing track in either the x or y direction. It is half the pitch so that means the tracks are centered around the origin.
Standard Cell Pin Placement
On-track standard cell pin placement is essential for DRC free PnR flow. Pins must align with the li1 and met1 preferred routing directions. During standard cell creation this concept must be accounted for. To ensure a cell is aligned with routing grids in Magic we can display a grid on top of the gds file.
To display the grid in magic:
Viewing the grid we can ensure our pin placement is optimized for PnR flow:
 
 
LEF file for inverter
 
 
 
 
 
 
 
Now since we generated the LEF file for the inverter, we need to include it with our design and test it. The main goal for this whole process till now and next, is to learn how one can add a custom cell design to the OpenLANE flow and work with it.
To proceed, we need to provide paths for the env variables of the OpenLANE using config.tcl file. There are three types of lib files - fast, slow and typical. 
 
 
Typical lib is the one used for the synthesis purposes and remaining two are same as the fast.lib and slow.lib files we usually come across in the ASIC flow. Below are the snapshots of the three lib files.
 
 
Typical lib file
 
Fast lib file
 
Slow lib file
 
Now, we perform synthesis and check whether the custom cell got mapped by abc mapping or not.
VsdInv getting mapped by the abc mapping tool
 
 
 
 
 
 
 
 
 
 
 
 
 
Below is the timing after the synthesis.
During the synthesis optimization process, we have env variables which effect the timing and area.
Below is the timing after the synthesis.
 
 
Negative sLack!! i.e. This happens after adding the CMOS inverter
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
17.96 to -
 
 
 
Here, we change: 1- The synthesize strategy to make timing it's priority over the area 2- Enable cell sizing in the design. For more information about these environmental variables, go to Configuration/READmd directory
 
 
 
Now we can see, Slack Value reduced from 17.76 to 5.36.
 
 
 
But we can observe area increases. Increased area is the overhead resulted due to decreasing slack value.
 
 
 
Run_floorplan
 
 
 
 
Run_placement
 
 
 
 
 
 
The layout of the placed cells on magic:
 
 
 
Changing fanout and running synthesis.
 
 
Again, I changed the maximum fanout to 4 and run synthesis again, we find that the slack value is further enhanced.
 
 
 
 
 
 
 
We now modified the fanouts. If you could observe the timing report, we have a buf chain in the design. This is because we enabled the BUFFERING env variable in the openLANE and performed the synthesis. Our next choice to improve the slack is to improve these buffers size. This will reduce the cap load and thus effects the slack.
 
 
We now modified the fanouts. If you could observe the timing report, we have a buf chain in the design. This is because we enabled the BUFFERING env variable in the openLANE and performed the synthesis. Our next choice to improve the slack is to improve these buffers size. This will reduce the cap load and thus effects the slack.
 
CTS
So, after getting the opt netlist in terms of timing, we proceed to next step i.e., building Clock Tree Synthesis (CTS). We have few parameters for this stage as well, which effects the performance of this stage.
CTS 
After that, I ran the floorplan and placement again, then I go throgh the cts stage. run_cts. 
Below is the snapshot where the CTS layers are created in the design.
 
 
 
 
 
 
 
After performing CTS, we need to check for our slacks again. This is what we call as "Post-CTS timing analysis". Here we check the hold slack as well because it was not having any significance in previous stages due to absence of clocks. Before that, we need to make some preparations such as creating a db file in the openROAD application etc.
 
After preparations, we are good to go to report the timing. Below are the snapshots which shows the report and also shows that Setup and Hold slacks are further enhanced.
 
 
 
 
 
 
Day 5 Final Steps in RTL to GDSII
 
After generating our clock tree network and verifying post routing STA checks we are ready to generate the power distribution network in OpenLANE:
 
Power Distribution Network Generation
To generate the PDN in OpenLANE:
 Hence we first build our Power Distribution Network (PDN) by using gen_pdn.
 
 
 
 
 
 
 
The PDN feature within OpenLANE will create:
1.	Power ring global to the entire core
2.	Power halo local to any preplaced cells
3.	Power straps to bring power into the center of the chip
4.	Power rails for the standard cells
 
 
Global and Detailed Routing
OpenLANE uses TritonRoute as the routing engine for physical implementations of designs. Routing consists of two stages:
1.	Global Routing - Routing guides are generated for interconnects on our netlist defining what layers, and where on the chip each of the nets will be reputed
2.	Detailed Routing - Metal traces are iteratively laid across the routing guides to physically implement the routing guides
 
To run routing in OpenLANE
 
If DRC errors persist after routing the user has two options:
1.	Re-run routing with higher QoR settings
2.	Manually fix DRC errors specific in tritonRoute.drc file
SPEF Extraction
After routing has been completed interconnect parasitics can be extracted to perform sign-off post-route STA analysis. The parasitics are extracted into a SPEF file. The SPEF extractor is not included within OpenLANE as of now.
 

