# Highly Deployable Windows Defender Application Control Policies for Testing --  Pre-Alpha

Main polices and block rule policies for testing on Windows 10 1903. Designed to impose application control over unknown/arbitrary binaries, as well as optionally over scripting, while being good starter policies for wide deployment with (relatively) few modifications required. Main policies use a combination of new 1903 admin-only write filepath rules and enablement of authorization of applications that have valid Microsoft Intelligent Security Graph app reputation. Note: Any intended use of any such policies presumes ordinary users without security expertise do not have administrative rights. Users with administrative rights may run any files as they choose from filepath locations allowed in these policies.

The block rule policies are based on the Microsoft recommended blocklist for Win 10 1903 (current as of July 5, 2019) except they also:

-allow mshta and wmic
(allowing actual use of mshta may also require "Disabled: Script Enforcement" option to be set)

-block various older WDAC scripting bypasses using rundll32, regsvr, and regasm

-block mavinject

-block use of flash.ocx by Word and Excel

-block cmstp

-block wdigest 

Deploy these policies manually by dropping the two .cip files for your choice of policy combinations, as appropriate, into the [ OS drive ]\Windows\System32\CodeIntegrity\CiPolicies\Active folder. **Do not rename the .cip policy files.** They must be named with their policy GUIDs to enable properly. The uncompiled xml for the policies is also provided.  

**IMPORTANT NOTE: THESE POLICIES ARE CURRENTLY AT A VERY EARLY STAGE IN TESTING, BOTH IN EVALUATING WHETHER THEY BLOCK THINGS THEY SHOULD NOT AND WHETHER THEY BLOCK THE THINGS THEY SHOULD. EVEN MORE STONGLY THAN WITH OTHER POLICIES POSTED IN THIS REPO, I DO NOT RECOMMEND DEPLOYING THESE IN PRODUCTION WITHOUT EXTENSIVE TESTING AND MODIFICATIONS AS NECESSARY TO SUIT YOUR ENVIRNOMENT/S. AS WITH ANY WINDOWS APPLICATION WHITELISTING/APPLICATION CONTROL POLICIES, BE PREPARED FOR THE PROSPECT THE ENFORCED POLICIES HERE MAY CAUSE BOOTING DIFFICULTIES IF NOT TESTED IN AUDIT MODE FIRST.**    
