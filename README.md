# Overview
Analysis code to generate figures and artifacts presented in Chapman, et al., “On Optimizing the Conditional Value-at-Risk of a Maximum Cost for Risk-Averse Safety Analysis,” submitted to IEEE Transactions on Automatic Control, December 2021.

## Dependencies
### Computational Environment
Running the code in this repository requires a recent version of Matlab. We have tested this repository using [__Matlab 2019b__](https://www.mathworks.com/products/matlab.html) on Windows 10 and Red Hat Enterprise Linux Server release 7.8.

## Setup Instructions

### Download a Copy of this Repository
Using [git](https://git-scm.com/) is the easiest way to download a copy of all the files you need to get up and running in Matlab. We tested these instructions against git v2.8.2.396. 

The files will be downloaded to a folder named __RSSAVSA-2021__. 

From a command line interface, navigate to the directory where you would like to download __RSSAVSA-2021__. 

Then execute the following command: 
```
git clone https://github.com/risk-sensitive-reachability/RSSAVSA-2021
```

![git clone animation](https://raw.githubusercontent.com/risk-sensitive-reachability/RSSAVSA-2021/main/misc/git-clone.gif)

### Setup Your Matlab Workspace
To setup your Matlab workspace: 
 - navigate to the parent directory containing __RSSAVSA-2021__
 - from the left-hand file tree, right click on __RSSAVSA-2021__ and select __Add To Path > Selected Folders and Subfolders__.
 
 ![setup workspace animation](https://raw.githubusercontent.com/risk-sensitive-reachability/RSSAVSA-2021/main/misc/add-to-working-path.gif)

### Defining Designs to Test
You can define designs to test by specifying a scenario (e.g., specify system dynamics) and a simulator configuration (e.g., details about gridding and precision, etc.). These are specified in `configuration_options/get_scenario.m` and `configuration_options/get_config.m`, respectively. These files already contain all the scenarios and configurations used in this paper, so there is no need to modify these files if you merely want to reproduce the original results. 

### Run All Designs
The `Run_All_Designs` method is a provided as a convenience that calls Run_Bellman_Recursion and `Run_Outer_Optimization` (where applicable) for each specific design presented in the paper. There is no need to call `Run_Bellman_Recursion` or `Run_Outer_Optimization` directly if you merely wish to reproduce the results presented in this paper. 

 ![run all designs](https://raw.githubusercontent.com/risk-sensitive-reachability/RSSAVSA-2021/main/misc/run-all-designs.gif)


#### Parallel Pool & Checkpointing
This code makes use of a parallel pool to speed up computation times. Please specify the pool size that reflects the resources you want to dedicate to these computations versus other tasks on your machine. 

The parallel pool size can be specified by calling `parpool('local', n)` prior to `Run_All_Designs` where n indicates the number of workers. Please see [parpool documentation](https://www.mathworks.com/help/parallel-computing/parpool.html) for more details.

Running all designs will take several days. Computations are checkpointed where possible and will pick up from the last checkpoint if you shutdown Matlab and later resume the computations by calling `Run_All_Designs` again. The console will print out 'Stage N complete' along with a timestamp each time the file is checkpointed. 
 
#### Run Bellman Recursion
The first step in the analysis of a particular configuration and scenario combination is to call `Run_Bellman_Recursion`. The first argument is a string identifying the scenario to run and the second argument is an integer identifying the simulator configuration to use. See the computational prerequisites column in the list of figures at the bottom of this page for examples. Note that running this will create files in the {Matlab_Working_Directory}/staging/ directory. These include both 'checkpoint' files that save results periodically and 'complete' files that are created once the entire recursion is finished. 

#### Run Outer Optimization
The exact method via state-augmentation requires an outer optimization step for each scenario of interest. The `Run_Outer_Optimization` step takes a scenarioID, a range of configuration IDs, and a set of alphas to use in this outer optimization. For more details, please see the notes in the `Run_Outer_Optimization` method. 

__Note:__ This method creates a file {Matlab_Working_Directory}/staging/outer_optimization.mat to store the outputs (which span multiple configurations). It will skip this step if the file already exists, so an existing outer_optimization.mat file should be deleted if you want to run a new outer optimization.

### Visualize Results
After all required inner and outer optimizations have been run, the next step is to visualize the results. 

#### Plot Disturbance Profile
The `Plot_Disturbance_Pofile` method takes only a single string argument identifying the scenario. It then plots the probability distribution associated with the disturbances for that scenario. It also prints the empirical mean, variance, standard deviation, and skew to the console. We only use one disturbance profile in this paper, which can be plotted by specifying any of the scenarios. e.g.,
`
Plot_Disturbance_Profile('WRSA0')
`

#### Generate Figure
`Generate_Figure` is a convenience function for generating the Figures 2, 4, 5, and 6 shown in this paper and takes the Figure number as a single numeric argument (e.g., `Generate_Figure(2)`).  This method creates corresponding `.png` and `.fig` files in the {Matlab_Working_Directory}/staging/figures/ directory.

| Figure | Command             | Computational Prerequisites         |
|--------|---------------------|-------------------------------------|
| 2      | Generate_Figure(2); | none                                |
| 4      | Generate_Figure(4); | Run_All_Designs();                  |
| 5      | Generate_Figure(5); | Run_All_Designs();                  |
| 6      | Generate_Figure(6); | Run_All_Designs();                  |

#### Generate Table
`Generate_Table` is a convenience function for generating Table IV from this paper and takes the Table number as a single numeric argument (i.e., `Generate_Table(4)`). The table will be printed to the console. 

| Table  | Command             | Computational Prerequisites         |
|--------|---------------------|-------------------------------------|
| 4      | Generate_Table(4); | Run_All_Designs();                  |
