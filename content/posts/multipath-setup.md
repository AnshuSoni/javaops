---
title: "Understanding Multipath and Creating LVM over Multipath Devices"
date: 2025-04-28T11:00:00Z
description: "Learn the basics of multipathing in Linux, and how to create a Logical Volume Manager (LVM) setup over multipath devices for performance and redundancy."
tags: [Linux, Storage, LVM, Multipath]
categories: [Linux, Storage]
---

Storage systems in enterprise environments often require both performance and high availability. Multipathing and LVM are critical technologies used to achieve these goals.

In this post, we'll explain what multipathing is, how it works, and how to set up an LVM volume group on top of multipath devices.

## What is Multipath?

**Multipath** provides multiple physical paths to connect storage devices (like SAN disks) to a server.

**Purpose**:
- **Redundancy**: If one path fails, traffic automatically switches to another.
- **Load Balancing**: Distributes I/O across multiple paths, improving performance.

Multipath configurations rely on `device-mapper-multipath`, which creates a single device node (`/dev/mapper/mpatha`, for example) that represents all physical paths.

### Typical Scenario

- Server has two Fibre Channel adapters.
- Storage array has multiple controllers.
- Multiple paths between server and storage are established.

Without multipath, Linux would show each path as a separate device. With multipath, they're combined into a single device.

## How Multipath Works Internally

- Linux kernel detects all physical paths.
- The `multipathd` daemon manages the path groupings.
- A pseudo-device is created in `/dev/mapper/`, representing the active path group.
- If a path fails, `multipathd` reroutes traffic without disruption.

## Setting Up Multipath

1. **Install the multipath tools**

```bash
sudo apt install multipath-tools    # Debian/Ubuntu
sudo yum install device-mapper-multipath -y  # RHEL/CentOS
```

2. **Configure multipath.conf**

Example minimal `/etc/multipath.conf`:
```bash
defaults {
    user_friendly_names yes
}
```

3. **Start and Enable Services**

```bash
sudo systemctl start multipathd
sudo systemctl enable multipathd
```

4. **Discover Multipath Devices**

```bash
sudo multipath -ll
```
- This lists active multipath devices.

## Creating an LVM on Multipath Devices

Once multipath devices are visible, you can treat them like any other block device.

### Step 1: Create Physical Volume (PV)

```bash
sudo pvcreate /dev/mapper/mpatha
```

### Step 2: Create a Volume Group (VG)

```bash
sudo vgcreate vg_storage /dev/mapper/mpatha
```

### Step 3: Create Logical Volumes (LV)

```bash
sudo lvcreate -n lv_data -L 100G vg_storage
```

### Step 4: Create Filesystem

```bash
sudo mkfs.ext4 /dev/vg_storage/lv_data
```

### Step 5: Mount the Filesystem

```bash
sudo mkdir /mnt/storage
sudo mount /dev/vg_storage/lv_data /mnt/storage
```

Add to `/etc/fstab` for persistent mounts.

Example entry:
```bash
/dev/vg_storage/lv_data /mnt/storage ext4 defaults 0 2
```

## Commands to Monitor Multipath and LVM

- View multipath devices:
  ```bash
  multipath -ll
  ```

- Monitor path status:
  ```bash
  multipathd show paths
  ```

- LVM information:
  ```bash
  pvs
  vgs
  lvs
  ```

## Final Thoughts

Combining multipathing with LVM offers the best of both worlds: 
- Path redundancy
- Scalability
- Flexibility in managing storage volumes

It's especially critical in production environments where downtime or data loss is not an option.

> **Tip**: Always test path failover before going into production to ensure your multipath configuration is correct.

---

Stay tuned for a future post on configuring advanced multipath policies like Round Robin and prioritizing specific paths!
