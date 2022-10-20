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


## Step 0: Installing Homebrew (only required for Mac Systems)
Homebrew (also called Brew) installs the stuff you need that Apple didn’t. [https://brew.sh](https://brew.sh/)

The most straightforward way is to open terminal and run the following command - 

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

It might take ~15 mins initially but it is a one-time install


## Step 1: Installing gitleaks
### - On Mac Systems
```
brew install gitleaks 
```
The above command would install the latest gitleaks from Homebrew and add it to your PATH variable.

### - On R-Studio Workbench
Let us start a new session of RStudio (any session will do but we will keep things clean).

Navigate to the "Terminal" tab on Workbench. Your screen should look similar to 


![image](https://user-images.githubusercontent.com/88150915/196962284-a3ef3dff-ba6d-463d-8af0-41b8808321dd.png)

Create a `machineapps` folder if not present already. This is where we will install gitleaks and any future workbench app. 
```
cd ~
mkdir machineapps
```
Note: The instructions are based on the directory structure listed in the steps. If you have different paths, you should be able to alter the commands to suit your environment.

Now we shall clone gitleaks on our workbench from the creator's github page.
NOTE: We used the `git clone https://github.com/zricethezav/gitleaks.git` command earlier but gitleaks updates for v8.13.0 introduced issues. 
Hence, we will stick to v8.12.0 as we have tested it on Workbench.

```
cd ~
cd machineapps
git clone --depth 1 --branch v8.12.0 https://github.com/zricethezav/gitleaks.git
```
This step has now brought all the packages required to install gitleaks on our machine. We will now proceed to actually install gitleaks with the following commands -
```
cd ~
cd machineapps/gitleaks
make build
```
This command takes ~1 min to run and should create a new file called "gitleaks" with no extension.

We can test if gitleaks is executable by running the following command
```
~/machineapps/gitleaks/gitleaks version
```
It should result in an output similar to "v8.11.2"

We are not done yet. 
We want to be able to run gitleaks with a simple `gitleaks` command and not having to specify the installation path.
For this, we will add our gitleaks installation to the linux terminal PATH variable.

#### Adding to PATH
Check if you have a ".bash_profile" file already. This file contains user specific installation paths.

##### Checking if .bash_profile exists
To check, simple run `cat ~/.bash_profile`. 
If you do not get an error, you have the file present.

##### Creating a .bash_profile if it doesn't exist
```
cd ~
touch .bash_profile
```

##### Adding gitleaks to .bash_profile
```
vi .bash_profile
```
`
<Insert> by pressing "I"
`
Right Click > Paste the following - 
```export PATH=$PATH:~/machineapps/gitleaks/```

Follow it up with saving and exiting the text editor
`
<Escape> by pressing Esc
`
```
:wq!
```

**Now Restart your R Session in Workbench.**


Testing native gitleaks
```
echo $PATH
gitleaks version
```
Your output should show a long string showcasing multiple installations as well as the version of gitleaks installed similar to "v8.11.2"

## Step 2: Adding git-hooks
NOTE: These steps are common to both Mac Systems and Workbench. You will want to use the commands on Workbench while Finder commands work for Mac Systems.

Assuming you have github installed (*check FAQ*), we will now add a path for git-hooks.
Configure hooksPath in user git configuration. The user git configuration generally is located at "~/.gitconfig" (*check FAQ if file missing*). This file is hidden in nature so you would have to perform "ls -lart" in terminal or "cmd+shift+." (period) in finder on your root directory to view the file

##### Opening ".gitconfig" in terminal
```
cd ~
vi .gitconfig
```

`
<Insert> by pressing "I"
`

##### Add the following :-
```
[core]
hooksPath = ~/.git-hooks
```

##### Save the file in Finder or in terminal using the following :-
`
<Escape> by pressing Esc
`
```
:wq!
```


Follow this up with creating a folder which will house all future git-hooks -:

```
mkdir -p ~/.git-hooks
```

Now add a pre-commit file

```
touch ~/.git-hooks/pre-commit
```

## Step 3: Configuring the Pre-Commit Script
Add the attached bash script to the "~/.git-hooks/pre-commit" file
script - https://mckinsey.box.com/s/kig70jdntmkumgd7d24wau8kruyzz1xs

##### On Workbench/Terminal
```
vi ~/.git-hooks/pre-commit
```
`
<Insert> by pressing "I"
`

Right Click > Paste the script verbatim in its entirity.

`
<Escape> by pressing Esc
`
```
:wq!
```

## Step 4: Make the gitleaks configuration an executable
```
chmod +x ~/.git-hooks/pre-commit
```

## Step 5: Create and Store toml file for gitleaks
Please download the TOML file from the path - 
https://mckinsey.box.com/s/ga3o5urv3qquhyyprbevvu2hg55rjvr0
Store it on path "~/.git-hooks/" as file "~/.git-hooks/gitleaks.toml"

You can either open the downloaded document using a notepad tool and then paste it it by using the following commands - 
```
touch ~/.git-hooks/gitleaks.toml
vi ~/.git-hooks/gitleaks.toml
```

`
<Press "I" on your keyboard> -> Paste using RTClick+Paste -> <Press Escape and type "!wq" and press Enter
`
or copy it using finder into he aforementioned path

**Now Restart the R Session in Workbench**

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
##### On your Mac System
![image](https://user-images.githubusercontent.com/88150915/140373532-97ac7d1b-ff38-4f6b-9d18-6074361e9c78.png)

##### On your Workbench
###### Commit without Secret
<img width="633" alt="image" src="https://user-images.githubusercontent.com/88150915/189146013-2ac98206-5617-4d47-a4ea-c8a0044c187c.png">

###### Commit with Secret
<img width="633" alt="image" src="https://user-images.githubusercontent.com/88150915/189147359-936758d4-923a-4500-9be9-92409e7e1985.png">


FAQs
1. How do I update my gitleaks?
   - In your Mac terminal, use command ```brew install gitleaks```. This command assumes you have brew installed
   - On your Workbench terminal, `Delete` the gitleaks folder under ~/machineapps/ directory and follow the steps of installation as listed
2. How do I update the pre-commit file?
    In your Mac or Workbench terminal-
      1. Navigate to the pre-commits file by using ```cd ~/.git-hooks```
      2. Update pre-commit using ```vi pre-commit```
      3. Press ```I``` on the keyboard for insert mode
      4. Delete contents of whole file
      5. Copy latest content from [https://mckinsey.box.com/s/kig70jdntmkumgd7d24wau8kruyzz1xs](https://mckinsey.box.com/s/kig70jdntmkumgd7d24wau8kruyzz1xs) and paste it in the terminal editor
      6. Press ```esc```
      7. Type ```:wq!``` and hit enter
3. How do I update the gitleaks.toml file?
    In your Mac or Workbench terminal-
      1. Navigate to the pre-commits file by using ```cd ~/.git-hooks```
      2. Update pre-commit using ```vi gitleaks.toml```
      3. Press ```I``` on the keyboard for insert mode
      4. Delete contents of whole file
      5. Copy latest content from [https://mckinsey.box.com/s/kig70jdntmkumgd7d24wau8kruyzz1xs](https://mckinsey.box.com/s/ga3o5urv3qquhyyprbevvu2hg55rjvr0) and paste it in the terminal editor
      6. Press ```esc```
      7. Type ```:wq!``` and hit enter
4. How do I check if Github is installed on my machine?
   - In your Mac or Workbench terminal - 
        ```git --version```
        You should see a response similar to `git version 2.32.1 (Apple Git-133)`
5. I am missing a .gitconfig file. How do I create it?
   - In your Mac or Workbench terminal -
        ```git config --global user.email "<email>@mckinsey.com"```
6. I removed my secret, why is it still throwing an error?
   - You need to revert your changes and then recommit your code.      
    
    
