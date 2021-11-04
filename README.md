# GitLeaks: Guide to Preventing Leaks

## Introduction:
We use Github for version control and as a means to store our code on a secure location of the cloud while retaining easy accessibility and the ability to share. 
Github uses a base branch called the "main branch" (erstwhile "Master") which contains peer reviewed production code and "Follower branch" which contains development code on top of production code that will be considered for production.


Github uses commands such as "Push", "Pull", "Branch", "Commit", "Checkout", "Rebase", etc to create or publish the follower branches into the main repository. It follows a timeline of events to store the changes to the code over time and this allows the ability to go back in time and retrieve code.


We Commit our code onto a Branch and then Push this Branch onto the Leader branch. 
Once "Commit"ed, the code remains on the Github repository forever. 


While we can use "Revert" command to undo a Commit, Github creates a new event to remove the changes. Hence, the code from a prior commit can still be accessed using the timeline feature of Github.


Hence, the solution to ensure passwords or secrets are not accidentally stored on Github is through using auditing tools such as Gitleaks.


# What is Gitleaks?
Gitleaks (https://github.com/zricethezav/gitleaks) is a rules based software audit tool which uses GoLang REGEX to identify commonly occurring or user-defined passwords and secrets.
It analyzes the code and provides error outputs upon discovering secrets within the code.


# How do we install?
Installation is easy and uses 5 steps and takes ~15 mins (All steps are for MacOS/Linux) - 


## Step 1: Installing gitleaks
brew install gitleaks 
The above command would install the latest gitleaks from Homebrew and add it to your PATH variable.


## Step 2: Adding git-hooks
Assuming you have github installed, we will now add a path for git-hooks.
Configure hooksPath in user git configuration. The user git configuration generally is located at "~/.gitconfig". This file is hidden in nature so you would have to perform "ls -lart" in terminal or "cmd+shift+." (period) in finder on your root directory to view the file
Open the file and add the following :-

```
[core]
hooksPath = ~/.git-hooks
```


Follow this up with creating a folder which will house all future git-hooks -:

```
mkdir -p ~/.git-hooks
touch ~/.git-hooks/pre-commit
```

## Step 3: Configuring the Pre-Commit Script
Add the attached bash script to the "~/.git-hooks/pre-commit" file
script - https://mckinsey.box.com/s/kig70jdntmkumgd7d24wau8kruyzz1xs


## Step 4: Make the gitleaks configuration an executable
```
sudo chmod +x ~/.git-hooks/pre-commit
```

## Step 5: Create and Store toml file for gitleaks
Please download the TOML file from the path - 
https://mckinsey.box.com/s/ga3o5urv3qquhyyprbevvu2hg55rjvr0
Store it on path "~/.git-hooks/" as file "~/.git-hooks/gitleaks.toml"


How do we run it?
There is plenty of documentation on the Gitleaks Git repository.
For our purposes, we shall configure our own .toml file for the rules and provide a path to the code local repository


There are 2 ways to run gitleaks:-


Implicit Run - 
Any time you are attempting to commit any code, github will check the complete repository for any leaks.
There are no additional steps required.


Explicit Run - 
```
gitleaks --path='[Path_To_Repository]' --config-path='[Path_To_TOML_File]' --no-git -v
```

For example: 
```
gitleaks --path='/Users/sankalp_devasthali/Documents/GitRepos/pam-pamda-pydq' --config-path='/Users/sankalp_devasthali/Documents/gitleaks.toml' --no-git -v
```

Expected output?
![image](https://user-images.githubusercontent.com/88150915/140373532-97ac7d1b-ff38-4f6b-9d18-6074361e9c78.png)

What is next?
- Add command explanation for various parameters of Gitleaks
