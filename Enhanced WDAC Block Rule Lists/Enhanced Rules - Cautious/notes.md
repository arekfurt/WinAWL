**The "Cautious" Ruleset**

This list draws the vast majority of its block rules from two sources: (1)Microsoft's recommended block list, all rules included, and; (2) a few different items of guidance produced on Device Guard (limited, admittedly) and EMET Attack Surface Reduction rules by the what used to be called the Information Assurance Division of the NSA (now NSA Cyber). These rules were chosen by Microsoft and the NSA, no doubt, with the aim of minimizing impact on the legitimate needs of the vast majority of users. However, beyond their concern with that I have been selective myself about which rules suggested by the NSA in their EMET ASR guidance to replicate. (For instance, the current version of this ruleset does not block Office applications from loading packager.dll, which would block malicious Office OLE attacks but while perhaps blocking some non-trivial amount legitimate use.) That said, I have added a few narrowly targeted rules of my own. 

This ruleset is likely to get more robust in the near future, rather than less so. Still, I intend this to remain an option that is decidedly conscious of avoiding "collateral damage." If you want something more protective but carries a bit more risk of unwanted negative side effects (although I believe still acceptable for most real world general use cases), check out my "balanced" custom ruleset.  

**Update (11/02/2018):** Cleaned the ruleset xml up a good bit, but didn't substantively change any of the rules. Thus, I'm keeping the "version" (if you want to use that term) at 1.0. Did add policy files incorporating the rules, which are just a product of merging the custom block ruleset with Microsft's AllowAll starter WDAC policy that ships with Win 10 Enterprise. Just in case anyone else wants to test out the block rules independently of any base whitelisting policy restrictions. 
_________________________________________________________
**What's different from the current (10/22/2018) Microsoft ruleset?**

-blocks Squidblydoo (from @subtee) in rundll32.exe and regsvr32.exe (NSA EMET guidance)
-blocks wdigest.dll (NSA WDAC guidance)
-blocks Flash from loading in Word and Excel (but not Powerpoint) (NSA EMET guidance) 
-blocks scriptrunner.exe
-blocks the mavinject.exe processes
(background: https://posts.specterops.io/mavinject-exe-functionality-deconstructed-c29ab2cf5c0e )


Note: If the scriptrunner and/or mavinject rules are used on a machine running applications with AppV it's possible you could see an issue. (If you don't know or aren't quite sure what AppV is, you almost certainly don't have to worry about that. And you can always just copy a troublesome unsigned policy out of the CodeIntegrity folder and reboot. But when in doubt audit first.)
