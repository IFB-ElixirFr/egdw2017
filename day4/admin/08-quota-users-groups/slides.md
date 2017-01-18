class: middle

# Users, Groups, and Quotas

## EGDW 2017

Slides by SLG<br/>
Inspired by Galaxy Administrator Courses from [Dagobah](https://github.com/martenson/dagobah-training)

.footer[
https://github.com/igbmc/egdw2017
]

---

layout: true
name: title
class: middle

.footer[
EGDW 2017
]

---

layout: true
name: content

.footer[
EGDW 2017
]

---

layout: true
name: hands-on

.footer[
EGDW 2017 - **Hands-on**
]

---

layout: true
name: empty-footer

---
template: content
# User Activation

Require verification that a user's email is real. You must enable SMTP first.

In `galaxy.ini`:
* `user_activation_on` require users to click link in email before running jobs.
* `activation_grace_period` time (hours) that a user can 'explore' Galaxy before activation lockout.
* `inactivity_box_content` message provided to non-activated users.
* Disposable domain blacklist
  * `blacklist_file` defines domains in XXX.YYY format that will be rejected as user emails.

---
template: content
# User Password and Session Policies

In `galaxy.ini`:
* `password_expiration_period` days before requiring a user to change password.
* `session_duration` minutes before invaliding a user's session, requiring relogin.

---
template: content
# User Control

In `galaxy.ini`:
* `require_login` can be enabled to prevent anonymous access.
* `show_welcome_with_login` show welcome page next to login page
* `allow_user_creation` allow user registration. When False, admins must create users; often coupled with `require_login`.
* `allow_user_dataset_purge` users can purge (permanently delete) their datasets.
* `api_allow_run_as` list of email addresses of API users who can make calls on behalf of other users.
* `expose_dataset_path` users to see the full path of datasets via the "View Details" option in the history.

---
template: content
# Admin Control

In `galaxy.ini`:
* `admin_users` comma-separated list of admin users' emails
* `allow_user_deletion` admins can delete users
* `allow_user_impersonation` admins can become other users. Great for debugging / user assistance.
* `master_api_key` admin super-key allows many API admin actions without having a real admin user.

---
template: content
# User Privacy

In `galaxy.ini`:
* `expose_user_name` users can view other registered usernames
* `expose_user_email` users can view other registered emails
* `new_user_dataset_access_role_default_private` newly created datasets are private to the creating user

---
template: content
# Roles and Groups (1/2)

Role Based Access Control (RBAC)

Admin can:
* create roles (each user automatically has their own 'private' role)
* create groups
* assign roles to groups
* asign users to groups
* assign groups to roles
* assign users to roles
* assign permission sets to roles

---
template: content
# Roles and Groups (2/2)

Permissions:
 - Datasets:
  * manage permissions: Users having associated role can manage the roles associated with permissions on this dataset
  * access: Users having associated role can use/view/download a dataset for analysis. Users must have every role associated with a dataset in order to access it
 - Libraries:
  * modify library item: Users having associated role can modify this library item
  * access library: Restrict access to this library to only users having associated role
  * add library item: Users having associated role can add library items to this library item
  * manage library permissions: Users having associated role can manage roles associated with permissions on this library item

---
template: content
# Quotas

In `galaxy.ini`:
* `enable_quotas` Enable enforcement of quotas.  Quotas can be set from the Admin interface (under Data).
* must create quotas in admin interface before any quota will be enforced, otherwise 'unlimited'

Amounts:
 - Examples: "10000MB", "99 gb", "0.2T", "unlimited"
 - = / + / -

Default for user class:
 - None (No)
 - Unregistered Users
 - Registered Users

or associated with Groups or Users
