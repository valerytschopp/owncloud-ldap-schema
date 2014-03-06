# LDAP Schema for OwnCloud

Reference: http://doc.owncloud.org/server/6.0/admin_manual/configuration/auth_ldap.html

## Quota Field

ownCloud can read an LDAP attribute and set the user quota according to its value. 
The attribute shall return human readable values, e.g. "2 GB".

## Usage

A user can be extended with the auxillary `objectClass: ownCloud` and the attribute `ownCloudQuota` can be used
to define the user specific quota limit.

## Installation

To install the schema in a OpenLDAP use the following command:

    root# ldapadd -Y EXTERNAL -H ldapi:/// -f owncloud.ldif
   
And check that the schema is correctly loaded:

    root# ldapsearch -H ldapi:// -Y EXTERNAL -LLL -b cn=config "(cn={*}owncloud)"
    ...
    # {5}owncloud, schema, config
    dn: cn={5}owncloud,cn=schema,cn=config
    objectClass: olcSchemaConfig
    cn: {5}owncloud
    olcAttributeTypes: {0}( 1.3.6.1.4.1.39430.1.1.1 NAME 'ownCloudQuota' DESC 'Use
     r Quota (e.g. 2 GB)' SYNTAX '1.3.6.1.4.1.1466.115.121.1.15' )
    olcObjectClasses: {0}( 1.3.6.1.4.1.39430.1.2.1 NAME 'ownCloud' DESC 'ownCloud 
      LDAP Schema' AUXILIARY MAY ( ownCloudQuota ) )


## Example

    root# ldapsearch -H ldapi:// -Y EXTERNAL -LLL -b ou=Users,dc=novalocal "(objectclass=owncloud)" 
    ...
    objectClass: top
    objectClass: person
    objectClass: organizationalPerson
    objectClass: inetOrgPerson
    objectClass: eduMember
    objectClass: ownCloud
    dn: cn=tschopp,ou=Users,dc=novalocal
    cn: tschopp
    sn: Tschopp
    givenName: Valery
    mail: valery.tschopp@switch.ch
    isMemberOf: ownCloud
    isMemberOf: OpenStack
    ownCloudQuota: 10 GB


