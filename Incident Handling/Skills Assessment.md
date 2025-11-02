``` bash
grep -B5 -A10 '"eventID": "4688"' wazuh_export.json | grep -i "mimikatz" -A1

jq '.[] | select(._source.agent.name == "DB01") | select(._source.rule.groups[] == "persistence")' wazuh_export.json

grep -B5 "diagnostics_data.zip" wazuh_export.json
```

