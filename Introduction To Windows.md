# Manage Local Accounts:

## Using GUI - Computer Management Tool:

![[Computer_management_screenshot.png]]

- All Windows computers have local accounts and groups, even if the computer is a member of a domain.
- We can use the `Computer Management` console.
- You can open it by typing: `WIN+R > compmgmt.msc`

## Using Terminal - PowerShell and CMD:

```powershell
Get-Help LocalUser
```

```cmd
net.exe help user
```

## Security ID Numbers:

- Every user account, computer account, and group has a unique identifying Security ID (`SID`) number

`S-1-5-21-2265252534-2850512945-2770680393-1001`

- Each computer creates SID numbers for its local users and groups. (Domain controllers do the same for domain accounts)
- These SID numbers are unique, except for the SID numbers of some well-known users and groups, which are shorter standardized across all boxes.

```txt
S-1-1-0 = Everyone group
S-1-5-11 = Authenticated Users group
S-1-5-32-544 = Local Administrators group
```

- You can rename the username of an account but the SID number stays the same.
- If a user is deleted and a new one is created, the new user will have a different SID number.
- If a user is named "Bob" on Computer A and another local account named on Computer B, These two accounts will have different SID number.
- Windows only cares about SID numbers when checking for privileges and permissions.
- When you log on, you will have an `ID card` in which you will have all of your SID numbers. Using this `ID Card` your computer identifies your programs and regulates your activities. This `ID Card` is called your `Security Access Token` (`SAT`).

```cmd
C:\> rem "Display the SID number of your user account"
C:\> whoami.exe /all /fo list
```

## Security Access Token (`SAT`):

- When you log on to your computer, your computer obtains the SID number for your user account and all the SID numbers for all the groups to which you belong. Your computer will also find out what your `local privileges` are on the machine, for example, the take ownership privilege.
- Every program and process you launch has a copy of your SAT, everything the computer needs to know to enforce permissions and privilege restrictions is contained in that token.
- Whenever a process you own tries to access a resource or to exercise a privilege, the operating system gets the SAT from that process and sees if you (and your groups) are permitted to perform the requested action.
- This mad possible by the access control lists (`ACLs`) 

## Workgroup:
- No domain controllers
- Users are typically local administrators of their own machines
- Local groups cannot have users from other machines
- To have a workgroup admin, you will need to create a separate administrative account on every machine.
- In a workgroup, if you have a user in one computer you can't use the same user in another computer.
- To do that, you will need to create the same account (same username and password) across all workgroup computers.
- But keep in mind, that each user you create (even if the account have the same username and password) will have a different SID number on each computer.

![[Workgroup.png]]

# Windows Active Directory and Group Policy:

### Active Directory Domains:

- Active Directory (AD) is a database of user accounts and other information that gets installed on a Windows Server (a `Domain Controller`)

### Domain Controllers:

- A `domain controller` is just a server that helps to manage the AD database.
- The AD database also contains all the SID numbers of the users, computers and groups.
- Each resource (file, database, email box, printer, whatever) has a list of SID numbers attached to it with the permissions that each SID number has to the resource. This list of permissions based on user and group SID numbers is called an `access control list` (`ACL`)
- `Multi-master replication`: When you make a change to the AD database on any domain controller in an AD domain, this change is then automatically replicated to all other domain controllers.
- If there is a conflict, the latter change overrides the earlier one.
- AD domain controllers use `timestamps` and `update sequence numbers` on every property of every object to keep the replication straight.
- A domain controller (Windows Server 2008 or later) that run as Read-Only Domain Controller (`RODC`) - Any change on the AD database of the RODC will never be replicated to any other controllers.
- `RODC` cache only the credentials of the groups specified by the  administrator.

### A user in the Domain:

- You are in the `domain` if you have an account (which have your SID number and other information about  you) in the Active Directory database.
- A `domain` is all the users, computers, and groups that have accounts in the AD database.

#### Who or what can be in the `domain`?

- Anything with a SID number can be in a domain.
- Users, groups, and computers have SID numbers.
- Computers on windows active directory have their accounts and passwords and automatically update them periodically. When a computer that is a member of a domain boots up,  it logs into the domain just like a human.

#### What else is in the active directory database?

- `Account information`: is the most important thing in the AD database
- Active Directory database is like a `registry` for the entire network.
- Windows computers store all their configuration settings in a database called the `registry`
- Active Directory can store many of the configuration settings for all users and computers. It stores these settings in the form of `Group Policy Objects` that modify Registries and other things.
##### A list of what can be stored in Active Directory:

- User account properties and passwords
- Groups and their memberships
- Computer properties and passwords
- Domain names and trust relationships
- Kerberos master keys
- Digital certificates and Certificate Trust Lists
- Organizational Units and their members
- LANs and IP subnets in the organization
- AD replication links and their settings
- Shared printer locations (UNC paths)
- Exchange Server directory information
- Group Policy Objects
- Any custom data you want to add

> **Note**: Active directory is a general-purpose database and can be accessed through an industry standard protocol `LDAP`. Active Directory uses the same database engine as Microsoft Exchange Server and can store millions of objects. (Max Size = 4000GB  = 4TB)

#### Global Vs Local Users/Groups:

- `Local` users and groups  are accounts in the database of non-domain controllers.
- `Domain` use, computer, group has its account in the AD database. These domain accounts are available for use by any computer that has joined the domain.

