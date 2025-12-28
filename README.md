

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

2. A rare event, a, is at the tail of the distribution, 3 standard deviations below the mean. Return times of all values of a are computed and shown below:
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
   number of trajectories overall). The killed trajectories take on the values of the cloned ones, with slight perturbations, the simulation is run again, and the process repeats. 

# Results

Below, 200 trajectories are simulated for 16 or 20 years (denoted in the plot titles). With k<-200, transitions occur around year 10. Trajectories are tracked to confirm the original ancestor of final shifted trajectories. Below, degeneracy of trajectories are shown: all shifted trajectories evolve from one common ancestor. This problem may be solved by initializing more trajectories.
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/lineplot_20year_200traj_-3000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/lineplot_20year_200traj_-2000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/lineplot_20year_200traj_-1000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/lineplot_20year_200traj_-500k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/barcode_20year_200traj_-3000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/barcode_20year_200traj_-2000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/barcode_20year_200traj_-1000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/barcode_20year_200traj_-500k.png" width="400">
</p>

I confirm that Importance Sampling has worked because my distribution of values after the transition has changed.

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/hist_shifted_values.png" width="500"
</p>

After observing transitions to the AMOC off state, it was interesting to investigate whether changing k=0 (effectively turning off the Rare Event algorithm) would allow for spontaneous transitions back to the on state. Below, it is shown that this did not occur in a 100-year simulation.

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/lineplot_22year_200traj_0k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/barcode_22year_200traj_0k.png" width="400">
</p>

It was also interesting to investigate whether transitions would be observed when forcing only Temperature or Salinity with the Rare Event algorithm. Using the bifurcation diagram below, k was calculated to target the AMOC off state for both temperature and salinity, with values of 0.55 and 0.4, respectively.

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/bifurcation.png" width="500">
</p>
(Mehling et al, 2024)

For salinity, a k value of 3000 is calculated using the targeted value. After some trial and error, a transition between two states was observed with a k value of 5000.

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/lineplot_30year_200traj_5000k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/barcode_30year_200traj_5000k.png" width="400">
</p>

An attempt was made to solve degeneracy of trajectories by increasing the number of trajectories used in the rare event algorithm from 200 to 1000. Below, it is shown that with the same values of k, degeneracy is still a problem for both AMOC and salinity.

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/amoc_lineplot_20year_1000traj_0k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/amoc_barcode_20year_1000traj_0k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/salinity_lineplot_20year_1000traj_0k.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/plots/salinity_barcode_20year_1000traj_0k.png" width="400">
</p>

It was interesting to observe whether the transition has an instanton - a least unlikely path between states. Below the transition paths between salinity on/off and AMOC on/off are compared. It is clear that they take the same path. The first plot below compares on and off states as they transition through time. The second plot denotes possible attractors by using a time cutoff using the line graphs for both transition states. 

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/transition_2D_time.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/transition_2D_state.png" width="400">
</p>

Since the final aim of this project is to develop Early Warning Systems, it was also interesting to investigate whether common Early Warning Signals, including an increase of variance of state variables and an increase in auto-correlation were observed prior to the transition paths for both forcing in AMOC and salinity. 

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/acfunc_Salinity RE Forcing_Salinity.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/acfunc_AMOC RE Forcing_Salinity.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/variance_Salinity RE Forcing_Temperature.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/variance_AMOC RE Forcing_Salinity.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/variance_Salinity RE Forcing_Salinity.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/variance_AMOC RE Forcing_Temperature.png" width="400">
</p>

Above an increase of variance can be observed for both temperature and salinity prior to transitions in both systems forced with RE sampling. An increase of auto-correlation was not observed for either transition. Atmospheric variables x,y, and z showed fluctuations and no clear trend for either auto-correlation or variance. 

The final aim of this study is to observe transitions back to the on state by turning the RE algorithm back on with opposite forcing (changing the sign of k). In both cases, transitions are observed back to the on state in the time series shown below. 

The trajectory path is again computed from the off state to the on state, shown below. 

# References:

1. (F. Ragone, personal communication, 2025)

2. Ragone F, Bouchet F. Computation of extreme values of time averaged observables in climate models with large deviation techniques. J Stat Phys. 2020;179(5-6):1637-65. doi:10.1007/s10955-019-02429-7.

3. Mehling, O., Börner, R. and Lucarini, V., 2024. Limits to predictability of the asymptotic state of the Atlantic Meridional Overturning Circulation in a conceptual climate model. Physica D: Nonlinear Phenomena, 459, p.134043.
