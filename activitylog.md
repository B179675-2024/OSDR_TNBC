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

#### Monday 26/05
