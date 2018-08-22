---
title: Workstation Local Admins and Auditing via Active Directory
date: 2017-02-27 16:04:17
tags:
  - Active Directory
  - Auditing
---
One of the common discussion points in any environment is local administrator permissions on workstations, servers and the entire domain. And whilst Microsoft have best practice guidelines around managing administrator access, actually adhering to these and maintaining a level of control over them for long periods is difficult.

You can read on the Least-Privilege Administrative Model on TechNet below:

[Implementing Least-Privilege Administrative Models](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)


I'll be the first to admit that my current environment doesn't handle Local Administrator privileges all too well in terms of MS best practices, which I won't go into here, but what we have done well is enable administrators to audit who has local admin rights on the workstations at any given moment.

### Justification

When a user requires local admin rights on their workstation, they have to supply a valid business case for this and justify which tasks they are performing that require elevated permissions.

In each case this will require approval from their manager before even coming through to anyone in the IT department. Whether there is enough understanding around the approval and justification at this point is questionable to the point of saying No.

### IT divine intervention

Once the approval has been sought and the request for workstation local admin rights has made it's way to the IT department, we then look at the justification and see if this is indeed justified or if there is the possibility that the user doesn't actually understand what they are asking for or that they aren't aware of another possible way of completing a specific task.

**Only then will we look at granting the users the local admin access that they desire.**

As many of you will be aware, maintaining control over your local administrators in your environment is time consuming and frustrating as it only takes someone to update Java on their machine to send you down a rabbit hole of incompatibility issues and whilst signing a disclaimer that you won't help them is favourable from a system administrator perspective, the IT executives don't like your unwillingness to help someone who is incapacitated.

### Active Directory & Group Policy to the rescue

In order to limit the irreversible damage caused by not following the MS best practices outlined above we have taken measures which allow us to maintain a level of control and auditability within the environment.

Every person that gets local admin access on their workstation gets a corresponding Active Directory Resource Group created in the following format

```
OU ac-L1234567-LocalAdmin
```

The format of the group is pretty self-explanatory and the group is created in the corresponding Organisational Unit.

The user requesting the local admin access then has their AD account added as a member of the newly created group.

In order for the user to inherit the required rights we use Group Policy to add the newly created group to the Local Administrators group on the users workstation.

![GPP NewGroup](/assets/img/LocalAdminGPO.png)

We can use this single group policy setting to add all groups created in the same fashion to the corresponding local admin group on the workstations by specifying the `%computername%` environment variable and by ensuring we have the two `Delete All Member(s)` boxes ticked we can ensure people aren't being added directly to the groups on their workstations.

This setting can be found under

`Computer Configuration > Preferences > Control Panel Settings > Local users and Groups`

### Summary

Administration of Local Admin permissions in a complex environment can be troublesome and seem a million miles away from best practice but damage limitation can be introduced allowing you to maintain at least a sensible amount of control over these permissions so when auditors come asking questions, you can at least give them the numbers that they require to do their job so you can continue to do yours.

I have created a Powershell function to assist with managing these groups which I will cover in my next post:

[Auditing Local Administrators with Powershell](http://millerb.co.uk/auditing-local-administrators-with-powershell/)