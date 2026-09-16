# PowerShell-Remove-DHCP-Role-with-Active-Scope-Safety-Lock
Removes the DHCP Server role from servers listed in a CSV.


#############
How the safety lock works
The important change is that the script doesn't trust the old CSV's ScopeCount. Immediately before removing each server, it connects to that server and queries the current DHCP configuration.
If it finds:
Active scopes: 2
you'll get:
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
               ACTIVE SCOPE SAFETY LOCK
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

DHCP ROLE REMOVAL IS BLOCKED.
and nothing is removed from that server.
It also creates DHCP-Scope-Backup.csv containing the scope information it discovered before any removal.
Recommended production settings
I'd start with these:
$AllowActiveScopeOverride = $false
$AllowReboot = $false
$RemoveDHCPAuthorization = $false
That gives you a very conservative workflow:
CSV → fresh check → active scope detection → LOCK if active → explicit per-server confirmation → remove role → verify removal → separate AD authorization cleanup.
One additional safeguard worth considering is having the script refuse to remove a DHCP server if it is the only authorized DHCP server in AD, even when it has no active scopes. That would give you another layer of protection against accidentally removing the last DHCP infrastructure in the environment.
