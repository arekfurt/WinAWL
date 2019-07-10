# Highly Deployable Windows Defender Application Control Sample Policies --  Pre-Alpha/Developmental

Main polices and block rule policies for testing on Windows 10 1903. Designed to impose application control over unknown/arbitrary binaries, as well as optionally over scripting, while being good starter policies for wide deployment with (relatively) few modifications required. Main policies use a combination of new 1903 admin-only write filepath rules and enablement of authorization of applications that have valid Microsoft Intelligent Security Graph app reputation. Note: Any intended use of any such policies presumes ordinary users without security expertise do not have administrative rights. Users with administrative rights may run any files as they choose from filepath locations allowed in these policies.

**Update (07/08)**: Fixed an error in the "Disabled: Script Enforcement" audit and enforced policy combinations that may have prevented script enforcement from actually being disabled. Also, for those not familiar with or fond of allowing programs that have good app reputation with the Microsoft Intelligent Security Graph to run added audit and enforced sample policy combinations that do not have that option enabled.

**Update (07/10)**: Fixed a dumb error in the "EnforcedPolicies" main policy that prevented said policy from being, well, enforced. Also changed folder names of what I currently consider the baseline policies of this effort (MISG authorization enabled; script enforcement enabled) to "Standard" to indicate that clearly. 

**Warning:** These policies are not divided between two policy files per configuration just for the sake of logic (main policy + block rules policy) or independent updatability. I've hit what appears to be a bug that appears to result from merging the policies together into one. The effects of which somehow lead to critical DLLs as not being recognized as authorized. Meaning in enforced mode your test machine won't boot. **Merging main policies and block list policies here is currently not recommended, at least in enforcement mode. Deploying the policies using the instructions provided below IS very much recommended.**  

The block rule policies are based on the Microsoft recommended blocklist for Win 10 1903 (current as of July 5, 2019) except they also:

-allow mshta and wmic
(allowing actual use of mshta may also require "Disabled: Script Enforcement" option to be set)

-block various older WDAC scripting bypasses using rundll32, regsvr, and regasm

-block mavinject

-block use of flash.ocx by Word and Excel

-block cmstp

-block wdigest 

**Instructions:** Deploy these policies manually on a test machine by dropping the two .cip files for your choice of configuration, as appropriate, into the C:\Windows\System32\CodeIntegrity\CiPolicies\Active folder. **Do not rename the .cip policy files.** They must be named with their policy GUIDs to enable properly. The uncompiled xml for the policies is also provided. (Note: For sake of simplicity the filepath rules in the policies currently assume your OS drive is the C: drive. I will attempt to switch to using environmental variables for future versions.) 

**IMPORTANT NOTE: THESE POLICIES ARE CURRENTLY AT A VERY EARLY STAGE IN TESTING, BOTH IN EVALUATING WHETHER THEY BLOCK THINGS THEY SHOULD NOT AND WHETHER THEY BLOCK THE THINGS THEY SHOULD. EVEN MORE STONGLY THAN WITH OTHER POLICIES POSTED IN THIS REPO, I DO NOT RECOMMEND DEPLOYING THESE IN PRODUCTION WITHOUT EXTENSIVE TESTING AND MODIFICATIONS AS NECESSARY TO SUIT YOUR ENVIRNOMENT/S. AS WITH ANY WINDOWS APPLICATION WHITELISTING/APPLICATION CONTROL POLICIES, BE PREPARED FOR THE PROSPECT THE ENFORCED POLICIES HERE MAY CAUSE BOOTING DIFFICULTIES IF NOT TESTED IN AUDIT MODE FIRST.**    
