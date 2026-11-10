
# Windows File Integrity Monitoring (FIM) with Wazuh

## Project Overview

This project demonstrates how to configure and use Wazuh SIEM to monitor file activity on a Windows endpoint. File Integrity Monitoring (FIM) was configured to detect file creation, modification, and deletion in real time.

The goal was to simulate file activity, investigate the resulting security alerts, and understand how endpoint monitoring helps security analysts identify potentially unauthorized changes.

## Tools & Technologies

- Wazuh SIEM 4.14.10
- Ubuntu Server (Wazuh Manager, Indexer, and Dashboard)
- Windows Endpoint (Wazuh Agent)
- VirtualBox
- File Integrity Monitoring (FIM)
- MITRE ATT&CK Framework

## Lab Environment

| Component | Configuration |
|---|---|
| SIEM | Wazuh 4.14.10 |
| Manager | Ubuntu VM |
| Endpoint | Windows host |
| Agent ID | 001 |
| Agent Name | S-C1L6FK3 |
| Agent IP | 192.168.56.1 |
| Monitored Directory | C:\FIM-Test |
| Monitoring Mode | Realtime |

## Configuration

Configured the Windows Wazuh Agent to monitor the `C:\FIM-Test\` directory.

Added the following configuration inside the existing `<syscheck>` section of `ossec.conf`:

```xml
<directories check_all="yes" realtime="yes">C:\FIM-Test\</directories>
```

Validated the XML configuration and restarted the Wazuh Agent service.

## Investigation 1: File Creation

**Action:** Created a test file named `SOC-Investigation.txt` inside `C:\FIM-Test`.

**Wazuh Detection:**
- Event: `syscheck_new_entry`
- Rule ID: 554
- Severity: Level 5
- Description: File added to the system.
- Detection Mode: Realtime

**Finding:** Wazuh successfully detected the creation of a new file in the monitored directory.

## Investigation 2: File Modification

**Action:** Modified `SOC-Investigation.txt` by adding the following text:

> Unauthorized modification simulation for SOC investigation.

**Wazuh Detection:**
- Event: `syscheck_integrity_changed`
- Rule ID: 550
- Severity: Level 7
- Description: Integrity checksum changed.
- Detection Mode: Realtime

### Observed Changes

| Attribute | Before | After |
|---|---|---|
| File Size | 48 bytes | 111 bytes |
| MD5 | `9e64c906066a0b663406ac67bf0b140a` | `681245c303c834d9ec19826f85fee260` |
| SHA1 | `69f627e8d9444f8ef299fdc28e9878203f95ad55` | `99fef9faec297dc0664e174fe32967bf59deaf62` |
| SHA256 | `ce25481752740beded92ee61668777138f12327686d5b5010d8b29e54072687d` | `643e4dd01f7ca0dfd50fcbad8697e2c1cb5d7e81be6d4abf30d1e05304ae3e4c` |

**MITRE ATT&CK Mapping:** T1565.001 – Stored Data Manipulation.

**Finding:** Wazuh detected changes to the file's size, modification time, and cryptographic hashes.

## Investigation 3: File Deletion

**Action:** Deleted `SOC-Investigation.txt` from the monitored directory.

**Wazuh Detection:**
- Event: `syscheck_deleted`
- Rule ID: 553
- Severity: Level 7
- Description: File deleted.
- Detection Mode: Realtime

### Observed Details

- File Path: `C:\FIM-Test\SOC-Investigation.txt`
- File Size Before Deletion: 111 bytes
- Previous SHA256: `643e4dd01f7ca0dfd50fcbad8697e2c1cb5d7e81be6d4abf30d1e05304ae3e4c`

**MITRE ATT&CK Mappings:**
- T1070.004 – File Deletion
- T1485 – Data Destruction

**Finding:** Wazuh generated a Level 7 alert after detecting the deletion of the monitored file.

## Investigation Summary

| Activity | Wazuh Rule ID | Severity | Result |
|---|---:|---:|---|
| File Creation | 554 | 5 | Detected |
| File Modification | 550 | 7 | Detected |
| File Deletion | 553 | 7 | Detected |

All three controlled file activities were successfully detected by Wazuh in real time.

## Security Analyst Takeaways

Through this investigation, I gained practical experience with:

- Configuring Wazuh File Integrity Monitoring.
- Monitoring Windows endpoint activity.
- Investigating SIEM alerts and event fields.
- Comparing file hashes to identify integrity changes.
- Understanding the security implications of file deletion and modification.
- Mapping security events to the MITRE ATT&CK framework.
- Analyzing alert severity and detection metadata.

## Conclusion

This project demonstrates how Wazuh can be used to monitor files and detect changes that may indicate suspicious activity.

The lab successfully detected file creation, modification, and deletion events on a Windows endpoint. These capabilities are important for SOC analysts investigating endpoint integrity, suspicious changes, and potential data destruction.

**Note:** All activities were intentionally performed in a controlled lab environment. MITRE ATT&CK mappings represent associated detection classifications and do not establish that a malicious attack occurred.

## Screenshots

Add screenshots of your investigations to a `screenshots/` folder:

1. `fim-configuration.png`
2. `file-creation-alert.png`
3. `file-modification-alert.png`
4. `file-deletion-alert.png`
