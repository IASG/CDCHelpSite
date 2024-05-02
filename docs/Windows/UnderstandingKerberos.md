---
title: Understanding Kerberos
layout: home
nav_order: 2
---

# Why Kerberos?

Anyone who has taken CYB E 230/231 has probably heard of LDAP (Lightweight Directory Access Protocol) and possibly SSL/TLS. Kerberos is like a combination of the two. It uses the key and encryption methods of SSL/TLS with the user searching and authorization of LDAP.

# Kerberos Overview

### Network Protocol

Kerberos works over port 88 and has to send four packets to first authenticate (AS-req/rep) and then authorize (TGS-req/rep) the user. For example, a request to access a service would generate the following kerberos packets:

AS-req: client -> AD
AS-rep: AD -> client
TGS-req: client -> AD
TGS-rep: AD -> client

Starting with AS-req/rep, these are to check with the authentication server (AS) and authenticates a user that wants access to any given service managed by the domain. The AS-req packet will send a few required objects as well as some optional requests for data. The required fields are cname (the user being authenticated), sname (the service that user is requesting), realm (the domain the service is under), and addresses (what machine the request is coming from). The additional data for a request is in the padata (pre-authentication data) section, and two common ones that are often found in requests are pA-ENC-TIMESTAMP and pA-PAC-REQUEST. pA-ENC-TIMESTAMP is technically required as of Kerberos version 5 to ensure that the request isn't the same as a previous one, and that the user making the request is allowed to request a ticket for the user in the cname, but it is not always required. pA-PAC-REQUEST asks for the privilege attribute certificate (PAC) which keeps track of authorization information about the user like which groups they're in. \[1\]

After receiving the AS-req, the AS checks that the host, domain, and user exist before sending two different encrypted items in the AS-rep. First, it sends a message encrypted with the client's private key (typically a hash of their password) that contains a session key that the client can use to communicate with the ticket granting service (TGS). The second item it sends is called a ticket granting ticket (TGT). Both of these names come from the fact that it is a ticket that is used to generate other tickets to access a service. The TGT is signed with the TGS's key and contains the client's session key mentioned earlier, the client name, and validity checking for the ticket. Once the client has both of these messages, it is ready to request access to a service from the TGS. \[1\]

In the TGS-req, the TGT, realm, and sname are included. The TGT is used to authenticate the sender. The realm is to show what domain it is for. The sname is the service that the request is for. With this, the ticket granting service can check that the requester is authenticated and grant access to the given service. \[1\]

Once the TGS receives the TGS-req, it fully authenticates the user and uses the information to generate a client/TGS session key. This session key can then be used by the client like a pass to allow access to a service. \[1\]

# Possible Problems Popping Up

As with all authentication methods, it has to work when the correct credentials are provided. This allows attackers to brute force common credentials. On top of this, Windows will say if preauth is enabled (important for AS-rep roasting later) or if the username is correct. \[2\]

One method to gain persistance or escalate privileges is to do something called kerberoasting. To do this, an attacker needs an active session ticket that they can pull offline for a service with a weak password. With this, they can crack the session account's password. This happens because the ticket is made up of the client's session key, cname, sname, realm, and a couple other flags that can be gathered from the initial TGS-rep the service ticket came from. With this, an attacker would likely be able to log on as the service itself with that service's access until the password is changed for the service account. \[2\]

Another way to gain persistence is to generate TGTs. Through various means (like having admin privileges on a box) an attacker would need to get the password hash for the KRBTGT user. Once acquired, the attacker can put whatever information they like into the TGT and use it to authenticate as any user. This is often called a golden ticket attack.

The last major misconfiguration requires preauth to be disabled, and it is called AS-rep roasting. For this, an attacker just needs to know a valid username, realm, service, and machine. With this, the attacker makes their own AS-req and receives an encrypted session key along with a valid TGT. The session key will be encrypted with the user's password, so an attacker can crack it using brute force. This works because the session key happens to be bundled with other information like the realm and principal name. These are used to check that the key was decrypted properly. \[2\]

Why does the session key need to be decrypted? can't we just use the TGT? Not quite, the client has to send a payload encrypted with the session key that has the client name and a timestamp. The TGS reviews it by decrypting the payload with the same session key from the TGT. This is made to ensure that: 
1) The TGT and session key were sent together 
2) The client knows the password in the AS that was used to encrypt the session key (AKA: their password)

# Spotting and Defending

### Ensuring preauth is on \[2\]

1. In the server manager, click on "Tools" in the upper right
2. Click on "Active Directory Users and Computers"
3. Click on the "Users" folder
4. Right click on a user and select "Properties"
5. In the top bar, select "Account"
6. Scroll to the bottom of the "Account Options" section
7. Ensure "Do not require Kerberos preauthentication" is **un**checked
8. For general security, all other options in that section should also be unchecked


### Auditing

kerberos can be very hard to audit because it is made to look like a normal user is logging in. There are a couple clues that may show up in the Windows event log, though.

### Viewing logs

1. Start by hitting the Windows key and searching for "Event Viewer"
2. Click the result that pops up and wait for it to load
3. Click the drop down by Windows Logs in the left pane
4. Click on security

Filter by log types:
1. Click on "Filter Current Log..." in the right pane
2. Enter an eventId or edit the XML
3. Click Ok

Important IDs 4624 (logon success), 4625 (logon failure)

Search for failed logon events for user Administrator:
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

What to look for:
- A large amount of preauth failures followed by a successful TGT request
- The Administrator account getting a kerberos ticket (Green team only uses the given users, and you shouldn't be logging in with domain admin (DA) remotely other than domain joining)

# Sources

\[1\] Neuman, Dr.C. et al. (2005) *RFC 4120: The Kerberos Network Authentication Service (v5)*, *IETF Datatracker*. Available at: https://datatracker.ietf.org/doc/html/rfc4120 (Accessed: 24 April 2024). 

\[2\] Evans, S. (2022) *Exploiting kerberos for lateral movement & privilege escalation*, *NOPSEC*. Available at: https://www.nopsec.com/blog/exploiting-kerberos-for-lateral-movement-and-privilege-escalation/ (Accessed: 24 April 2024). 