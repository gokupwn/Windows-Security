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

- In a workgroup, if you have a user in one computer you can't use the same user in another computer.
- To do that, you will need to create the same account (same username and password) across all workgroup computers.
- But keep in mind, that each user you create (even if the account have the same username and password) will have a different SID number on each computer.

![[Workgroup.png]]

# Windows Active Directory and Group Policy:

