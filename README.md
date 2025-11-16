# Gottwald Model

A coupled model of the simplified ocean from Stommel and the Lorenz-84 atmosphere. 
<p>
See more:
</p><p>
  <a href="https://github.com/amethystaurora-robo/simulation_practice/blob/main/" target="_blank">
    <img src="https://img.shields.io/badge/Gottwald%20Model-3366BB?style=for-the-badge&logo=adobeacrobatreader&logoColor=white"/>
  </a>
</p>

# Rare Event Algorithm

An algorithm applied to a distribution of values with rare events, a, at the tail of the distribution-enabling the distribution to shift and increase the number of rare events, making the events common.

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/importance_sampling.png" width="500">
</p>
(F. Ragone, 2025)

The algorithm used is a cloning algorithm, where trajectories are given weights based on how likely they are to lead to a rare event. Trajectories with weights close to zero are killed, and trajectories with higher weights are cloned, as in the schematic below.

<p>
  <img src="https://github.com/amethystaurora-robo/Rare_event_algorithms/blob/main/rare_trajecs.png" width="500">
</p>

# Steps

1. A control run of the Gottwald model is initiated.
2. The auto-correlation function is computed, by finding correlations between each value of the AMOC from the Gottwald model to each other value
3. The integral auto-correlation is computed, to determine the system memory and resampling time.
4. This time is used to split the run into blocks, and resample at the end of each block.

5. Runs are begun with 6-7 different initial conditions.
6. At the end of the first time block, the trajectories are put into the rare event algorithm, which computes the weight for each trajectory (its likelihood to generate a rare event),
   by using an exponentially increasing time average multiplied by the integral autocorrelation time and divided by a weighting parameter.
7. After the weights have been computed, trajectories with weights near zero are killed and those with higher weights are cloned proportionally to their weight (keeping the same
   number of trajectories overall). The killed trajectories take on the values of the cloned ones, with slight perturbations, the simulation is run again, and the process repeats.

To check that the algorithm has worked, I examine the PDFs of the old trajectories and the new trajectories after applying the algorithm.

References:

(F. Ragone, personal communication, 2025)
