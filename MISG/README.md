# Windows Defender Application Control + Microsoft Intelligent Security Graph App Reputation

In the Windows 10 1709 release Microsoft introduced a new feature to the newly-renamed Windows Defender Application Control: the ability to allow any applications to run that have obtained positive application reputation in Microsoft's Intelligent Security Graph cloud service.  In theory, WDAC (which now comprises most, but not all, of the functionality that used to fall under the label "Device Guard" pre-1709) when integrated with MISG could hold the potential to make adoption of application whitelisting much less painful for organizations and individuals by allowing commonly used Windows programs from reputable publishers to run without it being necessary to have specific, per-application or per-publisher rules for them specifically set out in a whitelisting policy. (MISG also helps power capabilities in Windows SmartScreen, Windows Defender AV, and Windows Defender ATP.) This collection of sample policies, related files, and assorted notes is the result of my somewhat newb-ish efforts to look at how well that promise is translating into practice so far.  

Currently, the policies hosted here are:

-A baseline WDAC policy, in audit and enforced versions, that authorizes the components of Windows and enables MISG authorization for other programs. (Note: The particular WDAC options that are and are not included in this policy result from efforts to mitigate issues that I found during my testing. I'll post here a detailed explanation of what options I put in/left out and why shortly.)   

-That baseline policy merged with Microsoft's recommended process and script blocklist ( https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/microsoft-recommended-block-rules ), in audit and enforced versions.

I will be adding more polices and extending the documentation of what I've seen in my testing in the days/weeks/months to come. I more than welcome you to post any suggestions and especially your notes about aspects of the policies that are causing problems in your environment/s on the Issues page, and will try to respond to them in a timely manner.

**IMPORTANT SECURITY NOTE #1:** Although it's not in any written documentation [at least as of 11/05/2018], according to a presentation given by Microsoft's Jeffery Sutherland at the Ignite 2018 conference on this topic ( video here: https://myignite.techcommunity.microsoft.com/sessions/64537#ignite-html-anchor ), an intentional "bypass" to MISG app reputation checking with WDAC exists in Windows 10. In short, where a user attempts to run a program from the Internet that lacks established reputation and gets a warning from the SmartScreen feature in Windows about that fact, if the user then chooses to click through that SmartScreen warning WDAC will--in theory--also allow that program to run. I say "in theory" because in my own testing thus far whether or not that actually happens has been somewhat unpredictable, with MISG enforcement being circumvented on some occasions but not others, and sometimes with no obvious reasons for the different behavior. That said, there's a pretty good chance that if you're implementing a WDAC with MISG policy in enforced mode you probably don't *want* a user's actions to easily render your policy protections moot. And my testing does confirm that even a standard user can trigger this “bypass”.

Fortunately, stopping this seems as straightforward as setting the SmartScreen action setting for unknown applications from "warn" to "block". This can easily be done in the Windows Security Center UI locally, through group policy, and through other mechanisms. 

**IMPORTANT SECURITY NOTE #2:**
The sample policies contained here are unsigned (and not backed by the Virtualization-Based Security protection that is available in Windows 10 if you meet the hardware requirements for it).  This is a practical necessity here: If I signed these policies myself and set the policy options to require any new policies bear that same signature, it would become substantially more of a burden--though hardly impossible, assuming you can access a UEFI menu-- to deactivate or replace one of them on your own equipment when you wished to do so.  But the fact they are unsigned is a double-edged sword. On the one hand, it is much easier to try out and then delete or replace a policy if it causes a problem, whether temporarily (see the “Concluding Implementation Advice” section below) or permanently. Microsoft has posted fairly straightforward instructions for doing so  ( https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/disable-windows-defender-application-control-policies ), but they essentially boil down to taking the SIPolicy.p7b file out of your CodeIntegrity folder and, if necessary, [EFI System Partition]\Microsoft\Boot folder and rebooting. On the other hand, this also means that a knowledgeable attacker who can gain administrative rights can do the same thing.  As is true with pretty much all other application control/whitelisting regimes available (AppLocker, Carbon Black, etc.) besides WDAC/Device Guard, of course.

In my own opinion, a reasonable course if you’re interested in the capabilities of WDAC/Device Guard to combat attackers who are able to gain admin rights is to first extensively test and then deploy unsigned WDAC policies, refine them and work out all significant compatibility issues that are apparent, and then deploy signed policies and configure VBS (on machines that support it). But if you’re inclined to take a more aggressive approach, there is as mentioned, a procedure that allows a locally-present user to disarm even a signed policy. (See the same link.) But I wouldn’t particularly recommend that, at least for organizations. 

______________________________________________________________________________
**Fair Warning**
Do not initially deploy "enforced" versions of even the unsigned sample/starter policies here to machines that you would be really disturbed to see not boot properly, be unable to run critical applications properly, etc. Even if you do first deploy the audit version of the policy on a machine and nothing vital shows up in the Windows CodeIntegrity event log as being problematic. Simply put, the CodeIntegrity log does not catch every issue that may render a machine unbootable. Moreover, complex applications can have a ton of components that can trip CodeIntegrity rules if they aren’t all registered and authorized correctly by MISG. **Test the enforced policies here on machines you use for TESTING things.** This is not one of those occasions where "Screw it. Let's just push this straight to production." is taking risks you probably want to take, at least if "production" is really important.
  
( If you do have boot issues, Matt Graeber has written a post that contains info that has been extremely helpful, at least for me, in troubleshooting them: https://posts.specterops.io/adventures-in-extremely-strict-device-guard-policy-configuration-part-1-device-drivers-fd1a281b35a8 )  

____________________________________________________________________________
**Instructions for Getting Up and Running with the MISG Policies Here**

I've formulated and tested the following procedure with the aim of getting a common set of steps that will work across Windows 10 Enterprise, Windows 10 Pro, and, yes, Windows 10 Home. The procedure is a little clumsy, but (at least in my testing) it reliably works across the versions:


**1.** Download the SIPolicy.p7b file for the policy you want to test and copy it into your C:/Windows/System32/CodeIntegrity directory. (If you use a different drive letter for your OS drive, obviously substitute that.)

**2.** Open an elevated command or Powershell prompt and run the following commands:

      sc.exe start appidsvc
      
      sc.exe config appidsvc start= auto
      
      appidtel.exe start
      
Respectively, these commands make sure the Application Identity Service--which is needed to interface with the MISG reputation service-- is started on the machine, set that service to start at boot from now on, and immediately start the appidtel executable. This isn't Microsoft's procedure for enabling MISG functionality, but I've had some issues with Microsoft's simpler version--basically, just run appidtel.exe--not working properly on Pro and Home.

**3.** Reboot. Despite what MS says, in my experience a reboot is often necessary for WDAC + MISG to work properly, especially on non-Enterprise versions of Windows. Even if you have the "Enabled:Update Policy No Reboot" option in place, as these policies do.  Note: This reboot may take substantially longer than a normal one if you have a driver issue that trips the "Enabled:Boot Audit on Failure" option. **I highly recommend you keep the Enabled:Boot Audit on Failure option in place, at least for initial testing of an enforced policy.** Better a device that boots slowly than not at all.   

**4.** If you're in audit mode, use your device normally and, at some point, begin to look for issues with legitimate software getting flagged as "unauthorized" in your CodeIntegrity log, as well as .msi and scripts flagged in the AppLocker log. (Which will get put there even if you don’t use AppLocker as such.) If you're in enforced mode, see if your machine boots (Note: I've selected options for these policies to try to minimize boot-stopping issues, and I think most PCs should start correctly, if sometimes a tad slowly, rather than failing to boot if there are issues. But no guarantees.), and then ascertain whether your PC is properly accessing the MISG service and approving known-good non-Windows applications.  The easiest way to do that is to just open something you already have installed that absolutely should be common enough to be approved--like Google Chrome--and see whether WDAC blocks it.

**5.** Determine how suitable WDAC + MISG is for your needs by testing applications and drivers you need to have in your environment. Keep in mind that expecting perfection--meaning you would need to expend no effort whatsoever to tailor a baseline WDAC policy to your needs thanks to MISG auto-authorization of all desired programs--will often be unrealistic. Also consider how much you care about the inherent increase in exposure to "living off the land"/LoLbin bypass techniques that relying on something like MISG authorization brings vs. only using specific rules. (The counter-consideration, of course, is that use of MISG may make deploying application control feasible for you where it otherwise wouldn't be.)  
____________________________________________________________________________
**Troubleshooting**

-If your device has trouble booting, access your advanced boot options menu (or a recovery menu), open a command prompt, and delete the SIPolicy.p7b file. Since signature enforcement for the policy isn't turned on, rebooting the machine will return it to normal. Then take a look at the CodeIntegrity log and the other logging info Matt Graeber discusses in the link mentioned above.  


-If programs that you know *must* have positive MISG reputation are still being blocked by WDAC, and you have go Internet connectivity, there’s a good chance that your device's AppID components aren't configured as they need to be. One trick that resolved this problem once or twice for me (but often doesn't seem to be necessary): create a scheduled task to actually run appidtel.exe at startup, every time. Otherwise, make sure the Appidsvc service is running by looking at Task Manager or the Services control panel, and beyond that... well, Google and MS tech forums are your friends for diagnosing AppID component problems.   

-In some cases, the primary executable for a program will be authorized by MISG but something will still go wrong when it runs, causing a crash, broken functionality, error messages, etc. Presumably ,as I mentioned, this is due to components of the program not being authorized even though the primary executable is. Not much you can do with these situations if you need the application to work, except go through one of the WDAC policy processes that will result in creating specific rules for the program. Note: The error messages popped by a malfunctioning program due to this may be somewhat misleading about the cause. For example, the current universal Windows Firefox installer [tested as of 10/05/2018] is authorized by MISG but fails with an error about how Firefox requires Windows 7 or higher.       

____________________________________________________________________________
**Resources**

-Microsoft's official guidance on WDAC isn't always complete (despite its length) and isn't always clear.  But, at the least, the section called the "Windows Defender Application Control deployment guide" is essential reading if you're going to do any work with WDAC. Really, though, that stuff makes much more sense and is placed in context by the "design guide" that comes before it and describes (among other significant things) how WDAC policies work. Worth at least skimming the whole thing, really: https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/windows-defender-application-control

-Among outside researchers, Matt Graeber (as mentioned) has done a ton of indispensable work on Device Guard/WDAC. You won't go wrong reading any or all of it. This item contains links to the bulk of his highly useful research on the topic (while also highlighting some of WDAC's shortcomings): http://www.exploit-monday.com/2018/06/device-guard-and-application.html

____________________________________________________________________________
**Concluding Implementation Advice**

-Thus far in my testing, WDAC with MISG integration has worked considerably better in my testing as a as a “lockdown” mechanism versus starting with a fresh OS installation and adding software. By that I mean that I have found it better to install the software you need on a machine and then enforce a WDAC + MISG policy, rather than putting an enforced policy in place first and then trying to download and install the software you need. Ideally, this wouldn’t be the case; certainly it would be preferable to start with a policy in place and be able to leave it in place. And I’m sure Microsoft is working toward that end. However, for the present--and, again, in my experience--MISG app reputation authorization seems far better at allowing already installed software to run than it is with allowing installers to run and complete their work successfully.

-If you have problems installing software that you know must have positive app reputation, using a direct download, full-size traditional installer version instead of a modern “skinny installer” that downloads components from the web can often help, I’ve found. But not always. [See my 11/01/2018 note on Adobe Reader below.]  

-It is fortunate that if you’re locally administering a machine and using an unsigned, enforced WDAC policy it is generally quite feasible to occasionally disable an the policy, install the software you need, and reenable that policy. Reboots may be required, but otherwise things aren’t too painful. On a standalone machine. However, trying to manage that at deployment scale in an organization, using Group Policy, Intune, scripts, or whatever, is likely to be a different ballgame.  Another good reason to ensure that you go through a good testing process for all changes to enforced policies on representative test machines before deployment if you’re in an organization that, well, really cares about things working. 

____________________________________________________________________________
**What Are Some Common Applications that Have Problems with WDAC + MISG?  (As of the Date Specified)**

For the reader's information, these are some significant issues that I've encountered in my test environment/s. I make no absolute guarantee that they would be replicated in yours. But, at least, they're things to watch out for.

- Firefox Windows common installer: runs but fails to install successfully. (10/05/2018)
(Note: Firefox browser itself works fine, though. As does the setup extractor for the Firefox Portable version.)
- Opera browser installer: runs but fails to install properly. (The installed browser sort-of works, though with errors.) (10/05/2018)
- VirtualBox (WDAC + MISG still hates, hates, hates VirtualBox. If you have it installed be prepared to deal with tons of error messages about VB components in the CodeIntegrity & AppLocker logs until you create specific rule/s to allow them.) (10/05/2018)
- Adobe Reader’s “skinny installer” and directly downloaded full-size installers run but do not successfully complete installation. (11/01/2018)
- Humorously, the process that hosts the Windows Event Viewer, mmc.exe, tries to load unauthorized components every time Event Viewer is opened. These being blocked does not seem to actually impact Event Viewer/MMC functionality, at least as far as I have seen. But it will be one of a number of sources of unauthorized loading attempts by stock Windows components that will clutter up your logs. (11/05/2018) 

 

