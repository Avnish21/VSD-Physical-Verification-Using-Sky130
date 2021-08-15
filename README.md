# PHYSICAL VERIFICATION USING Sky130
![Workshop-Flyer](https://user-images.githubusercontent.com/43104014/129164916-292014b6-ffa7-4858-a03a-92803914e51d.jpeg)
### What this workshop brings:
This course aims at bringing a solid understanding of Physical Verification methodology using Magic, NetGen and OpenLane Flow in order to be able to rectify the various problems that come up during the design cycle as Design Rule Check (DRC) or Layout vs Schematic (LVS) violations. Over the course, spanning 5 days, we also cover the Sky130 Process Design Kit (PDK) in detail. The couurse contained rigorous labs for every topic, which were performed on the VSD-IAT platform on the Sky130 PDK.
### Instructor Overview:
#### Tim Edwards: Senior Vice President at eFabless Corporation.
### Course Curriculum
#### Day 1: Introduction to Sky130
* Introduction to Skywater PDK
* Opensource EDA Tools
* Understanding Skywater PDK
* Opensource Tools And Flows
#### Day 2: DRC/LVS Theory
* Understanding GDS Format
* Extraction Commands, Styles and Options In Magic
* Advanced Extraction Options In Magic
* GDS Reading Option In Magic
* GDS Writing, Input, Output Styles and Output Issues
* DRC Rules In Magic
* Extraction Rules And Errors In Magic
* LVS Setup For Netgen
* Verification By XOR
#### Day 3: Frontend & Backend DRC
* Introduction To Basic Silicon Manufacturing Process
* Backend Metal Layer Rules
* Local Interconnect Rules
* Front-End Rules, Transistors Implants, ID and Boundary Layers, Wells And Same Net Rules
* Deep N-Well And High Voltage Rules
* Device Rules
* Miscellaneous Rules Latch-up Antenna Stress Rules
#### Day 4: PNR and Physical Verification
* The OpenLANE Flow
* RTL2GDS For Demo Design
* Interactive OpenLANE Run
* Interactive OpenLANE Run - Final Steps
* Techniques To Avoid Common DRC Errors
* Techniques To Manually Fix Violations
#### Day 5: Running LVS
* Physical Verification Of Extracted Netlist
* How LVS Matching Works
* LVS Netllist Vs Simulation Netlists
* The Netgen Core Matching Algorithm
* Netgen Prematch Analysis, Hierarchical Checking And Flattening
* Pin Checking And Property Checking
* Series Parallel Combining
* Symmetry Breaking
* Interpreting Netgen Results


### Day 1: Introduction to Sky130 PDK
The SkyWater PDK is a collaboration between Google and SkyWater Technology Foundry to provide a fully open source Process Design Kit (PDK), making it possible for the first time, to work off libraries and setups for open source tools build around a real manufacturable process, which can be used to facricate real designs at SkyWater's facility. As of May 2020, this repository is targeting the SKY130 process node, 130 denoting the minimum feature length (length of the smallest manufacturable transistor in the process) of 130 nanometers.

The SkyWater Open PDK is divided into a number of public repositories, mainly:
* [Documentation](https://skywater-pdk--136.org.readthedocs.build)
* [PDK Library and Files](https://github.com/google/skywater-pdk)

Backing this is also a huge community, mainly commuicating over a [slack server](https://join.skywater.tools).

#### Steps to install Sky130 PDK
1. Clone the repository  
```git clone "https://github.com/RTimothyEdwards/open_pdks"```  
2. Enter the open pdks folder  
```cd open_pdks```  
3. Tell open_pdks to compile and install the Sky130 PDK from the Google Skywater sources  
```configure --enable-sky130-pdk```  
4. Run make to automatically grab the SkyWater and a few other third-party repositories and submodules from github  
```make```
5. Build libraries from the various individual files and repositories. This may take a **LONG TIME** but only happens once, so be patient  
```sudo make install```  

#### SkyWater Sky130 libraries:  
* DIGITAL CELLS:  
![image](https://user-images.githubusercontent.com/43104014/129203160-dc503c60-0207-4ea4-a866-caea8142f42b.png)
* PRIMITIVE DEVICES:  
![image](https://user-images.githubusercontent.com/43104014/129202831-c06b1dbf-ccac-4725-8838-1b27e424a89d.png)
* I/O CELLS:  
![image](https://user-images.githubusercontent.com/43104014/129202951-5aef97de-a181-41f9-9893-84cab092b1bc.png)
* THIRD-PARTY LIBRARIES:  
![image](https://user-images.githubusercontent.com/43104014/129203030-5654733e-d5ff-4e9f-befb-c78237d20b9a.png)

#### Sky130 PDK Metal Stack
The metal layers in the Sky130 PDK are in a fashion of progressive thicknesses, varying from a top metal 5 layer of 1.26 μm to the lowerst metal 1 layer of 0.36 μm. Below the metal 1 layer and above polysilicon is a 0.1μm layer of TiN (Titanium Nitride) known as LI or local interconnect.  

![image](https://user-images.githubusercontent.com/43104014/129206731-5b12b335-139e-4a43-be61-0e00133bc440.png)

#### Creating a simple schematic and understanding the basic DRC/LVS design flow:
In this section we will go about forming a basic foundation on how the Sky130 PDK works, create a simple layout and schematic and perform DRC/LVS checks using xSchem and magic.
To begin with, let's first run basic checks on the tool installations so far. Firstly, we will check magic.  
Running ```magic``` should open a layout window and a console window as:  

![image](https://user-images.githubusercontent.com/43104014/129212671-620b8313-3bea-4b80-8a7b-2605c6b062ad.png)

Type ```quit``` in the magic console to quit magic.  
Next, we will try netgen. In the terminal, run ```netgen``` and it should open a singular command line window.  

![image](https://user-images.githubusercontent.com/43104014/129218971-1876762c-e02f-4b63-995e-2cf504e01506.png)

Similarly like magic, type ```quit``` in the netgen command line in order to quit netgen.  
To run xschem, type ```xschem``` in the terminal and hit enter. This will open a GUI however unlike magic and netgen, the tcl command line is in the terminal itself, so to close xschem, simply type exit in the terminal and run.

![image](https://user-images.githubusercontent.com/43104014/129219573-505cfde0-c176-42aa-aecd-d8bcd25ebad9.png)

Lastly, to start ngspice, type ```ngspice``` on the linux command line and a new prompt will start within the terminal. Unlike all the others, netgen does not have its own command line or a GUI.

![image](https://user-images.githubusercontent.com/43104014/129220157-0bce7ca2-bf74-4638-838e-4af0e231ed5e.png)

A similar behaviour can be replicated in magic as well as netgen by appending the respective open commands with ```-noconsole``` . Magic can even be run with no graphics by using ```magic -dnull -noconsole``` and this mode of calling magic is especially used when running magic from a script.

![image](https://user-images.githubusercontent.com/43104014/129220630-30f31523-1746-4e33-8954-7598386dd4f5.png)

Once all the checks are done and confirmed, we can go on to the next step.

### Creating Sky130 Device Layout in Magic
In this section we will start off by making a simple circuit layout in magic after setting up all the appropriate filesystems and folders for the programs we need to run. We start by moving to the home directory using ```cd```. We will start simple by making a basic CMOS inverter, so we create an appropriate folder for the same.  
```console
avnish@pv-workshop-final-03:~$ cd
avnish@pv-workshop-final-03:~$ mkdir inverter
avnish@pv-workshop-final-03:~$ cd inverter
avnish@pv-workshop-final-03:~/inverter$ mkdir xschem
avnish@pv-workshop-final-03:~/inverter$ mkdir mag
avnish@pv-workshop-final-03:~/inverter$ mkdir netgen
avnish@pv-workshop-final-03:~/inverter$
avnish@pv-workshop-final-03:~/inverter$ cd xschem
avnish@pv-workshop-final-03:~/inverter/xschem$ ln -s /usr/share/pdk/sky130A/libs.tech/xschem/xschemrc
avnish@pv-workshop-final-03:~/inverter/xschem$ ln -s /usr/share/pdk/sky130A/libs.tech/ngspice/spinit .spiceinit
avnish@pv-workshop-final-03:~/inverter/xschem$
avnish@pv-workshop-final-03:~/inverter/xschem$ cd ../mag
avnish@pv-workshop-final-03:~/inverter/mag$ ln -s /usr/share/pdk/sky130A/libs.tech/magic/sky130A.magicrc .magicrc
avnish@pv-workshop-final-03:~/inverter/mag$ cd ../netgen
avnish@pv-workshop-final-03:~/inverter/netgen$ ln -s /usr/share/pdk/sky130A/libs.tech/netgen/sky130A_setup.tcl setup.tcl
avnish@pv-workshop-final-03:~/inverter/netgen$ cd ../xschem
avnish@pv-workshop-final-03:~/inverter/xschem$ xschem
```
If all the links are correctly created you will now see this  

![image](https://user-images.githubusercontent.com/43104014/129243245-390621f3-e163-4663-9eb1-af41041716ba.png)

Next, exit xschem via the file menu and move to our magic directory using  
```cd ../mag```
You should now see

![image](https://user-images.githubusercontent.com/43104014/129244128-a63cce3d-4954-41a3-9e90-0bdd90674670.png)

Notice the technology name now shows Sky130. You should see a white box in the middle. Use Shift+Z to zoom out, left click to relocate the box and right click to resize. Hover your cursor over one of the colour tabs on the right and either press your middle mouse button or over and press E to fill the area within the white box with the corresponding colour.

![image](https://user-images.githubusercontent.com/43104014/129246359-93a3f0e9-4ac8-453a-b4d7-d5f6a8f8630d.png)

The devices tabs on the top labelled devices 1 and devices 2 are what allow us to create devices in magic. Experiment yourself and try to create a device. Shown is an n-mosfet with width = 1.5um length = 0.355um and Fingers = 2.

![image](https://user-images.githubusercontent.com/43104014/129247835-e520a641-569d-4370-bd96-bcc7bca09df3.png)

We can now close magic for now as we will later come back to it.  
We now move back to xschem by ```cd ../xschem``` and typing xschem. Open xschem and after resizing the window to your comfort, open a new schematic from the file menu and press the insert key.  

![image](https://user-images.githubusercontent.com/43104014/129249009-8079644a-6a47-4a4c-8e2b-7546f62fa9a4.png)

Go over to the sky130 directory and select the skky130_fd_pr option for primitive cells, since we need nfet and pfet to build our inverter. Here, we see different types of fets, such as nfet3, nfet, pfet, pfet3.  

![image](https://user-images.githubusercontent.com/43104014/129251582-7b26d553-2e7b-4cb6-8a46-16412e1b3b37.png)

A mosfet is a 4 terminal device and it is easier to avoid the fet3 options since they need us to specify the bulk pin as a parameter every time. However here, we will choose the nfet and pfet3, both with 01v8.sym types for demonstration purposes. Go back to the insert menu and select home. Go to the xschem library and then to devices. Here we need a ipin, opin and an iopin. Once you have all your components on the schematic, use the w button and mouse cursor to connect all wirings and use the q key to change names of the labels. Move over to the pfet and tap q to see the parameters.  

![image](https://user-images.githubusercontent.com/43104014/129251649-5c8636d0-5959-4642-b8a4-26bcfc5a7366.png)

Change the length to 0.18 since anything below 0.18 is only allowed for SRAM cells. Change width to 3 and nF to 3. Width is counted as total width and is w x nF so that means each finger's width is 1um. For the nfet change length to 0.18 again and change width to 4.5 and nF to 3 since this time we want a width of 1.5u per finger. Once your connections are done your schematic is ready.

![image](https://user-images.githubusercontent.com/43104014/129252068-d8186dd9-a502-43a0-9a23-d932b8811b54.png)

Go to file --> "Save as" and save as inverter.sch and click on OK.

Now, our main goal is to create a layout of this circuit but first, we need to functionally validate it and for that we need to create an independent testbench with the schematic as a symbol. Go to the symbol menu and click on "Make symbol from schematic". Now, we start our testbench schematic by opening a new schematic from file. Once the schematic is open, press the insert key, move to the local directory --> inverter.sym.

![image](https://user-images.githubusercontent.com/43104014/129256909-a102faea-afd2-47cd-8a7b-f1c0456f0e08.png)

Bring the symbol of our inverter into the new schematic and add all the extra components like voltage sources, gnd terminal and opins for the comparison between vin and vout. Once done, your schematic should look like this.  

<img width="568" alt="sym" src="https://user-images.githubusercontent.com/43104014/129258102-bf715e6a-722a-4ea8-8255-3018ab9a1b63.png">

Now, we need to add the parameters. Go to V2 and set the value to 1.8. Go to V1 and set the value to a PWL sweep as shown.

![image](https://user-images.githubusercontent.com/43104014/129258615-4feb94c0-3958-4449-a8a0-4ac602448b94.png)

Press insert again and search and add code_shown.sym 2 times to the schematic. s1 will tell ngspice where to find the device models for the devices used in the schematic.

![image](https://user-images.githubusercontent.com/43104014/129259570-f407f998-9fd8-4eab-90cd-6396eb204ece.png)

s2 will run a transient analysis for 1 microsecond and plot the values for Vin and Vout.

![image](https://user-images.githubusercontent.com/43104014/129259840-6064edbd-16f0-48ab-bbee-c04a32733c42.png)

Save the file again as inverter_tb.sch and then tap netlist and lastly click simulate. This step takes a while so be patient. Once the output shows, if you did everything right, you should see this:  

![image](https://user-images.githubusercontent.com/43104014/129260591-4c91dc97-05c6-48d1-9d64-015cb4c5adef.png)

Now we have verified the working of our inverter schematic and we need a netist of exclusively that, and not our entire testbench. Open the inverter from the files menu and go to suimulation menu and select "LVS netlist: top lvl is a subsckt" and then tap netlist. Exit xschem.

Now, we need to import the netlist to magic to create the layout. Open magic by moving to the mag directory and using ```magic -d XR``` to force hardware accelerated graphics for bolder colours. Go to file --> Import SPICE and select our netlist from the inverter folder.

![image](https://user-images.githubusercontent.com/43104014/129262167-cb0ad44a-bd68-461d-aee2-f5265844b3e3.png)

Once celected, click OK and the tap v on the layout window and you should see this:

![image](https://user-images.githubusercontent.com/43104014/129261783-b118bdc5-469a-4aa2-a666-f8a3ae8495a5.png)

Clearly, the schematic is unable to do analog placement and routing which is an extremely complex process. Hence, here we have to do it ourselves. It is not necessary to do this in the most optimal way since our goal here is just to get a working layout. Select cells by hovering over them and pressing i. Move them to a new location by hovering over the point you want the bottom left corner of the cell to be and pressing m. Once you move the cells around you should have a layout like this.

<img width="960" alt="magic" src="https://user-images.githubusercontent.com/43104014/129313801-2fcc18f6-4a3f-4be7-9563-fadbc1d57290.png">

Select the pfet device using i and press ctrl+p to open the parameters window. Find the options for "Source via coverage", "Drain via coverage" and "Top guard ring via coverage" and set them all as per the following:

![image](https://user-images.githubusercontent.com/43104014/129316965-ef31d278-73b5-4b46-9fa2-b35e40f810ae.png)

Similarly,for the nfet carry out the similar steps, only changing the bottom guard ring instead of the top. Now using the box tool and the wire connect tool that can be obtained by scrolling through the pointers by pressing space (looks like an arrow) make and fill in the connections on the layout.

![image](https://user-images.githubusercontent.com/43104014/129330410-c0a6d72d-d1f2-486d-b0d4-98dd2ef7ffa0.png)

Now, go to File --> save and select autowrite. We're not done yet. Go to the command window and type the following:  

```extract do local```  
```extract all```

Extract do local is an instruction to perform all extractions to the local directory and extract all does the actual extraction. We want our extraction for lvs to be in the spice format, so run the following commands.  

```ext2spice lvs```  
```ext2spice```  

Now, we can close magic.  
If we run an ```ls``` in this directory we should see our .ext files and .mag files for the devices. In case there are extra devices we can run the following commands to remove all unused .ext files.  

```console
avnish@pv-workshop-final-03:~/inverter/mag$ ls
inverter.ext	sky130_fd_pr__nfet_01v8_B9CQBZ.ext  sky130_fd_pr__pfet_01v8_KG2LE3.mag
inverter.mag	sky130_fd_pr__nfet_01v8_B9CQBZ.mag  sky130_fd_pr__pfet_01v8_U3KX2J.ext
inverter.spice	sky130_fd_pr__nfet_01v8_N39HBR.mag  sky130_fd_pr__pfet_01v8_U3KX2J.mag
avnish@pv-workshop-final-03:~/inverter/mag$ rm *.ext
avnish@pv-workshop-final-03:~/inverter/mag$ ls -al
total 32
drwxrwxr-x 2 avnish avnish 4096 Aug 13 08:52 .
drwxrwxr-x 5 avnish avnish 4096 Aug 12 15:36 ..
lrwxrwxrwx 1 avnish avnish   54 Aug 12 17:27 .magicrc -> /usr/share/pdk/sky130A/libs.tech/magic/sky130A.magicrc
lrwxrwxrwx 1 avnish avnish   53 Aug 12 15:41 .magicrc.old -> usr/share/pdk/sky130A/libs.tech/magic/sky130A.magicrc
-rw-rw-r-- 1 avnish avnish 1461 Aug 13 08:45 inverter.mag
-rw-rw-r-- 1 avnish avnish 1242 Aug 13 08:49 inverter.spice
-rw-rw-r-- 1 avnish avnish 3596 Aug 13 08:45 sky130_fd_pr__nfet_01v8_B9CQBZ.mag
-rw-rw-r-- 1 avnish avnish 3508 Aug 13 08:45 sky130_fd_pr__nfet_01v8_N39HBR.mag
-rw-rw-r-- 1 avnish avnish 3365 Aug 13 08:45 sky130_fd_pr__pfet_01v8_KG2LE3.mag
-rw-rw-r-- 1 avnish avnish 3463 Aug 13 08:45 sky130_fd_pr__pfet_01v8_U3KX2J.mag
avnish@pv-workshop-final-03:~/inverter/mag$ /usr/share/pdk/bin/cleanup_unref.py -remove .
Removed unused parameterized cell sky130_fd_pr__nfet_01v8_N39HBR
Removed unused parameterized cell sky130_fd_pr__pfet_01v8_KG2LE3

Done!
avnish@pv-workshop-final-03:~/inverter/mag$ S
```  
Now we are ready to run LVS. Move to the netgen folder and run the following:  

```netgen -batch lvs "../mag/inverter.spice inverter" "../xschem/inverter.spice inverter"```  

If everything is correct so far, the comparison will show the netlists to be identical.

![image](https://user-images.githubusercontent.com/43104014/129341322-bea64297-502e-4694-a248-dfe7b79ee055.png)

Here we see there are some mismatches which we will learn to fix in the upcoming sections.

### Day 2: Introduction to DRC and LVS
Fundamentals of physical verification:-  
Physical verification is the barrier between physical design and design failure. The goal of physical verification is to check for any potential design errors that may inhibit proper functioning or even lead to a denial of manufacturing by the foundry. Verious tools are used to be able to confirm and validate correct electrical and logical functionality and even manufacturability. Verification involves numerous steps such as: 

* Design Rule Check (DRC)
* Layout vs Schematic (LVS)
* XOR
* Antenna Checks
* Electrical Rule Check

The two most important ones are: 
* Design Rule Check:  
Making sure that the design layout meets all the necessary silicon foundry specifications for manufacturing the mask.
* Layout vs Schematic:  
Making sure the extracted netlist from a design layout matches one from a simulatable schematic by checking electrical connectivity and devices.

Modern LVS in verification works by comparing the netlists, one starting from an initial schematic described by an RTL (Verilog/VHDL) and then running through synthesis, and the other one moving backwards from the completed layout, back to the extraction to obtain a netlist for comparison. Some popular open source tools for the same are:  

![image](https://user-images.githubusercontent.com/43104014/129353999-7d9202c1-4ba3-41ba-b6b4-15592c964a06.png)

#### The GDS Format
The Graphic Design System (GDS), more popularised by its successor, GDSII is a database file format which is the most widely used and preferred format for mask manufacturing foundries. It is a binary file format denoting geometric shapes, text labels, and other information about the layout in hierarchical form. This data is widely divided into:
1. Data (Rectangles, Polygons, Subcells)
2. Metadata (Labels, Cell names, Instance names)  

#### Extraction and Netlists
Extraction is the process of generating a netlist from an existing integrated circuit layout that it is meant to represent. Extraction in the magic layout editor is a fairly straightforward process, in which, in two steps, magic generates an intermediate form of its own devising called .ext and this is then converted into a more well known format such as .spice for further use.  

![image](https://user-images.githubusercontent.com/43104014/129372306-509f7126-54bc-4992-ac75-db4668ca8151.png)

The first step is the most complicated part of the process, which creates a file (.ext) that enumerates all electrical nets in the layout, all devices and instances used within that cell, all the common subcells as well as the paracitic capacitances between nets in the layout. The second step simply gathers all information from the .ext file and lists it in a known format like .spice. The commands used for said extraction are:  

```extract do local```  -Saves to local directory
```extract all```       -Actual extraction
```ext2spice lvs```     -Sets options for running LVS
```ext2spice```         -Converts .ext files to .spice netlist

#### DRC Rules in Magic
Magic has a veature in that it runs an idle DRC engine process which allows for interactive DRC. It shows this by indiciating a white stripple pattern on the part of the layout where the DRC violation has occurred. Magic is smart with running its DRC checks, such that it only rechecks the parts of the layout that have been most recently changed. However, if a large change such as movement of the pad frame occurs, magic runs checks on the entire layout again and this can take a long time for large layouts.

Fig: Shows spacing error in Magic
![image](https://user-images.githubusercontent.com/43104014/129389520-9f8040d4-792f-419b-8635-532dd7f0bcf3.png)

To counter the time issue, we have DRC styles in magic that define the way checks are run in the software. There are three types based on their speed and functionality:  
* drc(full)  - Runs complete checks [SLOW]
* drc(fast)  - Runs only typical checks [FAST]
* drc(routing)  -Runs exclusively metal checks [FASTEST]

DRC can be turned off by entering ```drc off``` in the magic console.

#### LVS in netgen
The tool we use for LVS here is called netgen. Netgen is a tool for comparing netlists. It does not know anything about layouts, the devices used in them or the processes. All it knows is netlists, how to read and compare them. LVS would be very simple if netlists could be simply compared one-to-one. However this is not always the case as we seenwhen different netlists use different naming schemes, subcircuits or hierarchies. In such cases, a good LVS tool should be able to handle these differing hierarchies to make a fair comparison of the netlists.
Flattening is a method used when there are differences in hierarchies in order to sort them out to enable fair comparison.

![image](https://user-images.githubusercontent.com/43104014/129393765-94429945-a18e-47cf-b88d-f19adf32eb90.png)

While this is valid in case of hierarchal differences, when there is a single mismatch in a high level hierarchy, it can carry over to the entire system when it is not actually the case.

<img width="367" alt="hier" src="https://user-images.githubusercontent.com/43104014/129394053-0b8c8fd3-95a3-4a57-ae72-415390f77976.png">

Note: Netgen has also a feature to carry out LVV (Layout vs Verilog).

#### XOR Verification
There is another form of physical verification that does not get as much attention as DRC or LVS and that is XOR verification. It works on simple principle of the boolean logic XOR. As shown in the venn diagram, it only gives an output when there is a mismatch in the given two inputs.

![image](https://user-images.githubusercontent.com/43104014/129394523-88f83bd8-8331-4c99-8d72-17e402e922c7.png)

In case of making a few small changes to your gds layout, an XOR can be run over the old and new GDS layouts that would output only the part of the layout over which you made changes.

![image](https://user-images.githubusercontent.com/43104014/129394712-3191f6a1-373e-4f05-9165-b485afbeca56.png)

Now with a basic understanding of DRC and LVS as modes of physical verification, we can move to our terminal to carry out some exercises of our own.

First, we need to work on GDS read and therefore styles in magic. Open magic in your workspace and enter ```cif istyle xxx```. The xxx could be anything, but the point is that the output shows the currently enabled style as well as all the available options.

![image](https://user-images.githubusercontent.com/43104014/129398017-06d8c29a-6808-452b-bc9c-9928e9c589be.png)

Now, using the default enabled style let's try a ```gds read``` from our sky130 directory. Type:  
```gds read /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/gds/sky130_fd_sc_hd.gds```

![image](https://user-images.githubusercontent.com/43104014/129398362-ca02e874-0f1e-4560-b954-24a51bee3647.png)

Now that the read is completed, select a simple cell from Options --> Cell manager such as and2_1:

![image](https://user-images.githubusercontent.com/43104014/129398742-a9a9bed8-dff2-4e82-9d80-8fe8233909c1.png)

Style: sky130()
![image](https://user-images.githubusercontent.com/43104014/129398972-926cd245-fa6a-4bc9-b28f-b6dcec07532f.png)

Style: sky130(vendor)
![image](https://user-images.githubusercontent.com/43104014/129399044-c30d7fd4-8ff5-450d-bf54-512facb3b0bd.png)

Now, to get information about ports, we can select a label (one at a time) and type ```what``` in the console. However selecting a particular label in large layouts is near impossible so, another way to do this is to use ```port first``` to get information about the port indexed by magic as 1.

![image](https://user-images.githubusercontent.com/43104014/129406591-99cd7d1e-b7c2-4002-8b5f-4f3953712eef.png)

As we can see, other than the name, the other fields are labeled as default and this is because GDS file-formats are unable to store metadata and class as well as use come under this type of data. A way to get around this and use vendor metadata to annotate the file is by using ```lef``` as follows:

![image](https://user-images.githubusercontent.com/43104014/129407168-3ec0bfd5-9117-4bd6-89f7-8668e746f4df.png)

Now, running the commands to check port data again, we get

![image](https://user-images.githubusercontent.com/43104014/129407280-82196f2f-3721-4630-ade8-ec2f49590ead.png)

Now, in order to annotate layouts from spice in order to get the right port order, we use:

![image](https://user-images.githubusercontent.com/43104014/129407602-2c4ca48d-e245-4639-9927-f3bb4eb3c1c8.png)

On using that, we load a new and gate from the cell manager

![image](https://user-images.githubusercontent.com/43104014/129407926-085f15ca-ae88-433a-a4e6-a8ea6aa76fbd.png)

And now, on checking the name of port 1, we get A instead of VPWR which is as per the spice annotation.

![image](https://user-images.githubusercontent.com/43104014/129408048-2e2d3087-2aa2-453e-a6e3-2bc9a8e1ebd5.png)

However, this can go wrong in the case of using abstract views. To test this, quit and start a new session of magic. Run the lef read command again and open a new instance of the and2_1 gate from the cell manager. You should see this

![image](https://user-images.githubusercontent.com/43104014/129408479-e9b83a36-78ac-41db-ae2e-9b528930986a.png)

Check the port names and right away we see that port 3 is named X, which is because lef files do not contain port order in the metadata. To fix this, simply run the readspice command from earlier. 

 ![image](https://user-images.githubusercontent.com/43104014/129444245-09711809-ed31-4013-817b-f1461447df95.png)

However, a point to remember about abstract views is that they aren't exact representations of the layout. If you load an abstract view into a layout, you get an incomplete (abstract) view. To see this, create a new file, load the same and2_1 cell and you should see the same abstract view. Save this and write to gds. Note the error message:

![image](https://user-images.githubusercontent.com/43104014/129444923-1909e97c-4baf-4c50-b321-f84f330746a4.png)

On restarting magic and reading gds, we see something odd

![image](https://user-images.githubusercontent.com/43104014/129444949-97bb05fd-49c2-4471-84cb-aaf66bfbf38c.png)

This is because abstreact files aren't supposed to be written to gds and this all comes down to metada. Lef files only know about 2 metals: pins and obstructions. Any metal that is not a pin disappears from the gds file. 

#### Extraction
Extraction is the process of generating a netlist from an existing layout. To perform basic extraction, open magic and load the sky130_fd_sc_hd__and2_1 gate and run:

```console
extract all
ext2spice lvs
ext2spice
```
![image](https://user-images.githubusercontent.com/43104014/129446910-718504a8-1797-4900-be10-b8dfc90b35a5.png)

As we can see, the netlist magic just confirmed is the same as the one in the PDK, with 6 devices (fets), each being a subcircuit call. Some other options for extraction are:  

1. Parasitic capacitance extraction  
```console
ext2spice cthresh <threshold>
```

2. Resistance extraction
```console
ext2sim labels on
ext2sim
extresist tolerance 10
extresist
```

3. RX Extraction
```console
ext2spice lvs 
ext2spice cthresh 0.01
ext2spice extresist on
ext2spice
```
The next sections will teach us how to perform DRC and LVS as well as XOR checks with our extracted netlists.

### Day 3: Fundamentals of DRC
In electronics engineering, a design rule is a geometric constraint imposed on circuit board, semiconductor device, and integrated circuit designers to ensure their designs work reliably and can be produced with acceptable yield. Design Rule Check (DRC) checks whether a specific design meets the constraints imposed by the foundry in question as per their process technology. DRC checking is an essential part of the physical design flow and ensures the design meets manufacturing requirements and will not result in a chip failure. A DRC failure will result in the design being rejected by the manufacturing foundry. The following is a discussion on the multiple DRC rules.

1. Backend Metal Layer Rules:
  * Width rule -  If a material is too thin, a large enough spot defect can cause a break in the material, thus resulting in an open circuit. The width rule defines the minimum width to be maintained for a feature in the layout.
  * Spacing rule - If 2 wires are close together, a simple defect in the mask or the material can cause them to short together. The spacing rule defines the minimum spacing to be maintained between separate features in a layout.
  * Minimum/Maximum area rules - These rules are defined to control the via resistance and optimize contact between layers.
  * Contact Cut (Via) Rules - Contact cuts are metal connections between two stacked layers in a layout. These rules define the specifications for these connections in the design.
  
2. Local Interconnect Rules:
  * Aspect ratio of __uncontacted__ local interconnect > 1:10

3. Frontend Rules:
  * Device-specific rules - Frontend rules are those that directly influence devices and are usually more complex than backend rules. These rules are device-specific, however the designer need not worry about these rules since the PDK includes an automatic parameterized device generator that generates devices that satisfy all device rules.
  * Wells and Taps - The placement of wells and taps is the task with the highest complexity for the designer. These are regions of complementary doping to that of the transistor it surrounds, for example, a pfet device would sit inside of an n-well.
  * Same-net spacing - These define the minimum spacing between the same nets in a layout and require the checker to identify the connections between these nets that in magic, are generally n wells.

4. High Voltage Rules:
  * Any device that is subjected to a voltage outside the range of GND to Vcc is considered a high voltage device. These devices are subjected to special design rules and biasing conditions. For example, transistor gates for high voltage must be both, wider and longer

5. Device rules:
  * Resistors - Resistors can be of two types, diffusion and poly resistors. The diffusion resistors require a paracitic diode from resistor to n well. Poly resistors have specifications for contact cuts for example square or rectangular contacts. Mostly, the inbuilt device builder creates resistor devices.
  * Capacitors - Capacitors are of 4 major types namely, Varactors, MOScap capacitors, MiM capacitors and Vertical Parallel Plate capacitors. All these variants have their own design rule specifications.
  * Diodes - These are formed by the pn junction between diffusion and well. They follow DRC rules for diffusion tap and well.
  * Fixed layout devices - These devices are considered DRC valid by design and only require attention to the spacing rules between them. The designer can simply instantiate the predefined cells without worrying about DRC rules.

6. Miscellaneous DRC Rules:
  * Off grid errors
  * Angle limitations
  * Seal ring rules
  * Latchup rules
  * Antenna rules
  * Stress Rules (Mechanical Damage)  
These miscellaneous rules are defined within the skywater pdk documentation under the [x category](https://skywater-pdk.readthedocs.io/en/latest/rules/periphery.html#x).

7. Density Rules:
When metal layers are anufactured, one of the steps in the process is physical polishing of the fill layers in order to grind down the surface and attain a uniform flatness.

![image](https://user-images.githubusercontent.com/43104014/129456676-47f432c3-fe36-4b4b-b28f-980e17aa1fa1.png)

![image](https://user-images.githubusercontent.com/43104014/129456682-c575746e-301c-4fd0-a2fc-400e6e32470f.png)

Generally, automated tools are used to generate fill patterns and there is one such provision in magic itself. 

8. Recommended, Manufacturing & ERC Rules:
  * Use redundant vias (RR)
  * Width, spacing, area, overlap, extension, angle, density rules (MR)
  * Electrical Rule Checks (ERC) are rules that there are no cases where the layout is DRC correct but has scertain failures that would cause electrical problems, for example, overvoltage and electromigration.

Now, we can move on to testing and implementing these rules for an actual layout.

Start by cloning the following git repository:  
```git clone https://github.com/RTimothyEdwards/vsd_drc_lab.git```  
cd into the lab folder and run ```./run_magic``` in the command line to start magic.

From file, open exercise1 which should open a .mag layout containing 4 types of DRC violations as shown

![image](https://user-images.githubusercontent.com/43104014/129457235-83fd8421-1ab3-43e7-9564-5eb20af930fc.png)

Zoom into the first violation and put the cursor box around the error area. From the drc menu select report or simply press ? on the keyboard. The following report should show up in the console:

![image](https://user-images.githubusercontent.com/43104014/129457304-23eb3672-a849-45ef-8470-82a6e31f2f80.png)

The way to fix this is to increase the width of the metal2 segment upto or aabove the required 0.14um. To do this, set your cursor box around the area and use the middle mouse key to paint the entire area within the box with metal 2.

![image](https://user-images.githubusercontent.com/43104014/129457675-30b8b9b9-9297-41c4-a2fb-02a4564e79da.png)

The error now vanishes. Similarly, for the spacing wrror we need to fix the spacing by moving one of the components by selecting and usi g the arrow keys to move.

![image](https://user-images.githubusercontent.com/43104014/129457723-6358d7fe-5b10-4156-b495-52de451d56b6.png)

The third error is an asymmetrical spacing error and is solved in the same way

![image](https://user-images.githubusercontent.com/43104014/129457762-51527183-631d-496b-a702-8188cf420b64.png)

The fourth error is solved by selecting half the figure and stretching it away from the notch using Shift+arrow key on numpad.

![image](https://user-images.githubusercontent.com/43104014/129457943-e48c37ce-ea45-4e8f-8198-539285e27681.png)

Next, go to file and open exercise 2. This one is all about vias and the violations they throw. The first one is simply solved by stretching to the side and then vertically.

![image](https://user-images.githubusercontent.com/43104014/129458200-af264c86-f541-4f69-9e9e-d3007faf0b24.png)

The second one is an area on which contact cuts will be drawn onto by the algorithm. To see the cuts, type ```cif see MCON``` after boxing the figure within your cursor box and you should see this

![image](https://user-images.githubusercontent.com/43104014/129458443-ba5b0b67-d79a-47d9-9ad0-24dcd67d56ff.png)

The third is a simple case of overlapping vias and the way to solve this is to surround the point with the desired width of metal1 (0.03um) and then by an additional 0.06um padding either horizontally or vertically. The result should look like this:

![image](https://user-images.githubusercontent.com/43104014/129458747-f3291dfa-60bb-40e9-8d49-930dc2efd02c.png)

The last part of this exercise shows us auto generation of vias on a strip of metal by selecting the area and running cif see MCON and cif see VIA1 and so on till 5.

![image](https://user-images.githubusercontent.com/43104014/129458847-7dedfa61-8c39-4ac2-860a-02a8e8e0583c.png)

Now, loading exercise 3 from the file menu, we see the next 2 types of DRC violations, minimum area and hole rules. These are simply fixed by increasing the area and in the first case above the minimum requirement as seen by pressing the ? key on selection.

![image](https://user-images.githubusercontent.com/43104014/129467729-74e75427-4052-4a3e-a9e7-3bdd70299345.png)

![image](https://user-images.githubusercontent.com/43104014/129467753-c887d186-417c-4fa9-a470-4ac088eb79eb.png)

The minimum hole rule doesn't show an error since minimum hole rules are compute expensive and since the DRC style is not on complete by default, we neeed to switch the style and run DRC check.

![image](https://user-images.githubusercontent.com/43104014/129467812-e89bef7e-b14c-467a-8b78-4daecd34e5af.png)

This is easily fixable by erasing some more metal fom the center to make the hole larger

![image](https://user-images.githubusercontent.com/43104014/129467824-bc7ab9d1-34a8-4449-8f6d-e7378e7b004e.png)

Next, open exercise 4 from the file menu and move to 4.a. This error deals with wells and since sky130 does't usually deal with p-wells, the p-well shown here is just seen as a p substrate and does not throw an error. The first error in the n well is that it should be connected to metal as an n-tap. Firstly we need to paint some nsubstratendiff into n-well. Next, it needs to be connected to a valid potential (nsubstratencontact). Post this, fix all overlap errors as done before by stretching and finally, extend the local interconnect along one axis to eliminate all the errors and get this

![image](https://user-images.githubusercontent.com/43104014/129468237-e5a8d47e-e10d-4dd3-9512-e0e74d972bb2.png)

In exercise 4b the problem is similarly fixed, but instead of n, we choose p substrate for the contact. In 4.c we deal with deep n-wells and on a query we are met with multiple errors, firstly a spacing error that says n-wells must be a certain distance away from each other and since the n-well in 4.c interacts with the one in 4.b we have this problem. Secondly, we need a ring of n-well around the hole for the p-substrate. Lastly, we need a contact like we have made before and on completing all the steps we may eliminate all DRC errors.

<img width="960" alt="nwell" src="https://user-images.githubusercontent.com/43104014/129468875-d46d33b7-04cd-424f-b141-5a8d2ac7c150.png">

Now, open exercise 5 to move on to derived layers. In 5.a we see the center of the drawn figure, what seems to be a transistor is labeled as nmoslvt and not nmos. However if we draw another transistor below, the same intersection is now labeled as nmos and we have to specifically select nmoslvt to fill the center with that.

![image](https://user-images.githubusercontent.com/43104014/129468963-9b91b39b-33d2-4a8d-96b7-ea07c230a548.png)

Next, in 5b we see a similar scenario but with the high voltage counterpart

![image](https://user-images.githubusercontent.com/43104014/129469270-ff064bf8-73fb-4fdd-9062-0737babda27a.png)

In 5.c we see poly contacts which require a special type. Not an implant, but a layer etch instead called Nitride PolyCut (NPC). NPC etches through any residue on the poly to ensure a firmer contact between the poly and local interconnect. Use ```cif see NPC``` to see the details about the Nitride Polycut layer.

![image](https://user-images.githubusercontent.com/43104014/129469435-650c15c6-f2c2-4aa7-85af-afc4f009c8e4.png)

Moving on, exercise 6 shows few empty squares with errors but on selecting them and expanding using "x" , we are able to see that these cells are in reality, parametrized cells. Zooming in on the first device shows an nfet with DRC errors and these errors occur because the metal 1 minimum area is not met. The reason this error exists is because connections are going to be made to these contact points anyways, which would fix the error. Putting metal 1 on the top and bottom contacts solves this.

![image](https://user-images.githubusercontent.com/43104014/129471681-3315e044-76fe-49c3-a79e-2f64b2656c71.png)

On to 6.b, we see an ESD fet. The error exists in the angled transistor gate edges. This layout in in reality, known good but the rule is too complex to be described by the DRC engine. These errors are solved by simply removing the angled edges of polysilicon by selecting and using the command ```erase poly``` in the console. A similar situation exists in 6.c where some errors are related to the angled edges and others are atatched to the existence of a deep n-well. This n-well is not really necessary since there are other ways to represent an isolated substrate.

![image](https://user-images.githubusercontent.com/43104014/129471984-dbd1034a-5a02-445e-aa2c-960050fadd3d.png)

Exercise 7.a represents off grid errors which result from angled lines intersecting at a point that does not meet in the grid of minimum dimensions specified by the manufacturing process.

![image](https://user-images.githubusercontent.com/43104014/129472129-5a379162-0738-408e-8e69-5a6b30d2bb61.png)

To solve this, we simply select one of the figures and move it by 1 unit.

<img width="480" alt="7a" src="https://user-images.githubusercontent.com/43104014/129472278-78c357a4-b7e5-46f6-a2b6-7159711f230f.png">

In 7.b, the angled edge simply needs to be eliminated by filling in.

![image](https://user-images.githubusercontent.com/43104014/129472299-aab79952-b6ad-4b0e-87f4-9b910bc4be7a.png)

7.c is a simple case of the angle not meeting the permitted values. the dimensions of the sides of the triangle are 201x200 which means the angle is not exactly 45 degrees. This is easily sorted by deleting the area and then using the ```splitpaint sw m1``` command.

<img width="960" alt="7b" src="https://user-images.githubusercontent.com/43104014/129472433-edcffe9b-adcf-4ede-bc6a-ee49e90e7439.png">

7.d shows an orverlap rule violation since they layers aren't really overlapping, but are in different cells. To fix this simply paint poly over the overlap area and delete the subcell fom the bottom layer. 7.e is an overlap violation in the case of overlapping cells where the contacts aren't in exact overap. ```cif see VIA2``` is used to make the vias visible.

![image](https://user-images.githubusercontent.com/43104014/129472828-81b770b0-4f14-4042-9a64-8d8fad11327c.png)

In the second case, the contact is made with the use of 2 horizontal overlapping cells where the square contacts aren't in exact overlap and it throws a violation. Select one and move the cell untill they overlap exactly and the error disappears.

![image](https://user-images.githubusercontent.com/43104014/129473209-bae99af3-3f6f-4739-96fb-20c150a765c4.png)

Exercise 9 demonstrates antenna rules. On expanding and querying, we get the following violation report:

![image](https://user-images.githubusercontent.com/43104014/129473843-5926ff11-aa79-4155-826e-5700b0b43b4b.png)

The latchup rule is so designed that the well and the substrate are pegged well enough that they won't vary from their expected voltage and cause trouble. This is a standard cell and is automatically handled by the PnR tools.

Exercise 10 moves away from magic's DRC engine and looks at electrical rule checks, specifically the antenna rule check. The first step in any ERC check is to extract a netlist, since to do electrical checks we need to have a knowledge of the circuit. Once extracted, run the antenna check commands as follows to obtain the error messages:

![image](https://user-images.githubusercontent.com/43104014/129474206-2b998c06-2dd4-4a8f-b1b1-fe443159e5f9.png)

There are 2 ways to solve antenna violations, the first being to tie down the route to a diffusion, which is a diode. If the feedback says the error is at plane metal 2, you have to connect anywhere to the highlighted metal down to the diffusion. On making this connection using the wiring tool, re-extracting (this step is necessary since the layout has been changed) and re running antennacheck shows no error. 

![image](https://user-images.githubusercontent.com/43104014/129475729-7568ee91-0ff8-4e5e-b1af-063466d8092e.png)

Exercise 11 deals with density checks. Metal 1 is a thin ring around the boundary and is thus underdensity and metal 2 is onversely, overdensity. The command to check density is ```cif cover MET1``` with MET2 for metal 2. Density rules are generally defined in smaller areas stepped in x and y and the density has to be satisfied in each of those areas so that it is covered across the whole chip and is thus performed by a density script. Firstly, write the file to gds and then, in a new terminal, cd to the workspace folder and run the following command:  

```/usr/share/pdk/sky130A/libs.tech/magic/check_density.py exercise_11.gds```

![image](https://user-images.githubusercontent.com/43104014/129476237-a308d502-1d46-4991-a791-5c89090e6653.png)

To deal with all the errors, there is another script called generate_fill.py which is run as follows

![image](https://user-images.githubusercontent.com/43104014/129476357-de898127-1023-4763-8239-c176a76af4fa.png)

The output of the file is a .gds file and can be loaded into magic using ```gds read exercise_11_fill_pattern``` 

![image](https://user-images.githubusercontent.com/43104014/129476398-536794b4-9e57-419a-809f-aafedcb35a5e.png)

This is to be combined with the original, so once again load exercise 11 and run the following:

![image](https://user-images.githubusercontent.com/43104014/129476449-2c735d10-96d0-4b21-9f2e-d19f6fc53874.png)

Once combined, your fill layer is completed and it should satisfy all density rules. Run checks using the console to ```cif``` see particular layers and look for any possible errors. 

### Day 4: OpenLane Flow
OpenLane is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, Fault and custom methodology scripts for design exploration and optimization. It s a tape-out-hardened flow that addresses two main use cases: hardening a macro and integrating a System-on-a-Chip (SoC).

#### OpenLane Flow Stages:
* Synthesis
RTL is converted to Gate level representation Tools used for this synthesis are:
  1. Yosys – RTL synthesis
  2. ABC – Technology mapping

* Static Timing Analysis:
OpenSTA performs static timing analysis on the product netlist using ideal clocks to generate the timing report.

* Floorplanning:
Involves various physical design flow stages such as floorplanning, powergrid distribution and network generation.

* Placement
Involves placement of various cells, macros as well as the essential stage of clock-tree synthesis.

* Routing
The FastRoute tool performs fill insertion for routing.

* Antenna diode insertion
Antenna diode insertion stage is necessary to DRC violations related to antenna, as seen previously.

* Parasitic (RC) extraction
Performed by Magic after which, another STA stage follows.

* Physical Verification
Physical verification, as this course demonstrates is performed using Magic, Klayout, Netgen, CVC.

GDSII is streamed out by Magic and Klayout. Klayout GDSII streamed from def file and used as a backup.

The Openlane flow can be run in one of two ways:
* Non interactive method: Involves a prewritten script such as a shell script to run all the aforementioned processes. This method requires no manual work but is also hard to intervene.
* Interactive method: As the name suggests, involves manually running each and every stage and making spontaneous changes in the process parameters as and when required.

### Day 5: Running LVS
Layout vs Schematic (LVS) and Design Rule Checking (DRC) are the two most important verification procedures before a chip design is sent to the foundry for manufacturing. However, as compared to DRC, a chip that fails LVS will pass the foundry checks and go into manufacturing, but stands at a risk of returning as a non-working chip.

![image](https://user-images.githubusercontent.com/43104014/129476888-4cee43d6-d696-4cb7-b5d2-46e2a5ad812e.png)

LVS tools have one major drawback in that, even though they are very good at pointing out when netlists do match, when they don't, they cannot accurately pinoint the reason, which usually entails a long cycle of trial and error before the design engineer is able to pass LVS.  

The very first step in LVS is preparation, in which it converts the two files, the layout and schematic into netlists, usually of a popular format such as SPICE. Other formats are:  
* LEF/DEF
* Verilog
* BLIF

This repository will focus on the LVS testing using SPICE formats, starting from schematics. When dealing with schematics in LVS there are a lot of rules about what to do and what not to do, all of which are underlined in the IO library [sky130_fd_io](https://skywater-pdk.readthedocs.io/en/latest/contents/libraries.html#io-and-periphery-libraries).  
It is important to underline and understand the difference between a netlist for LVS and a netlist for simulation. While both cases consider devices in design, when it comes to parameters, an LVS netlist only includes basic parameters (W,L) whereas simulation netlists consider ALL parameters, including parasitic capacitances and even resistances if needed. In an LVS netlist, all nets in the design are included, whereas in a simulation netlist, all nets are rewired around parasitics. Parasitic capacitances do not have too much of an effect on simulation runtime but parasitic resistances can slow a simulation down significantly.  

Netgen is a tool used for LVS testing and comparison and can be run from its console which is a Tcl/tk interpreter. Netgen commands can be run in batch mode and the syntax to run LVS in netgen is:  

```netgen -batch lvs "file1 circuit1" "file2 circuit2" setup_file output_file```

#### The Netgen LVS process:
The Netgen algorithm works in a number of steps as follows:
* Start with 2 netlists in lets say, SPICE formats.
* First iteration: Create a list of devices and a list of nets from both netlists
* Second iteration: Works through each device in the list of devices, assigning a number called hash (#) and each pin, a pin number which is a hash of the pin name.
* Third iteration: Generates a new hash number for every net based on the connections which is the result of the hash numbers of the pins it is connected to.
* Fourth iteration: Runs through all the devices, giving them a new hash number based on the nets that its pins are connected to.
* Fifth iteration: Creates 2 lists, one of nets and one of devices with the same hash number, called partitions.  

This whole process is repeated for every partition untill ideally there are as many partitions containing a pair of nets or devices as there are nets or devices. If this condition occurs, the netlists match.

Some other processes that occur in LVS are pin matching and property checking. Pin matching is the process of identifying two differently labelled pins as the same equivalent if they have the same electrical connections. For example, in a NOT gate if you label input A and output B and in they layout you name input X and output Y, the LVS checker should be able to identify them the same despite the difference in nomenclature. Property checking is the process of matching device property while running LVS such as width and length of a transistor. 2 transistors may have identical electrical connections but if they are completely different transistors, the LVS check should not pass. Netgen has some acceptable values by which parameter values can differ and still be considered matching since no teo transistors are completely identical.

#### Combinations in Netgen: Series/Parallel:
The way series or parallel connections are made in schematics and/or layouts can differ and LVS should be able to see 2 equivalent combinations as the same before declaring them a mismatch! The general combination rules followed by Netgen LVS are:  

1. Transistors combine in parallel
  * For same l, transistors can combine to a parallel W by adding

2. Capacitors combine in parallel
  * Parallel devices can be merged by adding area

3. Resistors combine in series or parallel
  * For same W: Series  l can merge by adding
  * For same W: Parallel l can merge by parallel sum

#### Interpreting LVS results:
Netgen provides a side-by-side comparison layout of the 2 netlists entered as arguments to the LVS command, labeled as circuit 1 and circuit 2, one being the netlist extracted from the layout and the other from the schematic. 

![image](https://user-images.githubusercontent.com/43104014/129482508-ad6fc5ed-3e0d-420f-ad90-49f3f5bee9b4.png)

This provides a number of reports:
* Series/Parallel merging report
* Side-by-side report of cell contents
* Pre-match analysis report
* Topology matching report
* Property matching report
* Pin matching report
* Failing partitions dump

The runtime output we see here is simply a summary of the entire LVS comparison process and should not be completely relied upon. The ```comp.out``` file contains the full detailed report of the comparison and contains all the information from the runtime output and more.

Let's now perform some LVS experiments in Netgen and get a hands-on idea on how the tool works

Starting very simple, lets consider 2 netlists with exactly the same nets and devices, only differing in the first line that is a comment.

![image](https://user-images.githubusercontent.com/43104014/129482961-0687c1c5-4a09-448a-b77f-4822a100e851.png)

On starting netgen and running lvs using the command ```lvs netA.spice netB.spice``` we get the following output

![image](https://user-images.githubusercontent.com/43104014/129483022-7ff0eeb8-21e1-4a0b-a492-0c5eb3b00bcc.png)

Now, as we know the details about the comparison lie in the comp.out file, so reading that we see

![image](https://user-images.githubusercontent.com/43104014/129483184-e61a2b61-32cb-43ec-a8fd-9b60d558beee.png)

There isn't much to learn from 2 netlists that are identical, so let's edit the first pin pf cell 3 in netA.spice from "C" to "B"

![image](https://user-images.githubusercontent.com/43104014/129483306-028779d9-1fa9-419c-8973-d560d4c9cb61.png)

This report clearly shows us the instances where the mismatches occur, which pins and which nets are missing from which cells in either of the two nets.  

Next, we see an example of LVS with subcircuits

![image](https://user-images.githubusercontent.com/43104014/129483554-90e188a2-c7ec-4c28-8bd3-a6c6e584c5e4.png)

On running LVS with netgen, we see:

![image](https://user-images.githubusercontent.com/43104014/129483597-808ee58a-c318-48e8-879e-06304cab7a29.png)

Which makes sense, since the two netlists do not include subcircuit calls, but only definitions. This means there are no circuit level components on the top level of the netlists and thus, nothing for netgen to compare. However, it is possible to specify to netgen which subcircuits in the netlist to compare in order to get a valid LVS check.

![image](https://user-images.githubusercontent.com/43104014/129484953-455e7700-9a8a-4c9c-bebf-507f942f87a2.png)
![image](https://user-images.githubusercontent.com/43104014/129484960-c4f1b3f7-b7a0-462f-a8fd-5c8ec389caa1.png)
![image](https://user-images.githubusercontent.com/43104014/129485021-9d94e3b9-e888-49e3-b3b1-71da619792ee.png)

In the next case, we deal with blackbox cells, netlist A and B being:

![image](https://user-images.githubusercontent.com/43104014/129485124-b74bf7e7-9319-4552-91d4-a800bd1877a4.png)

Running lvs on these two netlists gives the following result:

![image](https://user-images.githubusercontent.com/43104014/129485500-a7cc084c-4b2d-4a03-9217-2a0d0803335c.png)

The main difference here we see is that it prints information about each of the subcells. If we edit the netA.spice, changing the pins on cell1 from {A B C} to {C B A} and run lvs again,

![image](https://user-images.githubusercontent.com/43104014/129485600-3be40690-0bef-4e75-a804-abcf0d09d504.png)

Now, changing the pins on cell1 to {A B D} we see

![image](https://user-images.githubusercontent.com/43104014/129485642-21c6daf3-e961-4987-89fc-a7459c72b344.png)

The comp.out file shows:

![image](https://user-images.githubusercontent.com/43104014/129485669-5883e894-9c51-4a89-9a5f-a9e33b03f546.png)

Now, let's look at LVS with low-level spice components. Here we have netA.spice and netB.spice as:

![image](https://user-images.githubusercontent.com/43104014/129485831-9b1299af-c5b6-4168-bffe-e67f2572397e.png)

![image](https://user-images.githubusercontent.com/43104014/129485839-886f5660-a706-4d33-9e26-e2494f7408ed.png)

Running lvs on this gives us a pass, saying that the netlists match uniquely. Now since we have resistors, if we swap pins on the resis tor we should get the same output right?

![image](https://user-images.githubusercontent.com/43104014/129485926-83a4bd5b-0754-4897-add9-574549595847.png)

This time round, we fail lvs, because we need to specifically tell netgen that we can swap pins A and C using the ```permute``` command. At the end of the setuo.tcl file, append:

```console
permute "circuit1 cell1" A C
permute "circuit2 cell1" A C
```

This tells netgen that these pins are permutable and now on running lvs, we get

![image](https://user-images.githubusercontent.com/43104014/129486294-53f24cab-6fc4-412b-ad62-ab9b3a729ecd.png)

Which finally passes LVS. Doing the same for a diode, while also flipping the diode, since it is not symmetrical also results in an LVS pass.

Next, we can move on to LVS on an actual circuit, in this case, a power on reset circuit. The xschem schematic of the same looks like this:

![image](https://user-images.githubusercontent.com/43104014/129486572-b7224554-eca5-4df2-af3d-6f143ca67a38.png)

From simulation menu, enable the option: "LVS netlist: top lvl is a .subckt" and tap netlist. This should generate a .spice file. Next, open magic and extract. Now, running lvs on these two netlists, we get a mismatch

![image](https://user-images.githubusercontent.com/43104014/129486954-ba27458b-46ee-496f-a00d-19571f82cd0f.png)

This is because we do not have the subcircuit definitions for the files. On running lvs for the wrapper we get a net match but a pin mismatch. The reason for this can be pinpointed by opening the weapper subcircuit in magic and finding that ip_analog[4] pin in the layout is tied to io_clamp_high[0]. This is fixed by replacing the bridge between the two nets with a resistor of type rmetal3 to separate the cells.

![image](https://user-images.githubusercontent.com/43104014/129487735-ff504043-fa89-43cd-8234-eb085436882a.png)

Now, extract the new netlist by using the extract commands and re-run lvs. This as expected, shows a problem since the device count in the layout netlist has changed, so the same change has to be made in xschem.

![image](https://user-images.githubusercontent.com/43104014/129487797-00e12687-9832-4f03-8e75-6c99a25d72ba.png)

Once again, set the top level circuit to be output, save the netlist and re-run  lvs. Now, you should see 9 pin mismatches. These can again be pinpointed on the layout to be shorted to vssa1 and vssd1 and need resistors added to separate the two nets. Similarly, adding resistors in xschem and then finally comparing the two netlists will make it LVS clean.

Next, we will see how to run layout vs verilog (LVV). First, we extract the schematic from our layout, which is a digital pll cell:

![image](https://user-images.githubusercontent.com/43104014/129487986-0e69c32a-5af2-46c1-8a6e-ecf2720c49bd.png)

However this throws a huge LVS mismatch

![image](https://user-images.githubusercontent.com/43104014/129488041-a71c7179-b0de-44ea-8e32-ed35ea91778e.png)

This is caused because there exists a net in the layout which because of having no primitive devices connected or having any other connections, only serves as a parasitic and therefore is completely optimized out of spice. This is fixed by adding the following line in the run lvs command:

```export MAGIC_EXT_USE_GDS=1```

This time, we get a match!

![image](https://user-images.githubusercontent.com/43104014/129488232-05f83572-c536-4179-9c60-a17ea9c5d7fe.png)

Lastly, we can do an LVS check in a case with property errors. Generate netlists from layout as well as schematic:

![image](https://user-images.githubusercontent.com/43104014/129488682-98ab0cf4-9d28-426f-a201-acf51b1ae809.png)

And then run lvs

![image](https://user-images.githubusercontent.com/43104014/129488690-42ebbefa-ca2f-44ea-9f69-85096c57aa10.png)

Here, clearly we see property errors in the netlists, namely in the MEMcap capacitors and in the resistors. Once the changes are applied to the schematic and a new netllist is extracted, the LVS is passed! With that the last activity of this course and the course itself comes to an end.

### Acknowledgement:
I wish to acknowledge the help provided by Mr.Kunal Ghosh, Mr. Tim Edwards for guiding me through the course and helping me understand the concepts. I also show my gratitude to the entire team of VSD for hosting the workshop. I also thank Ms. Sai Charan, Mr. Manjunath R V, Mr. Anmol Purty for the kind support.

### REFERENCES

https://www.vlsisystemdesign.com/?v=a98eef2a3105

https://vsdiat.com/
