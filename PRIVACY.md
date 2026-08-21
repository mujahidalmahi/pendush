# Pendush Privacy Policy

**Last Updated:** August 2026  
**Version:** 1.0.1  
**Publisher:** PinkDotHash / Akruno  

---

## Zero Data Collection Guarantee

Pendush is built from the ground up as a **100% private, offline-first USB utility**. 

### 1. No Telemetry or Analytics
Pendush contains **no analytics SDKs, tracking pixels, crash telemetry, or diagnostics reporting services**. We do not collect:
- Your IP address
- Your operating system details
- The serial numbers or models of your USB devices
- The files or ISOs you process
- Usage duration, frequency, or error logs

### 2. Local Operational Logs
Operational event and troubleshooting logs are maintained locally on your computer in your local roaming application directory (`%APPDATA%\PinkDotHash\Pendush\logs\pendush.log`) with automatic rotation (maximum 3 files, 2MB each). These logs contain local execution records (e.g. drive letters, partition styles, ISO labels, and Win32 error codes) to help diagnose hardware issues.
- **Never Uploaded**: Local logs are never transmitted over the network.
- **User Control**: You can export logs to a file of your choice via the **Export Logs** button, or completely wipe all in-memory and on-disk log files at any time via the **Clear Logs** button in the **Logs & Settings** tab.

### 3. Local Hardware Operations
All partition formatting, raw byte streaming, MBR/GPT table modifications, ISO extractions, filesystem checks, and benchmark operations are executed directly on your physical hardware via standard Windows Win32 kernel subsystem APIs. No data from your storage media is ever transferred off your computer.

### 4. Update Checking
When the **Automatically Check for Updates** option is enabled, Pendush periodically queries the public GitHub Releases REST API (`https://api.github.com/repos/mujahidalmahi/pendush/releases/latest`) using HTTPS:
- Only public release metadata (`tag_name`, `browser_download_url`, `html_url`) is queried.
- No cookies, user identifiers, authentication tokens, or hardware parameters are transmitted in the request headers.
- If you prefer complete offline operation, uncheck "Automatically Check for Updates" in the **Logs & Settings** tab at any time.

### 5. USB Autorun Vaccine
The USB autorun protection feature creates a static local tag marker (`pendush_vaccine.tag`) containing a hardcoded ASCII string (`PENDUSH_VACCINE_v1.0.1`). It does not store user data or unique device fingerprints.

---

## Open Source & Transparency

Pendush's source code is publicly available for static inspection and independent verification at [https://github.com/mujahidalmahi/pendush-code](https://github.com/mujahidalmahi/pendush-code).
