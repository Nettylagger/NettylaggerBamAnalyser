# Nettylagger BAM Analyzer

PowerShell forensic utility for checking Windows BAM registry execution traces.

Nettylagger BAM Analyzer reads the Windows Background Activity Moderator (BAM) registry keys and displays recently executed programs with timestamps, users, file paths, and signature status. It is designed for quick manual checks when reviewing suspicious executions, unknown programs, client launches, or recently opened tools on a Windows system.

## Features

- Reads BAM entries from `HKLM:\SYSTEM\CurrentControlSet\Services\bam\UserSettings`
- Reads BAM State entries from `HKLM:\SYSTEM\CurrentControlSet\Services\bam\State\UserSettings`
- Shows recently executed applications detected by Windows BAM
- Displays application name, resolved path, user, SID, and registry source path
- Converts BAM FILETIME values into readable timestamps
- Shows execution time in UTC and local/user time
- Checks Authenticode file signature status
- Opens results in an interactive `Out-GridView` table
- Supports searching, sorting, and filtering inside the grid view
- Requires Administrator permissions because BAM registry keys are protected

## Usage

Run PowerShell as Administrator, then start the analyzer with:

```powershell
powershell -ExecutionPolicy Bypass -Command "Invoke-Expression (Invoke-RestMethod https://raw.githubusercontent.com/Nettylagger/NettylaggerBamAnalyser/refs/heads/main/NettyBamAnalyser)"
```

The script must be started with Administrator permissions because Windows protects the BAM registry keys.

## Signature Checks

The analyzer checks each resolved file path with Windows Authenticode signature verification.

Possible signature results:

- Valid Signature
- Invalid Signature (NotSigned)
- Invalid Signature (HashMismatch)
- Invalid Signature (NotTrusted)
- Invalid Signature (UnknownError)
- File Was Not Found

This helps separate signed system or vendor files from unsigned, modified, missing, or suspicious executables.

## What It Helps With

Nettylagger BAM Analyzer can help identify programs that were recently executed on the system, including suspicious tools, renamed executables, unsigned files, deleted files, or programs launched from unusual locations.

It is useful for checking traces of:

- suspicious executables
- unknown launchers
- renamed tools
- deleted programs that still have BAM traces
- unsigned applications
- files launched from Downloads, Temp, Desktop, or external folders
- tools started by another Windows user account

## How It Works

The script reads BAM registry values for each user SID, parses the stored Windows FILETIME timestamp, converts it into readable time formats, resolves the executable path, and checks the file's digital signature.

Each entry is returned with:

- Examiner time
- Last execution time in UTC
- Last execution time in local/user time
- Application name
- Resolved file path
- Signature status
- Windows user
- SID
- Registry path

Results are displayed in an interactive grid view titled with the total number of BAM entries and the detected user timezone information.

## Requirements

- Windows
- PowerShell
- Administrator permissions
- `Out-GridView` support

## Notes

BAM is a Windows execution artifact. It does not prove what a program did, but it can show that Windows recorded the application as executed.

Missing files may still appear if the executable was deleted, moved, or renamed after running.

Some Windows versions may store BAM data differently, so unsupported systems may return fewer results or fail to parse the BAM registry keys.
