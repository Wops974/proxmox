# Passthrough GPU/iGPU 

## Installation

## Step 1: Edit GRUB  
  Execute: 
```bash
nano /etc/default/grub
```
  Change this line from 
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
     to 
 ```bash
 GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt pcie_acs_override=downstream,multifunction nofb nomodeset video=vesafb:off,efifb:off"
 ```
Cpu Intel 
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on i915.enable_gvt=1 iommu=pt pcie_acs_override=downstream,multifunction video=efifb:off video=vesa:off vfio_iommu_type1.allow_unsafe_interrupts=1 kvm.ignore_msrs=1 modprobe.blacklist=radeon,nouveau,nvidia,nvidiafb,nvidia-gpu"
 ```
nvidia ok
si defaut avec les autre equipement cette commande fonctionne
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt pcie_acs_override=downstream,multifunction"
```
  Save file and exit the text editor  
   
## Step 2: Update GRUB  
  Execute the command: 
```bash
update-grub 
```

## Step 3: Edit the module files   
  Execute:  
```bash
nano /etc/modules
```

   Add these lines: 
```bash
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
```
  Save file and exit the text editor  
   
## Step 4: IOMMU remapping  
 a) Execute: 
```bash
nano /etc/modprobe.d/iommu_unsafe_interrupts.conf 
```
   Add this line: 
```bash
options vfio_iommu_type1 allow_unsafe_interrupts=1
```
  Save file and exit the text editor  
 
 b) Execute: 
```bash
nano /etc/modprobe.d/kvm.conf 
```
   Add this line:  
```bash
options kvm ignore_msrs=1
```  
Save file and exit the text editor  
   
## Step 5: Blacklist the GPU drivers  
  Execute: 
```bash
nano /etc/modprobe.d/blacklist.conf
```
  Add these lines: 
```bash
blacklist radeon
blacklist nouveau
blacklist nvidia
blacklist nvidiafb
```

Pour IGPU  ajouter dans le balcklist 👇
```bash
blacklist snd_hda_codec_hdmi
blacklist snd_hda_intel
blacklist snd_hda_codec
blacklist snd_hda_core
blacklist amdgpu
#blacklist Kvmgt
```

  Save file and exit the text editor  
   
##Step 6: Adding GPU to VFIO  
 a) Execute: 
```bash
 lspci -v 
```
   Look for your GPU and take note of the first set of numbers 

 b) Execute: 
```bash
lspci -n -s (PCI card address)
``` 
   This command gives you the GPU vendors number.
Or 
Execute: 
```bash
 lspci -nnk | grep -A 3 -i vga
```

 c) Execute: 
```bash
nano /etc/modprobe.d/vfio.conf 
```
   Add this line with your GPU number and Audio number: 
```bash
options vfio-pci ids=(GPU number,Audio number,iGPU) disable_vga=1
```
  
  Save file and exit the text editor  
   
## Step 7: Command to update everything and Restart  
 a) Execute: 
```bash
update-initramfs -u 
```
 b) Then restart the your Proxmox Node

# FIN


# Bonus 

## Qemu-guest-agent install

```bash
sudo apt update && sudo apt install qemu-guest-agent -y && sudo systemctl enable qemu-guest-agent && sudo systemctl start qemu-guest-agent
```


## Pour du Debian réparation du grub 

Boot sur l'iso ubuntu "essayer" puis lancer le terminal

Puis 
```
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt update && sudo apt install -y boot-repair
```

Ensuite 
```
boot-repair
```

# Reseaux n'a pas de DHCP actif 

Pour debian
```
nano /etc/network/interfaces
```

Pour ubuntu 
```
nano /etc/netplan/*.yaml
```
```
sudo reboot
```



