**"Balanced" Ruleset**

More documentation coming soon. Suffice to say for now that, in addition to incorporating the rules in the "Cautious" ruleset, this ruleset currently:

-blocks cmstp.exe (evasive way to download & execute scripts; being used in the wild)
-blocks Flash in Powerpoint
-blocks InstallUtil.exe (allows vectors of attack that can potentially abuse .Net to bypass whitelisting; worth doing at least until .Net hardening option actually works; most likely of these to have undesirable side effects on legitimate things)
-blocks ability to use (via scrobj.dll, scrrun.dll, mshtml.dll) Squiblydoo with regasm.exe 

Mainly because of the block on InstallUtil.exe, I especially recommend testing this block ruleset in audit mode before enabling enforcement. At the least, I recommend being prepared to quickly remove an enforced policy from the CodeIntegrity folder and reboot on any or all machines an enforced policy is applied to in case problems arise.
