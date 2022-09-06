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


## Step 0: Installing Homebrew
Homebrew (also called Brew) installs the stuff you need that Apple (or your Linux system) didn’t. [https://brew.sh](https://brew.sh/)

The most straightforward way is to open terminal and run the following command - 

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

It might take ~15 mins initially but it is a one-time install


## Step 1: Installing gitleaks
```
brew install gitleaks 
```
The above command would install the latest gitleaks from Homebrew and add it to your PATH variable.


## Step 2: Adding git-hooks
Assuming you have github installed (*check FAQ), we will now add a path for git-hooks.
Configure hooksPath in user git configuration. The user git configuration generally is located at "~/.gitconfig" (*check FAQ if file missing). This file is hidden in nature so you would have to perform "ls -lart" in terminal or "cmd+shift+." (period) in finder on your root directory to view the file
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

You can either open the downloaded document using a notepad tool and then paste it it by using the following commands - 
```
touch ~/.git-hooks/gitleaks.toml
vi ~/.git-hooks/gitleaks.toml
<Press "I" on your keyboard> -> Paste using RTClick+Paste -> <Press Escape and type "!wq" and press Enter
```
or copy it using finder into he aforementioned path

## How do we run it?
There is plenty of documentation on the Gitleaks Git repository.
For our purposes, we shall configure our own .toml file for the rules and provide a path to the code local repository

Gitleaks runs in 2 modes - 
1. Protect - Command that we will use by default. This command scans the currently staged or committed objects and runs the secrets rules against them
2. Detect - Command that we can use for historic repositories or when making the repository public. It scans the whole repository and its list of commits to identify leaks


There are 2 ways to run gitleaks:-

Implicit Run - 
Any time you are attempting to commit any code, github will check the staged commits for any leaks.
This is the default behaviour expected after installation using this guide. There are no additional steps required.


Explicit Run - 
```
DETECT:
gitleaks detect --verbose --redact --source='[Path_To_Repository]' --config='[Path_To_TOML_File]' --log-ops = '[git-log options]'

PROTECT:
gitleaks detect --verbose --redact --source='[Path_To_Repository]' --config='[Path_To_TOML_File]' --staged
```

For example: 
```
gitleaks detect --verbose --redact --source=$(git rev-parse --show-toplevel) --config=$glpath --staged
```

Expected output?
![image](https://user-images.githubusercontent.com/88150915/140373532-97ac7d1b-ff38-4f6b-9d18-6074361e9c78.png)

FAQs
1. How do I update my gitleaks?
  A. In your Mac terminal, use command ```brew install gitleaks```. This command assumes you have brew installed
2. How do I update the pre-commit file?
    In your Mac terminal-
      1. Navigate to the pre-commits file by using ```cd ~/.git-hooks```
      2. Update pre-commit using ```vi pre-commit```
      3. Press ```I``` on the keyboard for insert mode
      4. Delete contents of whole file
      5. Copy latest content from [https://mckinsey.box.com/s/kig70jdntmkumgd7d24wau8kruyzz1xs](https://mckinsey.box.com/s/kig70jdntmkumgd7d24wau8kruyzz1xs) and paste it in the terminal editor
      6. Press ```esc```
      7. Type ```:wq!``` and hit enter
3. How do I update the gitleaks.toml file?
    In your Mac terminal-
      1. Navigate to the pre-commits file by using ```cd ~/.git-hooks```
      2. Update pre-commit using ```vi gitleaks.toml```
      3. Press ```I``` on the keyboard for insert mode
      4. Delete contents of whole file
      5. Copy latest content from [https://mckinsey.box.com/s/kig70jdntmkumgd7d24wau8kruyzz1xs](https://mckinsey.box.com/s/ga3o5urv3qquhyyprbevvu2hg55rjvr0) and paste it in the terminal editor
      6. Press ```esc```
      7. Type ```:wq!``` and hit enter
4. How do I check if Github is installed on my machine?
   
    In your Mac terminal - 
        ```git --version```
        You should see a response similar to `git version 2.32.1 (Apple Git-133)`
5. I am missing a .gitconfig file. How do I create it?
    In your mac terminal -
        ```git config --global user.email "<email>@mckinsey.com"```
    

What is next?
- Add command explanation for various parameters of Gitleaks
