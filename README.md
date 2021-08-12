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

