# Managing and Transferring Data at CARC

This guide provides a comprehensive overview of managing, transferring research data at the University of Southern California's Center for Advanced Research Computing (CARC).

---

## 1. System Architecture Overview
The CARC environment is designed for high-performance workloads, separating access, computation, and high-speed data movement.



* **Head/Login Nodes:** Primary entry points (`discovery.usc.edu`, `endeavour.usc.edu`) used for code editing, compiling, and job submission.
* **Compute Nodes:** Approximately 500 nodes running the Rocky Linux operating system where the actual heavy-duty processing occurs.
* **Data Transfer Nodes (DTNs):** Dedicated nodes (`hpc-transfer1`, `hpc-transfer2`) with 100 Gbps connections optimized for large-scale data movement to reduce load on login nodes.
* **Scheduler:** The SLURM resource manager handles all job scheduling and resource allocation.

---

## 2. CARC Filesystem Structure
Storage is partitioned based on performance needs, backup requirements, and collaborative access.

| Directory Type | Path Schema | Quota (Default) | Backup Policy | Best For... |
| :--- | :--- | :--- | :--- | :--- |
| **Home** | `/home1/<user>` | 100 GB / 255k files | Snapshots (2 weeks) | Scripts, configuration files, small tools. |
| **Project** | `/project2/<pi_id>` | 15 TB free per PI | Snapshots (2 weeks) | Shared group data and active research files. |
| **Scratch** | `/scratch1/<user>` | 10 TB (temporary) | **NO BACKUP** | Large temp files and high-speed job I/O. |

### Storage Costs & Expansion
* **Free Tier:** 15 TB of project per PI is provided at no cost.
* **Expansion:** Additional storage can be purchased in 5 TB increments at $60/TB/year.
* **Cold Storage:** For long-term archiving of inactive data, use the `arcput` command. This is an unlimited (for now) archive space for files >10MB.

---

## 3. Data Sensitivity & Security Policies
* **Prohibited Data:** CARC systems do not currently support sensitive or regulated data, including HIPAA (Protected Health Information), FERPA (Student Records), or PII (Personally Identifiable Information).
* **Consultation:** If your research requires the use of restricted data, you must contact `carc-support@usc.edu` for a consultation before uploading any files.

---

## 4. Data Transfer Methods
Choose the method that best fits your file size and technical comfort level.

### A. Web-Based (Open OnDemand)
* **URL:** [ondemand.carc.usc.edu](https://ondemand.carc.usc.edu)
* **Features:** Provides a visual file explorer in your browser. Best for moving small individual files or managing directory structures visually.

### B. Command Line (rsync)
The most robust tool for syncing data between your local machine and CARC. It can resume interrupted transfers and only copies files that have changed.

```bash
# SCRIPT: Syncing local data to CARC
# Note: A trailing '/' after the source folder copies only the contents.
rsync -rltvh ~/Documents/my_data/ <username>@discovery.usc.edu:/project2/<pi_id>/my_data

# Flags:
# -r: recursive (include subdirectories)
# -l: preserve symlinks
# -t: preserve modification times
# -v: verbose (show progress)
# -h: human-readable file sizes
```

### C. GUI Clients (SFTP)
* Use standalone applications like **CyberDuck** or **FileZilla**.
* Connect using hostnames: `hpc-transfer1.usc.edu` or `discovery.usc.edu`.

### D. Globus & Rclone
* **Globus:** Ideal for fast transfers between different HPC centers or sharing with external collaborators.
* **Rclone:** Used to link cloud storage (Google Drive, OneDrive) to CARC; involves a more technical initial setup.

---

## 4. File Permissions & Ownership
Managing access is critical for collaboration and quota management.

### Permission Values
Permissions are calculated by summing numeric values:
* **4:** Read (`r`)
* **2:** Write (`w`)
* **1:** Execute (`x`)

### Essential Commands
* **Change Permissions (`chmod`):**
    ```bash
    chmod 640 file.txt       # User: rw, Group: r, Others: none 
    chmod +x script.sh       # Add execute permission 
    chmod -R g-w directory   # Recursively remove group write access 
    ```
* **Change Group Ownership (`chgrp`):**
    Quota is tracked via group ownership. Use this to ensure collaborators can access project files.
    ```bash
    chgrp ttroj_412 data.txt
    chgrp -R ttroj_412 /project/ttroj_412/group_data [cite: 7]
    ```

---

## 5. Data Integrity (Checksums)
To verify that files were not corrupted during transfer, use the `md5sum` utility to compare file "fingerprints".

### Single File Verification
```bash
md5sum <filename> 
# Example output: a71f703ed688eeca637dc27df714e854  data.zip 
