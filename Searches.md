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
