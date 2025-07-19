# PROJECT ACTIVITY LOG

Author: Gabin Rousseau

This file serves to track my daily activities throughout the MSc project associated with the repository, should be helpful for the dissertation write-up!
The log is arranged in a weekly basis with a list of key goals/achievements when I feel like updating them, followed by daily entries that describe what I did.

## Week 0 | 20/05 - 23/05
Goals: 

- [X] Prepare version-controlled work environment for the project.
 
#### Tuesday 20/05
- Had first meeting with Linus. About proposal: highlighted that some extra explanatory figures adapted from the key references would have been a really positive addition; overall positive.
- Office space: set up a personal user session on a desktop in the Bioinformatics office. However, the desk space itself is used until end of June, is there no free space that will be consistently available, so it is important to make sure I can comfortably work remotely.
- Joined the group GitHub (here).
- Communication: Teams for general use, email for big things that need to be traced.
- Somer _et al._ code: no updates, still trying to get an reply. Assumption to not have access to it at all: the goal will be to start reproducing the method from scratch and attempt to replicate results for simulated data.
- Meetings: sent a regular calendar invite for Tuesdays at 1pm.
- Installed conda on user session (added to PATH), set up a remote connection via SSH (I am using MobaXTerm to do this on my Windows 11 laptop).
- Github: created repository, created fine-grained token (pending), will use it to clone the repository on my session when approved.

#### Wednesday 21/05

- GitHub token approved, repository cloned to the workstation, tested token with first commit.
- Git push with token succesfull, then tested pulling.
- Pull succesfull, version-control environment in place and ready for use.
- Adjusted bashrc proxy to work with conda (http address in the https setting). Created a fresh conda environment, 'osdr', for the project from an environment.yml file (that will be updated with exports as needed). Environment features Python 3.12.2 to start with.
- Data: device mounted on the workstation. fstab edited so that it can remain mounted every reboot. Soft link to the main table 'cell_table_bothneighbourhoods.csv' created in a data directory adjacent to my repository clone.
- Installed jupyter lab if required on the workstation. Succesfully ran workstation's jupyterlab remotely [(requires linking ports)](https://towardsdatascience.com/access-remote-code-in-a-breeze-with-jupyterlab-via-ssh-8c6a9ffaaa8c/).

#### Thursday 22/05
- Prepared VS Code to interact with the workstation through the Remote - SSH extension. Should be compatible with Jupyter and Quarto for notebooks and dissertation writing. To do so, I had to prepare ssh key autentication to remove the need to log in with a password every time, and add a local .ssh/config file that VS code can recognise for easy connection. VS Code needs to install VSC Server on the remote machine (in my user home dir) to work, although there isn't enough space on the disk to complete installation. I notified Linus to help as sudo priviledges are needed to check what can be safely removed, hopefully that will lead to a good solution.

#### Friday 23/05
- Old user data cleaned, VS Code Server installed succesfully (this entry is written _via_ its file explorer).
- Installed quarto, used it to render a placeholder notebook that I will probably use for some notes or presentations. (And will write the dissertation following a similar format). Got to load the data on a data exploration notebook, the symbolic link works fine.
- Worked on designing an initial implementation plan, keeping in mind the first goal is to repeat the validation by rescuing simulated data results described in Figure S2 of Somer _et al._.

## Week 1 | 26/05 - 30/05
Goals: 

- [ ] Program an ODE to phase portrait component to work with cell circuit models.
- [ ] Program the data simulation component for OSDR evaluation.

#### Monday 26/05
- Supervisor meeting: Discussed how the authors approached their method validation step. Authors should provide some information with how ki67 relates to the final sampling step described under "Validating OSDR by simulating known dynamical models". It's not an absolute necessity for me to use the same known dynamical models as Somer _et al._, since the key aspect of the validation stage is to see if OSDR can estimate expected dynamics (assuming the data used for this estimation is sensibly biased towards the expected dynamics.) The authors reference Figs S2C-G, but really the figures to look at for the step start at S2G, esp. S2H. I will start with the validation step (up to the data simulation part). The cellcircuits repository is a good starting point to help me work with setting a known model (see analysis.py in cellcircuits) and plotting a phase portrait for it (plotting.py). The general workflow will be to formulate the known reference with ODEs (integrate with _e.g._ scipy odent), then compute fixed points and plot nullclines (known model only, this won't apply to the estimated dynamics for simulated data).

#### Tuesday 27/05 - Friday 30/05
- Isolated key functions from cellcircuits for phase portrait plotting. Used as is to test plotting succesfully. Require a rework to have a ground truth model that can imitate figure S2H.
- Coded a function to generate a random tissue with a distribution of two cell types, computing neighbourhood data and storing results in a dataframe. Seaborn scatterplot and kdeplot to show cell distribution and neighbourhood heatmap.
--> See validation_simulations.ipynb

## Week 2 | 02/06 - 06/06
Goals:
- [X] Program an ODE to phase portrait component to work with cell circuit models.
- [X] Program the data simulation component for OSDR evaluation.

#### Monday 02/06
- Read up further into types of ODEs and ODE solving, which made the approach to the ground truth clearer.

#### Tuesday 03/06
- Supervisor meeting: discussed cellcircuits code, my bedrock code for random tissues and how I can formulate my ODEs to get something SIMILAR to fig. S2H. I was thinking initially about a simple linear equation with dX/dt = k*[M] - c, which could work, but the rate would essentially be a generalistic proliferation rate, which would have to be negative since the density goes down when the tissue is overpopulated. It would be more sensible to actually follow the neighbourhood dynamics equation dX/dt = [X](p^+([X]) - p^-([X])), where p+ can be a function of X, and p- a constant for simplicity (in any case, that remains a second order equation which will make the parameter choice a little more complex). There was an interesting bit of discussion about comparing my KDE neighbourhood plot with the cell-specific distribution mabs found in S2H and below. although the phrasing is different, it seems my interpretation was correct, but I would need to create two separate plots for both cell types.

#### Wednesday 04/06
- Looked into the ODE I can sensibly use given the trends and steady states we expect. Went for a simple autocatalysis-like logistic equation (as discussed yesterday) with dX/dt=aX - bX**2. _A priori_ aiming for a steady state of X=10000, we must have a=10000b. Played around with ChatGPT, which estimated with parameter fitting using this equation format and the S2H figure (assuming the divion-death rates correspond to dX/dt, which is probably wrong...) that the optimal solution didn't make sense (negative a) for this equation format.
- 3 figures for neighbourhood plotting: one with superposed neighbourhood profiles for both cell types (M/F), or separately. ![Neighbourhood profiles of a random uniform tissue prior to stochastic profileration.](https://github.com/Schumacher-group/OSDR_TNBC/blob/main/logfigs/firstneighbourhoods.png)

#### Thursday 05/06
- Changed ODE definition with the new assumption that S2H plots refer to cell density as a log2 of the neighbour cell counts. Added parameter fitting code (using scipy.optimize.curve_fit) co-written by ChatGPT-4o that aims to get the best a and b values constrained to a SS of 16 (log2(16)=4), with the division-death rate substraction matching S2H (assuming this value is a-bX). Started adjusting the phase portrait code for this ODE, should be complete tomorrow.
- Results of the parameter fitting suggest that my ODE format really can't reproduce the exact same progression of proliferation rates, although there is some additional difficulty in figuring out what that means in a mathematical sense for now. If my assumption is correct, then my isn't suitable for a 1:1 reproduction of S2H, but it definitely can produce the same general trends (stable SS at 16 cells, decrease in overpopulation, increase in underpopulation).

#### Friday 06/06
- Interacted with ELM (ChatGPT-4o) to adapt from the cellcircuits code a simplified phase portrait plot for the known model. I'm using fsolve to get nullclines given the steady-state expectation I already known, and also to confirm expected fixed points (including unstable ones). I'm using Harvard's autograd library to decide whether an FP is stable or not based on the real component of the Jacobian's eigenvalues (if all negative: stable). There is an issue in reproducing the S2H figure though, and that is linked to the log2 nature of my axes. I am getting streamlines over a log2 space (with some tweaking because plt.streamplot only takes linearly distributed points.) which means that covering the 0 axes or negatives is troublesome: I can't properly show 0 on a log scale, only approach it, is that what Somer et al do? With the streamlines at 0 behaving as expected close to 0, their portraits might not use a log scale. The question for me is: am I right in designing the ODE relative to the number of cells and then effectively plotting things over a log2 scale, or would it best to think about things in terms of cell density, which DOES correspond to the log2 of the cell number? I fear this just confuses things though, but if it's an worthwhile adaptation, that means this is a variable that needs to be tracked in the random sim component.

## Week 3 | 09/06 - 13/06
Goals:
- [X] Toy with the ODEs and see if we can better explain S2H by including for example a Hill function.
- [X] Parallelize simulation and lengthen to observe a good comparison to the S2H neighbourhood distribution figures.
- [X] Begin model inference, focus on getting regressions for the simulated tissue and get a stochastic model equation that can contribute to the inferred phase portrait (Fig2).

#### Monday 09/06
- Stochastic proliferation written, accompanied by plots. The function takes a tissue dataframe, a steps (n) argument and optionally a time argument that can modify the rates for faster proliferation over time (try to get results without though).

#### Tuesday 10/06
- Supervisor meeting: discussed progress, agreed that based on the curve fitting and the phase portrait the equation doesn't quite fit. There's probably a division component (eg Hill, see goals). With my 200 steps, it isn't enough to really showcase the validity of my simulating code, to be extended. I can now start setting up model inference.
- Careful with the log2 scale plots (that use log-transformed data), the KDE is applied on log data, so all interpretations are log-scale specific!

#### Wednesday 11/06

- Reworked fixed point stability determination to include semi-stability (no jacobian eigenvalues > 0, but not all strictly < 0.) Didn't change current phase portrait.
- Used Pool().starmap to enable parallelisation of what was first in a loop through tissue df row ids. Now adapted to a cell_action function that is fed to starmap. Performance gain seems to be about 30% (from 1.30it/s to 1.70it/s). After n=3000 steps, I am getting results that diverge a bit from expectations with two peaks and neighbourhoods that are too enriched.

#### Thursday 12/06
- Compared starting densities close and far from steady state, all reached steady state after n=3000. Somer et al choice of n: "comparable to real data", so a bit off. I can use our data for inspiration and once I have a method for model inference, see how n affects accuracy of model estimation.

#### Friday 13/06
- Tried curve fitting on different version of the model taking inspiration from adler without growth factors (which is close to makes removal a constant independent of X so...) and an X-dependent logistic regression type term (exponential decay) but results weren't very conclusive. I should probably first focus on model estimation to pinpoint what's the best way to define the model in the phase portrait specifically.

## Week 4 | 16/06 - 20/06
Goals:
- [ ] Refactor notebook to import functions rather than have blocks and blocks of definitions. --> wait until component is done to fully refactor and avoid losing time with kernel loading.
- [X] Finish a correct run of 100 tissue sim and sample as in Somer for a first attempt at model inference.
- [X] Explore the model 2^(-aX) - b for curve fitting/phase portrait.

#### Monday 16/06
- Reworked parallelisation with master and child seeds ahead of a starmap instance (and reworked to function to include a seed argument passed to a number generator). Used this to make a function to parallelise multi-tissue proliferation. Tested on 10 tissues with n=10, running now for 100 tissues with n=1000. Output will be two dfs, one for the starting randomised tissues (between 250 and 9750 cells for each type) and another ("post" df) for the post-proliferation data. Is taking a while, if I run this again I should find a way to better track progress although multiprocessing complicates this (used to tqdm for loops). Found an error with p_death where F cells were using the M cells rates, now corrected. Just need to rerun the code.


#### Tuesday 17/06
- 100 tissue simulation finished after 4.33 hours. Created some plots to show cell number per tissue and average neighbourhood densities per cell type per tissue (both bar plots, one stacked, one grouped).
- Supervisor meeting: look for a "screen" method to go back and forth the session run remotely without a need to be constantly online, maybe staying off a notebook will help. Clean up notebook with imports. Could try using a 2d scatterplot that uses different point sizes. Model inference, get the k-cells sample described in Somer et al, calculate p_div/death, record events (but don't act on them), regression between cell densities and events. Try a simpler exponential decay like 2^(-aX) -b. Focus on model inference.

#### Wednesday 18/06
- Finished correct 100 tissue sim, worked out how to clean up notebook by writing function to modules and importing those. Updated environment.yml to reflect all current requirements.

#### Thursday 19/06
- Explored model options with curve fitting (through scipy.optimize.minimize) trying to leverage 2** or log2, and log2 scaled deaths lead to the best results for sensible rate values, but that creates issues with 0. I have stored some key plots but for now I will focus on X(a-bX) model I already have a phase portrait for to test model estimation, now that I have a full set of simulated data generated properly.

#### Friday 20/06
- cell_sampler() takes in a tissue dataset (post-proliferation) and samples 50k cells with replacement. From this sample, 4 child samples are drawn without replacement (respectively), leading to 4 cell datasets from various tissues (with neighbourhood values from their original tissues) with 1k, 5k, 10k, and 25k cells as in Somer et al.

## Week 6 | 23/06 - 27/06
Goals:
- [X] Apply model inference to the k-samples from last week.
- [X] Prepare progress presentation and attend lightning talks.

#### Monday 23/06
- Applied model inference following the algorithm described, where p+ is the logistic function taking z as w0 + w1\*X1 + w2*X2..., and p- the mean of division observations. These "division observations are first recorded by running the same rng to determine whether a cell divides, dies or persists as in tissue proliferation (the event is simply recorded as 1 if division is determined, otherwise 0, could be -1 if we want to go from binary labels to categorical). Inference method then consists in using scikitlearn's LogisticRegression to fit a cell type-specific logistic regression model on cell neighbourhoods (normally on all cell type neighbourhood columns available, but first tried with the same column as the row's cell type) as features, and division as label. Extract intercept and coefficients (negative coef means with more cells of corresponding type in the neighbourhood, a cell of the fitted type is less likely to divide).
- Results looked promising, with sensible p+ and p- values at expected steady-state, but need phase-portrait observations to properly evaluate. F cells were fit with F neighbourhoods, M cells with M neighbourhoods (for simplicity given our expectations, but generall)

#### Tuesday to Friday 24/06 - 27/06
- Presentation. Feedback about simulation figures: rather than the bar plots, it would be better to adjust simulations to record states over a certain t range and plot some key statistics over that time. Comment said explaining what the logistic regression means can be useful. No questions from students, waiting for feedback from programme director.

## Week 6 | 30/06 - 04/07
Goals:
- [ ] Make phase portraits for inferred models.
- [ ] Confirm that the root cause for inappropriate inferred parameters is due to the late SS-like distributions I worked with at first.

#### Monday 30/06
- Plotted streamlines for the 4 models inferred. Realised that there can actually be a big issue with positive coefficients being inferred, completely twisting the expected relationships between division probability and neighbourhood densities. Only the 10k sample's model had a negative coef for both the F cell ODE and the M cell ODE, leading to a reasonable streamline portrait. The rest, highlighted the reversed dynamics due to positive coefs.

#### Tuesday 01/07
- Supervisor meeting: to add fixed points, refer to the bottom of figure 2. It might be clever to do repeat samples from the same 50k parent sample (or compare across different t stopping points), rather than run 10 different simulations or so. Streamlines are from one model only in their plots. Fixed points by using fsolve on the ODE system function checking over a wide lattice of points? And using jacobian. I need to check whether the models were prone to have wrong coef signs because the data is spread too closely around the steady state, which impairs the ability to really infer dynamics outside of the steady state. Can do this first by plotting p+ over X from the simulations and plot sigma(z) as a curve over that, if points are too clustered it could help visualise this hypothesized issue. Then, add t step recording in the simulations to try inference on models that proliferated for a shorter time, thus further away from steady state.
- Beyond that, I should familiarise myself with the BC data, look at ki-67 and see if I can replicate S1B for example.

#### Wednesday 02/07
- Reviewed notes and recorded progress, had a meeting a King's.

#### Thursday 03/07
- Did some light exploration of the BC dataset, recorded key columns of interest to map cells spatially, their type, Ki-67, and what patient they originate from with the FOV.
- Created plots that show p+ - p- against X, featuring points (hexbins to visualise data density) for cells and their computed probabilities based on the known model, as well as a curve showing the equivalent for the inferred model.

#### Friday 04/07
- Worked on making more systematic phase portraits for inferred models, by using fsolve on a point lattice to find approximate fixed points, but ran into several value issues, so will stick to streamplots until I can focus more on repeating inference in multiple samples.

## Week 7 | 07/07 - 11/07
Goals:
- [ ] Confirm that the root cause for inappropriate inferred parameters is due to the late SS-like distributions I worked with at first.

Notes:

- Rewrote simulation to accept a time step argument t, that defines at which interval of time the tissues are recorded (with a time_step column added to reflect this).
- Updated sampler and inference code to deal with time column and have rng seed passed as arguments (formerly rng fixed to seed 0).
- 50k sample part of the code: switched from iloc indexing passed to sampling to loc, column index labels are better than true indexes after the addition of time step data.
- Ran a new simulation also ending at t=1000, but this time recorded ever 100 time step. The bigger size of the dataframe (10x) had a very negligible impact on performance (a few minutes of difference in a 1h45 minutes process).
- When applying inference to this dataset (with respect to unique time step data), the seed 0 led to an error as for one of the samples there were no predicted diviviions. Seed 1 completed succesfully.
- While in the algorithm described, fits use binary division label (0,1), the description of neighbourhood dynamics describe death observations as -1, a potential consideration for inference adjustements, although I don't that should be impactful in the parameter value issue I am running into (moreso about infering death as a function of neighbourhood density on top of division, that shouldn't change).
- Plotted phase portraits and p+/p- curves for all samples at all time steps. Unfortunately, a strong inconsistency remains in the models which doesn't appear to improve over time (more samples for converging streamlines depending on time step.) By eye, I couldn't associated the different neighbourhood data spreads to really relate to improvements in model fits with respect to the corresponding time step. I note that out of 11 good (convergent streamlines, even highly unbalanced ones) graphs (total graphs: 40), 8 were distributed among 10k+ samples, and no time step had more than 2 good graphs. This leads me to think that the core issue might be more tied to the sample sizes in the samples fed to inference.

## Week 8 | 14/07 - 18/07
Goals:
- [ ] Plots: average cell densities over time
- [ ] Plots: O vs. X (including logistic regression curve from inference)
- [ ] Question 1: is sample size inaproppriate for reliable model inference in my implementation?
- [ ] Question 2: does the difference between known and inference model's mathematical definitions explain the low quality inferences I got in the past two weeks?

Meeting:
- Since I now have data over time, I should make plots to show that neighbourhood densities are getting succesfully biased towards the known model over time.
- It's impossible to ascertain the inference is working as expected with the curve plots I currently have, as the known model data does not rely on the labels actually used for training. As a sanity check, I should plot events as a function of neighbourhood composition and plot the logistic curve (p+), similar to what can be found on the wikipedia page for logistic regressions.
- Q1: Was model inference negatively impacted by sampling? I could try repeating the same sample size with multiple seeds and beyond and see how models end up (for the same tissue).
- Q2: Is the difference between the known model (only p- is dependent on neighbourhood) and inference model (only p+ is) relevant to the incoherent models? I now have a good reference model to match S2H with inference model formalism they describe, I could try generating data for this and see how inference compares. 

Notes:

## Week 9 | 21/07 - 25/07
Goals:

Meeting:

Notes:

## Week 10 (End goal) | 28/07 - 01/08
Goals:

Meeting:

Notes:

