

# Deployability-Focused Windows Defender Application Control Starter Policies for Testing and Refinement

Windows 10 1903 introduces several capabilities that offer the promise of (hopefully) significantly lightening the burdens of deploying and maintaining application whitelisting/application control without sacrificing too much security robustness. The starter policies offered here are designed to both provide some functioning examples of how some of these new capabilities--such as multiple policy support, the ability to create filepath allow rules for locations that are admin-write-only, and the ability to choose whether Constrained Language Mode will or will not be on--may be used. And also to provide some practical policies that can give those interested in getting started with WDAC policy auditing and customization for their environments a bit of a head start, in terms of lessening the overall amount of refinement work they'll need to put in to eventually get to policies that are really usable. (For detection in audit mode, or detection and blocking in enforced mode).

Currently, four policy set combinations are here, with audit and enforced policies available for each. For each of these eight overall policy set options there are two .cip policy files (one main policy and one block list policy) for each:

1. A "Core" policy combo with Script Enforcement (ie. CLM) on.
2. A Core policy combo with Script Enforcement disabled.
3. A "Standard" policy combo (Core + Intelligent Security Graph authorization) with Script Enforcement on. **Recommended.**
4. A Standard policy combo with Script Enforcement disabled.

Each policy also is accompanied by the .xml file that was compiled to create it.

**Important Note:** MS has confirmed that a bug exists that causes serious deauthorization issues of vital Windows components if you attempt to merge certain allow rule lists and certain deny rule lists. The rules used in these policies trigger this bug. **Until further notice, DO NOT MERGE THE MAIN AND BLOCK LIST POLICY FILES HERE.** Just use the two separate policies included for each policy combo.

Another note: Any intended use of any such policies presumes ordinary users without security expertise do not have administrative rights. Users with administrative rights may run any files as they choose from filepath locations allowed in these policies.


I have at least lightly tested these policies on both Windows 10 Enterprise and Windows 10 Pro test machines and personal devices. With reasonably fair results. That said, I'd call attention to the word "lightly" there. These are basically alpha quality policies at this point in terms of maturity. **These policies are for TESTING and to provide a starting basis for customization. Do not even think of putting them into enforced production use on important machines, at least without doing both extensive audit testing and refining and then a cautious enforced test deployment first.** Indeed, I include enforced versions of policies here largely because, in my experience with 1903 so far, there may be a few issues which, despite the best audit testing, only materialize once enforcement is turned on.   

In terms of which starter policy set is "best", there's clearly not one always-right answer. Personally, I tend to believe that having a good set of core allow rules, a good set of block rules, MISG authorization on to automatically allow many applications that have known good reputation to run, along with CLM/Script Enforcement enabled has perhaps the best potential to produce refined policies in many environments that are effective against the vast bulk of attacker malware threats in the wild while requiring as little effort required in creating, refining, and maintaining policies as possible. But your judgment and circumstances may vary, of course. Thus, there are options. (With more likely to come.)

The core rules included in the policies here will most certainly evolve as I have time to further study and experiment with both enhancements to what they allow and to malicious behaviors they can block. (If you’re interested, keep an eye on the brand new dev branch to see what may be coming here.)   

Note: Any intended use of any such policies presumes ordinary users without security expertise do not have administrative rights. Users with administrative rights may run any files as they choose from filepath locations allowed in these policies.

## July 15 Improvements

-created new Core (ie. without MISG enabled)-Script Enforcement Disabled audit and enforced policy sets
-merged in EKU and EKUCert-related signing stuff from official MS sample policies (hadn't done so previously due to bug assessment efforts)
-recompiled all 16 (8 main, 8 block list) policies with new and unique GUIDs, descriptive names, and Policy ID strings
-renamed files and folders descriptively 
-further tested policies (found and reported an apparent hta script enforcement logging bug to MS)
-documentation improvements 

##Block List Rules  

The block rule policies are currently identical for each policy set in what processes and modules they block, although not in the policy rule options they use. Use each block list file with its main policy file in the same folder. The core block list is based on the Microsoft recommended blocklist for Win 10 1903 (current as of July 5, 2019) except it also:

-allows mshta and wmic
(allowing actual use of mshta may also require "Disabled: Script Enforcement" option to be set)

-blocks various older WDAC scripting bypasses using rundll32, regsvr, and regasm

-blocks mavinject

-blocks use of flash.ocx by Word and Excel

-blocks cmstp

-blocks wdigest 

##Instructions 
Deploy these policies manually on a test machine by dropping the two .cip files for your choice of configuration, as appropriate, into the C:\Windows\System32\CodeIntegrity\CiPolicies\Active folder. **Do not rename the .cip policy files.** They must be named with their policy GUIDs to enable properly.  (Note: For sake of simplicity the filepath rules in the policies currently assume your OS drive is the C: drive. I will attempt to switch to using environmental variables for future versions.) 


**Issue reports and specific suggestions for rules/etc. that you'd like to see included are very welcome. Pull requests welcome. Feedback in general is welcome.**
