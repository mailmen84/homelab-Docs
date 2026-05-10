# Immich Troubleshooting Solution

**System:** Immich in Docker Compose on Ubuntu VM  
**Storage:** TrueNAS NFS dataset  
**Correct TrueNAS IP:** `192.168.20.112`  
**Immich VM user:** `przemek`  
**Immich Docker Compose folder:** `/home/przemek/immich`  
**Immich upload location from `.env`:** `/mnt/immich/library`  
**TrueNAS dataset:** `tank/immich/library`  
**Expected TrueNAS NFS export path:** `/mnt/tank/immich/library`

---

## 1. Problem Summary

Immich login worked, the web interface loaded, but photos/videos did not open. They stayed loading forever.

The Immich server logs showed missing file errors:

```text
ENOENT: no such file or directory
/data/thumbs/...
/data/encoded-video/...
```

This means:

> Immich database existed and knew about the assets, but the real media files/thumbnails/videos were not available in the storage path used by the container.

---

## 2. Root Cause

The `.env` file showed:

```bash
UPLOAD_LOCATION=/mnt/immich/library
DB_DATA_LOCATION=/var/lib/immich/postgres
IMMICH_VERSION=v2
```

But `/mnt/immich/library` was mounted from the local Ubuntu VM disk:

```text
/dev/mapper/ubuntu--vg-ubuntu--lv ext4 24G ...
```

The TrueNAS screenshot showed the real Immich dataset:

```text
tank/immich/library
Used: 124.03 GiB
Shared via NFS
```

So the issue was:

> The TrueNAS NFS share was not mounted into the Immich VM at `/mnt/immich/library`.

Immich was looking at a nearly empty local folder instead of the real NAS storage.

---

## 3. Commands Used to Diagnose the Issue

### Check Docker containers

```bash
docker ps
```

Expected important containers:

```text
immich_server
immich_postgres
immich_machine_learning
immich_redis
```

The containers were healthy, so Docker/Immich itself was running.

---

### Find the Immich Docker Compose folder

```bash
sudo find / -name "docker-compose.yml" 2>/dev/null | grep -i immich
```

Result:

```text
/home/przemek/immich/docker-compose.yml
```

Then enter the correct folder:

```bash
cd /home/przemek/immich
```

---

### Check Immich environment file

```bash
grep -E "UPLOAD_LOCATION|DB_DATA_LOCATION|IMMICH_VERSION" .env
```

Result:

```text
UPLOAD_LOCATION=/mnt/immich/library
DB_DATA_LOCATION=/var/lib/immich/postgres
IMMICH_VERSION=v2
```

---

### Check whether the upload path is local disk or NAS

```bash
UPLOAD=$(grep '^UPLOAD_LOCATION=' .env | cut -d= -f2-)
echo "$UPLOAD"
df -hT "$UPLOAD"
findmnt -T "$UPLOAD"
ls -lah "$UPLOAD"
```

Result showed:

```text
/mnt/immich/library
/dev/mapper/ubuntu--vg-ubuntu--lv ext4 ...
```

This confirmed that the path was using the local VM disk, not NFS.

---

### Check data usage

```bash
sudo du -h --max-depth=1 /mnt/immich/library | sort -h
```

Result showed mostly empty folders and about 1.2 GB of database backups only.

This was wrong because the TrueNAS dataset had about 124 GB.

---

### Check actual files

```bash
sudo find /mnt/immich/library -type f | head -20
```

Only `.immich` marker files and database backups were visible. The real library files were missing.

---

### Check Immich logs

```bash
docker logs -f --tail=100 immich_server
```

The logs showed:

```text
ENOENT: no such file or directory
/data/thumbs/...
ENOENT: no such file or directory
/data/encoded-video/...
```

This confirmed the storage path problem.

---

## 4. Recovery Procedure

### Step 1 - Stop Immich

```bash
cd /home/przemek/immich
docker compose down
```

This prevents Immich from writing to the wrong local folder while repairing storage.

---

### Step 2 - Move the wrong local folder out of the way

```bash
sudo mv /mnt/immich/library /mnt/immich/library_local_backup
```

This protects the temporary local folder and its database backups.

Create a clean mount point:

```bash
sudo mkdir -p /mnt/immich/library
```

---

### Step 3 - Install NFS client tools

```bash
sudo apt update
sudo apt install nfs-common -y
```

---

### Step 4 - Check TrueNAS NFS export

Use the correct TrueNAS IP:

```bash
showmount -e 192.168.20.112
```

Expected export:

```text
/mnt/tank/immich/library
```

---

### Step 5 - Mount the TrueNAS NFS share manually

```bash
sudo mount -t nfs -o nfsvers=4,proto=tcp 192.168.20.112:/mnt/tank/immich/library /mnt/immich/library
```

Verify:

```bash
df -hT /mnt/immich/library
findmnt /mnt/immich/library
```

Expected type:

```text
nfs
```

or:

```text
nfs4
```

---

### Step 6 - Make the mount permanent

Edit `/etc/fstab`:

```bash
sudo nano /etc/fstab
```

Add this line:

```text
192.168.20.112:/mnt/tank/immich/library /mnt/immich/library nfs nfsvers=4,proto=tcp,_netdev,nofail 0 0
```

Reload systemd:

```bash
sudo systemctl daemon-reload
```

Test:

```bash
sudo mount -a
df -hT /mnt/immich/library
findmnt /mnt/immich/library
```

If `mount -a` prints nothing, that usually means success.

---

### Step 7 - Start Immich

```bash
cd /home/przemek/immich
docker compose up -d
```

Check status:

```bash
docker compose ps
```

Check logs:

```bash
docker logs --tail=100 immich_server
```

The previous `ENOENT` errors should stop after the NFS mount is correct.

---

### Step 8 - Test Immich

First test locally:

```text
http://IMMICH_VM_IP:2283
```

Then test through the public domain:

```text
https://photos.nodecrafts.org
```

If local access works but public access fails, the remaining issue is probably Cloudflare Tunnel / reverse proxy, not Immich storage.

---

## 5. Optional: Copy Local Database Backups Back to NAS

If `/mnt/immich/library_local_backup/backups/` contains useful database backups, copy them to the mounted NAS backup folder:

```bash
sudo rsync -av /mnt/immich/library_local_backup/backups/ /mnt/immich/library/backups/
```

Do not delete the local backup folder until Immich works correctly after a reboot.

---

## 6. Reboot Test

After everything works:

```bash
sudo reboot
```

After the VM comes back:

```bash
df -hT /mnt/immich/library
findmnt /mnt/immich/library
docker ps
```

Expected result:

- `/mnt/immich/library` shows `nfs` or `nfs4`
- `immich_server` is running
- photos open correctly

---

## 7. Important Notes

Do not delete:

```bash
/mnt/immich/library_local_backup
```

until the NAS mount and Immich are confirmed working after reboot.

Do not reset the Immich database unless the media files are permanently lost.

Do not use the TrueNAS web admin account for normal SMB/NFS user-level access unless there is a clear reason.

For Immich v2, the container path shown in logs is:

```text
/data
```

not:

```text
/usr/src/app/upload
```
