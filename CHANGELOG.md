# Changelog

SharkTAKDisplay versions (the payload in `payloads/library/recon/SharkTAKDisplay`). Newest first.

## 1.0.1
- **Data packages build again**:
  - The package failed and the payload could stop whenever the hour, minute or second was 08 or 09, because Bash read those as octal numbers. The device's number pickers are guarded the same way.
  - About half the files in each package got a bad checksum: BusyBox `awk` caps numbers at 2147483647, so that math now runs in Bash.
  - The zip writer no longer needs `od` (it falls back to `hexdump` or plain Bash), and a failed build is explained in `logs/zipcheck.log`.
- **Big scans no longer lock up the jack**: nmap runs at the lowest CPU priority as a connect scan (`-sT`, `SCAN_TYPE`) with `-T3`, a 50 packets/s cap (`SCAN_RATE`) and capped parallelism. `logs/vitals.log` records memory and load every 3 s while scanning. The service scan runs in batches of 4 hosts (`SCAN_BATCH`), each saved as it finishes. A stuck batch is stopped after 240 s (`BATCH_LIMIT`), and version detection (`-sV`, about 20 MB) only runs with 30 MB free (`MIN_FREE_KB`), so on the Shark Jack Display it's a plain port scan. A memory watchdog stops nmap if free memory drops below 3 MB (`WATCHDOG_KB`) instead of letting the jack freeze; the results so far are kept. On battery with more than 24 live hosts (`BIG_SCAN_HOSTS`), SharkTAKDisplay asks whether to continue or run a lighter scan (top 20 ports, no version detection).
- What the TAK Server sends during a session is capped at 512 KB in memory and freed right after contacts are read, and each session's size is logged.
- **Logs survive a shutdown**: logs moved to `/root/sharktakdisplay/logs/` on flash, with `history.log` and the last step that was running. After an unclean stop, the next start shows where it stopped.

## 1.0.0
- First release, as SharkTAKDisplay, with TAKtical Sharky as the logo.
- **Easy setup**: the first run stages `/root/sharktakdisplay/certs/` with a README and writes a `default.cfg` with every main setting. An ATAK **connection package** (`.zip`) dropped in `certs/` imports the server, port and both certificates. *Settings > Setup Check* shows packages and TAK status, fixes what's missing and tests the link. *Settings > Install Packages* installs everything in one press. Settings live in one file, the payload's `default.cfg`.
- **Works with the firmware's basic nmap**: the DHCP and rogue-DHCP check uses a DHCP request of its own plus `tcpdump`, and doesn't need nmap scripts. *Install Packages* offers `nmap-full` for the optional NSE scripts, and Setup Check shows whether nmap's script engine is there.
- **Data packages without a zip package**: OpenWrt 23.05 has no `zip` for the Shark Jack Display's chip, so SharkTAKDisplay builds the zip itself with the firmware's `gzip`, and checks every package with `unzip -t` before sending.
- On exit the LED goes back to what it was before the payload started, or off if that's unknown. TAK traffic uses STAGE2.
- **Settings that stay put**: SharkTAKDisplay writes its own `default.cfg`, one line per setting changed in place, because Hak5's save command adds a new line on every save. A file with repeated settings is tidied up on the next run, keeping your values over the defaults (`default.cfg.bak` keeps the old one).
- **SSH while the payload runs**: the payload starts SSH, and *Settings > SSH / IP* shows the address.
- **Scans**: network fingerprint (IP / gateway / DNS, DHCP servers with rogue DHCP detection, LLDP/CDP switch, port and VLAN in receive-only mode, SNMP sysLocation, internet check, public IP), nmap host and service discovery with optional light safe NSE scripts, per-network baseline diff (new and missing devices, new open ports), and packet capture.
- **TAK output**: a survey marker with the summary in the remarks, plus a MANIFEST v2 data package with the results attached to the marker. Send it to one contact or everyone, or only upload it to the TAK Server. Optional per-host markers and a GeoChat alert on baseline changes or rogue DHCP.
- **Store and forward**: results queue when the TAK Server can't be reached and are sent with *Send Queue*.
- **Location**: LLDP-MED coordinates, anchor to your own ATAK position from the TAK Server, saved sites, manual lat/lon, coordinates in the switch location text, public IP geolocation, or the last known position.
- **TAK link**: TLS 8089 with a client certificate (PEM, .p12 conversion, or enrollment with a TAK username on 8446), TCP, or UDP. Works without Bash `/dev/tcp` by falling back to `nc` or `nmap`.
