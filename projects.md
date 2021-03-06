# List of Projects

## 1. Two-factor Authentication for Keystone

Two-factor authentication adds an extra layer of security on top of user name
and password style authentication that Keystone supports by default. In this
scheme, in addition to supplying a user name and a password,
the user includes a code that changes frequently. The code is generated using
 a hardware device such as an RSA SecureID token, or apps like the Google
 Authenticator app on iOS, Android and BlackBerry that support
[TOTP](http://tools.ietf.org/html/rfc6238).

In this project, you will design and implement a Keystone API extension to
support two factor authentication that uses TOTP protocol. Here
are the project deliverables.

1. An API specification for two-factor authentication to support the following
  * Enable/disable two factor authentication for a user
  * Enable/disable two factor authentication for a project
  * Authentication with user name, password and a code
2. An API extension to Keystone.
3. A default implementation to support TOTP.
3. Unit tests
4. An extension to Horizon to enable a user or an admin to enable two
factor authentication
4. API support to `python-keystoneclient`

See http://throwingfire.com/you-can-be-a-twofactor-hero/ for an
example of two-factor authentication implemented in Python. Check
[AWS Multi-Factor Authentication](http://aws.amazon.com/iam/details/mfa/) for
 an example of how two-factor authentication works in a cloud.

## 2. Access Keys

Imagine a private cloud integrated into a corporate authentication system. 
Users login to Keystone by using their corp credentials. In order to
use OpenStack APIs in code, users will need to include their credentials in
code. This may not be desirable due to security and policy reasons. Access 
keys allow the user to include a temporary key and secret in place of their 
credentials.

In this project, you will design and implement a Keystone API extension to
let users generate a temporary key and secret that can be used in place of
username and password for authentication. Project deliverables must include
the following.

1. An API specification to support the following capabilities
   * Given a project ID and an expiration time, return a key and a secret.
   The secret must be generated using an algorithm like HMAC SHA1.
   * List access key-secret pairs for a user
   * Revoke an access key
   * Delete an access key
2. A default implementation of the above
3. Changes to Keystone implementation to allow login via access key and secret. 
   The implementation must work with the existing Keystone `POST v2.0/tokens` API.
4. Unit tests
5. An extension to Horizon to enable a user to manage his/her access keys

For an example of access keys might work, see
[HP Cloud Identity API](http://docs.hpcloud.com/api/identity).

## 3. Flyway: Forklift Resources from One Cloud to Another

In this project you will build a service to copy/move resources from one OpenStack 
cloud to two another. The resources include the following:

1. Users and their respective roles in projects
2. Projects with related metadata
3. Project quotas
4. Private Keys
5. Images/Snapshots and their metadata.
6. VMs
7. Flavor mappings

The hypervisor is assumed to be KVM - which is the default in devstack. To
the extent possible the service must use OpenStack APIs of source and
destination clouds, but the project many directly manipulate underlying
database and files (for instance virtual machine files).

You will be provided source code of a version of such a tool to get started.
You may refactor and reuse as necessary.

The deliverables mut include the following:

1. A service to copy/move resources from one OpenStack cloud to another
2. The service must have an API to copy/move the above resources
3. Documentation on how to setup and use

## 4. Repoman: VM Reclamation Service (not selected)

In this project you will write a service to cleanup virtual machines based on
 pluggable policies. Examples policies include

- A VM that is marked as live in nova but does not exist on the hypervisor
- A VM whose project has been deleted in Keystone
- A VM whose project has no users
- A VM is healthy but a user has not confirmed that (s)he needs the VM

The service must include a periodic job that loops through all virtual
machines. The job must run the policies against each VM. If a user could be
associated, the service must notify the user before taking any action. If
allowed by the user, the service must clean up the VM.

## 5. Nova Scheduler Performance

When selecting a hypervisor, the scheduler must loop through all
available hypervisors (nodes running nova-compute) and run a set of
filters against each hypervisor. This makes the scheduler `O(n)`. In this
project you will come up with ways to improve scheduler performance.

The deliverables include

- Analyze and present ways to improve
- Implement one or more performance improvements
- Unit tests


## 6. Nova Compute Simulator

nova-compute is the component in OpenStack that is responsible for managing 
the lifecycle of VMs. During this process it gets requests from nova's 
scheduler to talks to various OpenStack APIs either directly over HTTP or 
using messages exchanged over RabbitMQ. 

In this project you will introduce a virtual nova compute component that can 
be used to launch a large number of VMs as well as simulating a large number 
of hypervisors. This virtual nova compute should introduce a driver simulating 
the creation of VM with minimal resource consumption, as well as provide a 
mechanism to launch multiple virtual nova compute. The goal is to be able 
to simulate load on OpenStack control plane without requiring a large 
infrastructure footprint.

The deliverables include

- Create nova-compute drivers in nova either as fake or using containers.
- The driver must support typical lifecycle phases like VM creation, VM 
  suspension, VM restoration, and VM deletion.
- A set of configuration changes to enable the above driver(s).
- Change devstack to run multiple nova-compute processes in the same host.


## 7. Hibernation of VM Instances

This project addreses the situation where a hypervisor is full of vm instances which are mostly idle because the instances are not needed currently but may have data or a configuration which is difficult to recreate.  The project will be to design and implement vm hibernation which releases all hypervisor resources but allows quick restoration of the vm instance when it's needed later.

The project will investigate existing openstack blueprints related to this feature and decide which implementation method best fits the use cases and openstack community plans.  It's likely there will be new coding and development needed for this feature.

The user will be able to select a vm and hiberate it.  That will remove the host from the cluster and delete it from the hypervisor but there are several side issues to resolve as well (should the host name be removed from dns? should the IP be deallocated?)

The user will be able to list the hibernated vms in their tenant.

The user will be able to select a hibernated vm and reconsitute it as a running VM.  

The user will be able to delete hibernated vms from the hiberated vm list which will completely delete the data.

Some possible related questions:
  * How to manage the hypervisors other than kvm.  We would like to hibernate vmware/esx instances too.
  * Can a hibernated instance be reconsitited on a different hypervisor type?  kvm -> esx for example?
  * How is the instance flavor and other non-disk-image related metadata captured when the instance is hibernated?
  * Maybe this could be used to template instances for flex up/flex down and orchestration projects
  * Provides a simple way to change the flavor of an instance or otherwise change the running instance's attributes


## Projects to Explore

1. Implement termination protection blueprint
   https://blueprints.launchpad.net/nova/+spec/disable-terminate


