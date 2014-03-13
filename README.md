# LDAP Schema for OwnCloud

Reference: http://doc.owncloud.org/server/6.0/admin_manual/configuration/auth_ldap.html

## ownCloud Schema

OwnCloud Inc. has register the [OID 1.3.6.1.4.1.39430](http://oid-info.com/get/1.3.6.1.4.1.39430) and we extended it to define the required LDAP objects 

- **OID**: 1.3.6.1.4.1.39430.1.2.1
- **ObjectClass**: ownCloud

### Quota Field

ownCloud can read an LDAP attribute and set the user quota according to its value. 
The attribute shall return human readable values, e.g. "2 GB".

- **OID**: 1.3.6.1.4.1.39430.1.1.1
- **AttributeType**: ownCloudQuota

#### Quota Field Syntax

The format of the quota field is a string, describing the size of the user quota. You can use the following units (case insensitive) for the size: **B**, **KB**, **K**, **MB**, **M**, **GB**, **G**, **TB**, **T**, **PB**, **P**. If no unit is specified, the default size is in byte (**B**). 

**Valid Value Examples**: "234234", "132594 PB", "748G" 

## Usage

A user can be extended with the auxillary `objectClass: ownCloud` and the attribute `ownCloudQuota` can be used
to define the user specific quota limit.

## Installation

To install the schema in a OpenLDAP using OLC (cn=config), use the `ldapadd` command:

    root# ldapadd -Y EXTERNAL -H ldapi:/// -f owncloud.ldif
   
And verify that the schema is correctly loaded:

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

If your LDAP server does not use OLC (cn=config), then add the schema `owncloud.schema` in the schema directory, and update your configuration accordingly.


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
    ownCloudQuota: 5 TB


