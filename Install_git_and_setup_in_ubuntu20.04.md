## How to install git and how to setup in ubuntu20.04

#### Install Git：
`sudo apt install git -y`
#### Setup Git：

1. Set your username: `git config --global user.name 'your username here'`
2. Set your email address: `git config --global user.email 'your email here'`

#### Setup SSH authentication：
1. Generate an ssh key: `ssh-keygen -t ed25519 -C "your_email@example.com"`,
   then press Enter twice.
2. copy the entire output: `cat ~/.ssh/id_ed25519.pub`
3. In your github page, go to `settings` > `SSH and GPG Keys`, Here add your ssh key that you copied to the clipboad in the step prior. Type `yes` (the whole word) and press enter and you are done
4. Test you ssh connection by using this command: `ssh -T git@github.com`

#### Ref:
https://cis106.com/guides/Ubuntu%20Github%20Setup/
