# Windows Defender Application Control + Microsoft Intelligent Security Graph App Reputation

In the Windows 1709 release last year, Mircrosoft introduced a new feature to the newly-renamed Windows Defender Application Control (which used to comprise most of Device Guard) in Windows 10: the ability to allow any applications to run that have obtained positive application reputation in Microsoft's Intelligent Security Graph cloud service, which also helps power capabilities in Windows SmartScreen, Windows Defender AV, and Windows Defender ATP.In theory, this technology could hold the potential to make adoption of application whitelisting much less painful for organizations (and individuals) by allowing commonly used Windows software from reputable publishers to run without it being necessary to have specfic rules to allow that in an whitelisting policy. This collection of sample policies, related files, and assorted notes is the result of some slightly clumsy efforts on my part to look at how well that promise translates into practice.  
I will be adding more polices and extending the documentation of what I've see in my testing in the days/weeks/months to come. 
______________________________________________________________________________
**Fair Warning**: Do not initially deploy "enforced" versions of the sample/starter policies here to machines that you would be really disturbed to see not boot properly, be unable to run critical applications properly, etc. Even if you deploy the audit version of the policy on a machine and nothing vital shows up in the Windows CodeIntegrity event log as being problematic. Simply put, that log does not catch everything that may render a machine unbootable. **Test the enforced policies here on machines you use for TESTING things.** Note that all the policies here are unsigned. You really, really want to make sure a policy works on a particular device before you sign it and turn on enforcement of that.    
( If you do have boot issues, Matt Graeber has written a post that contains info that has been exteremly helpful, at least for me, in troubleshooting them: https://posts.specterops.io/adventures-in-extremely-strict-device-guard-policy-configuration-part-1-device-drivers-fd1a281b35a8 )  
______________________________________________________________________________
**Instructions for Getting Up and Running with the MISG Policies Here**

I've forumlated and tested the following proceedure with the aim of getting a common set of steps that will work accross Windows 10 Enterprise, Windows 10 Pro, and, yes, Windows 10 Home. The proceedure is a little clumsy, but (at least in my testing) it reliably works accross the versions:

**1.** Copy the SIPolicy.p7b file for the policy you want to test into your C:/Windows/System32/CodeIntegrity directory.
**2.** Open an elevated command or Powershell prompt and run the following commands:
      sc.exe start appidsvc
      sc.exe config appidsvc start= auto
      appidtel.exe start
(These make sure the Application Identity Service--which is needed to interface with the MISG reputation service-- is started on the machine, make sure that service starts at boot from now on, and immediately start the appid executable. This isn't Microsoft's proceedure for enabling MISG functionality, but I've had some issues with Microsoft's simpler version--basically, just run appidtel.exe--not working properly on Pro and Home.)
**3.** Reboot. (Despite what MS says, in my experience a reboot is often necessary for WDAC + MISG to work properly even if you have the "Enabled:Update Policy No Reboot" option in place.   
**4.** If you're in audit mode, use your device normally and, at some point, check your CodeIntegrity log. If you're in enforced mode,   see if it boots (I've selected options for these policies to try to minimize boot-stopping issues, and I think most PCs should start correctly, if sometimes a tad slowly, instead of fail to boot if there are issues. But no guarantees.), and then see how the MISG service treats your applications when you try to run them.    


