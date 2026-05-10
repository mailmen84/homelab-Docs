# Immich Troubleshooting - Command Explanations

**Purpose of this file:** explain what each command was used for, why it was used, and possible alternatives.

**Correct TrueNAS IP:** `192.168.20.112`

---

## 1. Password Reset

### Command

```bash
docker exec -it immich_server immich-admin reset-admin-password
```

### What it does

Runs the Immich admin command inside the `immich_server` Docker container and resets the admin password.

### Why it was used

The web login was not working, but SSH access to the VM was available.

### Explanation

- `docker exec` - runs a command inside an already running container.
- `-it` - interactive terminal mode, needed because the command asks for input.
- `immich_server` - name of the Immich server container.
- `immich-admin reset-admin-password` - Immich CLI command to reset the admin password.

### Alternative

If you are in the Docker Compose folder:

```bash
docker compose exec immich-server immich-admin reset-admin-password
```

If the container name is different:

```bash
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Image}}"
```

Then replace `immich_server` with the correct name.

---

## 2. Check Running Containers

### Command

```bash
docker ps
```

### What it does

Shows running Docker containers.

### Why it was used

To confirm that Immich services were running and healthy.

### What we looked for

Containers such as:

```text
immich_server
immich_postgres
immich_machine_learning
immich_redis
```

### Alternative

From the Docker Compose folder:

```bash
docker compose ps
```

This gives a cleaner view for the Immich stack.

---

## 3. Find the Docker Compose File

### Command

```bash
sudo find / -name "docker-compose.yml" 2>/dev/null | grep -i immich
```

### What it does

Searches the whole system for files named `docker-compose.yml`, then filters only paths containing `immich`.

### Why it was used

The first attempt to run `docker compose ps` failed because the current folder was not the Immich Compose folder.

### Explanation

- `sudo` - required to search protected folders.
- `find /` - search from the root of the filesystem.
- `-name "docker-compose.yml"` - search for that exact file name.
- `2>/dev/null` - hide permission denied errors.
- `grep -i immich` - show only results containing `immich`, case-insensitive.

### Alternative

Search for both possible Compose file names:

```bash
sudo find / -name "docker-compose.yml" -o -name "compose.yml" 2>/dev/null
```

---

## 4. Enter the Immich Folder

### Command

```bash
cd /home/przemek/immich
```

### What it does

Changes the current working directory to the Immich Docker Compose folder.

### Why it was used

Commands like `docker compose ps`, `docker compose down`, and `docker compose up -d` need to be run from the directory where the Compose file exists.

### Alternative

Run Compose with an explicit file path:

```bash
docker compose -f /home/przemek/immich/docker-compose.yml ps
```

---

## 5. Check the Immich `.env` File

### Command

```bash
grep -E "UPLOAD_LOCATION|DB_DATA_LOCATION|IMMICH_VERSION" .env
```

### What it does

Reads the `.env` file and prints only the important Immich variables.

### Why it was used

To discover where Immich expects its uploaded media files and database files to be stored.

### Result

```text
UPLOAD_LOCATION=/mnt/immich/library
DB_DATA_LOCATION=/var/lib/immich/postgres
IMMICH_VERSION=v2
```

### Explanation

- `grep` - searches text.
- `-E` - enables extended regular expressions.
- `"A|B|C"` - means match A or B or C.
- `.env` - Immich environment file.

### Alternative

Show the full file:

```bash
cat .env
```

Be careful: `.env` files may contain secrets or passwords.

---

## 6. Save Upload Path to a Variable

### Command

```bash
UPLOAD=$(grep '^UPLOAD_LOCATION=' .env | cut -d= -f2-)
echo "$UPLOAD"
```

### What it does

Reads the `UPLOAD_LOCATION` value from `.env` and stores it in the shell variable `UPLOAD`.

### Why it was used

To avoid typing `/mnt/immich/library` repeatedly and reduce typing mistakes.

### Explanation

- `grep '^UPLOAD_LOCATION=' .env` - finds the line that starts with `UPLOAD_LOCATION=`.
- `cut -d= -f2-` - splits the line at `=` and keeps everything after it.
- `UPLOAD=$(...)` - stores the result in a variable.
- `echo "$UPLOAD"` - prints the value.

### Alternative

Manually use the path:

```bash
df -hT /mnt/immich/library
```

---

## 7. Check Filesystem Type and Free Space

### Command

```bash
df -hT "$UPLOAD"
```

or:

```bash
df -hT /mnt/immich/library
```

### What it does

Shows which filesystem contains the path, how much space is used, and the filesystem type.

### Why it was used

To check whether `/mnt/immich/library` was mounted from TrueNAS NFS or was only a local VM folder.

### Key finding

It showed:

```text
/dev/mapper/ubuntu--vg-ubuntu--lv ext4
```

This means local Ubuntu disk, not NFS.

### Explanation

- `df` - disk filesystem usage.
- `-h` - human-readable sizes.
- `-T` - show filesystem type.
- `"$UPLOAD"` - path to check.

### Correct result after repair

```text
192.168.20.112:/mnt/tank/immich/library nfs4 ...
```

or similar.

---

## 8. Check Mount Source

### Command

```bash
findmnt -T "$UPLOAD"
```

or:

```bash
findmnt /mnt/immich/library
```

### What it does

Shows what device or network share is mounted at a path.

### Why it was used

`df` showed the filesystem type, but `findmnt` clearly shows the mount source.

### Correct result after repair

The source should look like:

```text
192.168.20.112:/mnt/tank/immich/library
```

### Alternative

```bash
mount | grep immich
```

or:

```bash
cat /proc/mounts | grep immich
```

---

## 9. List the Upload Folder

### Command

```bash
ls -lah "$UPLOAD"
```

or:

```bash
ls -lah /mnt/immich/library
```

### What it does

Lists files and folders in the upload location.

### Why it was used

To see whether the expected Immich folders existed.

### Explanation

- `ls` - list files.
- `-l` - long format.
- `-a` - show hidden files.
- `-h` - human-readable sizes.

### Expected folders

```text
backups
encoded-video
library
profile
thumbs
upload
```

---

## 10. Check Folder Sizes

### Command

```bash
sudo du -h --max-depth=1 /mnt/immich/library | sort -h
```

### What it does

Shows how much space each top-level folder uses.

### Why it was used

To compare the local folder with the expected NAS dataset size.

### Key finding

The folder showed only about 1.2 GB, mostly database backups. TrueNAS showed about 124 GB. That mismatch proved Immich was not using the real NAS library.

### Explanation

- `du` - disk usage.
- `-h` - human-readable sizes.
- `--max-depth=1` - only show one folder level.
- `sort -h` - sort by human-readable size.

### Alternative

For a faster high-level check:

```bash
sudo du -sh /mnt/immich/library
```

---

## 11. Search for Actual Files

### Command

```bash
sudo find /mnt/immich/library -type f | head -20
```

### What it does

Finds files inside the Immich library folder and prints the first 20.

### Why it was used

To check if real photo/video files existed in the current path.

### Key finding

Only marker files and database backups appeared. Real photo/video library files were missing.

### Explanation

- `find /path` - search under that path.
- `-type f` - only files.
- `head -20` - show only first 20 results.

### Alternative

Search for common image/video files:

```bash
sudo find /mnt/immich/library -type f \( -iname "*.jpg" -o -iname "*.mp4" -o -iname "*.heic" \) | head
```

---

## 12. Read Immich Server Logs

### Command

```bash
docker logs -f --tail=100 immich_server
```

### What it does

Shows the last 100 lines of Immich server logs and keeps following new logs live.

### Why it was used

To see what happens when a photo/video is opened in the web interface.

### Key error

```text
ENOENT: no such file or directory
/data/thumbs/...
/data/encoded-video/...
```

### Meaning

Immich tried to open a thumbnail or encoded video file, but the file was not present in the container storage path.

### Explanation

- `docker logs` - show logs from a container.
- `-f` - follow live output.
- `--tail=100` - show only recent 100 lines first.
- `immich_server` - container name.

### Alternative

From the Compose folder:

```bash
docker compose logs -f --tail=100 immich-server
```

---

## 13. Correct Container Path for Immich v2

### Initial command that was not correct for this setup

```bash
docker exec -it immich_server sh -lc 'df -h /usr/src/app/upload && ls -lah /usr/src/app/upload'
```

### What happened

The container returned:

```text
df: /usr/src/app/upload: No such file or directory
```

### Correct path for this Immich v2 setup

Immich logs showed the container path is:

```text
/data
```

Correct command:

```bash
docker exec -it immich_server sh -lc 'df -h /data && ls -lah /data'
```

### Why this matters

The host path `/mnt/immich/library` is mounted inside the container as `/data`.

---

## 14. Stop Immich

### Command

```bash
cd /home/przemek/immich
docker compose down
```

### What it does

Stops and removes the Immich containers created by Docker Compose.

### Why it was used

To prevent Immich from writing to the wrong local folder while the storage mount was being repaired.

### Explanation

- `docker compose` - works with the current folder's Compose file.
- `down` - stops and removes the Compose containers and network.
- It does not normally delete named volumes unless `-v` is used.

### Alternative

Stop without removing Compose containers:

```bash
docker compose stop
```

For this repair, `down` was acceptable.

---

## 15. Move the Wrong Local Folder

### Command

```bash
sudo mv /mnt/immich/library /mnt/immich/library_local_backup
```

### What it does

Renames the current local folder so it is no longer used as the mount point.

### Why it was used

The folder contained local backup files and marker files. We did not want to delete it. We needed an empty mount point for the TrueNAS NFS share.

### Explanation

- `mv` - move or rename.
- `sudo` - required because the folder may be owned by root.
- Source: `/mnt/immich/library`
- Destination: `/mnt/immich/library_local_backup`

### Alternative

Copy it instead of moving:

```bash
sudo cp -a /mnt/immich/library /mnt/immich/library_local_backup
```

But moving is cleaner when preparing a mount point.

---

## 16. Create the Mount Point

### Command

```bash
sudo mkdir -p /mnt/immich/library
```

### What it does

Creates the empty directory where the NAS share will be mounted.

### Why it was used

Linux needs an existing folder as a mount point.

### Explanation

- `mkdir` - make directory.
- `-p` - create parent folders if needed and do not error if the folder already exists.

### Alternative

Any mount point can be used, but it must match `UPLOAD_LOCATION` from `.env` unless you also edit `.env`.

---

## 17. Install NFS Client Tools

### Command

```bash
sudo apt update
sudo apt install nfs-common -y
```

### What it does

Installs Ubuntu tools needed to mount NFS shares.

### Why it was used

TrueNAS shared the Immich dataset via NFS.

### Explanation

- `apt update` - updates package lists.
- `apt install nfs-common` - installs NFS client utilities.
- `-y` - automatically answer yes.

### Alternative

If using SMB instead of NFS, you would install:

```bash
sudo apt install cifs-utils -y
```

But for Immich server storage, NFS is usually more suitable than SMB on Linux.

---

## 18. Show TrueNAS NFS Exports

### Command

```bash
showmount -e 192.168.20.112
```

### What it does

Asks TrueNAS which NFS exports are available.

### Why it was used

To confirm the exact NFS export path before mounting.

### Expected result

```text
/mnt/tank/immich/library
```

### Explanation

- `showmount` - queries NFS mount daemon.
- `-e` - show exports.
- `192.168.20.112` - TrueNAS IP.

### Alternative

Check directly in TrueNAS GUI:

```text
Shares -> Unix Shares (NFS)
```

---

## 19. Test Network Connectivity

### Command

```bash
ping -c 4 192.168.20.112
```

### What it does

Tests if the Immich VM can reach TrueNAS at IP level.

### Why it was used

A mount timeout can be caused by network or routing problems.

### Explanation

- `ping` - sends ICMP echo packets.
- `-c 4` - send only 4 packets.

### Alternative

If ping is blocked, test the actual NFS port:

```bash
nc -vz 192.168.20.112 2049
```

---

## 20. Test NFS Port

### Command

```bash
nc -vz 192.168.20.112 2049
```

### What it does

Tests whether TCP port 2049, the main NFS port, is reachable.

### Why it was used

If ping works but port 2049 fails, NFS service/firewall/export access may be the issue.

### Explanation

- `nc` - netcat network test tool.
- `-v` - verbose output.
- `-z` - scan mode, do not send data.
- `2049` - NFS port.

### Alternative

Install `nc` if missing:

```bash
sudo apt install netcat-openbsd -y
```

---

## 21. Mount the TrueNAS NFS Share Manually

### Command

```bash
sudo mount -t nfs -o nfsvers=4,proto=tcp 192.168.20.112:/mnt/tank/immich/library /mnt/immich/library
```

### What it does

Mounts the TrueNAS Immich library dataset into the Immich VM at the path Immich already expects.

### Why it was used

To immediately restore access to the real Immich media files.

### Explanation

- `sudo mount` - mount a filesystem.
- `-t nfs` - filesystem type is NFS.
- `-o nfsvers=4,proto=tcp` - use NFS version 4 over TCP.
- `192.168.20.112:/mnt/tank/immich/library` - TrueNAS export.
- `/mnt/immich/library` - local mount point on the Immich VM.

### Alternative

Try NFS v3 if v4 fails:

```bash
sudo mount -t nfs -o nfsvers=3,proto=tcp 192.168.20.112:/mnt/tank/immich/library /mnt/immich/library
```

But use NFSv4 if it works.

---

## 22. Make the Mount Permanent with `/etc/fstab`

### Command

```bash
sudo nano /etc/fstab
```

Add:

```text
192.168.20.112:/mnt/tank/immich/library /mnt/immich/library nfs nfsvers=4,proto=tcp,_netdev,nofail 0 0
```

### What it does

Configures Ubuntu to mount the TrueNAS NFS share automatically during boot.

### Why it was used

Manual mounts disappear after reboot. Immich needs this storage every time the VM starts.

### Explanation of fields

```text
192.168.20.112:/mnt/tank/immich/library
```

NFS source.

```text
/mnt/immich/library
```

Local mount point.

```text
nfs
```

Filesystem type.

```text
nfsvers=4,proto=tcp,_netdev,nofail
```

Mount options.

- `nfsvers=4` - use NFS version 4.
- `proto=tcp` - use TCP.
- `_netdev` - this is a network device; wait for network.
- `nofail` - do not stop boot if NAS is temporarily unavailable.

```text
0 0
```

No dump backup and no fsck check for this network filesystem.

### Alternative

Use a systemd mount unit for more control, but `/etc/fstab` is simpler and correct for this setup.

---

## 23. Reload systemd

### Command

```bash
sudo systemctl daemon-reload
```

### What it does

Reloads systemd configuration.

### Why it was used

After editing `/etc/fstab`, Linux warned that systemd was still using the old version.

### Explanation

- `systemctl` - controls systemd.
- `daemon-reload` - reloads unit files and generated mount units.

### Alternative

A reboot also reloads systemd, but `daemon-reload` is faster and safer.

---

## 24. Test All fstab Mounts

### Command

```bash
sudo mount -a
```

### What it does

Attempts to mount all filesystems listed in `/etc/fstab` that are not already mounted.

### Why it was used

To test the new permanent mount without rebooting.

### Important note

If `mount -a` prints nothing, that usually means success.

If it prints:

```text
mount.nfs: Connection timed out
```

then the VM cannot reach the NFS share.

### Alternative

Mount only one target manually:

```bash
sudo mount /mnt/immich/library
```

This works if the `/etc/fstab` line is correct.

---

## 25. Verify the Mount

### Commands

```bash
df -hT /mnt/immich/library
findmnt /mnt/immich/library
sudo du -h --max-depth=1 /mnt/immich/library | sort -h
```

### What they do

Confirm that `/mnt/immich/library` is now NFS and contains the real data.

### Expected result

- `df` shows `nfs` or `nfs4`
- `findmnt` shows source `192.168.20.112:/mnt/tank/immich/library`
- `du` shows around the same usage as TrueNAS, about 124 GiB

---

## 26. Start Immich Again

### Command

```bash
cd /home/przemek/immich
docker compose up -d
```

### What it does

Starts the Immich Docker Compose stack in detached mode.

### Why it was used

After the NAS mount was repaired, Immich could safely start again and access the real media files.

### Explanation

- `up` - create/start containers.
- `-d` - detached mode, run in background.

### Alternative

Start and watch output live:

```bash
docker compose up
```

Press `CTRL + C` to stop if running in foreground.

---

## 27. Check Immich Status

### Command

```bash
docker compose ps
```

### What it does

Shows service status for the Immich stack.

### Why it was used

To confirm all containers were running and healthy after the repair.

### Alternative

```bash
docker ps
```

---

## 28. Check Logs After Repair

### Command

```bash
docker logs --tail=100 immich_server
```

### What it does

Shows the latest Immich server logs.

### Why it was used

To verify that the previous missing-file errors stopped.

### Alternative

Live view:

```bash
docker logs -f --tail=100 immich_server
```

---

## 29. Copy Local Backup Files Back to NAS

### Command

```bash
sudo rsync -av /mnt/immich/library_local_backup/backups/ /mnt/immich/library/backups/
```

### What it does

Copies database backup files from the temporary local folder to the real NAS-mounted Immich backup folder.

### Why it was used

The old local folder contained database backup files that should not be lost.

### Explanation

- `rsync` - reliable file copy/sync tool.
- `-a` - archive mode, preserves permissions/timestamps.
- `-v` - verbose output.
- Trailing slash after `backups/` means copy the contents of the folder.

### Alternative

Simple copy:

```bash
sudo cp -a /mnt/immich/library_local_backup/backups/. /mnt/immich/library/backups/
```

`rsync` is preferred because it is safer for repeated copies.

---

## 30. Reboot Test

### Commands

```bash
sudo reboot
```

After reboot:

```bash
df -hT /mnt/immich/library
findmnt /mnt/immich/library
docker ps
```

### What they do

Confirm that the NAS mount comes back automatically and Immich starts correctly after reboot.

### Why it matters

A repair is not complete until it survives reboot.

---

## 31. Other Possible Approaches

### Option A - Change Immich `.env` instead of mounting to the old path

You could change:

```text
UPLOAD_LOCATION=/mnt/immich/library
```

to another path, for example:

```text
UPLOAD_LOCATION=/mnt/truenas/immich-library
```

But then Docker Compose must also use that value correctly, and the new path must be mounted before Immich starts.

For this repair, mounting TrueNAS to the existing path was better because the Immich configuration already expected that path.

---

### Option B - Use SMB/CIFS instead of NFS

Possible, but not ideal for Linux server-to-NAS application storage.

Example package:

```bash
sudo apt install cifs-utils -y
```

NFS is usually cleaner for Linux VM to TrueNAS storage.

---

### Option C - Restore everything from backup

This would only be needed if the real media files were lost. In this case, the TrueNAS dataset still existed and had 124 GiB, so restoring from backup was not the first choice.

---

## 32. Safety Rules for This Immich Setup

1. Do not start Immich if `/mnt/immich/library` is not mounted from TrueNAS.
2. Always check:

```bash
df -hT /mnt/immich/library
```

before major Immich maintenance.
3. Do not delete `/mnt/immich/library_local_backup` until the system works after reboot.
4. Keep database backups, but remember: database backups are not the same as photo/video backups.
5. If Immich shows photos in the UI but files do not open, check logs first:

```bash
docker logs -f --tail=100 immich_server
```

6. If logs show `ENOENT`, first check storage mount and file paths.
