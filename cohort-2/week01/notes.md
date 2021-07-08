# Week 01 Notes  


## Resources  

[Plutus](https://github.com/input-output-hk/plutus)  
[Plutus Pioneer Program](https://github.com/input-output-hk/plutus-pioneer-program)  

[Plutus Community Documentation](https://docs.plutus-community.com/)  


## Windows Terminal Setup  

[Install and set up Windows Terminal](https://docs.microsoft.com/en-us/windows/terminal/get-started)  


## Windows WSL 2 Setup  

[WSL Installation Guide for Windows 10](https://docs.microsoft.com/en-us/windows/wsl/install-win10)  

1. Enable Windows Features (Windows Subsystem for Linux and Virtual Machine Platform)  

    Open PowerShell as Administrator and run:  
    
    - Window Subsystem for Linux  
    `dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart`  
    - Virtual Machine Platform  
    `dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart`  

2. Restart computer  

3. Download the Linux kernel update package

    [WSL2 Linux Kernel update package for x64 machines.](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)  
    
4. Set WSL 2 as your default version  

    Open PowerShell and run:  
    
    `wsl --set-default-version 2`  

5. Install preferred Linux distro  


## Install Docker Desktop on Windows  

[Docker Desktop for Windows](https://desktop.docker.com/win/stable/amd64/Docker%20Desktop%20Installer.exe)  
  

## Windows Plutus Setup with WSL2 and nix  

### Building plutus and dev container  

1. Open Windows Terminal and select Linux Distro  

2. Create folder for project:  

    `mkdir ~/cardano`  
    `cd ~/cardano`  
    
3. Clone Plutus and Plutus-Pioneer-Program  

    `git clone https://github.com/input-output-hk/plutus`  
    `git clone https://github.com/input-output-hk/plutus-pioneer-program`  

4. Install nix  

    `curl -L https://nixos.org/nix/install | sh`  
  
5. Set up and configure /etc/nix/nix.conf  

    `sudo mkdir /etc/nix`  
    `sudo vim /etc/nix/nix.conf`  

    - Write to /etc/nix/nix.conf:  
    `sandbox = false`  
    `use-sqlite-wal = false`  
    `system-features = kvm`  
    `substituters = https://hydra.iohk.io https://iohk.cachix.org https://cache.nixos.org/`  
    `trusted-public-keys = hydra.iohk.io:f/Ea+s+dFdN+3Y/G+FDgSq+a5NEWhJGzdjvKNGv0/EQ= iohk.cachix.org-1:DpRUyj7h7V830dp/i6Nti+NEO2/nhblbov/8MW7Rqoo= cache.nixos.org-1:6NCHdD59X431o0gWypbMrAURkbJ16ZPMQFGspcDShjY=`  
    
6. Relaunch shell  
7. Build Plutus with the nix command line  

    `cd ~/cardano/plutus`  
    
    `nix build -f default.nix plutus.haskell.packages.plutus-core.components.library`  
    
8. Launch the Docker container from WSL2  

    `mkdir -p ~/.cabal/packages`  
    
    `docker load < $(nix-build default.nix -A devcontainer)`  
    

### Setting up VSCode with the dev container  

1. Get VSCode for Windows  

    [Download for Windows](https://code.visualstudio.com/sha/download?build=stable&os=win32-x64-user)  
    
2. Launch VSCode and get Remote Development extension  
3. Close VSCode and launch Windows Terminal, using WSL2 shell  

    `cd ~/cardano`  
    `git clone https://github.com/input-output-hk/plutus-starter`  
    `cd ~/cardano/plutus-starter`  
    
    `code .`  
    
4. VSCode will launch in Windows and ask to reopen in Dev Container, select Yes  
5. Open a [nix] shell in VSCode, then use cabal commands to build the Haskell  

    `cabal update`  
    `cabal build all`  
    

### Setting up the Plutus Playgroud  

1. Build the Plutus Playground Client/Server  

    `nix-build -A plutus-playground.client`  
    `nix-build -A plutus-playground.server`  
    
2. Build other Plutus dependencies  

    `nix-build -A plutus-playground.generate-purescript`  
    `nix-build -A plutus-playground.start-backend`  
    `nix-build -A plutus-pab`  

3. Go into nix-shell  

    `nix-shell`  
    
4. Inside of nix-shell  

    `cd plutus-pab`  
    `plutus-pab-generate-purs`  
    `cd ../plutus-playground-server`  
    `plutus-playground-generate-purs`  
    
5. Start playground server  

    `cd ../plutus-playground-client`  
    `plutus-playground-server`  
    
6. In a new Windows Terminal window, using WSL2 shell  

    `cd ~/cardano/plutus`  
    `nix-shell`  
    `cd plutus-playground-client`  
    `npm run start`  
    
### Open Browser  

[https://localhost:8009](https://localhost:8009/)  
