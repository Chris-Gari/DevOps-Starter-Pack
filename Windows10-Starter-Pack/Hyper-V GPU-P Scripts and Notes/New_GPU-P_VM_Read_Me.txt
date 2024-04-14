Prerequisites;
	Windows 10+ Pro edition
	Minimum of 16GB RAM, Recommended 32+GB
	Minimum 6 Core CPU, 8+ Core CPU recommended.
	A GPU with enough power to play the desired game below 50% GPU load.
	Enough Disk space to house the VM and Game. Recommended ~256GB for larger games like Vermintide 2.
	Windows ISO of the same version
	
Steps;
1. Enable VM stuff in UEFI
2. Windoes search "Features", you want "Turn Windows features on or off"
3. Enable Hyper-V, a reboot will happen.
4. Open Hyper-V Manager
5. Right click the hosts name on the left and open Hyper-V settings.
6. Disable both "Enhanced Session Mode Policy" and "Enhanced Session Mode", close.
7. Right click the hosts name on the left and open Virtual Network Manager.
8. Create a New External Switch, Name it accordingly such as External, and assign it to your current host network adapter. This will allow the VM to behave as though there phisicaly systems and will appear on your network as there own entities.
9. Create a New Virtual machine using the New button on the right when the host is selected on the left.
10. Name the VM and designate the location where you will want the VM files (not VM disk) to be stored, it will make its own sub directories.
11. Chose Gen 2 VM.
12. set the amount of RAM you want the VM to have access to, and DISABLE Dynamic Memory.
13. select the external network you made earlier.
14. Create a virtual disk, Name the disk that will be made, Designate the location for the VHD, (the default should be a sub directory of the VM folder made earlier, but if can be anywhere you like) and the Size of the VHD (note that it will only use the space as needed, aka if you set it to 256GB but the VM is only using 60GB the VHD will only use 60GB on the host)
15. Select to install an OS from an image, and point it to the Windows ISO image.
16. Check that all is good and click Finish, DO NOT START THE VM!
17. Right click the VM and go to settings.
18. In firware make sure the CD drive (ISO file) is before the HDD, with the network adapter last.
19. In security enable secure boot and TPM
20. In Processor change the number of processors to the number of CPU Threads you want to give to the VM. If the host has say a 6 core 12 thread CPU you will probably want to assign 4 threads.
21. In Integration Services Disable Backup and Guest services.
22. In checkpoints disable it.
23. Set Automatic start to nothing, and automatic stop to shutdown.
24. Apply the settings changed.
25. Connect to the VM, then start it. you will have to be quick to press a key to boot from the ISO.
26. Install windows, update all the things, set appropeate things like the PC name.
27. Install VNC, VB cable, and USBMMIDD_V2 (but do not activate) Configure VNC then reboot the VM. Close the connection window. From this point on NEVER USE RDP TO CONNECT TO THE VM!!!
28. Using VNC to connect to the VM, go into device manager and diable the Hyper-V display adapter.
29. Shutdown the VM.
30. Mount the VM VHD to the host.
31. On the Host the folder: C:\Windows\System32\DriverStore\FileRepository\nv_dispi.inf_amd64... Has to be copied to the client to the folder location: C:\Windows\System32\HostDriverStore\FileRepository\
32. On the Host all files in C:\Windows\System32\ that start with "NV" Have to be copied to the same on the Client.
33. On the host from Disk Managment unmount the VHD.
34. On the host open up Powershell ISE as admin, then on the bottom blue half run "Set-ExecutionPolicy unrestricted" and allow with "Y" (run to enable or disable the ability to run scripts. Y enables scripts, N disables scripts. you may want to run this again after to disable scripts again.)
35. In the top half paste in the following; changing the top line to the name of the VM you made. It is case sensitive. This should allow the VM to have access to aproxamently 50% of the GPU in all aspects.

$vm = "Your VM Name"
Remove-VMGpuPartitionAdapter -VMName $vm
Add-VMGpuPartitionAdapter -VMName $vm
Set-VMGpuPartitionAdapter -VMName $vm -MinPartitionVRAM 1
Set-VMGpuPartitionAdapter -VMName $vm -MaxPartitionVRAM 500000000
Set-VMGpuPartitionAdapter -VMName $vm -OptimalPartitionVRAM 499999999
Set-VMGpuPartitionAdapter -VMName $vm -MinPartitionEncode 1
Set-VMGpuPartitionAdapter -VMName $vm -MaxPartitionEncode 500000000
Set-VMGpuPartitionAdapter -VMName $vm -OptimalPartitionEncode 499999999
Set-VMGpuPartitionAdapter -VMName $vm -MinPartitionDecode 1
Set-VMGpuPartitionAdapter -VMName $vm -MaxPartitionDecode 500000000
Set-VMGpuPartitionAdapter -VMName $vm -OptimalPartitionDecode 499999999
Set-VMGpuPartitionAdapter -VMName $vm -MinPartitionCompute 1
Set-VMGpuPartitionAdapter -VMName $vm -MaxPartitionCompute 500000000
Set-VMGpuPartitionAdapter -VMName $vm -OptimalPartitionCompute 499999999
Set-VM -GuestControlledCacheTypes $true -VMName $vm
Set-VM -LowMemoryMappedIoSpace 1Gb -VMName $vm
Set-VM -HighMemoryMappedIoSpace 32GB -VMName $vm

36. at the top of the UI there should be a green play button. press it. It should then partition the GPU.
37. Start the VM and use VNC to connect.
38. Make a scheduled task that runs USBMMIDD_V2 that will enable a display at system start., then run such.
39. Open Windows display setting, change the resolution of the new display to 1080p, make it default, and change it so that windows only displays on display 2.
40. Install Sunshine and allow for pin pairing on the network.
41. disconnect from VNC, pin pair Moonlight, and connect.

Notes;
1. Every time the GPU drivers on the host get updated you will have to perform steps 30-33 replacing the files.
2. Sunshine has a tendancy to start before the display is made. I found that simply setting the Sunshine service to delayed start gets around the issue.
3. if you ever decide to delete the VM be sure to run line 1-2 from the powerline script to remove the GPU partition.
4. The host will always take priority over the GPU partition. so if the hjosts game is using more than 50% in this case the VM may suffer and run slow.