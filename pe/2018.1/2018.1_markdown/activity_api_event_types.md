# Event types reported by the activity service

Activity reporting provides a useful audit trail for actions that change role-based access control \(RBAC\) entities, such as users, directory groups, and user roles.

## Local users

These events are displayed in the console on the **Activity** tab for the affected user.

|Event|Description|Example|
|-----|-----------|-------|
|**Creation**|A new local user is created. An initial value for each metadata field is reported.|`Created with login set to "jean".`|
|**Metadata**|Any change to the `login`, `display name`, or `email` keys.|`Display name set to "Jean Jackson".`|
|**Role membership**|A user is added to or removed from a role. The display name and user ID of the affected user are displayed.|`User Jean Jackson (973c0cee-5ed3-11e4-aa15-123b93f75cba) added to role Operators.`|
|**Authentication**|A user logs in. The display name and user ID of the affected user are displayed.|`User Jean Jackson (973c0cee-5ed3-11e4-aa15-123b93f75cba) logged in.`|
|**Password reset token**|A token is generated for a user to use when resetting their password. The display name and user ID of the affected user are shown.|`A password reset token was generated for user Jean Jackson (973c0cee-5ed3-11e4-aa15-123b93f75cba).`|
|**Password changed**|A user successfully changes their password with a token.|`Password reset for user Jean Jackson (973c0cee-5ed3-11e4-aa15-123b93f75cba).`|
|**Revocation**|A user is revoked or reinstated.|`User revoked.`|

## Remote users

These events are displayed in the console on the **Activity** tab for the affected user.

|Event|Description|Example|
|-----|-----------|-------|
|**Role membership**|A user is added to or removed from a role. These events are also shown on the page for the role. The display name and user ID of the affected user are displayed.|`User Kalo Hill (76483e62-5ed4-11e4-aa15-123b93f75cba) added to role Viewers.`|
|**Revocation**|A user is revoked or reinstated.|`User revoked.`|

## Directory groups

These events are displayed in the console on the **Activity** tab for the affected group.

|Event|Description|Example|
|-----|-----------|-------|
|**Importation**|A directory group is imported. The initial value for each metadata field is reported \(these cannot be updated using the RBAC UI\).|`Created with display name set to "Engineers".`|
|**Role membership**|A group is added to or removed from a role. These events are also shown on the page for the role. The group's display name and ID are provided.|`Group Engineers (7dee3acc-5ed4-11e4-aa15-123b93f75cba) added to role Operators.`|

## Roles

These events are displayed in the console on the **Activity** tab for the affected role.

|Event|Description|Example|
|-----|-----------|-------|
|**Metadata**|A role's `display name` or `description` changes.|`Description set to "Sysadmins with full privileges for node groups."`|
|**Members**|A group is added to or removed from a role. The display name and ID of the user or group are provided. These events are also displayed on the page for the affected user or group.|`User Kalo Hill (76483e62-5ed4-11e4-aa15-123b93f75cba) removed from role Operators.`|
|**Permissions**|A permission is added to or removed from a role.|`Permission users:edit:76483e62-5ed4-11e4-aa15-123b93f75cba added to role Operators.`|
|**Delete**|A role has been removed.|The Delete event is recorded and available only through the activity service API, not the **Activity** tab.|

## Orchestration

These events are displayed in the console on the **Activity** tab for the affected node.

|Event|Description|Example|
|-----|-----------|-------|
|**Agent runs**|Puppet runs as part of an orchestration job. This includes runs started from the orchestrator or the PE console.|`Request Puppet agent run on node.example.com via orchestrator job 12.`|
|**Task runs**|Tasks run as orchestration jobs set up in the console or on the command line.|`Request echo task on neptune.example.com via orchestrator job 9,607`|

## Authentication tokens

These events are displayed in the console on the **Activity** tab on the affected user's page.

|Event|Description|Example|
|-----|-----------|-------|
|**Creation**|A new token is generated. These events are exposed in the console on the **Activity** tab for the user who owns the token.|`Amari Perez (c84bae61-f668-4a18-9a4a-5e33a97b716c) generated an authentication token.`|
|**Direct revocation**|A successful token revocation request. These events are exposed in the console on the **Activity** tab for the user performing the revocation.|`Administrator (42bf351c-f9ec-40af-84ad-e976fec7f4bd) revoked an authentication token belonging to Amari Perez (c84bae61-f668-4a18-9a4a-5e33a97b716c), issued at 2016-02-17T21:53:23.000Z and expiring at 2016-02-17T21:58:23.000Z.`|
|**Revocation by username**|All tokens for a username are revoked. These events are exposed in the console on the **Activity** tab for the user performing the revocation.|`Administrator (42bf351c-f9ec-40af-84ad-e976fec7f4bd) revoked all authentication tokens belonging to Amari Perez (c84bae61-f668-4a18-9a4a-5e33a97b716c).`|

## Directory service settings

These events are not exposed in the console. The activity service API must be used to see these events.

|Event|Description|Example|
|-----|-----------|-------|
|**Update settings \(except password\)**|A setting is changed in the directory service settings.|`User rdn set to "ou=users".`|
|**Update directory service password**|The directory service password is changed.|`Password updated.`|

