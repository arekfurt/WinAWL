# Windows Defender Application Control + Microsoft Intelligent Security Graph App Reputation

In the Windows 1709 release last year, Microsoft introduced a new feature to the newly-renamed Windows Defender Application Control (which used to comprise most of Device Guard) in Windows 10: the ability to allow any applications to run that have obtained positive application reputation in Microsoft's Intelligent Security Graph cloud service, which also helps power capabilities in Windows SmartScreen, Windows Defender AV, and Windows Defender ATP. In theory, this technology could hold the potential to make adoption of application whitelisting much less painful for organizations (and individuals) by allowing commonly used Windows programs from reputable publishers to run without it being necessary to have specific, per-application rules in a whitelisting policy. This collection of sample policies, related files, and assorted notes is the result of some slightly clumsy efforts on my part to look at how well that promise translates into practice.  

I will be adding more polices and extending the documentation of what I've seen in my testing in the days/weeks/months to come. 
______________________________________________________________________________
**Fair Warning**: Do not initially deploy "enforced" versions of the sample/starter policies here to machines that you would be really disturbed to see not boot properly, be unable to run critical applications properly, etc. Even if you do first deploy the audit version of the policy on a machine and nothing vital shows up in the Windows CodeIntegrity event log as being problematic. Simply put, the CodeIntegrity log does not catch every issue that may render a machine unbootable. **Test the enforced policies here on machines you use for TESTING things.** 
  
( If you do have boot issues, Matt Graeber has written a post that contains info that has been extremely helpful, at least for me, in troubleshooting them: https://posts.specterops.io/adventures-in-extremely-strict-device-guard-policy-configuration-part-1-device-drivers-fd1a281b35a8 )  

Also note that all the policies here are unsigned. Signing a policy and implementing signature enforcement on it should be among the last steps in the development and deployment of a WDAC policy.  
____________________________________________________________________________
**Instructions for Getting Up and Running with the MISG Policies Here**

I've formulated and tested the following procedure with the aim of getting a common set of steps that will work across Windows 10 Enterprise, Windows 10 Pro, and, yes, Windows 10 Home. The procedure is a little clumsy, but (at least in my testing) it reliably works across the versions:

**1.** Copy the SIPolicy.p7b file for the policy you want to test into your C:/Windows/System32/CodeIntegrity directory. (If you use a different drive letter for your OS root, obviously substitute that.)

**2.** Open an elevated command or Powershell prompt and run the following commands:
      sc.exe start appidsvc
      sc.exe config appidsvc start= auto
      appidtel.exe start
      
Respectively, these make sure the Application Identity Service--which is needed to interface with the MISG reputation service-- is started on the machine, make sure that service starts at boot from now on, and immediately start the appidtel executable. This isn't Microsoft's procedure for enabling MISG functionality, but I've had some issues with Microsoft's simpler version--basically, just run appidtel.exe--not working properly on Pro and Home.

**3.** Reboot. (Despite what MS says, in my experience a reboot is often necessary for WDAC + MISG to work properly even if you have the "Enabled:Update Policy No Reboot" option in place.)  

**4.** If you're in audit mode, use your device normally and, at some point, begin to look for issues with legitimate software failing in your CodeIntegrity log. If you're in enforced mode,  see if it boots (Note: I've selected options for these policies to try to minimize boot-stopping issues, and I think most PCs should start correctly, if sometimes a tad slowly, rather than failing to boot if there are issues. But no guarantees.), and then ascertain whether your PC is properly accessing the MISG service and approving known good non-Windows applications.  The easiest way to do that is to just open something that absolutely should be common enough to be approved--like Google Chrome--and see whether WDAC blocks it.

**5.** Determine how suitable WDAC + MISG is for your needs by testing applications and drivers you need to have in your environment. Keep in mind that expecting perfection--meaning you'll need to expend no effort whatsoever to tailor a baseline WDAC policy to your needs thanks to MISG auto-authorization of all desired programs--will very often be unrealistic. Also consider how much you care about the inevitable increase in exposure to "living off the land"/LoLbin bypass techniques that relying on something like MISG authorization brings. (The counter-consideration, of course, is that use of MISG may make deploying application control feasible for you where it otherwise wouldn't be.)  
____________________________________________________________________________
**Troubleshooting**

-If your device has trouble booting, access your advanced boot options menu (or a recovery menu), open a command prompt, and delete the SIPolicy.p7b file. Since signature enforcement for the policy isn't turned on, rebooting the machine will return it to normal. (If signature enforcement for the policy was enabled, then recovery would be considerably more complex.) Then take a look at the CodeIntegrity log and the other logging info Matt Graeber discusses in the link mentioned above.  

-If programs that you know *must* have positive MISG reputation are still being blocked by WDAC, and you have go Internet connectivity, then it's very likely that your device's AppID components aren't configured as they need to be. One trick that resolved this problem once or twice for me (but often doesn't seem to be necessary): create a scheduled task to actually run appidtel.exe at startup, every time. Otherwise, make sure the Appidsvc service is running by looking at Task Manager or the Services control panel, and beyond that... well, Google and MS tech forums are your friends for diagnosing AppID component problems.   

-In some cases, the primary executable for a program will be authorized by MISG but something will still go wrong when it runs, causing a crash, broken functionality, error messages, etc. Presumably this is due to components of the program not being authorized even though the primary executable is. Not much you can do with these situations if you need the application to work, except go through one of the WDAC policy processes that will result in creating specific rules for the program. Note: The error messages popped by a malfunctioning program due to this may be somewhat misleading about the cause. For example, the current universal Windows Firefox installer, as of this writing, is authorized by MISG but fails with an error about how Firefox requires Windows 7 or higher.       

____________________________________________________________________________
**Resources**

-Microsoft's official guidance on WDAC isn't always complete (despite its length) and isn't always clear.  But, at the least, the section called the "Windows Defender Application Control deployment guide" is essential reading if you're going to do any work with WDAC. Really, though, that stuff makes much more sense and is placed in context by the "design guide" that comes before it and describes (among other significant things) how WDAC policies work. Worth at least skimming the whole thing, really: https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/windows-defender-application-control

-Among outside researchers, Matt Graeber (as mentioned) has done a ton of indispensable work on Device Guard/WDAC. You won't go wrong reading any or all of it. This item contains links to the bulk of his highly useful research on the topic (while also highlighting some of WDAC's shortcomings): http://www.exploit-monday.com/2018/06/device-guard-and-application.html

____________________________________________________________________________
**What Are Some Common Applications that Have Problems with WDAC + MISG?  (As of the Date Specified)**

- Firefox Windows common installer (runs but fails to install) (10/05/2018)
(Note: Firefox browser itself works fine, though. As does the setup extractor for the Firefox Portable version.)
- Opera browser (installer runs but fails to install; installed browser sort-of works, though with errors) (10/05/2018)
- VirtualBox (WDAC + MISG still hates, hates, hates VirtualBox. If you have it installed be prepared to deal with tons of error messages about VB components in the CodeIntegrity log every boot until you create specific rule/s to allow them.) (10/05/2018) 


 

