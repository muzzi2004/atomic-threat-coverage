| Title                | Copying sensitive files with credential data                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Files with well-known filenames (sensitive files with credential data) copying                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0006: Credential Access](https://attack.mitre.org/tactics/TA0006)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1003: Credential Dumping](https://attack.mitre.org/techniques/T1003)</li></ul>  |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1003: Credential Dumping](../Triggers/T1003.md)</li></ul>  |
| Severity Level       | medium |
| False Positives      | <ul><li>Copying sensitive files for legitimate use (eg. backup) or forensic investigation by legitimate incident responder or forensic invetigator</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://room362.com/post/2013/2013-06-10-volume-shadow-copy-ntdsdit-domain-hashes-remotely-part-1/](https://room362.com/post/2013/2013-06-10-volume-shadow-copy-ntdsdit-domain-hashes-remotely-part-1/)</li><li>[https://www.slideshare.net/heirhabarov/hunting-for-credentials-dumping-in-windows-environment](https://www.slideshare.net/heirhabarov/hunting-for-credentials-dumping-in-windows-environment)</li><li>[https://dfironthemountain.wordpress.com/2018/12/06/locked-file-access-using-esentutl-exe/](https://dfironthemountain.wordpress.com/2018/12/06/locked-file-access-using-esentutl-exe/)</li></ul>  |
| Author               | Teymur Kheirkhabarov, Daniil Yugoslavskiy, oscd.community |
| Other Tags           | <ul><li>car.2013-07-001</li><li>car.2013-07-001</li></ul> | 

## Detection Rules

### Sigma rule

```
title: Copying sensitive files with credential data
id: e7be6119-fc37-43f0-ad4f-1f3f99be2f9f
description: Files with well-known filenames (sensitive files with credential data) copying
author: Teymur Kheirkhabarov, Daniil Yugoslavskiy, oscd.community
date: 2019/10/22
modified: 2019/11/13
references:
    - https://room362.com/post/2013/2013-06-10-volume-shadow-copy-ntdsdit-domain-hashes-remotely-part-1/
    - https://www.slideshare.net/heirhabarov/hunting-for-credentials-dumping-in-windows-environment
    - https://dfironthemountain.wordpress.com/2018/12/06/locked-file-access-using-esentutl-exe/
tags:
    - attack.credential_access
    - attack.t1003
    - car.2013-07-001
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        - Image|endswith: '\esentutl.exe'
          CommandLine|contains:
            - 'vss'
            - '/m'
            - '/y'
        - CommandLine|contains:
            - '\windows\ntds\ntds.dit'
            - '\config\sam'
            - '\config\security'
            - '\config\system'
            - '\repair\sam'
            - '\repair\system'
            - '\repair\security'
            - '\config\RegBack\sam'
            - '\config\RegBack\system'
            - '\config\RegBack\security'
    condition: selection
falsepositives:
    - Copying sensitive files for legitimate use (eg. backup) or forensic investigation by legitimate incident responder or forensic invetigator
level: medium
status: experimental

```





### es-qs
    
```
((Image.keyword:*\\\\esentutl.exe AND CommandLine.keyword:(*vss* OR *\\/m* OR *\\/y*)) OR CommandLine.keyword:(*\\\\windows\\\\ntds\\\\ntds.dit* OR *\\\\config\\\\sam* OR *\\\\config\\\\security* OR *\\\\config\\\\system* OR *\\\\repair\\\\sam* OR *\\\\repair\\\\system* OR *\\\\repair\\\\security* OR *\\\\config\\\\RegBack\\\\sam* OR *\\\\config\\\\RegBack\\\\system* OR *\\\\config\\\\RegBack\\\\security*))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/Copying-sensitive-files-with-credential-data <<EOF\n{\n  "metadata": {\n    "title": "Copying sensitive files with credential data",\n    "description": "Files with well-known filenames (sensitive files with credential data) copying",\n    "tags": [\n      "attack.credential_access",\n      "attack.t1003",\n      "car.2013-07-001"\n    ],\n    "query": "((Image.keyword:*\\\\\\\\esentutl.exe AND CommandLine.keyword:(*vss* OR *\\\\/m* OR *\\\\/y*)) OR CommandLine.keyword:(*\\\\\\\\windows\\\\\\\\ntds\\\\\\\\ntds.dit* OR *\\\\\\\\config\\\\\\\\sam* OR *\\\\\\\\config\\\\\\\\security* OR *\\\\\\\\config\\\\\\\\system* OR *\\\\\\\\repair\\\\\\\\sam* OR *\\\\\\\\repair\\\\\\\\system* OR *\\\\\\\\repair\\\\\\\\security* OR *\\\\\\\\config\\\\\\\\RegBack\\\\\\\\sam* OR *\\\\\\\\config\\\\\\\\RegBack\\\\\\\\system* OR *\\\\\\\\config\\\\\\\\RegBack\\\\\\\\security*))"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "((Image.keyword:*\\\\\\\\esentutl.exe AND CommandLine.keyword:(*vss* OR *\\\\/m* OR *\\\\/y*)) OR CommandLine.keyword:(*\\\\\\\\windows\\\\\\\\ntds\\\\\\\\ntds.dit* OR *\\\\\\\\config\\\\\\\\sam* OR *\\\\\\\\config\\\\\\\\security* OR *\\\\\\\\config\\\\\\\\system* OR *\\\\\\\\repair\\\\\\\\sam* OR *\\\\\\\\repair\\\\\\\\system* OR *\\\\\\\\repair\\\\\\\\security* OR *\\\\\\\\config\\\\\\\\RegBack\\\\\\\\sam* OR *\\\\\\\\config\\\\\\\\RegBack\\\\\\\\system* OR *\\\\\\\\config\\\\\\\\RegBack\\\\\\\\security*))",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Copying sensitive files with credential data\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
((Image.keyword:*\\\\esentutl.exe AND CommandLine.keyword:(*vss* *\\/m* *\\/y*)) OR CommandLine.keyword:(*\\\\windows\\\\ntds\\\\ntds.dit* *\\\\config\\\\sam* *\\\\config\\\\security* *\\\\config\\\\system* *\\\\repair\\\\sam* *\\\\repair\\\\system* *\\\\repair\\\\security* *\\\\config\\\\RegBack\\\\sam* *\\\\config\\\\RegBack\\\\system* *\\\\config\\\\RegBack\\\\security*))
```


### splunk
    
```
((Image="*\\\\esentutl.exe" (CommandLine="*vss*" OR CommandLine="*/m*" OR CommandLine="*/y*")) OR (CommandLine="*\\\\windows\\\\ntds\\\\ntds.dit*" OR CommandLine="*\\\\config\\\\sam*" OR CommandLine="*\\\\config\\\\security*" OR CommandLine="*\\\\config\\\\system*" OR CommandLine="*\\\\repair\\\\sam*" OR CommandLine="*\\\\repair\\\\system*" OR CommandLine="*\\\\repair\\\\security*" OR CommandLine="*\\\\config\\\\RegBack\\\\sam*" OR CommandLine="*\\\\config\\\\RegBack\\\\system*" OR CommandLine="*\\\\config\\\\RegBack\\\\security*"))
```


### logpoint
    
```
(event_id="1" ((Image="*\\\\esentutl.exe" CommandLine IN ["*vss*", "*/m*", "*/y*"]) OR CommandLine IN ["*\\\\windows\\\\ntds\\\\ntds.dit*", "*\\\\config\\\\sam*", "*\\\\config\\\\security*", "*\\\\config\\\\system*", "*\\\\repair\\\\sam*", "*\\\\repair\\\\system*", "*\\\\repair\\\\security*", "*\\\\config\\\\RegBack\\\\sam*", "*\\\\config\\\\RegBack\\\\system*", "*\\\\config\\\\RegBack\\\\security*"]))
```


### grep
    
```
grep -P '^(?:.*(?:.*(?:.*(?=.*.*\\esentutl\\.exe)(?=.*(?:.*.*vss.*|.*.*/m.*|.*.*/y.*)))|.*(?:.*.*\\windows\\ntds\\ntds\\.dit.*|.*.*\\config\\sam.*|.*.*\\config\\security.*|.*.*\\config\\system.*|.*.*\\repair\\sam.*|.*.*\\repair\\system.*|.*.*\\repair\\security.*|.*.*\\config\\RegBack\\sam.*|.*.*\\config\\RegBack\\system.*|.*.*\\config\\RegBack\\security.*)))'
```



