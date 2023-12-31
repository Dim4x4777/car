---
title: "CAR-2021-12-001: Scheduled Task Creation or Modification Containing Suspicious Scripts, Extensions or User Writable Paths"
layout: analytic
submission_date: 2021/12/04
information_domain: Host
subtypes: Process
analytic_type: TTP
contributors: Lucas Heiligenstein
applicable_platforms: Windows
---
<br><br>
Detection of the creation or modification of Scheduled Tasks with a suspicious script, extension or user writable path. Attackers may create or modify Scheduled Tasks for the persistent execution of malicious code. This detection focuses at the same time on EventIDs 4688 and 1 with process creation (SCHTASKS) and EventID 4698, 4702 for Scheduled Task creation/modification event log.


### ATT&CK Detections

|Technique|Subtechnique(s)|Tactic(s)|Level of Coverage|
|---|---|---|---|
|[Scheduled Task/Job](https://attack.mitre.org/techniques/T1053/)|[Scheduled Task](https://attack.mitre.org/techniques/T1053/005/)|[Execution](https://attack.mitre.org/tactics/TA0002/), [Persistence](https://attack.mitre.org/tactics/TA0003/), [Privilege Escalation](https://attack.mitre.org/tactics/TA0004/)|Medium|


### D3FEND Techniques

|ID|Name|
|---|---| 
|D3-PSA | [Process Spawn Analysis](https://d3fend.mitre.org/technique/d3f:ProcessSpawnAnalysis)| 



### Data Model References

|Object|Action|Field|
|---|---|---|
|[process](/data_model/process) | [create](/data_model/process#create) | [command_line](/data_model/process#command_line) |



### Implementations

#### Creation of Suspicious Scheduled Tasks (Pseudocode, CAR native)


This detects the creation of suspicious scheduled tasks, either via a new process (command line) or direct through the corresponding Windows EIDs.


```
processes = search Process:create
susp_tasks_processes = filter processes where command_line CONTAINS("*SCHTASKS*") AND (command_line CONTAINS("*/CREATE*") OR command_line CONTAINS("*/CHANGE*")) AND (command_line CONTAINS("*.cmd*") OR command_line CONTAINS("*.ps1*") OR command_line CONTAINS("*.vbs*") OR command_line CONTAINS("*.py*") OR command_line CONTAINS("*.js*") OR command_line CONTAINS("*.exe*") OR command_line CONTAINS("*.bat*") OR (command_line CONTAINS("*javascript*") OR command_line CONTAINS("*powershell*") OR command_line CONTAINS("*wmic*") OR command_line CONTAINS("*rundll32*") OR command_line CONTAINS("*cmd*") OR command_line CONTAINS("*cscript*") OR command_line CONTAINS("*wscript*") OR command_line CONTAINS("*regsvr32*") OR command_line CONTAINS("*mshta*") OR command_line CONTAINS("*bitsadmin*") OR command_line CONTAINS("*certutil*") OR command_line CONTAINS("*msiexec*") OR command_line CONTAINS("*javaw*") OR (command_line CONTAINS("*%APPDATA%*") OR command_line CONTAINS("*\\AppData\\Roaming*") OR command_line CONTAINS("*%PUBLIC%*") OR command_line CONTAINS("*C:\\Users\\Public*") OR command_line CONTAINS("*%ProgramData%*") OR command_line CONTAINS("*C:\\ProgramData*") OR command_line CONTAINS("*%TEMP%*") OR command_line CONTAINS("*\\AppData\\Local\\Temp*") OR command_line CONTAINS("*\\Windows\\PLA\\System*") OR command_line CONTAINS("*\\tasks*") OR command_line CONTAINS("*\\Registration\\CRMLog*") OR command_line CONTAINS("*\\FxsTmp*") OR command_line CONTAINS("*\\spool\\drivers\\color*") OR command_line CONTAINS("*\\tracing*"))))
tasks = search Task:create
susp_tasks = filter tasks where (task_content CONTAINS("*.cmd*") OR task_content CONTAINS("*.ps1*") OR task_content CONTAINS("*.vbs*") OR task_content CONTAINS("*.py*") OR task_content CONTAINS("*.js*") OR task_content CONTAINS("*.exe*") OR task_content CONTAINS("*.bat*") OR (task_content CONTAINS("*javascript*") OR task_content CONTAINS("*powershell*") OR task_content CONTAINS("*wmic*") OR task_content CONTAINS("*rundll32*") OR task_content CONTAINS("*cmd*") OR task_content CONTAINS("*cscript*") OR task_content CONTAINS("*wscript*") OR task_content CONTAINS("*regsvr32*") OR task_content CONTAINS("*mshta*") OR task_content CONTAINS("*bitsadmin*") OR task_content CONTAINS("*certutil*") OR task_content CONTAINS("*msiexec*") OR task_content CONTAINS("*javaw*") OR (task_content CONTAINS("*%APPDATA%*") OR task_content CONTAINS("*\\AppData\\Roaming*") OR task_content CONTAINS("*%PUBLIC%*") OR task_content CONTAINS("*C:\\Users\\Public*") OR task_content CONTAINS("*%ProgramData%*") OR task_content CONTAINS("*C:\\ProgramData*") OR task_content CONTAINS("*%TEMP%*") OR task_content CONTAINS("*\\AppData\\Local\\Temp*") OR task_content CONTAINS("*\\Windows\\PLA\\System*") OR task_content CONTAINS("*\\tasks*") OR task_content CONTAINS("*\\Registration\\CRMLog*") OR task_content CONTAINS("*\\FxsTmp*") OR task_content CONTAINS("*\\spool\\drivers\\color*") OR task_content CONTAINS("*\\tracing*"))))
output susp_tasks_processes, susp_tasks

```


#### Splunk Search - Scheduled Task creation or modification containing suspicious script, extension or user writable path. (Splunk)


This is a Splunk representation of the above pseudocode search.


```
(((EventCode="4688" OR EventCode="1") CommandLine="*SCHTASKS*" (CommandLine="*/CREATE*" OR CommandLine="*/CHANGE*")) ((CommandLine="*.cmd*" OR CommandLine="*.ps1*" OR CommandLine="*.vbs*" OR CommandLine="*.py*" OR CommandLine="*.js*" OR CommandLine="*.exe*" OR CommandLine="*.bat*") OR (CommandLine="*javascript*" OR CommandLine="*powershell*" OR CommandLine="*wmic*" OR CommandLine="*rundll32*" OR CommandLine="*cmd*" OR CommandLine="*cscript*" OR CommandLine="*wscript*" OR CommandLine="*regsvr32*" OR CommandLine="*mshta*" OR CommandLine="*bitsadmin*" OR CommandLine="*certutil*" OR CommandLine="*msiexec*" OR CommandLine="*javaw*") OR (CommandLine="*%APPDATA%*" OR CommandLine="*\\AppData\\Roaming*" OR CommandLine="*%PUBLIC%*" OR CommandLine="*C:\\Users\\Public*" OR CommandLine="*%ProgramData%*" OR CommandLine="*C:\\ProgramData*" OR CommandLine="*%TEMP%*" OR CommandLine="*\\AppData\\Local\\Temp*" OR CommandLine="*\\Windows\\PLA\\System*" OR CommandLine="*\\tasks*" OR CommandLine="*\\Registration\\CRMLog*" OR CommandLine="*\\FxsTmp*" OR CommandLine="*\\spool\\drivers\\color*" OR CommandLine="*\\tracing*"))) OR ((EventCode="4698" OR EventCode="4702") ((TaskContent="*.cmd*" OR TaskContent="*.ps1*" OR TaskContent="*.vbs*" OR TaskContent="*.py*" OR TaskContent="*.js*" OR TaskContent="*.exe*" OR TaskContent="*.bat*") OR (TaskContent="*javascript*" OR TaskContent="*powershell*" OR TaskContent="*wmic*" OR TaskContent="*rundll32*" OR TaskContent="*cmd*" OR TaskContent="*cscript*" OR TaskContent="*wscript*" OR TaskContent="*regsvr32*" OR TaskContent="*mshta*" OR TaskContent="*bitsadmin*" OR TaskContent="*certutil*" OR TaskContent="*msiexec*" OR TaskContent="*javaw*") OR (TaskContent="*%APPDATA%*" OR TaskContent="*\\AppData\\Roaming*" OR TaskContent="*%PUBLIC%*" OR TaskContent="*C:\\Users\\Public*" OR TaskContent="*%ProgramData%*" OR TaskContent="*C:\\ProgramData*" OR TaskContent="*%TEMP%*" OR TaskContent="*\\AppData\\Local\\Temp*" OR TaskContent="*\\Windows\\PLA\\System*" OR TaskContent="*\\tasks*" OR TaskContent="*\\Registration\\CRMLog*" OR TaskContent="*\\FxsTmp*" OR TaskContent="*\\spool\\drivers\\color*" OR TaskContent="*\\tracing*")))

```


#### Elastic Search - Scheduled Task creation or modification containing suspicious script, extension or user writable path. (Elastic)


This is an ElasticSearch representation of the above pseudocode search.


```
((winlog.event_id:("4688" OR "1") AND process.command_line:*SCHTASKS* AND process.command_line:(*\/CREATE* OR *\/CHANGE*)) AND (process.command_line:(*.cmd* OR *.ps1* OR *.vbs* OR *.py* OR *.js* OR *.exe* OR *.bat*) OR process.command_line:(*javascript* OR *powershell* OR *wmic* OR *rundll32* OR *cmd* OR *cscript* OR *wscript* OR *regsvr32* OR *mshta* OR *bitsadmin* OR *certutil* OR *msiexec* OR *javaw*) OR process.command_line:(*%APPDATA%* OR *\\AppData\\Roaming* OR *%PUBLIC%* OR *C\:\\Users\\Public* OR *%ProgramData%* OR *C\:\\ProgramData* OR *%TEMP%* OR *\\AppData\\Local\\Temp* OR *\\Windows\\PLA\\System* OR *\\tasks* OR *\\Registration\\CRMLog* OR *\\FxsTmp* OR *\\spool\\drivers\\color* OR *\\tracing*))) OR (winlog.event_id:("4698" OR "4702") AND (winlog.event_data.TaskContent:(*.cmd* OR *.ps1* OR *.vbs* OR *.py* OR *.js* OR *.exe* OR *.bat*) OR winlog.event_data.TaskContent:(*javascript* OR *powershell* OR *wmic* OR *rundll32* OR *cmd* OR *cscript* OR *wscript* OR *regsvr32* OR *mshta* OR *bitsadmin* OR *certutil* OR *msiexec* OR *javaw*) OR winlog.event_data.TaskContent:(*%APPDATA%* OR *\\AppData\\Roaming* OR *%PUBLIC%* OR *C\:\\Users\\Public* OR *%ProgramData%* OR *C\:\\ProgramData* OR *%TEMP%* OR *\\AppData\\Local\\Temp* OR *\\Windows\\PLA\\System* OR *\\tasks* OR *\\Registration\\CRMLog* OR *\\FxsTmp* OR *\\spool\\drivers\\color* OR *\\tracing*)))

```


#### LogPoint Search - Scheduled Task creation or modification containing suspicious script, extension or user writable path. (Logpoint)


This is a LogPoint representation of the above pseudocode search.


```
((event_id IN ["4688", "1"] CommandLine="*SCHTASKS*" CommandLine IN ["*/CREATE*", "*/CHANGE*"]) (CommandLine IN ["*.cmd*", "*.ps1*", "*.vbs*", "*.py*", "*.js*", "*.exe*", "*.bat*"] OR CommandLine IN ["*javascript*", "*powershell*", "*wmic*", "*rundll32*", "*cmd*", "*cscript*", "*wscript*", "*regsvr32*", "*mshta*", "*bitsadmin*", "*certutil*", "*msiexec*", "*javaw*"] OR CommandLine IN ["*%APPDATA%*", "*\\AppData\\Roaming*", "*%PUBLIC%*", "*C:\\Users\\Public*", "*%ProgramData%*", "*C:\\ProgramData*", "*%TEMP%*", "*\\AppData\\Local\\Temp*", "*\\Windows\\PLA\\System*", "*\\tasks*", "*\\Registration\\CRMLog*", "*\\FxsTmp*", "*\\spool\\drivers\\color*", "*\\tracing*"])) OR (event_id IN ["4698", "4702"] (TaskContent IN ["*.cmd*", "*.ps1*", "*.vbs*", "*.py*", "*.js*", "*.exe*", "*.bat*"] OR TaskContent IN ["*javascript*", "*powershell*", "*wmic*", "*rundll32*", "*cmd*", "*cscript*", "*wscript*", "*regsvr32*", "*mshta*", "*bitsadmin*", "*certutil*", "*msiexec*", "*javaw*"] OR TaskContent IN ["*%APPDATA%*", "*\\AppData\\Roaming*", "*%PUBLIC%*", "*C:\\Users\\Public*", "*%ProgramData%*", "*C:\\ProgramData*", "*%TEMP%*", "*\\AppData\\Local\\Temp*", "*\\Windows\\PLA\\System*", "*\\tasks*", "*\\Registration\\CRMLog*", "*\\FxsTmp*", "*\\spool\\drivers\\color*", "*\\tracing*"]))

```



### Unit Tests

#### Test Case 1

Creation Scheduled Task with cmd. Calc.exe will be launched every minute

```
SCHTASKS /CREATE /SC MINUTE /MO 1 /TN "CALC_TASK" /TR "C:\Windows\System32\calc.exe"
```

#### Test Case 2

Creation Scheduled Task with cmd. Ping will be launched every minute

```
SCHTASKS /CREATE /SC MINUTE /MO 1 /TN "PING_TASK" /TR "cmd /c ping 8.8.8.8"
```


