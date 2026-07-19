# Nettylagger BAM Analyser

![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-5391FE?logo=powershell&logoColor=white)
![Windows](https://img.shields.io/badge/Platform-Windows-0078D6?logo=windows&logoColor=white)
![Forensics](https://img.shields.io/badge/Forensics-BAM%20Registry-orange)
![Use](https://img.shields.io/badge/Use-Defensive%20Analysis-yellow)

**Nettylagger BAM Analyzer** is a PowerShell forensic utility for checking Windows Background Activity Moderator
execution traces. It reads BAM registry keys and displays recently executed programs with timestamps, users,
resolved file paths, registry source paths, and Authenticode signature status.

It is designed for quick manual reviews when checking suspicious executions, unknown launchers, renamed tools,
client starts, deleted programs, or recently opened utilities on a Windows system.

## Short Description

```text
Windows BAM registry forensic analyzer for recently executed programs, timestamps, users, paths, and signature checks.
```

## Features

- Reads BAM entries from `HKLM:\SYSTEM\CurrentControlSet\Services\bam\UserSettings`
- Reads BAM State entries from `HKLM:\SYSTEM\CurrentControlSet\Services\bam\State\UserSettings`
- Shows recently executed applications recorded by Windows BAM
- Displays application name, resolved path, user, SID, and registry source path
- Converts BAM FILETIME values into readable timestamps
- Shows execution time in UTC and local/user time
- Checks Authenticode file signature status
- Detects missing files that still have BAM traces
- Opens results in an interactive `Out-GridView` table
- Supports searching, sorting, and filtering inside the grid view
- Uses an orange console theme

## Usage

Run PowerShell as Administrator, then start the analyzer:

```powershell
powershell -ExecutionPolicy Bypass -Command "Invoke-Expression (Invoke-RestMethod https://raw.githubusercontent.com/Nettylagger/NettylaggerBamAnalyser/refs/heads/main/NettyBamAnalyser)"
```

Administrator permissions are required because Windows protects the BAM registry keys.

## Output Fields

Each BAM entry contains:

| Field | Description |
| --- | --- |
| Examiner Time | Timestamp converted for the examiner/local system |
| Last Execution Time (UTC) | Execution timestamp in UTC |
| Last Execution User Time | Execution timestamp adjusted using the detected user timezone bias |
| Application | Executable filename recorded by BAM |
| Path | Resolved local path when possible |
| Signature | Authenticode signature status |
| User | Windows account resolved from the SID |
| SID | User security identifier |
| Regpath | BAM registry source path |

## Signature Checks

The analyzer checks resolved file paths with Windows Authenticode verification.

Possible signature results:

- `Valid Signature`
- `Invalid Signature (NotSigned)`
- `Invalid Signature (HashMismatch)`
- `Invalid Signature (NotTrusted)`
- `Invalid Signature (UnknownError)`
- `File Was Not Found`

This helps separate signed system/vendor files from unsigned, modified, missing, or suspicious executables.

## What It Helps With

Nettylagger BAM Analyzer can help identify programs that were recently executed on the system, including:

- suspicious executables
- unknown launchers
- renamed tools
- deleted programs that still have BAM traces
- unsigned applications
- files launched from `Downloads`, `Temp`, `Desktop`, or external folders
- programs started by another Windows user account
- client launchers or tools used shortly before a review

## How It Works

The script enumerates user SIDs under the BAM registry paths, reads executable entries, parses the stored Windows
FILETIME timestamp, converts it into readable time formats, resolves the executable path, and checks the file's digital
signature.

Results are shown in an interactive `Out-GridView` window titled with the total number of BAM entries and detected
timezone information.

## Requirements

- Windows
- PowerShell 5.1+
- Administrator permissions
- `Out-GridView` support

## Notes

BAM is a Windows execution artifact. It can show that Windows recorded an application as executed, but it does not prove
what the program did after launch.

Missing files may still appear if the executable was deleted, moved, or renamed after running.

Some Windows versions store BAM data differently, so unsupported systems may return fewer results or fail to parse the
BAM registry keys.

## Intended Use

This tool is intended for defensive analysis, screenshare checks, system review, and local forensic triage.
