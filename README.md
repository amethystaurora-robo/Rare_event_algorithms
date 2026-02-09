

# Introduction
## Gottwald model

A coupled model of the simplified ocean from Stommel and the Lorenz-84 atmosphere. 
<p>
See more:
</p><p>
  <a href="https://github.com/amethystaurora-robo/simulation_practice" target="_blank">
    <img src="https://img.shields.io/badge/Gottwald%20Model-3366BB??style=for-the-badge&logo=github&logoColor=white"/>
  </a>
</p>

## Rare Event Algorithm

An algorithm applied to a distribution of values with rare events, a, at the tail of the distribution-enabling the distribution to shift and increase the number of rare events, making the events common.

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/importance_sampling.png" width="500">
</p><p>
  (F. Ragone, 2025)
</p>
<p>
The algorithm used is a cloning algorithm, where trajectories are given weights based on how likely they are to lead to a rare event. Trajectories with weights close to zero are killed, and trajectories with higher weights are cloned, as in the schematic below.
</p>
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/rare_trajecs.png" width="500">
</p>

# Methods

1. A control run of the Gottwald model is initiated with initial conditions leading to the stable attractor. The time series and distribution of AMOC indices is shown below:
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/timeseries_controlrun.png" width="500">
</p>
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/control_hist.png" width="500">
</p>

2. A rare event, a, is at the tail of the distribution, 30 standard deviations below the mean. Return times of all values of a are computed and shown below:
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/returntimes_control.png" width="500">
</p>

3. The auto-correlation function is determined by finding correlations between an AMOC index and itself at lag. This shows the system memory over time, and is used to determine resampling time for the rare event algorithm, by computing the integral auto-correlation time. 
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/acfunc_control.png" width="500">
</p>

4. The integral auto-correlation time, tau, is used to compute k, which determines the shift of the distribution at stationarity
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/k_equation.png" width="500">
</p><p>
  (F. Ragone, 2025)
</p>
  
5. From the control run, initial conditions T,S,x,y,z are taken 5 time units apart, resulting in 200 trajectories with unique initial conditions. At the end of the first time block, the trajectories are put into the rare event algorithm, which computes the weight for each trajectory using the equation below:
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/weights.png" width="500">
</p>
6. Trajectories with weights near zero are killed and those with higher weights are cloned proportionally to their weight (keeping the same
   number of trajectories overall). The killed trajectories take on the values of the cloned ones, with slight perturbations, the simulation is run again, and the process repeats. The weighting is applied to state variables AMOC Index, Salinity, and Temperature separately to allow for transitions based on each state variable. 

# Results

The anomaly which was targeted in these experiments was 30 standard deviations below the mean. This allowed rare event sampling applied to one state variable to push the system into the alternate system state. Below is the equation used to find the value of k, the parameter which shifts the distribution of the ensemble.

k = a/(τ/σ²)

where a is the anomaly targeted and τ is the integral auto-correlation time. 

| State Variable | Anomaly Target | Resulting *k* Value | *k* Values to Try               |
|----------------|----------------|---------------------|---------------------------------|
| AMOC           | μ − 30σ         | -1744               | Try: -500, -1000, -2000          |
| Salinity       | μ − 30σ         | 8282                | Try: 7000, 8000, 9000            |
| Temperature    | μ − 30σ         | -46866              | Try: -40000, -50000, -60000      |

Rare event sampling applied to the state variables results in a shift of the distribution of values of state variables, as shown below, for AMOC. 

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/hist_shifted_values.png" width="500"
</p>

Following the table above, simulations were run to target the off state of the AMOC by applying rare event sampling to each of the three state variables described. Each of the simulations was attempted with ensembles of 200 trajectories and 1000 trajectories. Below is a systematic comparison of the resulting time series, colored by the "ancestors" spawning the shifted trajectories:

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/AMOC_shifts/temp_lineplot_40year_200traj_-500k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/AMOC_shifts/temp_lineplot_40year_200traj_-1000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/AMOC_shifts/temp_lineplot_40year_200traj_-2000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/AMOC_shifts/amoc_lineplot_40year_1000traj_-500k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/AMOC_shifts/temp_lineplot_40year_1000traj_-1000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/AMOC_shifts/temp_lineplot_40year_1000traj_-2000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/salinity_shifts/salinity_lineplot_40year_200traj_7000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/salinity_shifts/salinity_lineplot_40year_200traj_8000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/salinity_shifts/salinity_lineplot_40year_200traj_9000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/salinity_shifts/salinity_lineplot_40year_1000traj_7000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/salinity_shifts/salinity_lineplot_40year_1000traj_8000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/salinity_shifts/salinity_lineplot_40year_1000traj_9000k.png" width="400">
</p>

When applying the rare event sampling to temperature, no transitions were observed for either of the two ensemble sizes or the three values of k selected. Below it is shown that when rare event sampling is applied to the salinity state variable and the system shifts to the AMOC off state, temperature transitions along with salinity to a higher value rather than a lower one, as expected. Temperature is shown below in green, and salinity in purple. 

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/salinity_shifts/salinity_temperature_lineplot_40year_200traj_7000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/salinity_shifts/salinity_temperature_lineplot_40year_200traj_8000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/salinity_shifts/salinity_temperature_lineplot_40year_200traj_9000k.png" width="400">
</p>

This result confirms that both an increase in temperature and in salinity are pushing the AMOC into the off state, rather than an increase in salinity and decrease in temperature. Once the transitions have occurred, the system ought to be able to return to the on state by applying rare event sampling in the opposite direction. However, equal forcing in the opposite direction (30 standard deviations above the mean) did not result in transitions back to the on state for either the AMOC or salinity, regardless of either ensemble size chosen. (Fix this, is it actually 30 standard deviations above the off state mean ???) Will check more, running simulations now.

It was interesting to observe whether the transition has an instanton - a least unlikely path between states. Below the transition paths of state variables are compared when rare event sampling is applied to AMOC and to salinity. It is clear that they take the same path. The first plot below compares on and off states as they transition through time. The second plot denotes possible attractors by using a time cutoff using the line graphs for both transition states. The plots are shown side by side to compare the transition pathways. The presence of an instanton can help with early warning indication, as a trajectory headed towards the instanton path is likely headed towards collapse, given the exponential likelihood that the state variables will make a transition to a collapsed state along the instanton. The next step of analysis in this study is to compare the distribution of pathways that trajectories take during the simulations. The instanton should be exponentially more likely than other paths in trajectories that transition to the off state. 

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/transition_2D_time.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/transition_2D_state.png" width="400">
</p>



The trajectory path is again computed from the off state to the on state, shown below. 


Working on coomparable plots of salinity and AMOC (and temperature) with target values of 30 standard deviations above/below mean. Then do importance sampling analysis. Can make agg label plots for all, edit plots so salinitya and AMOC are side-by-side, instanton are side by side. Change titles, make graphs, finish up presentation and GitHub, then can do poster.

# References:

1. (F. Ragone, personal communication, 2025)

2. Ragone F, Bouchet F. Computation of extreme values of time averaged observables in climate models with large deviation techniques. J Stat Phys. 2020;179(5-6):1637-65. doi:10.1007/s10955-019-02429-7.

3. Mehling, O., Börner, R. and Lucarini, V., 2024. Limits to predictability of the asymptotic state of the Atlantic Meridional Overturning Circulation in a conceptual climate model. Physica D: Nonlinear Phenomena, 459, p.134043.
