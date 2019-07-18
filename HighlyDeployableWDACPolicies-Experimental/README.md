# Developmental-- Deployability-Focused Windows Defender Application Control Sample Policies 


**07/18 Update**: Added policies that use "%OSDRIVE%\*" to authorize arbitrary locations on the OS drive that only Admin-level accounts can write to. Seems to work, but lots more evaluation left to go. (Tip: If it doesn't work, scrutinize file and folder permissions carefully. Including for any unexpected inheritance of "write" or "modify" permissions or unelevated individual "user" account permissions (ie. even if you're logged in as account that has admin privileges). However, if a location is only writable by System, Administrators, and Trusted Installer it seems (seems) to work. New policies also include MISG auth for max flexibility, and are available with Script Enforcement/CLM on or off. 

Additionally, merged in signer rules from first dev policies intended to allow Chrome and Firefox to install successfully using their stub installers.
