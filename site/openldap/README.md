# OpenLDAP

## OpenLDAP server

```
#  yum install -y openldap openldap-clients openldap-servers

```

<pre>
# vi /etc/openldap/slapd.d/cn=config/olcDatabase={2}hdb.ldif

# AUTO-GENERATED FILE - DO NOT EDIT!! Use ldapmodify.
# CRC32 92e0fc4d
dn: olcDatabase={2}hdb
objectClass: olcDatabaseConfig
objectClass: olcHdbConfig
olcDatabase: {2}hdb
olcDbDirectory: /var/lib/ldap
olcSuffix: dc=my-domain,dc=com
<b>olcRootDN: cn=admin,dc=barenode,dc=org</b>
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