# OpenLDAP

## OpenLDAP server

<pre>
#  yum install -y openldap openldap-clients openldap-servers
</pre>

Create olcRootPW Root Password. Create olcRootDN Account as Admin. Create olcSuffix Domain Name

<pre>
# slappasswd
New password:
Re-enter new password:
{SSHA}gOaWP1cqDKInFRCmp88MlZTD6XqJGRIy
</pre>

<pre>
# vi /etc/openldap/slapd.d/cn=config/olcDatabase={2}hdb.ldif

# AUTO-GENERATED FILE - DO NOT EDIT!! Use ldapmodify.
# CRC32 92e0fc4d
dn: olcDatabase={2}hdb
objectClass: olcDatabaseConfig
objectClass: olcHdbConfig
olcDatabase: {2}hdb
olcDbDirectory: /var/lib/ldap
<b>olcSuffix: dc=barenode,dc=org</b>
<b>olcRootDN: cn=admin,dc=barenode,dc=org</b>
<b>olcRootPW: {SSHA}gOaWP1cqDKInFRCmp88MlZTD6XqJGRIy</b>
olcDbIndex: objectClass eq,pres
olcDbIndex: ou,cn,mail,surname,givenname eq,pres,sub
structuralObjectClass: olcHdbConfig
entryUUID: 31ac720c-485c-1036-8595-05a87a91403e
creatorsName: cn=config
createTimestamp: 20161126194222Z
entryCSN: 20161126194222.495145Z#000000#000#000000
modifiersName: cn=config
modifyTimestamp: 20161126194222Z
</pre>

<pre>
# vi /etc/openldap/slapd.d/cn=config/olcDatabase={1}monitor.ldif

# AUTO-GENERATED FILE - DO NOT EDIT!! Use ldapmodify.
# CRC32 c56f6506
dn: olcDatabase={1}monitor
objectClass: olcDatabaseConfig
olcDatabase: {1}monitor
<b>olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=extern
 al,cn=auth" read by dn.base="cn=admin,dc=barenode,dc=org" read by * none</b>
structuralObjectClass: olcDatabaseConfig
entryUUID: 12a91e8e-4db4-1036-990a-5dc9eec3c7fd
creatorsName: cn=config
createTimestamp: 20161203145401Z
entryCSN: 20161203145401.990071Z#000000#000#000000
modifiersName: cn=config
modifyTimestamp: 20161203145401Z
</pre>

<pre>
# slaptest -u
5842dfe4 ldif_read_file: checksum error on "/etc/openldap/slapd.d/cn=config/olcDatabase={1}monitor.ldif"
5842dfe4 ldif_read_file: checksum error on "/etc/openldap/slapd.d/cn=config/olcDatabase={2}hdb.ldif"
<b>config file testing succeeded</b>
</pre>

<pre>
# ldapsearch -x -W -D "cn=admin,dc=barenode,dc=org" -b "dc=barenode,dc=org" "(objectclass=*)"

Enter LDAP Password:
# extended LDIF
#
# LDAPv3
# base <dc=barenode,dc=org> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# search result
search: 2
result: 32 No such object
</pre>