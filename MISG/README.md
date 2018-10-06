# Windows Defender Application Control + Microsoft Intelligent Security Graph App Reputation

In the Windows 1709 release last year, Mircrosoft introduced a new feature to the newly-renamed Windows Defender Application Control (which used to comprise most of Device Guard) in Windows 10: the ability to allow any applications to run that have obtained positive application reputation in Microsoft's Intelligent Security Graph cloud service, which also helps power capabilities in Windows SmartScreen, Windows Defender AV, and Windows Defender ATP.In theory, this technology could hold the potential to make adoption of application whitelisting much less painful for organizations (and individuals) by allowing commonly used Windows software from reputable publishers to run without it being necessary to have specfic rules to allow that in an whitelisting policy. This collection of sample policies, related files, and assorted notes is the result of some slightly clumsy efforts on my part to look at how well that promise translates into practice.  
______________________________________________________________________________
**Fair Warning**: Do not initially deploy "enforced" versions of the sample/starter policies here to machines that you would be really disturbed to see not boot properly, be unable to run critical applications properly, etc. Even if you deploy the audit version of the policy on a machine and nothing vital shows up in the Windows CodeIntegrity event log as being problematic. Simply put, that log does not catch everything that may render a machine unbootable. **Test the enforced policies here on machines you use for TESTING things.**    
( If you do have boot issues, Matt Graeber has written a post that contains info that has been exteremly helpful, at least for me, in troubleshooting them: https://posts.specterops.io/adventures-in-extremely-strict-device-guard-policy-configuration-part-1-device-drivers-fd1a281b35a8 )  
______________________________________________________________________________
Instructions for Using the Sample Microsoft Intelligent Graph Policies Here
I've forumlated and tested the following proceedure with the aim of getting a common set of steps that will work accross Windows 10 Enterprise, Windows 10 Pro, and, yes, Windows 10 Home. The proceedure is a little clumsy, but (at least in my testing) it reliably works:

**1.** Copy the SIPolicy.p7b file for the policy you want to test into your C:/Windows/System32/CodeIntegrity directory.
**2.** Open an elevated command or Powershell prompt and run the following commands:
      sc.exe start appidsvc
      sc.exe config appidsvc start= auto
      appidtel.exe start


