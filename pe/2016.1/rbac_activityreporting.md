---
layout: default
title: "Activity reporting"
canonical: "/pe/latest/rbac_activityservice.html"
---

The RBAC service's activity reporting is intended to provide a useful
audit trail for actions that change entities within the purview of
RBAC. The following sections describe the potential events for each RBAC entity.

## Local users

These events are displayed in the console on the **Activity** tab for the affected user.

### Creation

An event is reported when a new local user is created. An initial value for each metadata field is reported. 

* Example: Created with login set to "kate"

### Metadata

An event is reported if any of these keys change: `login`, `display name`, or `email`. 

* Example: Display name set to "Kate Gleason"

### Role membership

An event is reported when a user is added to or removed from a role. These events, the display name, and user ID of the affected user are displayed on the page for the role. 

* Example: User Kate Gleason (973c0cee-5ed3-11e4-aa15-123b93f75cba) added to role Operators
   
### Authentication

An event is reported when a user logs in. The display name and user ID of the affected user are displayed. 

* Example: User Kate Gleason (973c0cee-5ed3-11e4-aa15-123b93f75cba) logged in.
   
### Password reset token

An event is reported when a token is generated for a user to use in resetting their password. The display name and user ID of the affected user are shown. 

* Example: A password reset token was generated for user Kate Gleason (973c0cee-5ed3-11e4-aa15-123b93f75cba).
   
### Password changed

An event is reported when a user successfully changes their password with a token. 

* Example: Password reset for user Kate Gleason (973c0cee-5ed3-11e4-aa15-123b93f75cba).

### Revocation

An event is reported when a user is revoked or reinstated. 

* Example: User revoked.

## Remote users

These events are displayed in the console on the **Activity** tab for the affected user.

### Role membership

An event is reported when a user is added to or removed from a role. These events are also shown on the page for the role. The display name and user ID of the affected user are displayed. 

* Example: User Charles Babbage (76483e62-5ed4-11e4-aa15-123b93f75cba) added to role Viewers.
   
### Revocation
 
An event is reported when a user is revoked or reinstated. 

* Example: User revoked. 

## Directory groups

These events are displayed in the console on the **Activity** tab for the affected group.

### Importation

An event is reported when a directory group is imported. The initial value for each metadata field is reported, (though these cannot later be updated using the RBAC UI). 

* Example: Created with display name set to "Engineers"
   
### Role membership

An event is reported when a group is added to or removed from a role. These events are also shown on the page for the role. The group's display name ID are provided. 

* Example: Group Engineers (7dee3acc-5ed4-11e4-aa15-123b93f75cba) added to role Operators

## Roles

These events are displayed in the console on the **Activity** tab for the affected role.

### Metadata

An event is reported if a role's `display name` or `description` changes. 

* Example: Description set to "Sysadmins with full privileges for node groups."
   
### Members

An event is reported when a group is added to or removed from a role. The display name of the user or group and its ID are provided. These events are also displayed on the page for the affected user or group. 

* Example: User Frances (76483e62-5ed4-11e4-aa15-123b93f75cba) removed from role Operators
   
### Permissions

An event is reported when a given permission is added to or removed from a role. 

* Example: Permission users:edit:76483e62-5ed4-11e4-aa15-123b93f75cba added to role Operators
   
## Authentication tokens

These events are displayed in the console on the **Activity** tab on the affected user's page.

### Creation
 
An event is generated each time a new token is generated. These events are exposed in the console on the **Activity** tab for the user who owns the token.

* Example: Franz Kafka (c84bae61-f668-4a18-9a4a-5e33a97b716c) generated an authentication token.

### Revocation

*Direct revocation*

An event is generated for each successful token revocation request. These events are exposed in the console on the **Activity** tab for the user performing the revocation.

* Example: Administrator (42bf351c-f9ec-40af-84ad-e976fec7f4bd) revoked an authentication token belonging to Franz Kafka (c84bae61-f668-4a18-9a4a-5e33a97b716c), issued at 2016-02-17T21:53:23.000Z and expiring at 2016-02-17T21:58:23.000Z.

*Revocation by username* 

An event is generated for each username whose tokens are to be revoked. These events are exposed in the console on the **Activity** tab for the user performing the revocation.

* Example: Administrator (42bf351c-f9ec-40af-84ad-e976fec7f4bd) revoked all authentication tokens belonging to Franz Kafka (c84bae61-f668-4a18-9a4a-5e33a97b716c).

## Directory service settings

These events are not exposed via the console. The activity service API must be used to see these events.

### Update settings (except password)

An event is generated for each setting changed in the directory service settings. 

* Example: User rdn set to "ou=users".

### Update directory service password

An event is generated when the directory service password is changed. 

* Example: Password updated.
