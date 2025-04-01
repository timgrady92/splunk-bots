### Using CyberChef directly in search to decode Hex encoded Auditd commands

```
index=botsv3 earliest=0 sourcetype=linux_audit type=USER_CMD
| cyberchef infield='cmd' operation="FromHex"
| table cmd
```
### Using a custom lookup table (.csv) to ignore IP ranges AND regex to search for specific file extensions in order to identify downloads from internal servers

```
index=botsv3 earliest=0 http_status=200
| lookup internal_ips.csv src_ip OUTPUT src_ip AS internal_ip 
| where isnull(internal_ip)
| regex uri_path=".*\.(html|jpg|jpeg|png|css|js|gif|pdf|gz|tar|txt)$"
| table src_ip, uri_path, _time
```
### Using eval, xyseries, and fillnull to create organized chart which identifies the number of successful logins, counting by logon type

```
index=botsv3 earliest=0 sourcetype=WinEventLog EventCode=4624 (Logon_Type=0 OR Logon_Type=2 OR Logon_Type=3 OR Logon_Type=4 OR Logon_Type=5 OR Logon_Type=7 OR Logon_Type=8 OR Logon_Type=9 OR Logon_Type=10 OR Logon_Type=11)
| stats count by Account_Name, Logon_Type
| eval Logon_Type_Description=case(
Logon_Type==2, "Interactive",
Logon_Type==3, "Network",
Logon_Type==4, "Batch",
Logon_Type==5, "Service",
Logon_Type==7, "Unlock",
Logon_Type==8, "Network Cleartext",
Logon_Type==9, "New Credentials",
Logon_Type==10, "RDP",
Logon_Type==11, "Cached Interactive"
)
| xyseries Account_Name Logon_Type_Description count
| fillnull value=0 "Interactive" "Network" "Batch" "Service" "Unlock" "Network Cleartext" "New Credentials" "RDP" "Cached Interactive"
```
