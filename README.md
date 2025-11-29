# 42-Guide_GitHub-alongside-Vogsphere
Guide on how to setup and implement using GitHub alongside Vogsphere.

## Setting Up SSH Key Pairs
For security reasons, it is recommended that a SSH public/private key pair be generated for each device to each git provider. Example illustrated below:

![ssh key pair](https://github.com/user-attachments/assets/4cf34780-b107-4e59-bfb2-c39eed040ed5)

### Generate SSH Keys Locally
Navigate to the SSH folder: 
```
cd ~/.ssh
```

Generate the SSH public and private key pair. You can choose from rsa/ecdsa/ed25519 encryption algorithms but ed25519 is most secure and recommended.
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

When asked for where to save the keys, by default it saves to:  
`~/.ssh/id_ed25519` (private key, secret)  
`~/.ssh/id_ed25519.pub` (public key, shareable)

Since we want to create multiple key pairs, give a meaningful name like `42pc_github_key`.  
Most 42 students would have used the default name `id_rsa` when setting up 42 Vogsphere key during piscine. It's recommended to change `id_rsa` and `id_rsa.pub` to a similar format as your GitHub key (ex: `42pc_vogsphere_key`) to distinguish your keys more easily in the future. Since only the public key content is uploaded into 42 intra profile settings, changing the filename will not affect the existing key in intra.

![setting up keys](https://github.com/user-attachments/assets/485cea5d-a71b-4492-b8f5-1a711ebc1e47)

### Upload Public Key to Git Server
Add the content of `keyname.pub` to your GitHub settings under SSH & GPG Keys.

<img src="https://github.com/user-attachments/assets/e1e5d021-8bdf-4575-8572-72005dad3e12" width="1000">


WARNING: Never share your private key with anybody as this is your secret access to any git provider.  
The concept of SSH keys is "This public lock belongs to you. If you can open it, I’ll trust you."

## Setting Up Git to Recognize Multiple Git Providers
By default, the OpenSSH client (the one Git uses) looks for private keys in:  
`~/.ssh/id_rsa`  
`~/.ssh/id_ecdsa`  
`~/.ssh/id_ed25519`  

If it finds one of these, it will try to use it automatically when connecting to whichever git provider. However, if you use multiple Git providers (e.g., GitHub + vogsphere), you can assign each one its own key.  
First edit the ssh configuration file:
`nano ~/.ssh/config`

Use the setup below and edit where necessary:
```
Host vogsphere.42iskandarputeri.edu.my
    HostName vogsphere.42iskandarputeri.edu.my
    User git
    IdentityFile ~/.ssh/42pc_vogsphere_key

Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/42pc_github_key
```

NOTE: make sure <ABC> in Host <ABC> and HostName <ABC> are EXACTLY the same, otherwise you will face some errors.

To determine hostname, enter any folder which is a git cloned repo, then display the remote URL:
```
git remote -v
```
The full name is usually: `git@hostname:user/gitrepo` (ex: git@<b>vogsphere.42iskandarputeri.edu.my</b>:vogsphere/intra-uuid-dc522b8b-a4a2-4aad-a040-b907b151252b-7097200-belam)

If your github repo is originally using https://… but you want to switch to SSH:
```
git remote set-url origin git@github.com:BenLam2000/EverybodyCodes2025.git
```

<img width="482" height="312" alt="image" src="https://github.com/user-attachments/assets/fbf15a8a-cd77-421b-80c9-5023312c2fdf" />

After done setting up the SSH config file, test the SSH connection:
```
ssh -T git@github.com
ssh -T git@vogsphere.42iskandarputeri.edu.my
```
There should be a notification if the SSH connection is successful:
<img width="685" height="34" alt="image" src="https://github.com/user-attachments/assets/04feb86e-866a-43a8-8b85-c9d75071ece9" />

Now, whenever you git push or git pull with an SSH remote, Git will pick the right SSH key depending on the remote host. Git add and git commit won’t be affected since they are done locally. 

### Setting up Multiple Remotes
For projects that you wish to do both from home and at 42, clone the repo from 42 project page as usual:
```
git clone git@vogsphere.42iskandarputeri.edu.my:vogsphere/intra-uuid-dc522b8b-a4a2-4aad-a040-b907b161272b-7097200-belam libft
```
Check the current remote using `git remote -v`, it should just contain 1 fetch and 1 push for origin which is linked to vogsphere. 
A remote is essentially just a label for URL to let git know where to push your project to/where to pull code from.  
Next add a remote to your GitHub Repo:
<img width="482" height="312" alt="image" src="https://github.com/user-attachments/assets/fbf15a8a-cd77-421b-80c9-5023312c2fdf" />
```
git remote add github git@github.com:User/Repo.git
```
To verify GitHub remote is added:  `git remote -v`
Your remote list should now contain 2 extra lines (1 fetch and 1 push for new github remote).

Next time you can choose which remote to push/pull (make sure to type the remote & branch, `git push <remote> <branch>`):
```
git push origin master
git pull github master
```
You can use `git branch` to check which branch you're on, for individual projects this would usually be 'master'.

## Summarized Steps for Full Workflow
1. Setting up remote (on 42pc):  
`git clone git@vogsphere.42iskandarputeri.edu.my:vogsphere/intra-uuid-dc522b8b-a4a2-4aad-a040-b907b161272b-7097200-belam libft`  
`git remote add github git@github.com:User/Repo.git`  
`git remote -v` to verify Github remote added

2. Make changes to local repo (on 42pc), `git add .`, `git commit -m "msg"` as usual.

3. Before leaving 42, to be able to continue at home:
```
git push github master
```

4. At home:
`git clone git@github.com:User/Repo.git project` (only for 1st time setting up repo at home)  
`git pull`, remote and branch not required since there will only be the GitHub remote  
Make changes, `git add .`, `git commit -m "msg"`, `git push`

5. At 42:
`git pull github master`  
Make changes  
When you're ready to submit, do one final `git push origin master`.

That’s it for setting up GitHub alongside Vogsphere in 42! If you’re interested in learning the very fundamentals of git and how branches work, check out [this conversation with Chatgpt](https://chatgpt.com/share/692484dd-ed64-8006-ab76-3086cab856d2).
