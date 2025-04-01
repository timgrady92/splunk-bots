### Using CyberChef directly in search to decode Hex encoded Auditd commands

index=botsv3 earliest=0 sourcetype=linux_audit type=USER_CMD
| cyberchef infield='cmd' operation="FromHex"
| table cmd

