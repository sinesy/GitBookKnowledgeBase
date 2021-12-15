# How to setup an LDAP based authentication

In order to connect Platform to an LDAP server, 3 steps are needed:

* gathering required connection settings from your client
* validating the connection settings
* setting up Platform with the connection settings, according to the needs



## Gathering required connection settings from your client

First of all, you have to ask your client to provide the following information:

* LDAP **host name** or IP; **this host must be reachable from your Platform server**, not only in terms of host but also of opened port
* LDAP **host post**, typically it is the 389 for a standard LDAP connection, but it could be different if the protocol is not LDAP
* LDAP **protocol**: in most of the cases it is LDAP, but it can be LDAPS (secured LDAP); in the latter case, it is likely that the port is not 386
* **Base DN**, i.e. a filtering path to use when authenticating a user on LDAP; this information is mandatory and **it cannot be empty**: your client cannot say it is empty; there is always a base filtering condition, at least composed of the company domain (e.g. "ou=mydeprtment,dc=sinesy,dc=it")
* the list of attributes to gather for each user (e.g. ssAMAccountName, displayName, givenName, sn, userPrincipalName)
* the attribute of a user which identifies univokely a user; there can be more than one attribute which can be used as a unique field, like ssAMAccountName or userPrincipalName; the right choice depends on the user format to digit in the username field on the login screen: if a user is identified in the LDAP with a domain also (e.g. "domain\username" or "username@domain") and **you do not want to force the users to type the domain in the login field, you must choose an attribute including the domain as well**

## Validating the connection settings

It is very likely that the information provided by your client is wrong somewhere, The best thing is always to check it out, using an LDAP client. You can download JXExplorer, a free LDAP client and use it to test the correct authentication, starting from the connection settings provided by your client. The best thing would be to install JXExplorer in the Platform server.

Typical errors here are:

* the LDAP is not reachable from Platform: ask you client to open port/host to let Platform to reach it
* the authentication failed with a 49 errore code; it means "invalid credentials" and it can be due to several reasons:
  * the username is not valid; for example, the right username could be domain\username
  * the password is not valid
  * the user is locked/not active in the LDAP
  * the Base DN is wrong

Once you verified the correct authentication in JXExplorer, you can continue with step 3.



## Setting up Platform with the connection settings, according to the needs

The LDAP authentication requires that the user to authenticate has been already registered in Platform tables, in terms of user (PRM01, SUB02/SUB3, PMR08) and roles (PRM06).

Consequently, there are 2 alternative ways to configure Platform with an LDAP:

* **activate users synchronization**, so that through a scheduled process (executed every night for example) all users are imported in Platform tables; once a user needs to authenticate to Platform, the authentication process is forwarded to the LDAP and after a successful authentication, the user profile is retrieved from Platform tables, where the user has been already configured.
  * Pros: a Platform administrator can see all users already imported from the sync process and assign the right roles to each user
  * Cons: it is not a suitable choice for LDAP servers containing a large amount of users (more than a few thousands); a Base DN filtering condition would be used in such a case, in order to significantly reduce the amount of users; if this is not possible, use the alternative configuration way reported below
* **execute authentication and create the user in Platform** tables just after a successful authentication in the LDAP



