# TrueNAS SMB on Windows - Detailed Explanation

**Correct TrueNAS IP:** `192.168.20.112`  
**Client:** Windows PC  
**Server:** TrueNAS Community Edition  
**Protocol:** SMB/CIFS  
**Issue solved:** Windows could see TrueNAS and some shares, but could not open `english_movies`, `movies`, and `series`.

---

## 1. Goal

The goal was to mount several TrueNAS SMB shares as Windows network drives.

Main shares:

```text
\\192.168.20.112\home_przemek
\\192.168.20.112\iso-library
\\192.168.20.112\english_movies
\\192.168.20.112\movies
\\192.168.20.112\series
```

Recommended drive letters:

```text
H: home_przemek
I: iso-library
E: english_movies
M: movies
S: series
```

---

## 2. Symptoms

Windows could open the TrueNAS server:

```text
\\192.168.20.112
```

and it displayed the available shares.

Some shares worked, for example:

```text
iso-library
home_przemek
```

But opening this share failed:

```text
\\192.168.20.112\english_movies
```

Windows showed:

```text
Windows cannot access \\192.168.20.112\english_movies
You do not have permission to access \\192.168.20.112\english_movies.
```

---

## 3. Important Finding

The command below successfully mapped the share:

```cmd
net use X: \\192.168.20.112\english_movies /user:192.168.20.112\przemek * /persistent:yes
```

Windows accepted the password and returned:

```text
The command completed successfully.
```

But when opening the mapped drive, Windows still showed permission denied.

This is important because it means:

```text
Authentication worked.
Authorization failed.
```

In simple words:

```text
The username/password was correct, but TrueNAS permissions did not allow access to the files/folders.
```

---

## 4. Final Root Cause

The issue was the parent dataset permission.

The failing SMB shares were under this parent dataset:

```text
tank/media
```

Child datasets/shares:

```text
tank/media/English_movies
tank/media/movies
tank/media/Series
```

The parent dataset owner/permissions were different, so Windows could see the SMB share, but the user could not traverse the parent dataset path.

Final confirmed cause:

```text
Incorrect owner/permissions on parent dataset tank/media.
```

---

## 5. Why Parent Dataset Permissions Matter

SMB access is checked through multiple layers:

```text
Windows PC
  -> SMB service
  -> SMB share definition
  -> TrueNAS dataset permissions
  -> Parent dataset traverse permission
  -> Child dataset permissions
```

Even if the child dataset is correct, the user must also have permission to pass through every parent directory.

For directories/folders:

```text
Execute = Traverse
```

This means permission to enter or pass through a folder.

Example:

```text
/mnt/tank/media/English_movies
```

The user needs access to:

```text
/mnt/tank
/mnt/tank/media
/mnt/tank/media/English_movies
```

In this case, the problem was:

```text
/mnt/tank/media
```

---

## 6. Why Some Shares Worked

`iso-library` worked because it was not affected by the same parent dataset permission problem, or its parent path allowed access.

The media shares failed because they were all under:

```text
tank/media
```

That is why several related shares failed at the same time:

```text
english_movies
movies
series
```

This pattern strongly indicates a parent dataset permission problem.

---

## 7. Commands Used on Windows

### Show current SMB connections

```cmd
net use
```

This shows mapped drives and active SMB connections.

---

### Remove one mapped drive

```cmd
net use X: /delete /y
```

This removes the mapped drive `X:`.

---

### Remove all mapped drives and SMB sessions

```cmd
net use * /delete /y
```

This clears old or incorrect SMB connections.

---

### Show saved Windows credentials

```cmd
cmdkey /list
```

This lists credentials saved in Windows Credential Manager.

---

### Delete saved TrueNAS credential

```cmd
cmdkey /delete:192.168.20.112
```

In our case Windows returned:

```text
CMDKEY: Element not found.
```

That simply means there was no saved credential under exactly that target name.

---

### Map a share and force a password prompt

```cmd
net use X: \\192.168.20.112\english_movies /user:192.168.20.112\przemek * /persistent:yes
```

Important parts:

```text
/user:192.168.20.112\przemek
```

forces Windows to log in as TrueNAS user `przemek`.

```text
*
```

forces Windows to ask for the password.

```text
/persistent:yes
```

makes Windows reconnect the drive after reboot/login.

---

## 8. Why Windows Did Not Always Ask for Password

Windows may not ask for a password because:

1. There is already an active SMB session to the NAS.
2. Windows cached the credentials during the current login session.
3. Credentials are saved in Credential Manager.
4. Windows is reusing an existing authenticated session.
5. The command did not include `*`.

To force a password prompt:

```cmd
net use X: \\192.168.20.112\english_movies /user:192.168.20.112\przemek * /persistent:yes
```

---

## 9. Normal CMD vs Administrator CMD

For mapped drives that should appear in File Explorer, use a normal Command Prompt:

```text
Start -> type cmd -> Enter
```

Avoid:

```text
Run as administrator
```

Reason: Windows separates normal user sessions and elevated administrator sessions. A drive mapped in Administrator CMD may not appear correctly in normal File Explorer.

---

## 10. TrueNAS Permission Fix

The final fix was done in TrueNAS by correcting the parent dataset:

```text
Storage -> Datasets -> tank -> media -> Permissions
```

The owner/permissions on `tank/media` were adjusted so the user/group could traverse/access it.

After that, the child shares worked:

```text
english_movies
movies
series
```

---

## 11. Recommended Permission Model

For simple personal homelab media storage:

Parent dataset:

```text
tank/media
```

Recommended access:

```text
User przemek: Read / Write / Execute
Group apps or builtin_users: Read / Execute or Read / Write / Execute
Other: None
```

Child datasets:

```text
tank/media/English_movies
tank/media/movies
tank/media/Series
```

Recommended access:

```text
Owner user: przemek
Owner group: apps
User: Read / Write / Execute
Group: Read / Write / Execute
Other: None
```

If other devices should only read media later, create a separate read-only user, for example:

```text
media_reader
```

---

## 12. Final Drive Mapping Commands

After permissions are fixed, map the drives:

```cmd
net use H: \\192.168.20.112\home_przemek /persistent:yes
net use I: \\192.168.20.112\iso-library /persistent:yes
net use E: \\192.168.20.112\english_movies /persistent:yes
net use M: \\192.168.20.112\movies /persistent:yes
net use S: \\192.168.20.112\series /persistent:yes
```

If credentials are needed:

```cmd
net use E: \\192.168.20.112\english_movies /user:192.168.20.112\przemek * /persistent:yes
```

---

## 13. Final Rule

```text
Can see TrueNAS but cannot open one share = dataset/share permission issue.
Can open some shares but not others = specific dataset or parent dataset permission issue.
Password accepted but access denied = TrueNAS authorization/permissions issue.
Several child shares under one parent fail = check the parent dataset first.
```

---

## 14. Final Result

The problem was solved after correcting the parent dataset owner/permissions on:

```text
tank/media
```

This allowed Windows user `przemek` to access child SMB shares:

```text
english_movies
movies
series
```
