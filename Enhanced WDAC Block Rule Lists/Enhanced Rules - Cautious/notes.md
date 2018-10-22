**The "Cautious" Ruleset**

This list draws the vast majority of its block rules from two sources: Microsoft's recommended block list, and guidance produced on Windows Defender Application Control (limited, admittedly) and EMET ASR rules by the what was, until recently, the Information Assurance Division of the NSA (now NSA Cyber). These rules were almost certainly chosen by Microsoft and the NSA with the aim of minimizing restrictions on the legitimate needs of the vast bulk of users in mind. However, beyond that factor I have been selective about which rules suggested by the NSA in their EMET ASR guidance to use. (For instance, the current version of this ruleset does not block Office applications from loading packager.dll, which would block malicious Office OLE attacks but while perhaps blocking some non-trivial amount legitimate use.) 
That said, I have added a few narrowly targeted rules of my own. And this ruleset is likely to get more robust in the near future, rather than less so. Still, I intend this to remain a pretty "collateral damage-conscious" option.
_________________________________________________________
**What's different from the current (10/22/2018) Microsoft ruleset?**

-blocks Squidblydoo (from @subtee) in rundll32.exe and regsvr32.exe (NSA EMET guidance)
-blocks wdigest.dll (NSA WDAC guidance)
-blocks Flash from loading in Word and Excel (but not Powerpoint) (NSA EMET guidance) 
-blocks scriptrunner.exe
-blocks the mavinject.exe processes
(background: https://posts.specterops.io/mavinject-exe-functionality-deconstructed-c29ab2cf5c0e )


Note: If the scriptrunner and/or mavinject rules are used on a machine running applications with AppV it's possible you could see an issue. (If you don't know or aren't quite sure what AppV is, you almost certainly don't have to worry about that. And you can always just copy a troublesome unsigned policy out of the CodeIntegrity folder and reboot. But when in doubt audit first.)
