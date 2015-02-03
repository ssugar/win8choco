# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

$prereboot = <<SCRIPT
#(iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1')))>$null 2>&1
#choco install googlechrome
#choco install flashplayerplugin
#choco install javaruntime
#choco install office365proplus
#choco install pswindowsupdate
import-module PSWindowsUpdate
Get-WUList | measure | select Count | fl
Get-WUInstall -AcceptAll -IgnoreReboot
#Get-WURebootStatus -Silent
SCRIPT

$postreboot = <<SCRIPT
import-module PSWindowsUpdate
Get-WURebootStatus -Silent
SCRIPT


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
   config.vagrant.host = :windows
   config.windows.halt_timeout = 60   
 
   config.vm.box = "mwrock/Windows8.1-amd64"
   config.vm.define :win8choco do |t|
   end

   config.vm.provider :hyperv do |v|
	 v.vmname = "win8choco"
	 v.memory = 1024
	 v.cpus = 2
   end

   config.vm.communicator = "winrm"
   config.vm.network :forwarded_port, guest: 3389, host: 3391
   config.vm.network :forwarded_port, guest: 5985, host: 5987, id: "winrm", auto_correct: true

   config.winrm.username = "vagrant"
   config.winrm.password = "vagrant"

   config.vm.synced_folder ".", "/vagrant", type: "smb", disabled: false, smb_host: "10.10.10.66", create: true

   #run the pre-reboot script
   config.vm.provision "shell", inline: $prereboot

   #reboot
   config.vm.provision :reload
   
   #run the script above
   config.vm.provision "shell", inline: $postreboot

end
