# TrueNAS SMB on Windows - Troubleshooting Guide

**Correct TrueNAS IP:** `192.168.20.112`  
**Problem solved:** Windows could see SMB shares but could not access media shares due to wrong parent dataset permissions.

---

## 1. Quick Problem Summary

Windows could open:

```text
\\192.168.20.112
```

and show shares, but could not open:

```text
\\192.168.20.112\english_movies
\\192.168.20.112\movies
\\192.168.20.112\series
```

The final confirmed issue was:

```text
Parent dataset tank/media had wrong owner/permissions.
```

---

## 2. Troubleshooting Logic

Use this logic:

```text
Cannot reach \\192.168.20.112
= network, VLAN, firewall, IP, or SMB service problem.

Can reach \\192.168.20.112 but cannot see shares
= SMB service/share configuration problem.

Can see shares but cannot open one or more
= TrueNAS dataset permissions or ACL problem.

Can open some shares but not others
= specific dataset or parent dataset permission problem.

Password accepted but access denied
= authentication works, authorization/permissions fail.
```

---

## 3. Step 1 - Check TrueNAS IP

Use:

```text
192.168.20.112
```

Do not use old IP references such as:

```text
192.168.10.112
```

---

## 4. Step 2 - Test Network Connectivity from Windows

Open Command Prompt:

```cmd
ping 192.168.20.112
```

Good result:

```text
Reply from 192.168.20.112
```

If ping fails, check:

- Windows network connection.
- pfSense firewall rules.
- VLAN routing.
- TrueNAS IP address.
- Whether TrueNAS is powered on.

---

## 5. Step 3 - Test SMB Port

Open PowerShell:

```powershell
Test-NetConnection 192.168.20.112 -Port 445
```

Good result:

```text
TcpTestSucceeded : True
```

If false, check:

- TrueNAS SMB service.
- Firewall rules.
- VLAN rules.
- Windows network profile.

---

## 6. Step 4 - Check SMB Service in TrueNAS

In TrueNAS:

```text
System Settings -> Services
```

Check:

```text
SMB = RUNNING
```

If needed, restart SMB after changing users/groups/permissions.

---

## 7. Step 5 - Check SMB Shares in TrueNAS

In TrueNAS:

```text
Shares -> SMB
```

Known shares:

```text
english_movies  -> /mnt/tank/media/English_movies
home_przemek    -> /mnt/tank/home_przemek
iso-library     -> /mnt/tank/iso-library
movies          -> /mnt/tank/media/movies
series          -> /mnt/tank/media/Series
```

Make sure the share is enabled.

---

## 8. Step 6 - Clear Old Windows SMB Connections

Show connections:

```cmd
net use
```

Remove a mapped drive:

```cmd
net use X: /delete /y
```

Remove all SMB mappings:

```cmd
net use * /delete /y
```

If Windows says:

```text
The network connection could not be found.
```

that is not serious. It means that specific connection did not exist.

---

## 9. Step 7 - Check Stored Windows Credentials

List stored credentials:

```cmd
cmdkey /list
```

Delete stored NAS credential if present:

```cmd
cmdkey /delete:192.168.20.112
```

If Windows says:

```text
CMDKEY: Element not found.
```

it means there was no saved credential under that exact name.

You can also check:

```text
Control Panel -> Credential Manager -> Windows Credentials
```

Look for:

```text
192.168.20.112
truenas
TrueNAS
```

---

## 10. Step 8 - Force Login as TrueNAS User

Use this command:

```cmd
net use X: \\192.168.20.112\english_movies /user:192.168.20.112\przemek * /persistent:yes
```

What it does:

```text
X:
```

maps the share as drive X.

```text
/user:192.168.20.112\przemek
```

uses the TrueNAS user `przemek`.

```text
*
```

forces a password prompt.

```text
/persistent:yes
```

reconnects after restart/login.

---

## 11. Step 9 - If Mapping Works but Opening Fails

If the command says:

```text
The command completed successfully.
```

but File Explorer says:

```text
You do not have permission to access...
```

then the password is correct, but the dataset permissions are wrong.

This means:

```text
Fix TrueNAS permissions.
```

---

## 12. Step 10 - Check Parent Dataset First

For these shares:

```text
english_movies
movies
series
```

the parent dataset is:

```text
tank/media
```

Check in TrueNAS:

```text
Storage -> Datasets -> tank -> media -> Permissions
```

The user must be able to traverse the parent dataset.

For directories:

```text
Execute = Traverse / Enter folder
```

Without this permission, Windows can see the share but cannot open it.

---

## 13. Step 11 - Fix Parent Dataset Permissions

In TrueNAS:

```text
Storage -> Datasets -> tank -> media -> Permissions -> Edit
```

Recommended for simple homelab use:

```text
Owner user: przemek
Owner group: apps or builtin_users
```

Permissions:

```text
User przemek: Read / Write / Execute
Group apps or builtin_users: Read / Execute or Read / Write / Execute
Other: None
```

Do not apply recursively to the entire pool.

Do not touch:

```text
/mnt/tank/immich
```

when fixing media SMB access.

---

## 14. Step 12 - Check Child Dataset Permissions

Check each child dataset:

```text
Storage -> Datasets -> tank -> media -> English_movies
Storage -> Datasets -> tank -> media -> movies
Storage -> Datasets -> tank -> media -> Series
```

Recommended:

```text
Owner user: przemek
Owner group: apps
User: Read / Write / Execute
Group: Read / Write / Execute
Other: None
```

Apply recursively only to the specific media dataset if needed, not to all of `tank`.

---

## 15. Step 13 - Reconnect from Windows

After TrueNAS permissions are fixed:

```cmd
net use X: /delete /y
net use X: \\192.168.20.112\english_movies /user:192.168.20.112\przemek * /persistent:yes
```

Then test:

```cmd
X:
dir
```

If `dir` shows files/folders, the share works.

---

## 16. Step 14 - Map Final Drives

```cmd
net use H: \\192.168.20.112\home_przemek /persistent:yes
net use I: \\192.168.20.112\iso-library /persistent:yes
net use E: \\192.168.20.112\english_movies /persistent:yes
net use M: \\192.168.20.112\movies /persistent:yes
net use S: \\192.168.20.112\series /persistent:yes
```

If Windows asks for username, use:

```text
192.168.20.112\przemek
```

or:

```text
przemek
```

---

## 17. Common Errors and Meanings

### `Windows cannot access \\192.168.20.112\share`

If the server is reachable and the share is visible:

```text
Dataset permission or ACL problem.
```

### `The network connection could not be found`

Usually:

```text
You tried to delete a connection that does not exist.
```

Not a serious issue.

### `CMDKEY: Element not found`

Usually:

```text
No saved credential exists under that exact target.
```

Not a serious issue.

### `System error 1219`

Usually:

```text
Windows already connected to the NAS with a different username.
```

Fix:

```cmd
net use * /delete /y
```

Then reconnect with one user only.

### Command completed successfully, but File Explorer shows access denied

Meaning:

```text
Login was accepted, but TrueNAS permissions denied access.
```

Fix:

```text
Check dataset and parent dataset permissions.
```

---

## 18. Final Fix Confirmed

The confirmed solution was:

```text
Fix owner/permissions on parent dataset tank/media.
```

After fixing the parent dataset, these shares worked:

```text
english_movies
movies
series
```

---

## 19. Safety Notes

Do not recursively change permissions on:

```text
/mnt/tank
```

Do not modify Immich storage permissions while fixing SMB media shares:

```text
/mnt/tank/immich
/mnt/tank/immich/library
```

Keep one Windows/TrueNAS SMB user for all mappings, preferably:

```text
przemek
```

Avoid mixing:

```text
truenas_admin
przemek
guest
```

from the same Windows session.
