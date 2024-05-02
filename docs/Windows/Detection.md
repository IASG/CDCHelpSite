---
title: Detection
layout: default
nav_order: 7
parent: Windows
---

# Windows Security Logs

Find users authenticating with NTLM
```
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">
      *[
        EventData[Data[@Name='LogonProcessName']='NtLmSsp']
      ] 
    </Select>
  </Query>
</QueryList>
```

Find failed logons for the Administrator user (change 4625 to 4624 if successful logins are needed):
```
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">
      *[
        EventData[Data[@Name='TargetUserName']='Administrator']
        and
        System[(EventID='4625')]
      ] 
    </Select>
  </Query>
</QueryList>
```

# net

`net localgroup Users`: Get all local users for the maching
`net localgroup Administrators`: Get all local admins
`net session` `query user` or `quser`: Get logged in session
`net share`: list all available SMB shares
\[1\]

# Sources
\[1\] C. Hope, “Net command,” Computer Hope, https://www.computerhope.com/nethlp.htm (accessed May 1, 2024). 