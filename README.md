### Windows
- [ ] Create `shared` dir in `%userprofile%` (*ConfigSshKey* generates a symlink: Windows -> WSL) ^043c19
- [ ] `winget install Git.MinGit` ^989783
    
    - 
        ```bash
        ssh-keygen -t rsa -b 4096 -C "<computer-name> <email>"
        
        git config --global user.name "First Last"
        git config --global user.email <email>
        git config --global branch.autoSetupMerge always
        git config --global core.autocrlf false
        
        ```
- [ ] Install wsl2 with distro
    
    -  
        ```powershell
        wsl --install -d debian
        ```
- [ ] Reboot
### WSL
```bash
sudo apt update && sudo apt install -y --no-install-recommends wget ca-certificates \
    mlocate keychain rsync curl man-db net-tools software-properties-common telnet
# VARS='GIT_CONF_EMAIL=email@mail.com GIT_CONF_FNAME=First GIT_CONF_LNAME=Last RAILS_VER=x.x.x RUBY_VER=x.x.x'
VARS='GIT_CONF_EMAIL=edit@me.com GIT_CONF_FNAME=Edit GIT_CONF_LNAME=Me' && wget -qO- https://raw.githubusercontent.com/ralphie02/ws-linux/master/_Init.md | sed '$ d' | sed '0,/```bash/d' | sed "/\#\!.*bash$/a \\\n$VARS" | bash
```

### Android
```bash
curl -s https://raw.githubusercontent.com/ralphie02/ws-linux/master/_ConfigObsidianOnAndroid.md | sed '$ d' | sed '0,/```bash/d' | bash
```
