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
- [ ] Program an ODE to phase portrait component to work with cell circuit models.
- [ ] Program the data simulation component for OSDR evaluation.

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
