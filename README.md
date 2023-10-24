---
### ABOUT DIFFSIM

Simulation of diffusion using a 3D random walk, with trapping into reversible sinks. 

**DIFFSIM** is framed to look at noble gas diffusion in minerals, most specifically helium diffusion in apatite analyzed using the continuous ramped heating (CRH) approach. It models diffusion (and trapping) during a geologic history and then diffusion and trapping during a laboratory outgassing phase designed to simulate CRH analysis. The code tracks the number of radiogenic daughter atoms created, lost by alpha ejection, lost by diffusion, and trapped during each phase. The code also calculates an apparent age based on the linear-in-time production of daughter atoms (a good-enough approximation for moderate-length and shorter geologic thermal histories), and the code determines a numerical closure temperature by interpolating the apparent age into the thermal history.

Trapping and escape from sinks obeys an exponentially temperature dependent probability distribution that is scaled as relevant for the laboratory and geologic time phases.

A companion pdf document provides much more information about the workings and application of **DIFFSIM**.

---
### AUTHOR

Peter Zeitler, Lehigh University, Bethlehem, PA USA

---
### COMPILATION

Compile `diffsim` like this (but see note below about helper files):

`g++ diffsim_par_314.cpp -o diffsim3_m2 -fopenmp -Ofast -march=native`

(substitute your local source file name for `diffsim_par_314.cpp`, and your preferred executable name for `diffsim3_m2`).

*Note about helper files*. In your compilation directory you also need to have the two support files for the pcg random number generator: `pcg_extras.hpp` and `pcg_random.hpp`. For more info about these, see [www.pcg-random.org](https://www.pcg-random.org).

**DIFFSIM** is coded to work with multiple CPU cores using OpenMP. If you do not compile the code with the`-fopenmp` flag, the code wil be substantially slower. Also, compilation will throw a few errors - you'll need to tweak a few statements related to OpenMP, including a few statements related to timing.

For MacOS, a good source for gcc and gfortran installer packages can be found at:

[hpc.sourceforge.net](https://hpc.sourceforge.net)

---
### REQUIREMENTS FOR PLOTTING

**DIFFSIM** assumes you have *gmt* installed (Generica Mapping Tools), either version 5 or 6. **DIFFSIM** will run without this but will not produce a plot, which is very useful to have as visual repord.

Installation packages and instructions for *gmt* can be found at:

[www.generic-mapping-tools.org/download/](https://www.generic-mapping-tools.org/download/)

---
### INPUT and OUTPUT

#### Input

Three files are required, both with UNIX-style line breaks. They are:

1. `diffsim_history.in`  which specifies the geologic thermal history for the model run. The first line gives the number of successive pairs of time-temperature information, followed by time (m.y.) and temperature (˚C) pairs. The final pair needs to specify the temperature today (0 m.y.).

2. `diffsim.in` specifies all the parameters for the model run. The table of input data is followed by table of rather terse text strings that serve as a reminder as to what the parameters are. Here is just a bit more information about each parameter:

	- grid nodes: number of grid nodes (essentially, the radius in cm)(MIN 50 MAX 1,000)
	- geo time nodes: number of time nodes (MIN 10 MAX 100,001)
	- atom production rate per time step (MIN 1 MAX 100,000)
	- D<sub>o</sub>: in cm<sup>2</sup>/s &nbsp;*Important note:* if you are modeling a particular sample's kinetics, you need to scale D<sub>o</sub> appropriately given the **DIFFSIM** convention that the number of nodes is the radius in cm! E.g., for a 100-micron-radius Durango grain, D<sub>o</sub>/a<sup>2</sup> is 5e5. Thus, if you run a 100-node model, which is 100 cm, we'd need a D<sub>o</sub> of 5.0e9 to result in D<sub>o</sub>/a<sup2</sup> of 5e5. *Further*, if you then want to change the number of nodes in the model you need to rescale your D<sub>o</sub> to keep D<sub>o</sub>/a<sup>2</sup> constant. For the example just discussed, here is how D<sub>o</sub> would have to be changed for different node values:   50 1.25e9;  100 5.0e9;  200 20e9; 500 125e9; 1000 500e9
	- Ea: is activation energy in cal/mol
	- number of trap types: number of different *types* of sink types (MAX 10)
	- traps E-trap Scale-temp: number of individual trap, sink-escape activation energy in cal, escape-probability scale temperature in ˚C.  E.g. 500  33000  1200 (repeat this line to match number of sink types)
	- lab start temp: start temperature for CRH heating (˚C) e.g. 200.
	- lab heating range: temperature span for CRH heating (˚C) starting with... start temperature, e.g. 900.
	- lab heating rate in degrees per second: CRH heating rate, .5 ˚C/min is the standard Lehigh heating rate we have used for most experiments
	- integration time in seconds: e.g. 10; this controls how many data are generated across the lab heating phase
	- Ft: (possible range is 0.4 to 0.999). If you want your modeled sample to experience alpha ejection, you have to supply an Ft value, since the **DIFFSIM** convention that radius is in cm and that D<sub>o</sub> is scaled accordingly makes it impossible to calculate an Ft value. **DIFFSIM** calculates a stopping distance to use when modeling alpha ejectios using the supplied Ft value.

3. `plot_diffsim.sh` is a shell script called by **DIFFSIM** to plot results. A copy must be in the working directory where the two input files are.

#### Output

**DIFFSIM** reports a summary of the run to the console and this is captured by the run-summary file `model_history.txt` along with other complete info about the run. The outplot plot will be named just `diffsim-results.pdf`. The files `geological_history.txt` and `lab_history_CRH.txt` are tab-delimited text files that track model behavior as a function of time. The first reports the time increment, model time, current temperature, total atoms produced, total atoms lost, and diffusion jumps for that time step. The second file reports more extensive info: incremental time, total time, temperature, dF per C, fractional loss, incremental lab loss, jumps for the time step, reciprocal T, lnD/a2, temperature, fractional loss, and step duration in minutes. There are some duplicates in these columns to make certain plots easier.

---
### USAGE

`./diffsim`

*NOTE: You can place place a copy of your `diffsim` executable in any directory in your PATH, and then do work with this code in any other directory that contains input data (i.e., you won't need to drag around copies of the executable). In this case, in any directory containing your input files, USAGE becomes simply:*

`diffsim`


---
### HELP and EXAMPLES

**DIFFSIM** is a work in progress. Our plans are to incorporate the code in a publication probably some time in late 2023 or 2024. We may also submit an overview paper to a preprint server before then. You can learn a little more about the code from the file `diffsim_background.pdf`.

There is an EXAMPLES directory including several subdirectories containing sample input and output.

---
