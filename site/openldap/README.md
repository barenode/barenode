# OpenLDAP

## Install OpenLDAP server

<pre>
<b>#  yum install -y openldap openldap-clients openldap-servers</b>
</pre>

Create olcRootPW Root Password. Create olcRootDN Account as Admin. Create olcSuffix Domain Name

<pre>
<b># slappasswd</b>
New password:
Re-enter new password:
{SSHA}IcSlX0Ipk/vD+ebj+b+R3qmx6zqeO8gC
</pre>

<pre>
<b># vi chrootpw.ldif</b>
# specify the password generated above for "olcRootPW" section
dn: olcDatabase={0}config,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}IcSlX0Ipk/vD+ebj+b+R3qmx6zqeO8gC
</pre>

<pre>
<b># ldapadd -Y EXTERNAL -H ldapi:/// -f chrootpw.ldif</b>
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "olcDatabase={0}config,cn=config"
</pre>

Import basic schemas.
<pre>
<b>#  ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif</b> 
<b>#  ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif</b>
<b>#  ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif</b> 
</pre>

Generate directory manager's password
<pre>
<b># slappasswd</b> 
New password:
Re-enter new password:
{SSHA}6S5lL15Xjyw26861sMqyByh1TqR4xk1J
</pre>

<pre>
<b># vi chdomain.ldif</b>
# replace to your own domain name for "dc=***,dc=***" section
# specify the password generated above for "olcRootPW" section
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"
  read by dn.base="cn=admin,dc=barenode,dc=org" read by * none

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=barenode,dc=org

dn: olcDatabase={2}hdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=admin,dc=barenode,dc=org

dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}6S5lL15Xjyw26861sMqyByh1TqR4xk1J

dn: olcDatabase={2}hdb,cn=config
changetype: modify
add: olcAccess
olcAccess: {0}to attrs=userPassword,shadowLastChange by
  dn="cn=admin,dc=barenode,dc=org" write by anonymous auth by self write by * none
olcAccess: {1}to dn.base="" by * read
olcAccess: {2}to * by dn="cn=admin,dc=barenode,dc=org" write by * read</pre>

<pre>
<b># ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif</b>
</pre>

<pre>
<b># ldapadd -x -D cn=admin,dc=barenode,dc=org -W -f basedomain.ldif</b>
</pre>

<pre>
<b># ldapsearch -x -W -D "cn=admin,dc=barenode,dc=org" -b "dc=barenode,dc=org" "(objectclass=*)"</b>

Enter LDAP Password:
# extended LDIF
#
# LDAPv3
# base <dc=barenode,dc=org> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# barenode.org
dn: dc=barenode,dc=org
objectClass: top
objectClass: dcObject
objectClass: organization
o: barenode
dc: barenode

# admin, barenode.org
dn: cn=admin,dc=barenode,dc=org
objectClass: organizationalRole
cn: Manager
cn: admin
description: Directory Manager

# user, barenode.org
dn: ou=user,dc=barenode,dc=org
objectClass: organizationalUnit
ou: users
ou: user

# group, barenode.org
dn: ou=group,dc=barenode,dc=org
objectClass: organizationalUnit
ou: groups
ou: group

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 4
</pre>



## Configuring TLS

### Creating self signed certificate

<pre>
<b># cd /etc/pki/tls/certs </b>
<b># make server.key </b>
...
Enter pass phrase:# set passphrase
Verifying - Enter pass phrase:# confirm
</pre>

Remove passphrase from private key
<pre>
<b># openssl rsa -in server.key -out server.key </b>
Enter pass phrase for server.key:# input passphrase
writing RSA key
</pre>

<pre>
<b># make server.csr</b>
...
A challenge password []:# Enter
An optional company name []:# Enter
</pre>

<pre>
<b># makeopenssl x509 -in server.csr -out server.crt -req -signkey server.key -days 3650</b>
Signature ok
...
Getting Private key
</pre>