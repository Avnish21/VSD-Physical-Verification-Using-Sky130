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
#### Day 4: 
#### Day 5: 

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
