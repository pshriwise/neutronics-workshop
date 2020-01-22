# Fusion Neutronics workshop with OpenMC
A selection of resources for learning OpenMC with particular focus on simulations relevant to fusion energy.

There are a few [slides](https://slides.com/openmc_workshop/neutronics_workshop) that introduce the workshop and show the expected outputs of each task.

The use of OpenMC for neutronics analysis requires several software packages and nuclear data. These have been installed inside a Docker container.
**It is recommended that this workshop be completed using the Docker container.**

The majority of the workshop can also be completed using Google Colab Notebooks which do not require Docker. The links to these notebooks are provided below. (Note - not all tasks can be completed in Colab as it lacks some required dependencies).

## Docker Container Installation

### Linux (Recommended)

1. Install Docker CE for [linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/), including the part where you enable docker use as a non-root user.

2. Pull the docker image from the store by typing the following command in a terminal window.

    ```docker pull openmcworkshop/openmc_nndc_workshop```

3. Now that you have the docker image you can enable graphics linking between your os and docker, and then run the docker container by typing the following commands in a terminal window.

    ```xhost local:root```

    ```docker run --net=host -it --rm -v /tmp/.X11-unix:/tmp/.X11-unix  -v $PWD:/my_openmc_workshop -e DISPLAY=unix$DISPLAY --privileged openmcworkshop/openmc_nndc_workshop```

**Permission Denied Error**
- If a permission denied error is returned when running docker commands, add ```sudo``` to the front of the command. This runs the command with administrative priviledges. You may be required to enter your password.

### Mac

1. Install Docker Desktop for [mac](https://store.docker.com/editions/community/docker-ce-desktop-mac).

2. Ensure Docker Desktop is running and pull the docker image from the store by typing the following command in a terminal window.

    ```docker pull openmcworkshop/openmc_nndc_workshop```

2. Install [XQuartz Version: 2.7.8](https://www.xquartz.org/releases/XQuartz-2.7.8.html) which is a visualization system for mac allowing you to run GUI applications. Restart your computer once install has completed - this updates your DISPLAY environment variable to point to XQuartz.app rather than X11.app (the previous visualization system for mac).

3. Open XQuartz using the command ```open -a XQuartz``` in a terminal window. Go to Preferences -> Security and select *Allow connections from network clients*. Close the XQuartz application to implement the selection.

4. Add your local machine network IP address to a variable called IP. You can use the commands:

    ```IP=$(ipconfig getifadd en0)``` or ```IP=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')```

5. Add your IP address to xhost. XQuartz should launch when this command is run (unless application is already open).

    ```xhost + $IP```

6. Run the docker container using the command:

    ```docker run --net=host -it --rm -v /tmp/.X11-unix:/tmp/.X11-unix  -v $PWD:/my_openmc_workshop -e DISPLAY=$IP:0 --privileged openmcworkshop/openmc_nndc_workshop```

### Windows (not yet implemented)

1. Install Docker Desktop for [windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows).

2. REST OF INSTRUCTIONS TO COME


Running the docker image should load up an Ubuntu 18.04 Docker container with OpenMC, Python3, Paraview, nuclear data and other libraries.

You can quickly test the graphics options worked by typing ```paraview``` in the container environment. This should open the paraview program.

Running the docker image places you in the ```/openmc_workshop``` directory which contains all of the files required to complete the workshop.

The docker container also contains a folder called ```/my_openmc_workshop``` which is mapped to the local directory from which you ran the image. Placing files into this directory allows you to tranfer files from your docker container to your local machine.

**IMPORTANT:** Any changes you make to scripts in the docker container will be lost when you exit the container. Make sure you copy any files you want to keep into the ```my_openmc_workshop``` folder before exiting the container. **Note:** The output files created by the task scripts are automatically copied to this folder.

## Core workshop tasks

- [Task 1 - Cross section plotting - 25 minutes](#task1)
- [Task 2 - Building and visualizing the model geometry - 25 minutes](#task2)
- [Task 3 - Visualizing neutron tracks - 20 minutes](#task3)
- [Task 4 - Finding neutron interactions with mesh tallies - 15 minutes](#task4)
- [Task 5 - Finding the neutron and photon spectra - 15 minutes](#task5)
- [Task 6 - Finding the tritium production - 15 minutes](#task6)
- [Task 7 - Finding the neutron damage and stochastic volume calculation - 15 minutes](#task7)

## Optional workshop tasks

- [Task 8 - Survey breeder blanket designs for tritium production - 25 minutes](#task8)
- [Task 9 - Optimize a breeder blanket for tritium production - 25 minutes](#task9)
- [Task 10 - Using CAD geometry - 30 minutes](#task10)

&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp;

## <a name="task1"></a>Task 1 - Cross section plotting

Google Colab Link: [Task_1](https://colab.research.google.com/drive/1Z5C7bxX-1iPjBfhDrgIzGVaTyfI2CdFa)

Please allow 25 minutes for this task.

Expected outputs from this task are also in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/13).

Knowing the interaction probabilities of isotopes and materials in your model can help you understand simulation results. There are several online tools for plotting nuclear cross sections such as [www.xsplot.com](http://xsplot.com), however, OpenMC is also able to plot cross sections.

From inside the docker container navigate to the task_1 directory and open the first example python script using the commands below.

```cd tasks/task_1```

```coder 1_example_isotope_plot.py```

This script plots the cross sections of certain reactions for a selection of isotopes. OpenMC is well documented so if the script does not make sense take a look at the relevant [documentation](https://openmc.readthedocs.io/en/v0.10.0/examples/nuclear-data.html).

Run the script using the following command.

```python 1_example_isotope_plot.py```

You should see a plot of the n,2n cross sections for isotopes of lead and beryllium, as shown below.

<p align="center"><img src="tasks/task_1/images/1_example_isotope_plot.png" height="500"></p>

To add different reactions to the plot we need their ENDF reaction numbers (MT numbers) which are available [here](https://www.oecd-nea.org/dbdata/data/manual-endf/endf102_MT.pdf).

- Try adding the other lead isotopes to the plot (Pb207 and Pb208).

- Try adding tritium production (n,Xt) in Li6 and Li7 to the same plot.

The plot should now be similar to the plot below, showing fusion relevant interactions (change scale to log log). These are important reactions for breeder blankets as they offer high probability of neutron multiplication and tritium production.

<p align="center"><img src="tasks/task_1/images/1_example_isotope_plot_2.png" height="500"></p>

- Try editing ```1_example_isotope_plot.py``` so that it plots tritium production or neutron multiplication for all stable isotopes.

Elemental properties can also be found with OpenMC. 

- Try to understand the example code ```coder 2_example_element_plot.py```

- Try plotting neutron multiplication using the ```python 2_example_element_plot.py``` script.

This should produce a plot similar to the plot shown below.

<p align="center"><img src="tasks/task_1/images/2_example_element_plot_16.png" height="500"></p>

-  Try changing the ```2_example_element_plot.py``` script so that it plots tritium production for all elements. Once produced you can change the axis scale (to log log) using the dropdown menu.

The tritium production should produce a plot similar to the plot shown below.

<p align="center"><img src="tasks/task_1/images/2_example_element_plot_205.png" height="500"></p>

A nice feature of OpenMC is that it can plot cross sections for complete materials made from combinations of isotopes and elements. The ```3_example_material_plot.py``` script shows how to plot tritium production in Li4SiO4 which is a candidate ceramic breeder blanket material.

Materials are created in OpenMC by combining isotopes and elements either by 'atom percent' or 'weight percent'.
- Atom percent (percent_type='ao') - Proportion of total number of atoms in material of a particular type.
- Weight percent (percent_type='wo') - Proportion of material of a particular isotope or element by weight.

- Try running the script and producing the material cross section plot ```python 3_example_material_plot.py```

The plot produced should look similar to the plot shown below. As you can see lithium enrichment only increases tritium production at lower neutron energies.

<p align="center"><img src="tasks/task_1/images/3_example_material_plot.png" height="500"></p>

 - Try editing the script so that other candidate breeder materials are added to the plot. ```coder 3_example_material_plot.py```

**Learning Outcomes**

- How OpenMC can be used to plot cross-sectional data for a variety of fusion-relevant interactions, e.g. (n,2n), (n,Xt). 
- Reaction probabilities vary for each isotope depending on the energy of the neutron. 
- Cross sections can be plotted for specific isotopes, elements and full materials. 
- Beryllium 9 has the lowest threshold energy for neutron multiplication reactions. 
- Lithium 6 has the highest probability of producing tritium at low neutron energies.
- Understand that lithium enrichment increases tritium production from low energy neutrons.

&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp; 

## <a name="task2"></a>Task 2 - Building and visualizing the model geometry.

Google Colab Link: [Task_2](https://colab.research.google.com/drive/17o94Go2_pQLHrrkcM_2K-asvKrSsMbtx)

Please allow 25 minutes for this task.

Expected outputs from this task are also in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/14).

OpenMC can provide both 2D and 3D visualizations of the Constructive Solid Geometry ([CSG](https://en.wikipedia.org/wiki/Constructive_solid_geometry)) of a model.

There are two methods for producing 2D slice views of model geometries. This can be done via a Python Matplotlib (```1_example_geometry_viewer_2d.py```) or via the production of xml files, again with Python (```2_example_geometry_viewer_2d_xml_version.py```). The first option is simpler to understand and use while the second option is slightly faster for complex geometries, we will use the simpler option in this workshop.

- Try understanding the example code ```coder 1_example_geometry_viewer_2d.py```

- Try running the example code ```python 1_example_geometry_viewer_2d.py```

Views of the model geometry from XY, YZ and XZ planes should appear one after the other.

<p align="center"><img src="tasks/task_2/images/xy_sphere.png" height="210"></p>

As the geometry is a spherical shell centred at the origin, its views in each plane are identical.

Edit the script and try adding a first wall and centre column to the model using the OpenMC [simple examples](https://openmc.readthedocs.io/en/stable/examples/pincell.html#Defining-Geometry) and the [documentation](https://openmc.readthedocs.io/en/stable/usersguide/geometry.html) for CSG operations.

- Try adding a 20cm thick first wall to the hollow sphere.

- Try assigning the eurofer material to the first wall - specification provided below. (Eurofer density = 7.75 g/cm3)

<p align="center">
<img src="tasks/task_2/images/eurofer_table.png"> 
</p>

- Try adding a centre column using a [ZCylinder](https://openmc.readthedocs.io/en/stable/pythonapi/generated/openmc.ZCylinder.html) surface with a 100cm radius. This must also be cut at the top and bottom by the firstwall sphere surface.

- Try creating a material from pure copper and assign it to the centre column.

- Colour the geometry plots by material - see the [documentation](https://openmc.readthedocs.io/en/stable/usersguide/plots.html) for an example.

By the time you have added the extra components, your geometry should look similar to the geometry contained within the next example script.

```coder 3_example_geometry_viewer_2d_tokamak.py```

```python 3_example_geometry_viewer_2d_tokamak.py```

Run this script to produce views of the tokamak model from different planes, as shown below, and compare these to the geometry produced by your edited script.

<img src="tasks/task_2/images/xy_tokamak.png" height="210"> <img src="tasks/task_2/images/xz_tokamak.png" height="210"> <img src="tasks/task_2/images/yz_tokamak.png" height="210">
<p align="center"><i>Left = XY plane, Middle = XZ plane, Right = YZ plane</i></p>

The next script shows how a simple geometry can be viewed in 3D using paraview. This converts the geometry into a block.

```coder 4_example_geometry_viewer_3d_tokamak.py```

```python 4_example_geometry_viewer_3d_tokamak.py```

Paraview should load up when the script completes, however, no geometry will be visible. Watch the video below to learn how to view geometry in Paraview.

<p align="center"><a href="http://www.youtube.com/watch?feature=player_embedded&v=VWjQ-iHcaxA
" target="_blank"><img src="tasks/task_2/images/task2thumbnail.png" height="400" /></a></p>

**Instructions:** To make the geometry visible click the "Apply" button and also the small eyeball icon on the left hand side. Then select "id" and "surface" in the dropdown menus to view the geometry. The threshold and slice operations can then be used to view specific parts of the geometry. (Instructions with screenshots are also provided in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/14/1)).

- Try using the threshold operation to remove the vacuum cell. Set the threshold to 0 then click the "Apply" button.

OpenMC has a plotter which can also be used for viewing 3D geometry. This has been pre-installed in the Docker image. To use the OpenMC plotter it must be run from the folder containing the xml files which are created by the Python API. The OpenMC plotter repository has [more details](https://github.com/openmc-dev/plotter) on how to use the application.

- Try running the OpenMC plotter using the ```openmc-plotter``` command and visualize the geometry.

**Learning Outcomes**

- Construction of simple Constructive Solid Geometry (CSG) geometry.
- Visualisation of models using 2D slices.
- Visualisation of models using 3D cube geometry.

&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp; 

## <a name="task3"></a>Task 3 - Visualizing neutron tracks

Google Colab Link: [Task_3](https://colab.research.google.com/drive/1kOFp9s3utX0o2D7llXXJ6pyyrvK_V-Nz)

Please allow 20 minutes for this task.

Expected outputs from this task are also in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/15).

When OpenMC runs, a statepoint (output) file is produced which contains information about the neutron source, tally results and additional information. This task focuses on extracting neutron source information from the statepoint file, while tasks 4, 5 and 6 focus on extracting other information from the statepoint file.

The ```1_plot_neutron_birth_energy.py``` file shows you how to access the statepoint file created during the simulation. In this example the birth energy of all the simulated neutrons is extracted. A plot of the energy distribution can be produced by running the script.

```python 1_plot_neutron_birth_energy.py```

The script will produce a plot of a mono-energetic energy source of 14 MeV neutrons, as shown below.

<p align="center"><img src="tasks/task_3/images/particle_energy_histogram.png" height="500"></p>

There are actually three different energy distributions available in the ```1_plot_neutron_birth_energy.py``` script (14 MeV monenergetic, Watt fission distribution, Muir fusion distribution).

- Try plotting the Watt and Muir spectra and compare them to the mono-energetic neutron source.

- Try changing the Muir plasma temperature from 20 KeV to 40 KeV.

In the next example the initial neutron birth locations and neutron trajectories for a very basic neutron point source are plotted. Again, this information is accessed from the statepoint file.

- Try running ```python 2_plot_neutron_birth_location.py``` to produce a plot of neutron birth  locations, the output should look similar to the plot shown below.

- Try running ```python 3_plot_neutron_birth_direction.py``` to produce a plot of neutron directions, the output should look similar to the plot shown below.

<p align="center"><img src="tasks/task_3/images/particle_location.png" height="300"> <img src="tasks/task_3/images/particle_direction.png" height="300"></p>

<p align="center"><i>Left = Neutron birth locations, Right = Neutron initial directions</i></p>

Now open the next example source plotting script ```4_plot_neutron_birth_locations_plasma.py```. Look for the part in the script where the source is defined - you should notice that an external source library is used. The ```source_sampling.so``` file is a precompiled plasma source file containing neutron positions, energies and directions for a given plasma source. This file is in the task_3 directory.

- Try running ```python 4_plot_neutron_birth_location_plasma.py``` to produce a plot of neutron birth locations for a more realistic plasma source. The output should look similar to the plot shown below.

- Try running ```python 5_plot_neutron_birth_direction_plasma.py``` to produce a plot of birth neutron directions for a more realistic plasma source. The output should look similar to the plot shown below.

<p align="center"><img src="tasks/task_3/images/plasma_particle_location.png" height="300"> <img src="tasks/task_3/images/plasma_particle_direction.png" height="300"></p>

<p align="center"><i>Left = Neutron birth locations, Right = Neutron initial directions</i></p>

OpenMC is also able to track particles as they pass through model geometries. Open the ```6_example_neutron_tracks.py``` script and notice that it contains ```model.run(tracks=True)```. This argument results in the creation of a h5 file for each neutron simulated which contains particle track information. 

The next example script defines a model of a hollow sphere made of two materials and a 14 MeV point source at the geometry centre.

- Try running ```python 6_example_neutron_tracks.py``` which simulates neutron movement through the geometry and produces particle h5 files from which neutron tracks can be visualized with the geometry.

**Instructions:** Watch the video below to learn how to load the geometry file, open the track files and slice the geometry such that the neutron tracks can be visualised. (Instructions with screenshots can also be found in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/15/4)).

<p align="center"><a href="http://www.youtube.com/watch?feature=player_embedded&v=uHTXw6Dza-Y
" target="_blank"><img src="tasks/task_3/images/task3thumbnail.png" height="400" /></a></p>

- Looking at the tracks can you tell which material is water and which is zirconium?

**Learning Outcomes**

- How to access information on the particle positions, energy and direction from the simulation.
- How to visualize particle tracks through the geometry.

&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp; 

## <a name="task4"></a>Task 4 - Finding neutron interactions with mesh tallies

Google Colab link: [Task_4](https://colab.research.google.com/drive/1TVgCaEU_GAnJziNuyDFEvDfFYLU-fQaJ)

Please allow 15 minutes for this task.

Expected outputs from this task are also in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/16).

OpenMC uses 'tallies' to measure parameters such as particle flux, absorption and other interactions, to obtain useful information from the simulation. Tallies can be recorded on model cells, surfaces or on a superimposed mesh. In this task mesh tallies will be produced and visualized.  The example script contains a simple hollow sphere geometry of a single material, a 14 MeV point source.

- Try opening the example script ```coder 1_example_neutron_flux.py```and identify the mesh tally which measures neutron flux. 

- Try running the example script ```python 1_example_neutron_flux.py```

You should see plots of the simple sphere geometry and the isotropic point source, as shown below. The colour map shows the neutron flux, as tallied by the mesh, which is seen to reduce as one moves away from the point source.

<img src="tasks/task_4/images/universe_point.png" height="300"> <img src="tasks/task_4/images/flux_point.png" height="300">

<p align="center"><i>Left = Geometry Plot, Right = Flux Plot</i></p>

- Try changing the "flux" tally to an "absorption" tally and re-run the simulation.

The next example script is the ```2_example_neutron_flux_tokamak.py``` file which measures tritium production on a mesh in a simple tokamak geometry. 

The model still has a point source but now it is located at x=150 y=150 z=0. The tritium production mesh tally is now 3D and is displayed in 3D using paraview.

- Try running the script with the following command ```python 2_example_neutron_flux_tokamak.py``` and use the log scale within Paraview to show the tritium production more clearly. Watch the video below to learn how to do this.

<p align="center"><a href="http://www.youtube.com/watch?feature=player_embedded&v=be3G3ceQSWU
" target="_blank"><img src="tasks/task_4/images/task4thumbnail.png" height="400" /></a></p>

Further instructions can also be found in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop#/16/1).

- Try changing the mesh tally from (n,Xt) to absorption to see the impact of the center column.

This should produce a 3D view of the mesh tally similar to the plots shown below.

<p align="center"><img src="tasks/task_4/images/tritium_production_tokamak.png" height="300">   <img src="tasks/task_4/images/absorption_on_mesh.png" height="300"></p>

<p align="center"><i>Left = Tritium production, Right = Neutron absorption</i></p>

OpenMC has a plotter which was first introduced in task 2 to view geometry can also be used for viewing mesh tallies. 

- Try running the OpenMC plotter using the ```openmc-plotter``` command and visualize the geometry.

- Try visualizing the mesh tally within the openmc-plotter by using the menus. Select data -> open statepoint and then select the "tally_on_mesh" tally within the tally dock.

**Learning Outcomes**

- How mesh tallies can be used in neutronics simulations to measure a variety of different reactions such as neutron absorption, tritium production and flux. 
- How neutrons are dissipated around the reactor.

&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp; 

## <a name="task5"></a>Task 5 - Finding the neutron and photon spectra

Google Colab Link: [Task_5](https://colab.research.google.com/drive/1piuEmG09E9kfkFTw2WZV6TdX_xovqmVj)

Please allow 15 minutes for this task.

Expected outputs from this task are in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/17).

In this task the neutron spectra at two different locations will be measured and visualized. OpenMC has several energy group structures such as VITAMIN-J-175 and [others](https://github.com/openmc-dev/openmc/blob/develop/openmc/mgxs/__init__.py) built in which makes the energy grid easy to define.

- Try opening the ```1_example_neutron_spectra_tokamak.py``` script to see how the neutron spectra is obtained for the breeder blanket cell.

- Try running the example script which plot the neutron spectra within the breeder blanket. ```python 1_example_neutron_spectra_tokamak.py```, the plot should look similar to the plot shown below.

<p align="center"><img src="tasks/task_5/images/1_example_neutron_spectra_tokamak.png" height="500"></p>

- Try adding the neutron spectra within the first wall cell to the same plot and compare it to the breeder blanket cell. Why might they be different?

The task now starts to look at secondary photons created from neutron interactions. These photons are often created in neutron scattering interactions where the nucleus is left excited and de-excites via photon production. To run OpenMC in coupled neutron, photon mode an additional setting is required to enable photon transport (which is disabled by default).

- Try opening the example script ```coder 2_example_photon_spectra_tokamak.py``` script to see how the photon spectra is obtained for the breeder blanket cell and photon transport is enabled.

- Try running the script to plot the photon spectra within the breeder blanket. ```python 2_example_photon_spectra_tokamak.py```. The output should look similar to the plot shown below.

Why do you think the photons generated are of lower energy?

<p align="center"><img src="tasks/task_5/images/2_example_photon_spectra_tokamak.png" height="500"></p>

**Learning Outcomes**

- Plotting neutron / photon spectra and observing the changing neutron energy at different locations in the reactor.
- Performing coupled neutron photon simulations where photons are created from neutron interactions.


&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp; 

## <a name="task6"></a>Task 6 - Finding the tritium production

Google Colab Link: [Task_6](https://colab.research.google.com/drive/188lPNZP_3clN1kC-nlJgI4HBMaSXKu5t)

Please allow 15 minutes for this task.

Expected outputs from this task are in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/18).

In this task you will find the tritium breeding ratio (TBR) for a single tokamak model using the ```1_example_tritium_production.py``` script. You will then find TBR values for several tokamak models with a range of different Li6 enrichment values using the ```1_example_tritium_production_study.py``` script.

- Try opening the example scripts and understanding how the TBR is found ```coder 1_example_tritium_production.py```

- Try running the example script and finding the TBR ```python 1_example_tritium_production.py```

You should see that TBR is printed along with its associated error. As you can see the error is large.

- Try increasing the number of ```batches``` to 10 and ```sett.particles``` to 500 and re-run the simulation. You should observe an improved estimate of TBR with better statsitical uncertainty. 

- Try changing '(n,Xt)' to '205' and you should get the same result as this is the equivalent [ENDF MT reaction number](https://www.oecd-nea.org/dbdata/data/manual-endf/endf102_MT.pdf) for tritium production.

There remains uncertainty in the nuclear interaction data and elsewhere but the statisitcal uncertainty can be decreased with more computing.

Your should find that the TBR value obtained from the simulation is below 1.0 so this design will not be self sufficient in fuel.

One option for increasing the TBR is to increase the Li6 content within the blanket. Open and run the next script and see how TBR changes as the Li6 enrichment is increased.

- Try opening and understanding how the next script changes the lithium 6 enrichment```coder 2_example_tritium_production_study.py```

- Try running the example script and observing the plot produced ```python 2_example_tritium_production_study.py```

The script should produce a plot of TBR as a function of Li6 enrichment, as shown below.

<p align="center"><img src="tasks/task_6/images/tbr_study.png" height="500"></p>

**Learning Outcomes**

- Finding TBR with OpenMC.
- Introduction to MT reaction numbers. e.g. (n,Xt) = 205.
- Simple methods of increasing the TBR using lithium enrichment.
- Improving the uncertainty on the result is possible with more computation.

&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp; 

## <a name="task7"></a>Task 7 - Finding the neutron damage and stochastic volume calculation

Google Colab Link: [Task_7](https://colab.research.google.com/drive/1wH1Y4I2UHewk2BS6DQpkMGBuLHwtGg6B)

Please allow 15 minutes for this task.

Expected outputs from this task are in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/19).

Displacements per atom (DPA) is one measure of damage within materials exposed to neutron irradiation. Damage energy can be tallied in OpenMC with MT reaction number 444 and DPA can be estimated.

In the case of DPA a neutronics code alone can't fully calculate the value as material science techniques are needed to account for the material and recombination effects. For example after a displacement there is a chance that the atom relocates to it's original latic position (recombination) and different atoms require different amounts of energy to [displace](https://fispact.ukaea.uk/wiki/Output_interpretation#DPA_and_KERMA). The DPA tally from neutronics is therefore only an estimate of the DPA.

The MT 444 / damage energy tally is in units of eV per source particle. Therefore the result needs scaling by the source intensity (in neutrons per second) and the irradiation duration (in seconds) and the number of atoms in the volume.


- Try to understand the post proccessing steps involved in converting a neutronics damage tally into displacements by reading the relevant section at the end of the example script ```coder 1_find_dpa.py```

- Find the total number of displacements in the firstwall by running the example script ```python 1_find_dpa.py```. This script assume a threshold displacement energy of 40eV is required and that 20% of the displaced atoms recombine.

- Open the example script and see how a stochastic volume calculation can be performed using OpenMC ```coder 2_find_cell_volume.py```

- Find the volume and number of iron atoms in the firstwall using the python script ```python 2_find_cell_volume.py```

- Calculate the displacements per atoms for a full power year by using the outputs of both scripts

- Using this information find the DPA on the first wall for the 3GW (fusion energy) reactor over a 5 year period. Does this exceed the Eurofer DPA limit of 70 DPA? If so what could be changed about the design to ensure this limit is now reached?

**Learning Outcomes**

- Finding damage energy deposited with the OpenMC MT 444 tally
- Find the volume of a cell using the stochastic volume method
- Perform post tally calculations to convert the neutronics numbers into something more useful
- Gain an appreciation of how neutronics results can influence the design (e.g. radius of reactor must be increased to prevent critical material damage)


&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp; 

## <a name="task8"></a>Task 8 - Survey breeder blanket designs for tritium production

Google Colab Link: [Task_8](https://colab.research.google.com/drive/1fDOBm2YMojXVtucPQQ9XSjFqtzMibvjD)

Please allow 25 minutes for this task.

Expected outputs from this task are in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop/#/20).

This task is more open ended - the aim is to find the minimum thickness of breeder material needed to obtain a TBR of 1.2. The scripts will need changing to make the serach more efficient as currently it just selects random points.

There are several candidate breeder materials including a lithium ceramic (Li4SiO4), Flibe, Lithium lead (eutectic) and pure lithium. Each material can have it's Li6 content enriched and the blanket thickness varied and these have an impact on the TBR.

Examine the ```simulate_tokamak_model.py``` file and try to understand how the model is created and particularly how the simulation parameters are saved in a json file with a unique ID.

- Currently the input parameters for Li6 enrichment and blanket thickness are randomly sampled so you might want to change this to speed up the search.

Run the script to perform simulations. There are two scripts to help you analyse the results.

- ```plot_simulation_results_2d.py``` will allow you to see the impact of changing either the Li6 enrichment or the blanket thickness on TBR.

- ```plot_simulation_results_3d.py``` will allow you to see the combined impact of changing the Li6 enrichment and the blanket thickness on TBR.

Ultimately you should come up with the minimum thickness needed for each candidate blanket material and the Li6 enrichment required at that thickness. Feel free to share simulation data with other groups and interpolate between the data points.

For 200 simulations, the 2D plots should look similar to the plots below.

<p align="center"><img src="tasks/task_8/images/TBR_vs_enrichment_fraction.png" height="500"></p>

<p align="center"><img src="tasks/task_8/images/TBR_vs_thickness.png" height="500"></p>

For 525 simulations, the 3D plots should look similar to the example plot shown below.

<p align="center"><img src="tasks/task_8/images/TBR_vs_thickness_vs_enrichment_fraction_lithium.png" height="500"></p>

**Learning Outcomes**

- A simple parameter study that makes use of unique ID's for each simulation.
- Some candiate breeder materials can meet the TBR requirment with a thinner blanket.
- Increasing the thickness of blanket or lthium 6 enrichment tend to increase the TBR but not for all materials.

&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp; 

## <a name="task9"></a>Task 9 - Optimize a breeder blanket for tritium production

Google Colab Link: [Task_9](https://colab.research.google.com/drive/1Zak3lrQH6x2-As1vKskXtNmYs6mdRUgj)

Please allow 25 minutes for this task.

Expected outputs from this task are in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop#/21).

The previous task sampled from the available parameters and used a brute force method of finding the optimal blanket composition. This task uses Gaussian processing to home in on the optimal solution and steer the sampling.

Open the following script which describes functions for constructing materials for use in simulations.

- ```material_maker_functions.py```

This task uses a [Gaussian process tool](https://github.com/C-bowman/inference_tools/blob/master/inference/gp_tools.py) developed by Chris Bowman to guide the simulations performed and optimize breeder blanket parameters for maximum TBR.

Take a look at the scripts below and try to understand how this works. Also try to understand how they use the material maker script to construct the model.

- ```lithium_enrichment_optimisation.py```

- ```lithium_enrichment_and_thickness_optimisation.py```. Note - This script does not currently work.

Initially, simulations are performed by sampling the parameter space of interest (according to the halton sequence) and the results fitted using Gaussian Regression. A further simulation is then performed using the parameters corresponding to max TBR as determined by the Gaussian fit. The simulation results are then fitted again, including this new point, and the process repeated. 

This iterative approach efficiently and accurately determines the point in the parameter space where TBR is maximum.

The output .gif shows how halton sampling is used to perform initial simulations before further simulations are informed by Gaussian interpolation.

<p align="center"><img src="tasks/task_9/images/output.gif" height="500"></p>

**Learning Outcomes**

- Halton sampling allows non-biased simulations to be performed over a parameter space.
- Data fitting can be used to optimise neutronics simulations.

&ensp; 
<p align="center"><b>
------------------------------------------------------------------------------------------
</b></p>
&ensp; 

## <a name="task10"></a>Task 10 - Using CAD geometry

Google Colab Link: [Task_10](https://drive.google.com/open?id=1EM5xd9yC4JariHRQ2ftzY2v_HRHoTp9u)

Please allow 30 minutes for this task.

Expected outputs from this task are in the [presentation](https://slides.com/openmc_workshop/neutronics_workshop#/22).

Constructive solid geometry (CSG) has been used in all the previous tasks. This task demonstrates the use of CAD geometry usage within openmc.

The use of CAD geometry has several advantages compared to CSG.

- Geometry containing complex shapes including spline curves can be modeled. This is essential for some fusion reactor designs (e.g. stellerators). 

- Geometry created outside of neutronics (e.g. design offices) is created in a CAD format. Using the CAD geometry directly avoids the manual process of converting CAD geometry into CSG geometry which is a bottleneck in the design cycle.

- Simulation reults such as heating can be mapped directly into engineering models which also use CAD geometry. This is benifitial when integrating neutronics into the design cycle or coupling neutronics with thermal analysis.

- Visulisation of neutronics models can be performed with CAD software which is mature and feature rich.

This taks depends on [DAGMC](https://svalinn.github.io/DAGMC/) and [FreeCAD](https://www.freecadweb.org/) both of which are installed on this docker image. 

The geometry can be viewed in FreeCAD. Open up FreeCAD by typing ```freecad``` in the command line.

Once loaded select file open and select blanket.stp, firstwall.stp and poloidal_magnets.stp. This should show the 3D model within the FreeCAD viewer. Watch the video tutorial below to learn how to do this.

<p align="center"><a href="http://www.youtube.com/watch?feature=player_embedded&v=pyZXQg0AsJ4
" target="_blank"><img src="tasks/task_10/images/task10thumbnail.png" height="400" /></a></p>

If you have Trelis or Cubit installed (they can't be included on this Docker image) then try creating the DAGMC neutronics geometry using the command ```trelis make_faceted_geometry_with_materials```.

The trelis / cubit script will load up the stp files and combine them to create a faceted geometry that can be used in neutronics simulations. Feel free to explore the script and the coresponding json config file.

The next step is to open the OpenMC python script with the command ```coder example_CAD_simulation.py```.

Read throught the script and try to spot the differences between a CSG and CAD simulation script. You might notice that the materials are defined in the script but not assigned to volumes and that no geometry is made in the script. Also the settings object has an additional dagmc property

The material assignment is not required as this is perfomed when combining the stp files within the Trelis step. Trelis produces the dagmc.h5m file which contains geometry and each geometry is taged with a material name. These material names must be defined in the openmc script by it is not nessecary to assign them as this is taken care of by DAGMC.

Try running the script using the command ```python example_CAD_simulation.py```. This will run the simulation using the CAD geometry and produce the output results.

**Learning Outcomes**

- CAD geometry can be used to build complex models for use in neutronics simulations.

&ensp; 

## Acknowledgments
Fred Thomas for providing examples from previous years Serpent workshop,
Enrique Miralles Dolz for providing the CSG tokamak model, Andrew Davis for his work on the fusion neutron source, Chris Bowman for his Gaussian process software, John Billingsley for the CoLab tasks and the OpenMC team for their software.
