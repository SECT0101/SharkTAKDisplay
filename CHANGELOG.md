# Changelog

SharkTAKDisplay versions (the payload in `payloads/library/recon/SharkTAKDisplay`). Newest first.

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
