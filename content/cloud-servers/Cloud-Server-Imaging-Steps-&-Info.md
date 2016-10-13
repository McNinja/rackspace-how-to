   ---
   permalink: Cloud Server Imaging Steps & Info/

   audit_date: 2016-10-13

   title: Cloud Server Imaging Steps & Info

   type: article

   created_date: '2016-10-13'

   created_by: Reese McJunkin, Aaron Davis, Brian King 

   last_modified_date: '2016-10-13'

   last_modified_by: Reese McJunkin

   product: Cloud Servers

   product_url: cloud-servers
   ---
   
   Rackspace Cloud Images: Frequently Asked Questions
Rackspace Cloud Servers other than OnMetal and “Boot From Volume” Flavors use a format known as Virtual Hard Disk(VHD) to store their system disks. When your server writes to the its disk, the VHD writes each difference between your original, base hard drive and the current system. Because of this, deleting files off your server's hard disk actually causes the underlying VHD to grow slightly. In Rackspace Cloud VHD’s will only grow in size and will generally be slightly larger than the underlying filesystem at its largest point, example below: 
	Base Cloud Server Build - Operating System ‘df -h’ reports 1.8 GB of usage // VHD size is 2.5 GB on the backend 
	Cloud Server, Some data on server - Operating Systems ‘df -h’ reports 100 GB of usage // VHD size is 108 GB on the backend 
	Cloud Server, Had a large amount of data at one time - The server use to have 100 GB of data but 95 GB’s of data was deleted, the Operating System reports 5 GB of usage // VHD size is 115 GB on the backend
More information on the VHD format and the imaging process can be found here (Understanding the Cloud Imaging Process) - https://community.rackspace.com/products/f/25/t/3778)

What actually happens when I create an image?
The steps below outline the cloud Server imaging prices at a high level, a more in depth guide can be found here (https://community.rackspace.com/products/f/25/t/3778). 
Step 1 / 0% as reported by the API - The image is queued for creation or preparing to start, a Coalesce is preformed to try and eliminate duplicate data between the backend VHD’s. 
Step 2 / 25% as reported by the API - The image is currently being created, this step is saving the backend VHD file & compressing it for step 3
Step 3 / 50% as reported by the API - The image is currently being uploaded to Cloud Files. This step will generally take the longest as we have to transfer the data from the backend hypervisor to Cloud Files. Depending on the VHD size / the largest your cloud server file system has been then can take some time. 

How long should it take to create an image?
A rule of thumb is once that data is actually being backed up (step 2 & 3) this generally takes 2 minutes per GB of data being uploaded (Again, this is the size of the underlying VHDs, rather than the output of df -h). This is based off how much data the servers file system contained when it was at it largest.  Several factors affect imaging time:
	- 	Daily / Weekly Imaging & Coalescing. A Coalesce is a backend operation preformed during step 1, this attempts to merge duplicate data in the VHD chain. If there is a large amount of differential data between the VHD’s this can take some time to complete and the image will only start once this is done.
	⁃	File System Size. how much disk space is taken up on your Cloud Server / How much at a did the server have when the file system was at its largest
	- 	File System Activity. Reads will not be highly impacting, but writes can make the image / coalesce take much longer. 
	⁃	Server Age. (the older it is, the larger the VHD as noted above)
	⁃	Cloud Server Flavor. Because public cloud is a shared environment, resources such as disk and network are shared. Newer Rackspace Cloud flavors such as General Purpose, Performance, and I/O have greater I/O and network resources.

Why did my image fail? 
Age of Cloud Server - Older flavor classes such as Standard / Classic live on older hardware that have limited network bandwidth for imaging operations, which can cause imaging to take longer to complete. There is no way around this other than to migrate to a newer flavor type, such as General Purpose, or to use a file level backup such as Cloud Backup. 
Coalesce Timeout - If you do not image your cloud server frequently or have large amount of data being changed, added, or modified this can all increase the completion time of the coalesce. If this timeout is hit, simply wait and issue the image again. If you see this issue repeatedly, it may be necessary to limit filesystem activity while imaging. If this continues to fail support can trigger a coalesce outside of a Cloud Server Image. 
And as always, if none of these failure scenarios seem to fit, you can check https://status.rackspace.com/ to determine if there is a larger impacting issue affecting your imaging attempt.
