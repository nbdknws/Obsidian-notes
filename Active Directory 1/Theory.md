### Key terms

**Domain controller** -  windows server with AD services installed, the hub and the core of AD.

Typically client computers on an internal network are connected to the DC and to other servers (not always domain-joint) like db, web etc.

**Object** - basic element of AD: user, group, computer, application etc. Every object has attributes, like user object can have name, username, password.

**Organzational units** - (comp. file folders) containers to group other objects

**SID** is the unique id for any object in active directory and has a structure like: S-R-I-S

S - identify string as SID
R - revision level, usually set to one
I - identifier-authority value, often 5 with AD
S - subauthority value, that has two parts: domain's numeric number identifier and relative identifier or **RID** (represent sepcific object in the domain)

example:  S-1-5-21-2536614405-3629634762-1218571035-1116

The SID defining the domain is the entire string except the RID

### Enumeration

Groups are used to assign permissions to users

**Domain Admins** group contains users that have full control over the system

### LDAP

LDAP **ADsPATH**

```
LDAP://HostName[:PortNumber][/DistinguishedName]
```

HostName - IP, domain
PortNumber - default 389, 636(SSL)

**Distinguished name** - LDAP reference an object by its distinguished name (DN). That is a sequence of related distinguished names(RDN) connected by commas. DN guarantees to be unique.

**RDN** is an attribute with an associated value in the format: attribute=value

List of RDN attribute types:

![[Pasted image 20220416220045.png]]


Examples of DN:

```
CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM
```


**Domain controllers** A domain controller is a Windows server that has Active Directory Domain Services (AD DS) installed and has been promoted to a domain controller in the forest. Domain controllers are the center of Active Directory -- they control the rest of the domain. I will outline the tasks of a domain controller below: 

-   holds the AD DS data store 
-   handles authentication and authorization services 
-   replicate updates from other domain controllers in the forest
-   Allows admin access to manage domain resources


**AD DS Data Store** The Active Directory Data Store holds the databases and processes needed to store and manage directory information such as users, groups, and services. Below is an outline of some of the contents and characteristics of the AD DS Data Store:

-   Contains the **NTDS.dit**- a database that contains all of the information of an Active Directory domain controller as well as password hashes for domain users
-   Stored by default in` %SystemRoot%\NTDS`
-   accessible only by the domain controller

A **forest** is a collection of one or more domain trees inside of an Active Directory network. It is what categorizes the parts of the network as a whole.

-   Trees - A hierarchy of domains in Active Directory Domain Services
-   Domains - Used to group and manage objects 
-   Organizational Units (OUs) - Containers for groups, computers, users, printers and other OUs
-   Trusts - Allows users to access resources in other domains
-   Objects - users, groups, printers, computers, shares
-   Domain Services - DNS Server, LLMNR, IPv6
-   Domain Schema - Rules for object creation

### AD Users

-   **Domain Admins** - This is the big boss: they control the domains and are the only ones with access to the domain controller.
-   **Service Accounts** (Can be Domain Admins) - These are for the most part never used except for service maintenance, they are required by Windows for services such as SQL to pair a service with a service account
-   **Local Administrators** - These users can make changes to local machines as an administrator and may even be able to control other normal users, but they cannot access the domain controller
-   **Domain Users** - These are your everyday users. They can log in on the machines they have the authorization to access and may have local administrator rights to machines depending on the organization


### AD Groups

-   **Security Groups** - These groups are used to specify permissions for a large number of users
-   **Distribution Groups** - These groups are used to specify email distribution lists. As an attacker these groups are less beneficial to us but can still be beneficial in enumeration


### Default security groups

-   Domain Controllers - All domain controllers in the domain
-   Domain Guests - All domain guests
-   Domain Users - All domain users
-   Domain Computers - All workstations and servers joined to the domain
-   Domain Admins - Designated administrators of the domain
-   Enterprise Admins - Designated administrators of the enterprise
-   Schema Admins - Designated administrators of the schema
-   DNS Admins - DNS Administrators Group
-   DNS Update Proxy - DNS clients who are permitted to perform dynamic updates on behalf of some other clients (such as DHCP servers).
-   Allowed RODC Password Replication Group - Members in this group can have their passwords replicated to all read-only domain controllers in the domain
-   Group Policy Creator Owners - Members in this group can modify group policy for the domain
-   Denied RODC Password Replication Group - Members in this group cannot have their passwords replicated to any read-only domain controllers in the domain
-   Protected Users - Members of this group are afforded additional protections against authentication security threats. See http://go.microsoft.com/fwlink/?LinkId=298939 for more information.
-   Cert Publishers - Members of this group are permitted to publish certificates to the directory
-   Read-Only Domain Controllers - Members of this group are Read-Only Domain Controllers in the domain
-   Enterprise Read-Only Domain Controllers - Members of this group are Read-Only Domain Controllers in the enterprise
-   Key Admins - Members of this group can perform administrative actions on key objects within the domain.
-   Enterprise Key Admins - Members of this group can perform administrative actions on key objects within the forest.
-   Cloneable Domain Controllers - Members of this group that are domain controllers may be cloned.
-   RAS and IAS Servers - Servers in this group can access remote access properties of users


Domain Trusts Overview -

﻿Trusts are a mechanism in place for users in the network to gain access to other resources in the domain. For the most part, trusts outline the way that the domains inside of a forest communicate to each other, in some environments trusts can be extended out to external domains and even forests in some cases.

![](https://i.imgur.com/4uGI3bF.png)  

There are two types of trusts that determine how the domains communicate. I'll outline the two types of trusts below: 

-   Directional - The direction of the trust flows from a trusting domain to a trusted domain
-   Transitive - The trust relationship expands beyond just two domains to include other trusted domains

The type of trusts put in place determines how the domains and trees in a forest are able to communicate and send data to and from each other when attacking an Active Directory environment you can sometimes abuse these trusts in order to move laterally throughout the network. 

Domain Policies Overview - 

Policies are a very big part of Active Directory, they dictate how the server operates and what rules it will and will not follow. You can think of domain policies like domain groups, except instead of permissions they contain rules, and instead of only applying to a group of users, the policies apply to a domain as a whole. They simply act as a rulebook for Active  Directory that a domain admin can modify and alter as they deem necessary to keep the network running smoothly and securely. Along with the very long list of default domain policies, domain admins can choose to add in their own policies not already on the domain controller, for example: if you wanted to disable windows defender across all machines on the domain you could create a new group policy object to disable Windows Defender. The options for domain policies are almost endless and are a big factor for attackers when enumerating an Active Directory network. I'll outline just a few of the  many policies that are default or you can create in an Active Directory environment: 

-   Disable Windows Defender - Disables windows defender across all machine on the domain
-   Digitally Sign Communication (Always) - Can disable or enable SMB signing on the domain controller


