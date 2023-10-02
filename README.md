# GitLab Tutorials - 1

## How to connect / clone GitLab Repo

### Pre-requisite: Git
Download and install Git for Windows

1) Download and run the installer from https://gitforwindows.org/. The options at each step should be suitable. When you reach the step about choosing the SSH executable, ensure the bundled OpenSSH is selected.
 
2) Once the installation is complete, open Git Bash from the Start menu.
 
3) In the terminal, check that OpenSSH has been successfully installed by running the following command:
$ ssh -V
The output should show the installed version of OpenSSH.

### Step 1) Start the SSH agent
	
**Windows OpenSSH users** 

 ```
	From a PowerShell, check if the SSH Agent is running using the Get-Service command. For example:
	$  Get-Service ssh-agent

           Status   Name         DisplayName
           ------   ----         -----------
           Stopped  ssh-agent    OpenSSH Authentication Agent
	
	To start the agent:
	$  Start-Service ssh-agent
	
	To check the service has been disabled  
	$  Get-Service -Name ssh-agent | Select -StartupType
	
	To configure the SSH agent to start each time the device is started, use the Set-Service command, such as:
	$  Get-Service -Name ssh-agent | Set-Service -StartupType Manual 
	or
	$  Get-Service -Name ssh-agent | Set-Service Automatic
```

### Step 2) Create SSH key pair
	
To create an SSH key pair:
 
1. Open a terminal and navigate to your home or user directory using cd, for example:
```
    cd ~
```

2. Generate a SSH key pair using ssh-keygen, such as:
```
    $ ssh-keygen -t ed25519 -b 4096 -C "{username@emaildomain.com}"
 
	Where:
	• {username@emaildomain.com}is the email address associated with the Bitbucket Cloud account, such as your work email account.
	• When prompted to Enter passphrase, you can either provide a password or leave the password empty. If you input a password, you will be prompted for this password each time SSH is used, such as using Git command that contact Bitbucket Cloud (such as git push, git pull, and git fetch). Providing a password will prevent other users with access to the device from using your keys.
	Once complete, ssh-keygenwill output two files:
	• {ssh-key-name}— the private key.
	• {ssh-key-name}.pub— the public key.
	These files will be stored in your user folder, such as C:\Users\<username>\.ssh\<ssh-key-name>.
```

### Step 3) Add private key to SSH agent
To add the SSH key to your SSH agent (ssh-agent):
Run the following command, replacing the {ssh-key-name}with the name of the private key:
```
  $ ssh-add C:\Users\<usrname>\.ssh\{ssh-key-name}
  e.g. $ ssh-add "C:\Users\Ajit\.ssh\id_ed25519"
```
### Step 4) Add public key to GitLab
1) Copy key from the public key file (C:\Users\Ajit\.ssh\id_ed25519.pub)
2) Paste / Publish Public key into the GitLab (profile > preferences > ssh key > add key)
3) Download the GitLab Repo
	
```
	$  git clone <repo>
```

### Step 5) Upload new codes
1) copy new files into the repo downloaded
2) commit new files:
```    
       $ cd  <repo>
       $ git status
       $ git add . 
       $ git commit -m "message"
       $ git push (if any error then run command $ git pull and run again $ git push)
```

  3) commit subsequent file(s)
``` 
       $ git diff
       $ git add .  X x x x 
       $ git commit -m "message"
       $ git push
```
