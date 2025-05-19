# Arctic Ice Modeling and Climate Analysis Using MITgcm

Analyzing the past effects of global warming on the Arctic, and understanding the future progression of the melting of the Arctic ice, and its implications. Monitoring the geography and condition of our planet is incredibly important to understanding the ways we are leaving some sort of impact on our planet.

One of the largest formations of land on our planet is the Arctic ice regions in the north. The goal of this project is to understand the condition of the Arctic over the years, analyze various environmental factors and effects, and build a model which will hopefully allow us to understand what the future of the Arctic region may look like.

The overall science question is, how will the artic sea ice bed continue to be effected with the increasing global temperatures over the next several years? 

## Configurations of the MITgcm Model

### Code
Contains header files and important configuration files such as `SIZE.h` which contains the model size, and processor utilization info.

- **Total Model Domain Size**: 720, 300 (Resolution of 0.5 degrees longitude × 0.1 degrees latitude)  
- **Vertical domain**: 50 levels  
- **Processor Parallelization**: 2 in x-direction (`nPx`), 1 in y-direction (`nPy`)  
- **Grid Size**: 360, 300  
- **Overlap (`OLx`, `OLy`)**: 4, 4  

These are important configurations needed in order to repeat the experiment with this specific domain and grid size.

### Needed Packages (`Code/packages.conf`)
- `gfd`, `cal`, `exf`, `obcs`, `kpp`, `seaice` (for sea ice modeling)  
- Certain packages like `seaice`, `exf`, and `obcs` need to be allowed for use in the `CPP_OPTIONS.h` file.  
- In `OBCS_OPTIONS.h`, open boundary conditions are considered in all 4 directions, given the presence of open water on all 4 sides of the Arctic ice bed.  
- **Sponge thickness** of 20 is also used to minimize external forcing going out of bounds.

### Namelist
Contains several data files which configure runtime information for the model.

### Simulation Startup and Run
As of current, the model simulation in the `data` file is set to model about **5 days** of simulation (timestep of 20 seconds with 21,024 timesteps). However, this is for testing, and ideally should be run for much longer, with a larger `deltaT` to gain insightful information on what the Arctic will look like several years into the future.

### Setup Instructions

To begin, clone the MITgcm code into your environment:

```bash
git clone https://github.com/MITgcm/MITgcm.git
cd MITgcm
```

Add the needed libraries and compilers (Fortran and C) to your environment. Copy this project directory, and copy the files to your MITgcm verification folder.

Then build the model entering into your build directory:

```bash
cd /path/to/MITgcm
mkdir -p build
cd build
../tools/genmake2 -mods=../verification/artic_ice/code -mpi
make depend
make -j4
```

Create a run directory and copy the files as well.

### Input Files Needed for the Model

- **Bathymetry file**
- **Initial conditions**
- **External conditions**
- **Boundary conditions**

#### Initial Conditions

**Ocean**:
- `THETA_IC.bin`, `SALT_IC.bin`, `UVEL_IC.bin`, `VVEL_IC.bin`, `ETAN_IC.bin`

**Sea ice**:
- `SIarea_IC.bin`, `SIheff_IC.bin`, `SIhsnow_IC.bin`

#### External Forcing Conditions
- Wind
- Radiation (to get ice melting)
- Temperature
- Humidity
- Precipitation

#### OBCS
- Temperature, salinity, velocity, and sea ice conditions at boundaries

### Running the Model

Once all the input files are added, the model is configured, and a batch script is created, submit the batch script using `sbatch`, and ensure the model is running using the `squeue` command.

If run properly, the simulation should produce several output files.

- The **run directory** will contain several initial state files (`U`, `V`, `T`, `S`, `Eta`)  
- The **Diags folder** will contain the diagnostic outputs configured from `data.diagnostics`  
- If configured to have them, you will also get **checkpoint files** for restarting the model.

### Notes

- This configuration is set for a short (4.8 day) simulation. For longer runs, adjust `nTimeSteps` in the `data` file  
- The model is parallelized for **2 MPI processes**, which can be increased if needed  
- All forcing files are from **2015** and should be changed if needed.
