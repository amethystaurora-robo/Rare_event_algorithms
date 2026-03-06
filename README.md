# Introduction

## Climate Tipping Points

Many climate systems exhibit multi-stability, meaning their dynamics can shift between multiple equilibrium states. In the diagram below showing a potential landscape of a 1D system, the two wells represent the system equilibrium states. The ball is the current state of the system, and it can pass between the two equilibria by passing the tipping point in different ways.
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/pic5.png" width="500" >
</p>

A few examples of climate systems which have undergone these critical transitions in the past are:
* the Earth from a warm state to a 'Snowball Earth' (Lucarini et al, 2017)
* The greening of the Sahara Desert (Pausata et al, 2020)
* ice-covered Arctic to ice-free Arctic (Jahn et al, 2024)

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/pic6.png" width="600">
</p>

This project is looking at the Atlantic Meridional Overturning Circulation (AMOC), which is a current in the Atlantic ocean that carries warm, salty water northward, where it cools, expands and sinks, circulating back southward in the lower branch of this 'conveyor belt'. The transport of heat provided by the AMOC is largely what keeps European climate mild. In a weakened or collapsed AMOC state (its alternate stable state), the average temperature of the Northern Hemisphere would be lower, and the Inter-tropical Convergence Zone (ITCZ) and equatorial rain belt may shift southward. (Wunderling et al, 2021)

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/pic8.png" width="500">
</p>

This has implications, termed 'cascading tipping points', where it has been posited that the following sequence of events can happen:
1. Greenland + the West Antarctic Ice Sheet shift into their alternate regime - an ice free state.
2. The influx of freshwater into the Atlantic weakens or collapses the AMOC into its alternate state
3. The collapse of the AMOC shifts the equatorial rainbelt southward, causing drying of the Amazon rainforest and shifting it into a more savanna-like state. (Wunderling et al, 2021)

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/pic7.png" width="500">
</p>

## Models

This project uses climate models to simulate the dynamics of the AMOC. 

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/pic9.png" width="500">
</p>

The 2-box Stommel oceanic model assumes that the ocean is split into a North and South box, each with state variables temperature and salinity. The differences of temperature and salinity control the flow, and can effectively simulate a simplified AMOC. (Stommel et al 1993)

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/pic10.png" width="500">
</p>

The Lorenz atmospheric model combines three state variables to give a deterministic system with chaotic dynamics. (Lorenz, 1963)

The coupling of these two models into the Gottwald model, allows for the creation of a fast-slow system, where the fast and chaotic dynamics of the atmosphere act as noise on the slower, more resilient, ocean. (Gottwald, 2017)
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/pic11.png" width="500">
</p>
A series of these little kicks in exactly the right order may be able to push the state of the system to a new equilibrium.
Unlike transitions from external forcing (like freshwater forcing mentioned above in the melting of Greenland and West Antarctic ice sheets), noise-induced transitions occur from a system's own internal variability. However, these noise-induced transitions occur only very rarely, so a rare event algorithm is applied to sample more of these rare events.

## Rare Event Algorithm

These rare events exist in the tail of the distribution of state variables under normal dynamics. The rare event algorithm shifts the distribution of the events, allowing the rare event to become more common.

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

1. A control run of the Gottwald model is initiated with initial conditions leading to the stable attractor. The time series is shown below:
<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/timeseries_controlrun.png" width="500">
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

The anomaly which was targeted in these experiments was 30 standard deviations away from the mean. This allowed rare event sampling applied to one state variable to push the system into the alternate system state. Below is the equation used to find the value of k, the parameter which shifts the distribution of the ensemble.

k = a/(τ/σ²)

where a is the anomaly targeted and τ is the integral auto-correlation time. 

| State Variable | Anomaly Target | Resulting *k* Value | *k* Values to Try               |
|----------------|----------------|---------------------|---------------------------------|
| AMOC           | μ − 30σ         | -1744               | Try: -500, -1000, -2000          |
| Salinity       | μ + 30σ         | 8282                | Try: 7000, 8000, 9000            |
| Temperature    | μ − 30σ         | -46866              | Try: -40000, -50000, -60000      |

Rare event sampling applied to the state variables results in a shift of the distribution of values of state variables, as shown below, for AMOC. 

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/hist_shifted_values.png" width="500">
</p>

Following the table above, a rare event algorithm was applied to each of the three state variables described in ensembles of simulated trajectories targetting the AMOC off state. Above is a comparison between simulations with 200 and 1000 ensemble members. Below is a systematic comparison of the resulting time series, colored by the "ancestors" spawning the shifted trajectories:

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

This result confirms that transitions occur in systems where salinity increases, and where temperature increases, at least initially. Once the transitions have occurred, the system ought to be able to return to the on state by applying rare event sampling in the opposite direction. However, equal forcing in the opposite direction (30 standard deviations above the mean) did not result in transitions back to the on state for either the AMOC or salinity, regardless of ensemble size chosen (200 or 1000 trajectories), and length of simulation (testing was done for 40 years and 100 years after reaching an equilibrium state.) This merits further investigation into the potential landscape of the system.

The final aim of this experiment was to develop an early warning indicator for transitions in this system. In the case of noise-induced transitions, there are many paths to collapse. However, there may exist an instanton, a transition path which is exponentially more likely than the others. Below, the path of transition with the rare event algorithm applied to AMOC and applied to salinity is shown, overlapping. This hints at the existence of an instanton in this model, but a mathematical proof would be required to verify this result.

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/transition_2D_time.png" width="400">
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/figures/transition_2D_state.png" width="400">
</p>

# References:

1. Lucarini, V. and Bódai, T., 2017. Edge states in the climate system: exploring global instabilities and critical transitions. Nonlinearity, 30(7), pp.R32-R66.
2. Pausata, F.S., Gaetani, M., Messori, G., Berg, A., de Souza, D.M., Sage, R.F. and DeMenocal, P.B., 2020. The greening of the Sahara: Past changes and future implications. One Earth, 2(3), pp.235-250.
3. Jahn, A., Holland, M.M. and Kay, J.E., 2024. Projections of an ice-free Arctic Ocean. Nature Reviews Earth & Environment, 5(3), pp.164-176.
4. Wunderling, N., Donges, J.F., Kurths, J. and Winkelmann, R., 2021. Interacting tipping elements increase risk of climate domino effects under global warming. Earth System Dynamics, 12(2), pp.601-619.
5. Stommel, H.M. and Young, W.R., 1993. The average T–S relation of a stochastically forced box model. Journal of physical oceanography, 23(1), pp.151-158.
6. Lorenz, E.N., 2017. Deterministic nonperiodic flow 1. In Universality in Chaos, 2nd edition (pp. 367-378). Routledge.
7. Gottwald, G.A., 2015. Model reduction for networks of coupled oscillators. Chaos: An Interdisciplinary Journal of Nonlinear Science, 25(5).
8. (F. Ragone, personal communication, 2025)
9. Ragone F, Bouchet F. Computation of extreme values of time averaged observables in climate models with large deviation techniques. J Stat Phys. 2020;179(5-6):1637-65. doi:10.1007/s10955-019-02429-7.
10.  Mehling, O., Börner, R. and Lucarini, V., 2024. Limits to predictability of the asymptotic state of the Atlantic Meridional Overturning Circulation in a conceptual climate model. Physica D: Nonlinear Phenomena, 459, p.134043.
