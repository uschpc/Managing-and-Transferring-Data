# Managing and Transferring Data at CARC
---

## 1. Computing Resources Overview
[cite_start]The CARC environment consists of several integrated components optimized for high-performance research[cite: 1, 15]:
* [cite_start]**Head/Login Nodes:** Primary entry points like `discovery.usc.edu` and `endeavour.usc.edu`[cite: 1, 21].
* [cite_start]**Compute Nodes:** Approximately 500 nodes running the Rocky Linux operating system[cite: 1, 23].
* [cite_start]**Networked Storage:** High-speed disk arrays and file servers connected via a low-latency network [cite: 1, 24-26].
* [cite_start]**Schedulers:** SLURM is used for resource allocation and job management[cite: 1, 27].
* [cite_start]**Data Transfer Nodes:** Specialized nodes (`hpc-transfer1`, `hpc-transfer2`) for moving large datasets efficiently[cite: 1, 28, 85].

---

## 2. CARC Filesystem & Storage
[cite_start]Storage is categorized into three main types based on use-case and backup policy[cite: 1, 38]:

| Storage Type | Path | Purpose | Quota | Backup Policy |
| :--- | :--- | :--- | :--- | :--- |
| **Home** | `/home1/<user>` | Personal scripts & settings | 100 GB / 255k files | [cite_start]Snapshots only (2 wks) [cite: 1, 62] |
| **Project** | `/project2/<pi_id>` | Group research data | 10 TB free per PI | [cite_start]**Backed up** [cite: 2, 49] |
| **Scratch** | `/scratch1/<user>` | Temporary work space | Variable (Large) | [cite_start]**NO BACKUP** [cite: 2, 58] |

> [cite_start]**Note:** Additional Project storage is available in 5 TB increments for $60/TB/year[cite: 2, 53].

---

## 3. Data Transfer Methods
[cite_start]There are multiple ways to move data onto CARC systems[cite: 2, 66]:

### A. Web Interface (Open OnDemand)
* [cite_start]Provides a visual file explorer in the browser[cite: 2, 68].
* [cite_start]Best for small file transfers and simple file management[cite: 2, 72].

### B. Command Line (rsync)
* [cite_start]**Best Practice:** Use `rsync` for most transfers as it can resume interrupted progress and works across different filesystems [cite: 2, 74-77].
* [cite_start]**Syntax:** `rsync -rltvh <source> <target>`[cite: 3, 79].
* [cite_start]**Pro Tip:** Adding a trailing slash to a directory path (e.g., `~/Data/`) copies only the *contents*, whereas omitting it (`~/Data`) copies the folder itself[cite: 3, 78].

### C. GUI Clients (SFTP)
* [cite_start]Use standalone applications like **CyberDuck** or **FileZilla**[cite: 2, 70].
* [cite_start]Connect using hostnames: `hpc-transfer1.usc.edu` or `discovery.usc.edu`[cite: 3, 85].

### D. Globus & Rclone
* [cite_start]**Globus:** Ideal for fast transfers between different HPC centers or sharing with external collaborators[cite: 3].
* [cite_start]**Rclone:** Used to link cloud storage (Google Drive, OneDrive) to CARC; involves a more technical initial setup[cite: 3].

---

## 4. File Permissions & Ownership
[cite_start]Managing access is critical for collaboration and quota management[cite: 5, 7].

### Permission Values
[cite_start]Permissions are calculated by summing numeric values[cite: 5]:
* **4:** Read (`r`)
* **2:** Write (`w`)
* **1:** Execute (`x`)

### Essential Commands
* **Change Permissions (`chmod`):**
    ```bash
    chmod 640 file.txt       # User: rw, Group: r, Others: none [cite: 6]
    chmod +x script.sh       # Add execute permission [cite: 6]
    chmod -R g-w directory   # Recursively remove group write access [cite: 6]
    ```
* **Change Group Ownership (`chgrp`):**
    Quota is tracked via group ownership. Use this to ensure collaborators can access project files[cite: 7].
    ```bash
    chgrp ttroj_412 data.txt
    chgrp -R ttroj_412 /project/ttroj_412/group_data [cite: 7]
    ```

---

## 5. Data Integrity (Checksums)
[cite_start]To verify that files were not corrupted during transfer, use the `md5sum` utility to compare file "fingerprints"[cite: 7, 8].

### Single File Verification
```bash
md5sum <filename> 
# [cite_start]Example output: a71f703ed688eeca637dc27df714e854  data.zip [cite: 9]
