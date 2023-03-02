My ubuntu 20.04 system on Dell XPS 9350 [?] could not connect with anything on bluetooth. 

Here is how it was fixed. 

## Step 1: Uninstall bluetooth
```bash
sudo apt-get autoremove blueman bluez-utils bluez bluetooth
```

## Step 2: Reinstall bluetooth
```bash
sudo apt install blueman -y && blueman-manager
```
