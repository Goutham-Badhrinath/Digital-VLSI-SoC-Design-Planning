## Day 3 - Design library cell using Magic Layout and ngspice characterization

### Theory

### Implementation

* Day 3 tasks:-
1. Clone custom inverter standard cell design from github repository: [Standard cell design and characterization using OpenLANE flow](https://github.com/nickson-jose/vsdstdcelldesign).
2. Load the custom inverter layout in magic and explore.
3. Spice extraction of inverter in magic.
4. Editing the spice model file for analysis through simulation.
5. Post-layout ngspice simulations.
6. Find problem in the DRC section of the old magic tech file for the skywater process and fix them.


#### 1. Clone custom inverter standard cell design from github repository

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Clone the repository with custom inverter design
git clone https://github.com/nickson-jose/vsdstdcelldesign

# Change into repository directory
cd vsdstdcelldesign

# Copy magic tech file to the repo directory for easy access
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .

# Check contents whether everything is present
ls

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```

Screenshot of commands run

![1](Screenshots/30.png)

#### 2. Load the custom inverter layout in magic and explore.

Screenshot of custom inverter layout in magic

![2](Screenshots/31.png)

NMOS and PMOS identified

![3](Screenshots/32.png)


#### 3. Spice extraction of inverter in magic.

Commands for spice extraction of the custom inverter layout to be used in tkcon window of magic

```tcl
# Check current directory
pwd

# Extraction command to extract to .ext format
extract all

# Before converting ext to spice this command enable the parasitic extraction also
ext2spice cthresh 0 rthresh 0

# Converting to ext to spice
ext2spice
```

Screenshot of tkcon window after running above commands

![9](Screenshots/32.png)

Screenshot of created spice file

![10](Screenshots/33.png)

#### 4. Editing the spice model file for analysis through simulation.

Measuring unit distance in layout grid

![11](Screenshots/34.png)

Final edited spice file ready for ngspice simulation

![12](Screenshots/35.png)
#### 5. Post-layout ngspice simulations.

Commands for ngspice simulation

```bash
# Command to directly load spice file for simulation to ngspice
ngspice sky130_inv.spice

# Now that we have entered ngspice with the simulation spice file loaded we just have to load the plot
plot y vs time a
```

Screenshots of ngspice run

![14](Screenshots/36.png)

Screenshot of generated plot

![16](Screenshots/37.png)

Rise transition time calculation

```math
Rise\ transition\ time = Time\ taken\ for\ output\ to\ rise\ to\ 80\% - Time\ taken\ for\ output\ to\ rise\ to\ 20\%
```
```math
20\%\ of\ output = 660\ mV
```
```math
80\%\ of\ output = 2.64\ V
```

20% Screenshots

![17](Screenshots/38.png)

80% Screenshots

![19](Screenshots/39.png)
![20](Screenshots/40.png)

```math
Rise\ transition\ time = 2.24636 - 2.18237 = 0.06399\ ns = 63.99\ ps
```

Fall transition time calculation

```math
Fall\ transition\ time = Time\ taken\ for\ output\ to\ fall\ to\ 20\% - Time\ taken\ for\ output\ to\ fall\ to\ 80\%
```
```math
20\%\ of\ output = 660\ mV
```
```math
80\%\ of\ output = 2.64\ V
```

20% Screenshots

![21](Screenshots/41.png)

80% Screenshots

![23](Screenshots/39.png)
![24](Screenshots/42.png)

```math
Fall\ transition\ time = 4.0955 - 4.0536 = 0.0419\ ns = 41.9\ ps
```

Rise Cell Delay Calculation

```math
Rise\ Cell\ Delay = Time\ taken\ for\ output\ to\ rise\ to\ 50\% - Time\ taken\ for\ input\ to\ fall\ to\ 50\%
```
```math
50\%\ of\ 3.3\ V = 1.65\ V
```

50% Screenshots

![25](Screenshots/43.png)
![26](Screenshots/44.png)

```math
Rise\ Cell\ Delay = 2.21139 - 2.14992 = 0.06147\ ns = 61.47\ ps
```

Fall Cell Delay Calculation

```math
Fall\ Cell\ Delay = Time\ taken\ for\ output\ to\ fall\ to\ 50\% - Time\ taken\ for\ input\ to\ rise\ to\ 50\%
```
```math
50\%\ of\ 3.3\ V = 1.65\ V
```

50% Screenshots

![27](Screenshots/45.png)
![28](Screenshots/46.png)

```math
Fall\ Cell\ Delay = 4.078 - 4.05 = 0.028\ ns = 28\ ps
```

#### 6. Find problem in the DRC section of the old magic tech file for the skywater process and fix them.

Link to Sky130 Periphery rules: [https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html](https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html)

Commands to download and view the corrupted skywater process magic tech file and associated files to perform drc corrections

```bash
# Change to home directory
cd

# Command to download the lab files
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz

# Since lab file is compressed command to extract it
tar xfz drc_tests.tgz

# Change directory into the lab folder
cd drc_tests

# List all files and directories present in the current directory
ls -al

# Command to view .magicrc file
gvim .magicrc

# Command to open magic tool in better graphics
magic -d XR &
```

Screenshots of commands run

![29](Screenshots/47.png)

Screenshot of .magicrc file

![31](Screenshots/48.png)

**Incorrectly implemented poly.9 simple rule correction**

Screenshot of poly rules

![32](Screenshots/49.png)

Example met3 model

![30](Screenshots/50.png)

Incorrectly implemented poly.9 rule no drc violation even though spacing < 0.48u

![33](Screenshots/51.png)
![34](Screenshots/52.png)

New commands inserted in sky130A.tech file to update drc

![35](Screenshots/54.png)
![36](Screenshots/55.png)

Commands to run in tkcon window

```tcl
# Loading updated tech file
tech load sky130A.tech

# Must re-run drc check to see updated drc errors
drc check

# Selecting region displaying the new errors and getting the error messages 
drc why
```

Screenshot of magic window with rule implemented

![37](Screenshots/53.png)
![38](Screenshots/copy_poly9.png)


**Incorrectly implemented nwell.4 complex rule correction**

Screenshot of nwell rules

![43](Screenshots/59.png)

Incorrectly implemented nwell.4 rule no drc violation even though no tap present in nwell

![44](Screenshots/56.png)

New commands inserted in sky130A.tech file to update drc

![45](Screenshots/60.png)
![46](Screenshots/61.png)

Commands to run in tkcon window

```tcl
# Loading updated tech file
tech load sky130A.tech

# Change drc style to drc full
drc style drc(full)

# Must re-run drc check to see updated drc errors
drc check

# Selecting region displaying the new errors and getting the error messages 
drc why
```

Screenshot of magic window with rule implemented

![47](Screenshots/57.png)
![48](Screenshots/58.png)
