# A cheatsheet for LDAP based AD enumeration

## To get the Domain name

```ldap
ldapsearch (&(objectClass=domain)) --attributes name
```

## To get the Parent Domain

```ldap
ldapsearch (objectClass=domainDNS) --attributes msDS-ParentDistName
```

## To get Domain Policy

```ldap
ldapsearch (objectClass=domainDNS) --attributes minPwdLength,maxPwdAge,lockoutThreshold,lockoutDuration,lockoutObservationWindow,pwdHistoryLength,pwdProperties,objectClass,distinguishedName
```

## To get the Domain Controller

```ldap
ldapsearch (userAccountControl:1.2.840.113556.1.4.803:=8192) --attributes name,dnshostname
```

## To get domain user

```ldap
ldapsearch (&(objectCategory=person)(objectClass=user)) --attributes samAccountName,displayName,memberOf,userPrincipalName
```

## To get info about specific user

```ldap
ldapsearch (samAccountName=[username]) --attributes displayName,memberOf
```

## To get Domain Computers

```ldap
ldapsearch (objectCategory=computer) --attributes operatingSystem,name,dnsHostName
```

## To get the domain groups

```ldap
ldapsearch (objectClass=group) --attributes name,distinguishedName,description,groupType
```

## To get group members

```ldap
ldapsearch (cn=<GroupName>) --attributes member
```

## To get local groups

```cmd
net localgroup
```

## To get members of local Administrator group

```cmd
net localgroup Administrators
```

## To enumerate GPOs

```ldap
ldapsearch (objectClass=groupPolicyContainer) --attributes displayName,gPCMachineExtensionNames,gPCUserExtensionNames,gPCFileSysPath
```

## To Enumerates shared folders

```cmd
shell net view \\<ComputerName>
```

## To Finds GPOs applied to a specific computer

1. Get the DN of the computer:

```ldap
ldapsearch (sAMAccountName=<ComputerName>$) --attributes distinguishedName
```

2. Get the computer’s OU and parent containers, walk up the tree.

3. At each level, look for GPO links:

```ldap
ldapsearch (&(objectClass=organizationalUnit)(distinguishedName=<OU_DN>)) --attributes gPLink
```

4. Parse gPLink values to extract GPO DNs.

5. For each GPO DN:

```ldap
ldapsearch (distinguishedName=<GPO_DN>) --attributes displayName
```

## To Finds users or groups added to local groups

1. Get gPCFileSysPath for all GPOs:

```ldap
ldapsearch (objectClass=groupPolicyContainer) --attributes displayName,gPCFileSysPath
```

2. Pull GptTmpl.inf or preference XML files from:

```ldap
\\<DC>\SYSVOL\<domain>\Policies\<GPO_GUID>\Machine\
```

3. Parse:

   1. For Restricted Groups: GptTmpl.inf under [Group Membership]

   2. For GPP: Groups.xml under Preferences\Groups\

## To get all Organizational Units (OUs)

```ldap
ldapsearch (objectClass=organizationalUnit) --attributes name
```

## To Find kerberostable account

```ldap
ldapsearch (&(objectCategory=person)(objectClass=user)(servicePrincipalName=*)) --attributes sAMAccountName,servicePrincipalName
```
