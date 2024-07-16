# Passthrough GPU/iGPU 

A brief description of what this project does and who it's for


## Installation

url

```bash
 https://docs.google.com/forms/d/e/1FAIpQLSej3F9ncMQBoutqAhvnnHRA0oMwfhlqRAs2urbmpgDVSQhS6A/viewform?usp=sf_link
```

## Step 1: Edit GRUB  
  Execute: nano /etc/default/grub 
     Change this line from 
   GRUB_CMDLINE_LINUX_DEFAULT="quiet"
     to 
 ```bash
   GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt pcie_acs_override=downstream,multifunction nofb nomodeset video=vesafb:off,efifb:off"
 ```
si defaut avec les autre equipement cette commande fonctionne
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt pcie_acs_override=downstream,multifunction"

  Save file and exit the text editor  
   
##Step 2: Update GRUB  
  Execute the command: update-grub 
   
##Step 3: Edit the module files   
  Execute:  
```bash
nano /etc/modules
```

   Add these lines: 
   vfio
   vfio_iommu_type1
   vfio_pci
   vfio_virqfd
  Save file and exit the text editor  
   
Step 4: IOMMU remapping  
 a) Execute: nano /etc/modprobe.d/iommu_unsafe_interrupts.conf 
     Add this line: 
   options vfio_iommu_type1 allow_unsafe_interrupts=1
     Save file and exit the text editor  
 b) Execute: nano /etc/modprobe.d/kvm.conf 
     Add this line: 
   options kvm ignore_msrs=1
  Save file and exit the text editor  
   
Step 5: Blacklist the GPU drivers  
  Execute: nano /etc/modprobe.d/blacklist.conf 
     Add these lines: 
blacklist radeon
blacklist nouveau
blacklist nvidia
blacklist nvidiafb
Kvmgt

Pour IGPU  ajouter dans le balcklist 👇

blacklist snd_hda_codec_hdmi
blacklist snd_hda_intel
blacklist snd_hda_codec
blacklist snd_hda_core
blacklist radeon
blacklist amdgpu

  Save file and exit the text editor  
   
Step 6: Adding GPU to VFIO  
 a) Execute: lspci -v 
     Look for your GPU and take note of the first set of numbers 
 b) Execute: lspci -n -s (PCI card address) 
   This command gives you the GPU vendors number.
 c) Execute: nano /etc/modprobe.d/vfio.conf 
     Add this line with your GPU number and Audio number: 
   options vfio-pci ids=(GPU number,Audio number) disable_vga=1
  Save file and exit the text editor  
   
Step 7: Command to update everything and Restart  
 a) Execute: update-initramfs -u 
 b) Then restart the your Proxmox Node
![image](https://github.com/user-attachments/assets/2fab3004-c71f-40ba-80dd-7a37fdf825b8)
