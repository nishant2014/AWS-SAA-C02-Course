# SAA-C02 Notes

Status: In Progress.

Notes in main README have been reviewed for spelling and
clarity. Notes in individual folders are likely to have errors.

> These are my personal notes from Adrian Cantrill's (SAAC02) course.
Learning Aids from
[aws-sa-associate-saac02](https://github.com/acantril/aws-sa-associate-saac02).
There will be errors, so please purchase his course to get the original content
and show support <https://learn.cantrill.io.>

## Table of Contents

- [Cloud-Computing-Fundamentals](#Cloud-Computing-Fundamentals)
- [AWS-Fundamentals](#AWS-Fundamentals)
- [IAM-Accounts-AWS-Organizations](#IAM-Accounts-AWS-Organizations)
- [Simple-Storage-Service-S3](#Simple-Storage-Service-S3)
- [Virtual-Private-Cloud-VPC](#Virtual-Private-Cloud-VPC)
- [Elastic-Cloud-Compute-EC2](#Elastic-Cloud-Compute-EC2)
- [Containers-and-ECS](#Containers-and-ECS)
- [Advanced-EC2](#Advanced-EC2)
- [Route-53](#Route-53)

---

## Cloud-Computing-Fundamentals

Cloud computing provides

1. On-Demand Self-Service: Provision and terminate using a UI/CLI without
human interaction.
2. Broad Network Access: Access services over any networks on any devices using
standard protocols and methods.
3. Resource Pooling: Economies of scale, cheaper service.
4. Rapid Elasticity: Scale up and down automatically in response to system load.
5. Measured Service: Usage is measured. Pay only for what you consume.

### Public vs Private vs Multi Cloud

- Public Cloud: using 1 public cloud such as AWS, Azure, Google Cloud.
- Private Cloud: using on-premises real cloud. Must meet 5 requirements.
- Multi-Cloud: using more than 1 public cloud in one deployment.
- Hybrid Cloud: using public and private clouds in one environment
  - This is **NOT** using Public Cloud and Legacy on-premises hardware.

### Cloud Service Models

The *Infrastructure Stack* or *Application Stack* contains multiple components
that make up the total service. There are parts that **you** manage as well
as portions the **vendor** manages. The portions the vendor manages and you
are charged for is the **unit of consumption**

1. On-Premises: The individual manages all components from data to facilities.
Provides the most flexibility, but also most IT intensive.
2. Data Center Hosting: Place equipment in a building managed by a vendor.
You pay for the facilities only.
3. Infrastructure as a Service (IaaS): Vendor manages facilities and everything
else related to servers up to the OS. You pay per second or minute for the OS
used to the vendor. Lose some flexibility, but big risk reductions.
4. Platform as a Service (PaaS): Good for running an application only. The
unit of consumption is the runtime environment. You manage the application
and the data, but the vendor manges all else.
5. Software as a Service (SaaS): You consume the software as a service. This
can be Outlook or Netflix. There are almost no risks or additional costs, but
very little control.

There are additional services such as *Function as a Service*,
*Container as a Service*, and *DataBase as a Service* which be explained later.

---

## AWS-Fundamentals

### Public vs Private Services

Refers to the networking only, not permissions.

- Public Internet: AWS is a public cloud platform and connected to the public
internet. It is not on the public internet, but is next to it.
- AWS Public Zone: Attached to the Public Internet.
S3 Bucket is hosted in the Public Zone, not all services are.
Just because you connect to a public service,
that does not mean you have permissions to access it.
- AWS Private Zone: No direct connectivity is allowed between the AWS Private
Zone and the public cloud unless this is configured for that service.
This is done by taking a part of the private service and projecting it into the
AWS public zone which allows public internet to make inbound or outbound
connections.

### [AWS Global Infrastructure](https://www.infrastructure.aws/)

#### Regions

AWS Region is an area of the world they have selected for a full deployment of
AWS infrastructure.

Areas such as countries or states

- Ohio
- California
- Singapore
- Beijing
- London
- Paris

AWS can only deploy regions as fast as their planning allows.
Regions are often not near their customers.

#### AWS Edge Locations

Local distribution points. Useful for services such as Netflix so they can store
data closer to customers for low latency high speed transfers.

If a customer wants to access data stored in Brisbane, they will stream data
from the Sydney Region through an Edge Location hosted in Brisbane.

#### AWS Management

Regions are connected together with high speed networking.
Some services such as EC2 need to be selected in a region.
Some services are global such as IAM

#### Region's 3 Benefits

- Geographical Separation
  - Useful for natural disasters
  - Provide isolated fault domain
  - Regions are 100% isolated
- Geopolitical Separation
  - Different laws change how things are accessed
  - Stability from political events
- Location Control
  - Tune architecture for performance
  - Duplicate infrastructure at closer points to customers

#### Regions and AZs

Region Name: Asia Pacific (Sydney)
Region Code: ap-southeast-2

AWS will provide between 2 and 6 AZs per region.
AZs are isolated compute, storage, networking, power, and facilities.
Components are allowed to distribute load and resilience by using multiple zones.

AZs are connected to each other with high speed redundant networks.

#### Service Resilience

1. Globally Resilient: IAM or Route 53. No way for them to go down. Data is
replicated throughout multiple regions.
2. Region Resilient: Operate as separate services in each region. Generally
replicate data to multiple AZs in that region.
3. AZ Resilient: Run from a single AZ. It is possible for hardware to fail in an
AZ and the service to keep running because of redundant equipment, but should
not be relied on.

### AWS Default VPC

VPC is a virtual network inside of AWS.
A VPC is within 1 account and 1 region which makes it regionally resilient.
A VPC is private and isolated until decided otherwise.

One default VPC per region. Can have many custom VPCs which are all private
by default.

#### Default VPC Facts

VPC CIDR - defines start and end ranges of the VPC.
IP CIDR of a default VPC is always: **172.31.0.0/16**

Configured to have one subnet in each AZ in the region by default.

Subnets are given one section of the IP ranges for the default service.
In general do not use the Default VPC in a region because it is not flexible.

Default VPC is large because it uses the /16 range.
A subnet is smaller such as /20
The higher the / number is, the smaller the grouping.

Two /17's will fit into a /16, sixteen /20 subnets can fit into one /16.

### Elastic Compute Cloud (EC2)

Default compute service. Provides access to virtual machines called instances.

**IaaS** - Infrastructure as as Service

The unit of consumption is an instance
EC2 instance is configured to launch into a single VPC subnet.
Private service by default, public access must be configured.
The VPC needs to support public access. If you use a custom VPC then you must
handle the networking on your own.

EC2 deploys into one AZ. If it fails, the instance fails.

Different sizes and capabilities all use On-Demand Billing - Per second.
Only pay for what you consume.

Charge for running the instance, CPU, memory and storage.
Extra cost for any commercial software the instance deploys with.

Local on-host storage or **Elastic Block Storage**

Pricing based on:

- CPU
- Memory
- Storage
- Networking

#### Running State

Charged for all four categories.

- Running on a physical host using CPU.
- Using memory even with no processing.
- OS is stored on disk allocated
- Networking is always ready to transfer information.

#### Stopped State

Charged for EBS storage  only.

- No CPU resources are being consumed
- No memory is being used
- Networking is not running
- Storage is allocated to the instance for the OS.

#### Terminated State

No charges, deletes the disk and prevents all future charges.

#### AMI (Server Image)

AMI can use used to create an instance or created from an instance.

Contains:

- Permissions: control which accounts can and can't use the AMI.

  - Public: Anyone can launch it.

  - Owner - Implicit allow, only the owner can use it  spin up new instances

  - Explicit - owner grants access to AMI for specific AWS accounts

- Root Volume: contain the **Boot Volume**

- Block Device Mapping: links the volumes that the AMI has and
how they're presented to the operating system. Determines which volume is a
boot volume and which volumes is a data volume.

#### Connecting to EC2

- Windows using RDP (Remote Desktop Protocol), Port 3389
- Linux SSH protocol, Port 22

Login to the instance using an SSH key pair.
Private Key - Stored on local machine to initiate connection.
Public Key - AWS places this key on the instance.

### S3 (Default Storage Service)

Global Storage platform. Runs from all regions and is a public service.
Can be accessed anywhere from the internet with an unlimited amount of users.

This should be the default storage platform

S3 is an object storage, not file, or block storage.
You can't mount an S3 Bucket.

#### Objects

Can be thought of a file. Two main components:

- Object Key: File name in a bucket
- Value: Data or contents of the object
  - Zero bytes to 5 TB

Other components:

- Version ID
- Metadata
- Access Control
- Sub resources

#### Buckets

- Created in a specific AWS Region.
- Data has a primary home region. Will not leave this region unless told.
- Blast Radius = Region
- Unlimited number of Objects
- Name is globally unique
- All objects are stored within the bucket at the same level.

If the objects name starts with a slash such as `/old/Koala1.jpg` the UI will
present this as a folder. In actuality this is not true, there are no folders.

### CloudFormation Basics

Templates can modify infrastructure to, create, update and delete.

Written in YAML or JSON

```YAML
## This is not mandatory unless a description is added
AWSTemplateFormatVersion: "version date"

## Give details as to what this template does.
## If you use this section, it MUST immediately follow the AWSTemplateFormatVersion.
Description:
  A sample template

## Can control the command line UI. The bigger your template, the more likely
## this section is needed
Metadata:
  template metadata

## Prompt the user for more data. Name of something, size of instance,
## data validation
Parameters:
  set of parameters

## Another optional section. Allows lookup tables, not used often
Mappings:
  set of mappings

## Decision making in the template. Things will only occur if a condition is met.
## Step 1: create condition
## Step 2: use the condition to do something else in the template
Conditions:
  set of conditions

Transform:
  set of transforms

## The only mandatory field of this section
Resources:
  set of resources

## Once the template is finished it can return data or information.
## Could return the admin or setup address of a word press blog.
Outputs:
  set of outputs
```

#### Resources

An example which creates an EC2 instance

```YAML
Resources:
  Instance: ## Logical Resource
    Type: 'AWS::EC2::Instance' ## This is what will be created
    Properties: ## Configure the resources in a particular way
      ImageId: !Ref LatestAmiId
      Instance Type: !Ref Instance Type
      KeyName: !Ref Keyname
```

Once a template is created, AWS will make a stack. This is a living and active
representation of a template. One template can create infinite amount of stacks.

For any **Logical Resources** in the stack,
CF will make a corresponding **Physical Resources** in your AWS account.

It is cloud formations job to keep the logical and physical resources in sync.

A template can be updated and then used to update the same stack.

### CloudWatch Basics

Collects and manages operational data on your behalf.

Three products in one

- Metrics: data relating to AWS products, apps, on-prem solutions
- Logs: collection, monitoring
- Events: event hub
  - If an AWS service does something, CW events can perform another action
  - Generate an event to do something at a certain time of day or time of week.

#### Namespace

Container for monitoring data.
Naming can be anything so long as it's not `AWS/service` such as `AWS/EC2`.
This is used for all metric data of that service

#### Metric

Time ordered set of data points such as:

- CPU Usage
- Network IN/OUT
- Disk IO

This is not for a specific server. This could get things from different servers

Anytime CPU Utilization is reported, the **datapoint** will report

- Timestamp = 2019-12-03
- Value = 98.3

**Dimensions** separate data points for different **things** or
**perspectives** within the same metric

#### Alarms

Has two states `ok` or `alarm`.State can send an SNS or action.
Third state can be insufficient data state. Not a problem, just wait.

### Shared Responsibility Model

AWS: Responsible for security **OF** the cloud

Customer: Responsible for security **IN** the cloud

### High Availability (HA), Fault-Tolerance (FT), and Disaster Recover (DR)

#### High Availability (HA)

- Aims to **ensure** an agreed level of operational **performance**, usually
**uptime**, for a **higher than normal period**
- Instead of diagnosing the issue, swap it out.
- Redundant hardware to minimize downtown
- User disruption is not ideal, but is allowed
  - The user might need to log back in or lose some data on their screen.
- Maximizing a system's uptime
  - 99.9% (Three 9's) = 8.7 hours downtime per year.
  - 99.999 (Five 9's) = 5.26 minutes downtime per year.

#### Fault-Tolerance (FT)

- System can **continue operating properly**
in the event of the **failure of some** (one or more faults within) of its
**components**
- Fault tolerance is much more complicated than high availability and more
expensive. Outages must be minimized and the system needs levels of
redundancy.
- An airplane is an example of system that needs Fault Tolerance. It has
more engines than it needs for redundancy.

Example:
A patient is waiting for a life saving surgery and is under anesthetic.
While being monitored, the life support system is dosing medicine.
This type of system cannot only be highly available, even a movement of
interruption is deadly.

#### Disaster Recover (DR)

- Set of policies, tools and procedures to **enable the recovery** or
**continuation** of **vital** technology infrastructure and systems
**following a natural or human-induced disaster**.
- DR can largely be automated to eliminate the time for recovery and errors.

This involves:

- Pre-planning
  - Ensure plans are in place for extra hardware
  - Do not store backups at the same site as the system
- DR Processes
  - Cloud machines ready when needed

This is designed to keep the crucial and non replaceable parts of the
system in place.

### Domain Name System (DNS)

DNS is a discovery service. Translates machines into humans and vice-versa.
It is a huge database and has to be distributed.

Parts of the DNS system

- DNS Client: Piece of software running on the OS for a device you're using.
- Resolver: Software on your device or server which queries DNS on your behalf.
- Zone: A part of the DNS database.
  - This would be www.amazon.com
  - What the data is, the substance
- Zonefile: physical database for a zone
  - How physically that data is stored
- Nameserver: where zonefiles are hosted

Steps:

Find the Nameserver which hosts a particular Zonefile.
Query that Nameserver for a record with that Zone.
It then passes the information back to the client.

#### DNS Root

The starting point of DNS.
DNS names are read right to left with multiple parts separated by periods.

`www.netflix.com.`

The period is assumed to be there in a browser when it's not present.
The DNS Root is hosted on DNS Root Servers (13). These are hosted
by 12 major companies.

**Root Hints** is a pointer to the DNS Root server

Process

1. DNS client asks DNS Resolver for IP address of a given DNS name.
2. Using the Root Hints file, the DNS Resolver communicates with one or
more of the root servers to access the root zone and begin the process
of finding the IP address.

The Root Zone is organized by IANA (Internet Assigned Numbers Authority).
Their job is to manage the contents of the root zone. IANA is in charge
of the DNS system because they control the root zone.

#### DNS Hierarchy

Assuming a laptop is querying DNS directly for www.amazon.com and using
a root hints file to know how to access a root server and query the root zone.

- When something is trusted in DNS, it is an **authority**.
- One piece can be authoritative for root.
- One piece can be authoritative for amazon.com
- The root zone is the start and the only thing trusted in DNS.
- The root zone can delegate a part of itself to another zone or entity.
- That someone else then becomes authoritative for that piece of itself only.
- The root zone is just a database of the top level domains.

The top level domains are the only things to the left of the DNS name.

- `.com` or `.org` are generic top level domains (GTLD)
- `.uk` is a country code top level domains (CCTLD)

**Registry** maintains the zones for a TLD (e.g .ORG)
**Registrar** has relationships with the .org TLD zone manager
allowing domain registration

### Route53 Fundamentals

- Registers domains
- Can Host Zone Files on managed nameservers
- This is a global service, no need to pick a region
- Globally Resilience
  - Can operate with failure in one or more regions

#### Register Domains

Has relationships with all major registries

- Route 53 will check with the top level domain to see if the name is available
- Router 53 creates a zonefile for the domain to be registered
- Allocates nameservice for that zone
  - Generally four of these for one individual zone
  - This is a hosted zone
  - The zone file will be put on these four managed nameservers
- Router 53 will communicate with the `.org` registry and add the nameserver
records into the zone file for the top level domain.
  - This is done with a nameserver record.

#### Route53 Details

**Zonefiles** in AWS
Hosted on four managed name servers

- Can be **public** or **private**

### DNS Record

- Nameserver (NS): Allows delegation to occur in the DNS.
- A and AAAA Records: Maps the host to a v4 or v6 host type. Most of the time
you will make both types of record, A and AAAA.
- CNAME Record Type: Allows DNS shortcuts to reduce admin overhead.
CNAMES cannot point directly at an IP address and only another name.
- MX records: How emails are sent. They have two main parts:
  - Priority: Lower values for the priority field are higher priority.
  - Value
    - If it is just a host, it will not have a dot on the right. It is assumed
to be part of the same zone as the host.
    - If you include a dot on the right, it is a ***fully qualified domain name***
- TXT Record: Allows you to add arbitrary text to a domain.
One common usage is to prove domain ownership.

#### TTL - Time To Live

This is a numeric setting on DNS records in seconds.
Allows the admin to specify how long the query can be stored
at the resolver server.
If you need to upgrade the records, it is smart to lower the TTL value first.

Getting the answer from an Authoritative Source is known as an
**Authoritative Answer**.

If another client queries the same thing, they will get back a
**Non-Authoritative** response.

---

## IAM-Accounts-AWS-Organizations

### IAM Identity Policies

Identity Policies are attached to AWS Identities which are
IAM users, IAM groups, and IAM roles. These are a set of security statements
that ALLOW or DENY access to AWS resources.

When an identity attempts to access AWS resources, that identity needs
to prove who it is to AWS, a process known as **Authentication**.
Once authenticated, that identity is known as an **authenticated identity**

#### Statement Components

- Statement ID (SID): Optional field that should help describe
  - The resource you're interacting
  - The actions you're trying to perform
- Effect: is either `allow` or `deny`.
  - It is possible to be allowed and denied at the same time
- Action are formatted `service:operation`. There are three options:
  - specific individual action
  - wildcard as an action
  - list of multiple independent actions
- Resource: similar to action except for format `arn:aws:s3:::catgifs`

#### Priority Level

- Explicit Deny: Denies access to a particular resource cannot be overruled.
- Explicit Allow: Allows access so long there is not an explicit deny.
- Default Deny (Implicit): IAM identities start off with no resource access.

#### Inline Policies and Managed Policies

- Inline Policy: grants access and assigned on each accounts individually.
- Managed Policy (best practice): one policy is applied to all users at once.

### IAM Users

Identity used for anything requiring **long-term** AWS access

- Humans
- Applications
- Service Accounts

If you can name a thing to use the AWS account, this is an IAM user.

When a **principal** wants to **request** to perform an action,
it will **authenticate** against an identity within IAM. An IAM user is an
identity which can be used in this way.

There are two ways to authenticate:

- Username and Password
- Access Keys (CLI)

Once the **Principal** has authenticated, it becomes an **authenticated identity**

#### Amazon Resource Name (ARN)

Uniquely identify resources within any AWS accounts.

This allows you to refer to a single or group of resources.
This prevents individual resources from the same account but in
different regions from being confused.

ARN generally follows the same format:

```bash
arn:partition:service:region:account-id:resource-id
arn:partition:service:region:account-id:resource-type/resource-id
arn:partition:service:region:account-id:resource-type:resource-id
```

- partition: almost always `aws` unless it is china `aws-cn`
- region: can be a double colon (::) if that doesn't matter
- account-id: the account that owns the resource
  - EC2 needs this
  - S3 does not need account-id because its globally unique
- resource-type/id: changes based on the resource

An example that leads to confusion:

- arn:aws:s3:::catgifs
  - This references an actual bucket
- arn:aws:s3:::catgifs/*
  - This refers to objects in that bucket, but not the bucket itself.

These two ARNs do not overlap

#### IAM FACTS

- 5,000 IAM users per account
- IAM user can be a member of 10 groups

### IAM Groups

Containers for users. **You cannot login to IAM groups** They have no
credentials of their own. Used solely for management of IAM users.

Groups bring two benefits

1. Effective administrative style management of users based on the team
2. Groups can have Inline and Managed policies attached.

AWS merges all of the policies from all groups the user is in together.

- The 5000 IAM user limit applies to groups.
- There is **no all users** IAM group.
  - You can create a group and add all users into that group, but it needs to be
created and managed on your own.
- No Nesting: You cannot have groups within groups.
- 300 Group Limit per account. This can be fixed with a support ticket.

**Resource Policy** A bucket can have a policy associated with that bucket.
It does so by referencing the identity using an ARN (Amazon Reference Name).
A policy on a resource can reference IAM users and IAM roles by the ARN.
A bucket can give access to one or more users or one or more roles.

**GROUPS ARE NOT A TRUE IDENTITY**
**THEY CAN'T BE REFERENCED AS A PRINCIPAL IN A POLICY**

An S3 Resource cannot grant access to a group, it is not an identity.
Groups are used to allow permissions to be assigned to IAM users.

### IAM Roles

A single thing that uses an identity is an IAM User.

IAM Roles are also identities that are used by large groups of individuals.
If have more than 5000 principals, it could be a candidate for an IAM Role.

IAM Roles are **assumed** you become that role.

This can be used short term by other identities.

IAM Users can have inline or managed policies which control which permissions
the identity gets within AWS

Policies which grant, allow or deny, permissions based on their associations.

IAM Roles have two types of roles can be attached.

- Trust Policy: Specifies which identities are allowed to assume the role.
- Permissions Policy: Specifies what the role is allowed to do.

If an identity is allowed on the **Trust Policy**, it is given a set
of **Temporary Security Credentials**. Similar to access keys except they
are time limited to expire. The identity will need to renew them by
reassuming the role.

Every time the **Temporary Security Credentials** are used, the access
is checked against the **Permissions Policy**. If you change the policy, the
permissions of the temp credentials also change.

Roles are real identities and can be referenced within resource policies.

Secure Token Service (sts:AssumeRole) this is what generates the temporary
security credentials (TSC).

### When to use IAM Roles

Lambda Execution Role.
For a given lambda function, you cannot determine the number of principals
which suggested a Role might be the ideal identity to use.

- Trust Policy: to trust the Lambda Service
- Permission Policy: to grant access to AWS services.

When this is run, it uses the sts:AssumeRole to generate keys to
CloudWatch and S3.

It is better when possible to use an IAM Role versus attaching a policy.

#### Emergency or out of the usual situations

Break Glass Situation - There is a key for something the team does not
normally have access to. When you break the glass, you must have a reason
to do.
A role can have an Emergency Role which will allow further access if
its really needed.

#### Adding AWS into existing corp environment

You may have an existing identity provider you are trying to allow access to.
This may offer SSO (Single Sign On) or over 5000 identities.
This is useful to reuse your existing identities for AWS.
External accounts can't be used to access AWS directly.
To solve this, you allow an IAM role in the AWS account to be assumed
by one of the active directories.
**ID Federation** allowing an external service the ability to assume a role.

#### Making an app with 1,000,000 users

**Web Identity Federation** uses IAM roles to allow broader access.
These allow you to use an existing web identity such as google, facebook, or
twitter to grant access to the app.
We can trust these web identities and allow those identities to assume
an IAM role to access web resources such as DynamoDB.
No AWS Credentials are stored on the application.
Can scale quickly and beyond.

#### Cross Account Access

You can use a role in the partner account and use that to upload objects
to AWS resources.

### AWS Organizations

Without an organization, each AWS account needs it's own set of IAM users
as well as individual payment methods.
If you have more than 5 to 10 accounts, you would want to use an org.

Take a single AWS account **standard AWS account** and create an org.
The standard AWS account then becomes the **master account**.
The master account can invite other existing standard AWS accounts. They will
need to approve their joining to the org.

When standard AWS accounts become part of the org, they
become **member accounts**.
Organizations can only have one **master accounts** and zero or more
**member accounts**

#### Organization Root

This is a container that can hold AWS member accounts or the master account.
It could also contain **organizational units** which can contain other
units or member accounts.

#### Consolidated billing

The individual billing for the member accounts is removed and they pass their
billing to the master account.
Inside an AWS organization, you get a single monthly bill for the master
account which covers all the billing for each users.
Can offer a discount with consolidation of reservations and volume discounts

#### Create new accounts in an org

Adding accounts in an organization is easy with only an email needed.
You no longer need IAM users in each accounts. You can use IAM roles
to change these.
It is best to have a single AWS account only used for login.
Some enterprises may use an AWS account while smaller ones may use the master.

#### Role Switching

Allows you to switch between accounts from the command line

### Service Control Policies

Can be used to restrict what member accounts in an org can do.

JSON policy document that can be attached:

- To the org as a whole by attaching to the root container.
- A specific Organizational Unit
- A specific member only.

The master account cannot be restricted by SCPs which means this
should not be used because it is a security risk.

SCPs limit what the account, **including root** can do inside that account.
They don't grant permissions themselves, just act as a barrier.

#### Allow List vs Deny List

Deny list is the default.

When you enable SCP on your org, AWS applies `FullAWSAccess`. This means
SCPs have no effect because nothing is restricted. It has zero influence
by themselves.

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }
}
```

SCPs by themselves don't grant permissions. When SCPs are enabled,
there is an implicit deny.

You must then add any services you want to Deny such as `DenyS3`

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Deny",
    "Action": "s3:*",
    "Resource": "*"
  }
}
```

**Deny List** is a good default because it allows for the use of growing
services offered by AWS. A lot less admin overhead.

**Allow List** allows you to be conscience of your costs.

- To begin, you must remove the `FullAWSAccess` list
- Then, specify which services need to be allowed access.
- Example `AllowS3EC2` is below

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
        "Effect": "Allow",
        "Action": [
            "s3:*",
            "ec2:*"
        ],
    "Resource": "*"
    }
  ]
}
```

### CloudWatch Logs

This is a public service, this can be used from AWS VPC or on premise
environment.

This allows to **store**, **monitor** and **access** logging data.

- This is a piece of information data and a timestamp
- Can be more fields, but at least these two

Comes with some AWS Integrations.
Security is provided with IAM roles or Service roles
Can generate metrics based on logs **metric filter**

#### Architecture of CloudWatch Logs

It is a regional service `us-east-1`

Need logging sources such as external APIs or databases. This sends
information as **log events**. These are stored in **log streams**. This is a
sequence of log events from the same source.

**Log Groups** are containers for multiple logs streams of the same
type of logging. This also stores configuration settings such as
retention settings and permissions.

Once the settings are defined on a log group, they apply to all log streams
in that log group. Metric filters are also applied on the log groups.

### CloudTrail Essentials

Concerned with who did what.

Logs API calls or activities as **CloudTrail Event**

Stores the last 90 days of events in the **Event History**. This is enabled
by default and is no additional cost.

To customize the service you need to create a new **trail**.
Two types of events. Default only logs Management Events

- Management Events:
Provide information about management operations performed on resources
in the AWS account. Create an EC2 instance or terminating one.

- Data Events:
Objects being uploaded to S3 or a Lambda function being invoked. This is not
enabled by default and must be enabled for that trail.

#### CloudTrail Trail

Logs events for the AWS region it is created in. It is a regional service.

Once created, it can operate in two ways

- One region trail
- All region trail
  - Collection of trails in all regions
  - When new regions are added, they will be added to this trail automatically

Most services log events in the region they occur. The trail then must be
a one region trail in that region or an all region trail to log that event.

A small number of services log events globally to one region. Global services
such as IAM or STS or CloudFront always log their events to `us-east-1`

A trail must have this enabled to have this logged.

AWS services are largely split into regional services or global services.

When the services log, they log in the region they are created or
to `us-east-1` if they are a global service.

A trail can store events in an S3 bucket as a compressed JSON file. It can
also use CloudWatch Logs to output the data.

CloudTrail products can create an organizational trail. This allows a single
management point for all the APIs and management events for that org.

#### CloudTrail Exam PowerUp

- It is enabled by default for 90 days without S3
- Trails are how you configure S3 and CWLogs
- Management events are only saved by default
- IAM, STS, CloudFront are Global Service events and log to `us-east-1`
  - Trail must be enabled to do this
- NOT REALTIME - There is a delay. Approximately 15 minute delay

#### CloudTrail Pricing

<https://aws.amazon.com/cloudtrail/pricing/>

---

## Simple-Storage-Service-(S3)

### S3 Security

**S3 is private by default!** The only identity which has any initial
access to an S3 bucket is the account root user of the account which owns that
bucket.

#### S3 Bucket Policy

This is a **resource policy**

- controls who has access to that resource
- can allow or deny access from different accounts
- can allow or deny anonymous principals
  - this is explicitly declared in the bucket policy itself.

Different from an **identity policy**

- controls what that identity can access
- can only be attached to identities in your own account
  - no way of giving an identity in another account access to a bucket.

Each bucket can only have one policy, but it can have multiple statements.

#### ACLs (Legacy)

A way to apply a subresource to objects and buckets.
These are legacy and AWS does not recommend their use.
They are inflexible and allow simple permissions.

#### S3 Exam PowerUp

When to use Identity Policy or Bucket Policy:

Identity

- Controlling high mix of different resources.
  - Not every service supports resource policies.
- Want to manage permissions all in one place, use IAM.
- Must have access to all accounts accessing the information.

Bucket

- Managing permissions on a specific product.
- If you need anonymous or cross account access.

ACLs: NEVER - unless you must.

### S3 Static Hosting

Normal access is via AWS APIs.
This allows access via HTTP using a web browser.

When you enable static website hosting you need two HTML files:

- index document
  - default page returned from a website
  - entry point for most websites
- error document
  - similar to index, but only when something goes wrong

Static website hosting creates a **website endpoint**.

This is influenced by the bucket name and region it is in.
This cannot be changed.

You can use a custom domain for a bucket, but then the bucket name matters.
The name of the bucket must match the domain.

#### Offloading

Instead of using EC2 to host an entire website, the compute service
can generate a HTML file which points to the resources hosted on a static
bucket. This ensures the media is retrieved from S3 and not EC2.

#### Out-of-band pages

This may be an error page to display maintenance if the server goes offline.
We could then change our DNS and move customers to a backup website on S3.

#### S3 Pricing

- Cost to store data, per GB / month fee
  - Prorated for less than a GB or month.
- Data transfer fee
  - Data in is always free
  - Data out is a per GB charge
- Each operation has a cost per 1000 operations.
  - Can add up for static website hosting with many requests.

### Object Versioning and MFA Delete

Without Versioning:

- Each object is identified solely by the object key, it's name.
- If you modify an object, the original of that object is replaced.
- The attribute, **ID of object**, is set to **null**.

Versioning

- This is off by default.
- Once it is turned on, it cannot be turned off.
- Versioning can be suspended and enabled again.
- This allows for multiple versions of objects within a bucket.
- Objects which would modify objects **generate a new version** instead.

The latest or current version is always returned when an object version
is not requested.

When an object is deleted, AWS puts a **delete marker** on the object
and hides all previous versions. You could delete this marker to enable
the item.

To delete an object, you must delete all the versions of that object
using their version marker.

#### MFA Delete

Enabled within version configuration in a bucket.
This means MFA is required to change bucket versioning state.
MFA is required to delete versions of an object.

In order to change a version state or delete a particular version of an object,
you need to provide the serial number of your MFA token as well as the code
it generates. These are concatenated and passed with any API calls.

### S3 Performance Optimization

Single PUT Upload

- Objects uploaded to S3 are sent as a single stream by default.
- If the stream fails, the upload fails and requires a restart of the transfer.
- Single PUT upload up to 5GB

Multipart Upload

- Data is broken up into smaller parts.
- The minimum data size is 100 MB.
- Upload can be split into maximum of 10,000 parts.
  - Each part can range between 5MB and 5GB.
  - Last leftover part can be smaller than 5MB as needed.
- Parts can fail in isolation and restart in isolation.
- The risk of uploading large amounts of data is reduced.
- Improves transfer rate to be the speed of all parts.

S3 Accelerated Transfer

- Off by default.
- Uses the network of AWS edge locations to speed up transfer.
- Bucket name cannot contain periods.
- Name must be DNS compatible.
- Benefits improve the larger the location and distance.
  - The worse the start, the better the performance benefits.

### Encryption 101

#### Encryption at Rest

- An example is a password on a laptop
  - If the laptop is stolen, the data is already encrypted and useless.
- Commonly within cloud environments. Even if someone could
find and access the base storage device, they can't do anything with it.
- Only one entity involved

#### Encryption in Transit

- An encryption tunnel outside the raw data.
- Anyone looking from the outside will only see a stream of scrambled data.
- Used when there are multiple parties or systems at play.

#### Terms

- plaintext: unencrypted data not limited to text
- key: a password
- ciphertext: encrypted data generated by an algorithm from plaintext and a key

#### Symmetric Encryption

The key is handed from one entity to another before the data.
This is difficult because the key needs to be transferred securely.
If the data is time sensitive, the key needs to be arranged beforehand.

#### Asymmetric Encryption

- public key: cannot decrypt data but can generate ciphertext
- private key: can decrypt data encrypted by the public key

The public key is uploaded to cloud storage.
The data is encrypted and sent back to the original entity.
The private key can decrypt the data.

This is secure because stolen public keys can only encrypt data.
Private keys must be handled securely.

#### Signing

Encryption by itself does not prove who encrypted the data.

1. An entity can encrypt a message with their private key.
2. Their public key is hosted in an accessible location.
3. The receiving party can use the public key to confirm who sent the message.

#### Steganography

Encryption is obvious when used. There is no denying that the
data was encrypted. Someone could force you to decrypt the data packet.

A file can be hidden in an image or other file. If it difficult
to find the message unless you know what to look for.

One party would take another party's public key and encrypt some data to create
ciphertext. That ciphertext can be hidden in another file so long as both
parties know how the data will be hidden.

### Key Management Service (KMS)

- Regional service
  - Every region is isolated when using KMS.
- Public service
  - Occupies the AWS public zone and can be connected to from anywhere.
- Create, store, and manage keys.
  - Can handle both symmetric and asymmetric keys.
- KMS can perform cryptographic operations itself.
- Keys never leave KMS.
- Keys use **FIPS 140-2 (L2)** security standard.
  - Some features are compliant with Level 3.
  - All features are compliant with Level 2.

#### CMKs - Customer Master Keys

- Managed by KMS and used within cryptographic operations.
- AWS services, applications, and the user can all use them.
- Think of them as a container for the actual physical master keys.
- These are all backed by **physical** key material.
- You can generate or import the key material.
- CMKs can be used for up to **4KB of data**.

It is logical and contains

- Key ID: unique identifier for the key
- Creation Date
- Key Policy: a type of resource policy
- Description
- State of the Key: active or not

#### Data Encryption Key (DEK)

- Generated by KMS using the CMK and `GenerateDataKey` operation.
- Used to encrypt data larger than 4KB in size.
- Linked to a specific CMK so KMS can tell that a specific DEK was
generated with a specific CMK.

KMS does not store the DEK, once provided to a user or service, it is
discarded. KMS doesn't actually perform the encryption or decryption
of data using the DEK or anything past generating them.

When the DEK is generated, KMS provides two version.

- Plaintext Version - This can be used immediately.
- Ciphertext Version - Encrypted version of the DEK.
  - This is encrypted by the CMK that generated it.
  - In the future it can be decrypted by KMS using the CMK assuming
  you have the permissions.

Architecture

1. DEK is generated right before something is encrypted.
2. The data is encrypted with the plaintext version of the DEK.
3. Discard the plaintext data version of the DEK.
4. The encrypted DEK is stored next to the ciphertext generated earlier.

#### KMS Key Concepts

- Customer Master Keys (CMK) are isolated to a region.
  - Never leave the region or KMS.
  - Cannot extract a CMK.
- AWS managed CMKs
  - Created automatically by AWS when using a service such
  as S3 which uses KMS for encryption.
- Customer managed CMKS
  - Created explicitly by the customer.
  - Much more more configurable, for example the key policy can be edited.
  - Can allow other AWS accounts access to CMKS

All CMKs support key rotation.

- AWS automatically rotates the keys every 1095 days (3 years)
- Customer managed keys rotate every year.

CMK itself contains:

- Current backing key, physical material used to encrypt and decrypt
- Previous backing keys created from rotating that material

KMS can create an alias which is a shortcut to a particular CMK.
Aliases are also per region. You can create a `MyApp1` alias in all regions
but they would be separate aliases, and in each region it would be pointing
potentially at a different CMK.

#### Key Policy (resource policy)

- Every CMK has one.
- Customer managed CMKs can adjust the policy.
- Unlike other policies, KMS has to be explicitly told that keys trust the AWS
account that they're in.
- The trust isn't automatic so be careful when adjusting key policies.
- You always need a key policy in place so the key trusts the account and so
that the account can manage it by applying IAM permission policies to IAM users
in that account.
- In order for IAM to work, IAM is trusted by the account, and the account
needs to be trusted by the key.
- It sets up this chain of trust from the key to the account to IAM and then
to an IAM user, if they're granted any identity permissions.

### KMS Key Demo

Linux/macOS commands

```bash
aws kms encrypt \
    --key-id alias/catrobot \
    --plaintext fileb://battleplans.txt \
    --output text \
    --query CiphertextBlob \
    --profile iamadmin-general | base64 \
    --decode > not_battleplans.enc
```

```bash
aws kms decrypt \
    --ciphertext-blob fileb://not_battleplans.enc \
    --output text \
    --profile iamadmin-general \
    --query Plaintext | base64 --decode > decryptedplans.txt
```

### Object Encryption

Buckets aren't encrypted, **objects are**.
Multiple objects in a bucket can use a different encryption methods.

Two main methods of encryption S3 is capable of supporting.
Both types are encryption at rest. Data sent from a user to S3 is automatically
encrypted in transit outside of these methods.

Client-Side encryption

- Objects being encrypted by the client before they leave.
- Data being sent the whole time it is sent as cypher text.
- AWS has no way to see into the data.
- The encryption burden is on the customer and not AWS.

Server-Side encryption

- Data is encrypted in transit using HTTPS
- Data inside the tunnel is still in its original unencrypted form.
- Data reaches S3 server in plain text form.
- After S3 sees the data, it is then encrypted.
- AWS will handle some or all of these processes.

#### SSE-C (Server-side encryption with customer provided keys)

- Customer is responsible for the keys themselves.
- S3 services manages the actual encryption and decryption
  - Offloads CPU requirements for encryption.
- Customer still needs to generate and manage the key.
- S3 will see the unencrypted object throughout this process.

SSE-C Encryption Steps

1. When placing an object in S3, you provide encryption key and plaintext object
2. Once the key and object arrive, it is encrypted.
3. A hash of the key is taken and attached to the object.
The hash can identify if the specific key was used to encrypt the object.
4. The key is then discarded after the hash is taken.
5. The encrypted and one-way hash are stored persistently on storage.

To decrypt the object, you must tell S3 which object to decrypt and provide
it with the key used to encrypt it. If the key that you supply is correct,
the proper hash, S3 will decrypt the object, discard the key, and return the
plaintext version of the object.

#### SSE-S3 AES256 (Server-side encryption w/ Amazon S3 managed keys)

AWS handles both the encryption and decryption process as well as the
key generation and management. This provides very little control over how
the keys are used, but has little admin overhead.

SSE-S3 Encryption Steps

1. When putting data into S3, only need to provide plaintext.
2. S3 generates fully managed and rotated **master key** automatically.
3. Object generates a key specific for each object that is uploaded.
4. The master key is used to encrypt the specific object key, and the
unencrypted version of that key is discarded.
5. The encrypted file and encrypted key are stored side by side in S3.

Three Problems with this method:

- Not good for regulatory environment where keys and access must be controlled.
- No way to control key material rotation.
- No role separation. A full S3 admin can decrypt data and open objects.

#### SSE-KMS (Server-side encryption w/ customer master keys stored in AWS KMS)

Much like SSE-S3, where AWS handles both the keys and encryption process.
KMS handles the master key and not S3. The first time an object is uploaded,
S3 works with KMS to create an AWS managed CMK. This is the default key
which gets used in the future.

Every time an object is uploaded, S3 uses a dedicated key to encrypt that object
and that key is a data encryption key which KMS generates using the CMK.
The CMK does not need to be managed by AWS and can be a customer managed CMK.

SSE-KMS Encryption Steps

1. S3 is provided a plaintext version of the data encryption key as well
as an encrypted version.
2. The data is encrypted with the plaintext key and the key discarded.
3. The encrypted key is stored alongside the encrypted object.

When uploading an object, you can create and use a customer managed CMK. This
allows the user to control the permissions and the usage of the key material.
In regulated industries, this is reason enough to use SSE-KMS
You can also add logging and see any calls against this key from CloudTrail.

The best benefit is the role separation. To decrypt any object, you need
access to the CMK that was used to generate the unique key that encrypted them.
The CMK is used to decrypt the data encryption key for that object.
That decrypted data encryption key is used to decrypt the object itself.
If you don't have access to KMS, you don't have access to the object.

### S3 Object Storage Classes

Picking a storage class can be done while uploading a specific object.
The default is S3 standard. Once an object is uploaded to a specific class,
it can be easily changed as long as some conditions are met.

Objects in S3 are stored in a specific region.

#### S3 Standard

- Default AWS storage class that's used in S3, should be user default as well.
- S3 Standard is region resilient, and can tolerate the failure of an AZ.
- Objects are replicated to at least 3+ AZs when they are uploaded.
- 99999999999% durability
- 99.99% availability
- Offers low latency and high throughput.
- No minimums, delays, or penalties.
- Billing is storage fee, data transfer fee, and request based charge.

All of the other storage classes trade some of these compromises for another.

#### S3 Standard-IA

- Designed for less frequent rapid access when it is needed.
- Cheaper rate to store data you will rarely need, but if you do need it, you
need it quickly.
- ~54% cheaper than S3 standard.
- Minimum 128KB charge for each object.
  - Cost benefits might be negated for smaller objects.
- 30 days minimum duration charge per object.
- Retrieval fee for every GB of data retrieved from this class.
- 99.9% availability, slightly lower than standard S3.

Designed for data that isn't accessed often, long term storage, backups,
disaster recovery files. The requirement for data to be safe is most important.

#### One Zone-IA

- Designed for data that is accessed less frequently but needed quickly.
- 80% of the base cost of Standard-IA.
- Same minimum size and duration fee as Standard-IA
- Data is only stored in a single AZ, no 3+ AZ replication.
- 99.5% availability, lower than Standard-IA

Great choice for secondary copies of primary data or backup copies.

If data is easily creatable from a primary data set, this would be a great
place to store the output from another data set.

#### S3 Glacier

- No immediate access to objects, retrieval in minutes or hours.
- Make a request to access objects then after a duration, you get access.
  - Retrieval time anywhere from 1 min - 12 hrs
- Secure, durable, and low cost storage for archival data.
- 17% of the base cost of S3 standard
- 99999999999% durability
- 99.99% availability
- 3+ AZ replication
- 40KB minimum object capacity charge
- 90 days minimum storage duration charge.

Retrieval methods:

- Expedited: 1 - 5 minutes, but is the most expensive
- Standard: 3 - 5 hours to restore.
- Bulk: 5 - 12 hours. Has the lowest cost and is good for a large set of data.

#### S3 Glacier Deep Archive

- Designed for long term backups and as a tape-drive replacement.
- 4.3% of the base cost of S3 standard
- 180 days minimum storage duration charge.
- Standard retrieval within 12 hours, bulk retrieval in 48 hours.
- Cannot use to make data public or download normally.

#### S3 Intelligent-Tiering

- Combination of standard and standard IA.
- Uses automation to remove overhead of moving objects.
- Additional fee of $0.0025 per 1,000 objects tracked.
- If an object is not accessed for 30 days, it will move into Standard-IA.

This is good for objects that are unknown their access pattern.

### Object Lifecycle Management

Intelligent-Tiering is used for objects where access patterns is unknown.
A lifecycle configuration is a set of **rules** that consists of **actions**.

#### Transition Actions

Change the storage class over time such as:

- Move an object from S3 to IA after 90 days
- After 180 days move to Glacier
- After one year move to Deep Archive

Objects must flow downwards, they can't flow in the reverse direction.

#### Expiration Actions

Once an object has been uploaded and changed, you can purge older versions
after 90 days to keep costs down.

### S3 Replication

There are two types of S3 replication available.

- Cross-Region Replication (CRR)
  - Allows the replication of objects from a source bucket to a destination
bucket in **different** AWS regions.
- Same-Region Replication (SRR)
  - Allows the replication of objects from a source bucket to a destination
bucket in the **same** AWS region.

Architecture for both is similar, only difference is if both buckets are
in the same account or different accounts.

The replication configuration is applied to the source bucket and configures
S3 to replicate from this source bucket to a destination bucket.
It also configures the IAM role to use for the replication process.
The role is configured to allow the S3 service to assume it based on
its trust policy. The role's permission policy allows it to read objects on the
source bucket and replicate them to the destination bucket.

When different accounts are used, the role is not by default trusted
by the destination account. If configuring between accounts, you must
add a bucket policy on the destination account to allow the IAM role from
the source account access to the bucket.

#### S3 Replication Options

- Which objects are replicated.
  - Default is all source objects, but can select a smaller subset of objects.
- Select which storage class the destination bucket will use.
  - Default is the same type of storage, but this can be changed.
- Define the ownership of the objects.
  - The default is they will be owned by the same account as the source bucket.
  - If the buckets are in different accounts, the objects in the destination
  could be owned by the source account and not allowed access.
- Replication Time Control (RTC)
  - Adds a guaranteed level of SLA within 15 minutes for extra cost.
  - This is useful for buckets that must be in sync the whole time.

#### Important Replication Tips

- Replication is not retroactive.
  - If you enable replication on a bucket that already has objects, the old
  objects will not be replicated.
- Both buckets must have versioning enabled.
- It is a one way replication process only.
- Replication by default can handle objects that are unencrypted or SSE-S3.
  - With configuration it can handle SSE-KMS, but KMS requires more
configuration to work.
  - It cannot replicate objects with SSE-C because AWS does not have the keys
necessary.
- Source bucket owner needs permissions to objects. If you grant cross-account
access to a bucket. It is possible the source bucket account will not own
some of those objects.
- Will not replicate system events, glacier, or glacier deep archive.
- No deletes are replicated.

#### Why use replication

SRR - Log Aggregation
SRR - Sync production and test accounts
SRR - Resilience with strict sovereignty requirements
CRR - Global resilience improvements
CRR - Latency reduction

### S3 Presigned URL

A way to give another person or application access to a object inside an S3
bucket using your credentials in a safe way.

IAM admin can make a request to S3 to generate a presigned URL by providing:

- security credentials
- bucket name
- object key
- expiry date and time
- indicate how the object or bucket will be accessed

S3 will create a presigned URL and return it. This URL will have encoded inside
it the details that IAM admin provided. It will be configured to expire at
a certain date and time as requested by the IAM admin user.

#### S3 Presigned URL Exam PowerUp

- You can create a presigned URL for an object you have do not have access to.
The object will not allow access because your user does not have access.
- When using the URL the permission that you have access to, match the identity
that generated it at the moment the item is being accessed.
- If you get an access deny it means the ID never had access, or lost it.
- Don't generate presigned URLs with an IAM role.
  - The role will likely expire before the URL does.

### S3 Select and Glacier Select

This provides a ways to retrieve parts of objects and not the entire object.

If you retrieve a 5TB object, it takes time and consumes 5TB of data.
Filtering at the client side doesn't reduce this cost.

S3 and Glacier select lets you use SQL-like statements to select part of the
object which is returned in a filtered way.
The filtering happens at the S3 service itself saving time and data.

---

## Virtual-Private-Cloud-VPC

### Networking Refresher

#### IPv4 - RFC 791 (1981)

Dotted decimal notation for human readability.

- 4 numbers from 0 to 255 separated by a period.
- Octet are the numbers between the period.

There are just over 4 billion addresses.
This was not very flexible because it was either too small or large for
some corporations. Some IP addresses was always left unused.

#### Classful Addressing

- Class A range
  - Starts at `0.0.0.0` and ends at `127.255.255.255`.
  - Split into 128 class A networks
  - Handed out to large companies
- Class B Range
  - Half the range of class A.
  - Starts at `128.0.0.0` and ends at `191.255.255.255`.
- Class C Range
  - Half of range class B
  - Starts at `192.0.0.0` and ends at `223.255.255.255`.

#### Internet / Private IPs - RFC1918

These can't communicate over the internet and are used internally only

- One class A network: `10.0.0.0` - `10.255.255.255`
- 16 Class B networks: `172.16.0.0` - `172.31.255.255`
- 256 Class C networks: `192.168.0.0` - `192.168.255.255`

#### Classless inter-domain routing (CIDR)

CIDR networks are represented by the starting IP address of the network
called the network address and the prefix.

CIDR Example: `10.0.0.0/16`

- `10.0.0.0` is the first address on the network
- /16 is the size of the network called the prefix.
  - The bigger the prefix, the smaller the network
  - The smaller the prefix, the bigger the network.
- /16 provides 65,536 addresses.
- `10.0.0.0/17` and `10.0.128.0/17` are each half of the original example.
  - This is called **subnetting**

#### IP address notations to remember

- `0.0.0.0/0` means all IP addresses
- `10.0.0.0/8` means 10.ANYTHING - Class A
- `10.0.0.0/16` means 10.0.ANYTHING - Class B
- `10.0.0.0/24` means 10.0.0.ANYTHING - Class C
- `10.0.0.0/32` means only 1 IP address

`10.0.0.0/16` is the equivalent of `1234` as a password. You should consider
other ranges that people might use to ensure it does not overlap.

#### Packets

Contains:

- source IP address
- destination IP address
- data the source IP wants to communicate with the destination IP.

TCP and UDP are protocols built on top of IP.

- TCPIP means TCP running with IP
- UDPIP means UDP running with IP

TCP/UDP Segment has a source and destination port number.
This allows devices to have multiple conversations at the same time.
In AWS when data goes through network devices, filters can be set based on
IP addresses and port numbers.

#### IPv6 - RFC 8200 (2017)

`2001:0db8:28ac:0000:0000:82ae:3910:7334`

The value is hex and there are two octets per spacing or one hextet.
The redundant zeros can be removed to create:

`2001:0db8:28ac:0:0:82ae:3910:7334`

or you can remove them all entirely once per address

`2001:0db8:28ac::82ae:3910:7334`

Each address is 128 bits long. They are addressed by the start of the network
and the prefix.
Since each grouping is 16 values, we can multiple the groups by this to achieve
the prefix.

`2001:0db8:28ac::/48` really means the network starts at
`2001:0db8:28ac:0000:0000:0000:0000:0000` and finishes at
`2001:0db8:28ac:ffff:ffff:ffff:ffff:ffff`

`::/0` represents all IPv6 addresses

### VPC Sizing and Structure

VPC Consideration

- What size should the VPC be. This will limit the use.
- Are there any networks we can't use?
- Be mindful of ranges other VPCs use or are used in other cloud environments
- Try to predict the future uses.
- VPC structure with tiers and resilience (availability) zones
- VPC min /28 network (16 IP)
- VPC max /16 (65456 IP)
- Avoid common range 10.0 or 10.1, include up to 10.10
  - Suggest starting of 10.16 for a nice clean base 2 number.

Reserve 2+ network ranges per region being used per account.
Think of the highest region you will operate in and add extra as a buffer.

An example using 4 AWS accounts.

- Regions with 2 ranges in each Region
  - 3 regions in US
  - 1 region in Europe
  - 1 region in AUS
- Total of 40 ranges, 10 ranges for each account.

#### How to size VPC

A subnet is located in one availability zone.
Try to split each subnet into tiers (web, application, db, spare).
Since each Region has at least 3 AZ's, it is a good practice to start
splitting the network into 4 different AZs.
This allows for at least one subnet in each AZ, and one spare.
Taking a /16 subnet and splitting it 16 ways will make each a /20.

### Custom VPC

- Regional Isolated and Resilient Service.
  - Operates from all AZs in that region
- Allows isolated networks inside AWS.
- Nothing IN or OUT of a VPC without explicit configuration.
  - Isolated blast radius. Any problems are limited to that VPC or anything
  connected to it.
- Flexible configuration
- Hybrid networking to allow connection to other cloud or on-prem networking.
- Default or Dedicated Tenancy. This refers to how the hardware is configured.
  - Default allows on a per resource decision later on.
  - Dedicated locks any resourced created in that VPC to be on dedicated
  hardware which comes at a cost premium.

#### Custom VPC Facts

IPv4 private and public IPs

- Allocated 1 mandatory private IPv4 CIDR blocks
  - Min /28 prefix (16 IP)
  - Max /16 prefix (65,536 IP)
- Can add secondary IPv4 Blocks after creation.
  - Max of 5, can be increased with a support ticket
  - When thinking of VPC, it has a pool of private IPv4 addresses and can
  use public addresses when needed.

Single assigned IPv6 /56 CIDR block

- Still being matured, not everything works the same as IPv4.
- With increasing use of IPv6, this should be added as a default
- Range is either allocated by AWS as in you have no choice on which range
to use, or you can select to use your own IPv6 addresses which you own.
- IPv6 does not have private addresses, they are all routed as public by
default.

#### DNS provided by R53

Available on the base IP address of the VPC + 2.
If the VPC is `10.0.0.0` then the DNS IP will be `10.0.0.2`

Two options that manage how DNS works in a VPC:

- Edit DNS hostnames
  - If true, instances with public IPs in a VPC are given public DNS hostnames.
  - If false, this is not available.

- Edit DNS resolution
  - If true, instances in the VPC can use the DNS IP address.
  - If false, this is not available.

### VPC Subnets

- AZ Resilient subnetwork of a VPC.
  - If the AZ fails, the subnet and services also fail.
  - High availability needs multiple components into different AZs.
- 1 subnet can only have 1 AZ.
- 1 AZ can have zero or many subnets.
- IPv4 CIDR is a subset of the VPC CIDR block.
  - Cannot overlap with any other subnets in that VPC
- Subnet can optionally be allocated IPv6 CIDR block.
  - (256 /64 subnets can fit in the /56 VPC)
- Subnets can communicate with other subnets in the VPC by default.

#### Reserved IP addresses

There are five IP addresses within every VPC subnet that you cannot use.
Whatever size of the subnet, the IP addresses are five less than you expect.

If using `10.16.16.0/20` (`10.16.16.0` - `10.16.31.255`)

- Network address: `10.16.16.0`
- Network + 1: `10.16.16.1` - VPC Router
- Network + 2: `10.16.16.2` - Reserved for DNS
- Network + 3: `10.16.16.3` - Reserved for future AWS use
- Broadcast Address: `10.16.31.255` (Last IP in subnet)

#### DHCP Options Set

This is how computing devices receive IP addresses automatically. There is
one options set applied to a VPC at one time and this configuration flows
through to subnets.

- This can be changed, can create new ones, but you cannot edit one.
- If you want to change the settings
  - You can create a new one
  - Change the VPC allocation to the new one
  - Delete the old one

#### IP allocation Options

- Auto Assign public IPv4 address
  - This will create a public IP address in addition to their private subnet.
  - This is needed to make a subnet public.
- Auto Assign IPv6 address
  - For this to work, the subnet and VPC need an allocation of addresses.

### VPC Routing and Internet Gateway

VPC Router is a highly available device available in every VPC which moves
traffic from somewhere to somewhere else.
Router has a network interface in every subnet in the VPC.
Routes traffic between subnets.

Route tables defines what the VPC router will do with traffic
when data leaves that subnet.
A VPC is created with a main route table. If you don't associate a custom
route table with a subnet, it uses the main route table of the VPC.

If you do associate a custom route table you create with a subnet, then the
main route table is disassociated. A subnet can only have one route table
associated at a time, but a route table can be associated by many subnets.

#### Route Tables

When traffic leaves the subnet that this route table is associated with, the
VPC router reviews the IP packets looking for the destination address.
The traffic will try to match the route against the route table. If there
are more than one routes found as a match, the prefix is used as a priority.
The higher the prefix, the more specific the route, thus higher priority.
If the target says local, that means the destination is in the VPC itself.
Local route can never be updated, they're always present and the local route
always takes priority. This is the exception to the prefix rule.

#### Internet Gateway

A managed service that allows gateway traffic between the VPC and the internet
or AWS Public Zones (S3, SQS, SNS, etc.)

- Regional resilient gateway attached to a VPC.
- One IGW will cover all AZ's in a region the VPC is using.
- A VPC can have either:
  - No IGW and be entirely private.
  - One IGW
- IGW can be created and attached to no VPC.
- Runs from within the AWS public zone.

#### Using IGW

In this example, an EC2 instance has:

- Private IP address of 10.16.16.20
- Public address of 43.250.192.20

The public address is not public and connected to the EC2 instance itself.
Instead, the IGW creates a record that links the instance's private IP
to the public IP. This is why when an EC2 instance is created it only
sees the private IP address. This is IMPORTANT. For IPv4 it is not configured
in the OS with the public address.

When the linux instance wants to communicate with the linux update service,
it makes a packet of data.
The packet has a source address of the EC2 instance and a destination address
of the linux update server. At this point the packet is not configured with
any public addressing and could not reach the linux update server.

The packet arrives at the internet gateway.

The IGW sees this is from the EC2 instance and analyzes the source IP address.
It changes the packet source IP address from the linux EC2 server and puts
on the public IP address that is routed from that instance. The IGW then
pushes that packet on the public internet.

On the return, the inverse happens. As far as it is concerned, it does not know
about the private address and instead uses the instance's public IP address.

If the instance uses an IPv6 address, that public address is good to go. The IGW
does not translate the packet and only pushes it to a gateway.

#### Bastion Host / Jumpbox

It is an instance in a public subnet inside a VPC.
These are used to allow incoming management connections.
Once connected, you can then go on to access internal only VPC resources.
Used as a management point or as an entry point for a private only VPC.

This is an inbound management point. Can be configured to only allow
specific IP addresses or to authenticate with SSH. It can also integrate
with your on premise identification service.

### Network Access Control List (NACL)

Network Access Control Lists (NACLs) are a type of security filter
(like firewalls) which can filter traffic as it enters or leaves a subnet.

All VPCs have a default NACL, this is associated with all subnets of that VPC
by default.
NACLs are used when traffic enters or leaves a subnet.
Since they are attached to a subnet and not a resource, they only filter
data as it crosses in or out.
If two EC2 instances in a VPC communicate, the NACL does nothing because
it is not involved.

NACLs have an inbound and outbound sets of rules.

When a specific rule set has been called, the one with the lowest
rule number first.
As soon as one rule is matched, the processing stops for
that particular piece of traffic.

The action can be for the traffic to **allow** or **deny** the traffic.

Each rule has the following fields related to traffic

- type
- protocol: tcp, udp, or icmp
- port range
- Inbound rule: Source - who traffic is from
- Outbound rule: Destination - who traffic is destined to

Examples:

- ssh: tcp port 22
- http: tcp port 80
- https: tcp port 443
- ping traffic: icmp

If all of those fields match, then the first rule will either allow or deny.

The rule at the bottom with `*` is the **implicit deny**
This cannot be edited and is defaulted on each rule list.
If no other rules match the traffic being evaluated, it will be denied.

#### NACLs example below

- Bob wants to view a blog using https(tcp/443)
- We need a NACL rule to allow TCP on port 443.
- All IP communication has two parts
  - Initiation
  - Response
- Bob is initiating a connection to the server to ask for a webpage
- Server will respond with an **Ephemeral** port
- Bob talks to the webserver connecting to a port on that server (tcp/443)
  - This is a well known port number
- Bob's PC tells the server it can talk to back to Bob on a specific port
  - Wide range from port 1024, 65535
  - That response is outbound traffic
- When using NACLs, you must add an outbound port for the response traffic
as well as the inbound port. This is the ephemeral port.
- If the webserver is not managing the apps server, it may communicate
back on a different port.
- This back and forth communication can be hard to configure for.

#### NACL Exam PowerUp

- NACLs are stateless
  - Initiation and response traffic are separate streams requiring two rules.
- NACLs are attached to subnets and only filter data as it crosses the
subnet boundary. Two EC2 instances in the same subnet will not check against
the NACLs when moving data.
- Can explicitly allow and deny traffic. If you need to block one particular
thing, you need to use NACLs.
- They only see IPs, ports, protocols, and other network connections.
No logical resources can be changed with them.
- NACLs cannot be assigned to specific AWS resources.
- NACLs can be used with security groups to add explicit deny (Bad IPs/nets)
- One subnet can only be assigned to one NACL at a time.

NACLs are processed in order starting at the lowest rule number until
it gets to the catch all. A rule with a lower rule number will be processed
before another rule with a higher rule number.

### Security Groups

- SGs are boundaries which can filter traffic.
- Attached to a resource and not a subnet.
- SGs have two sets of rules like NACLs.
- SGs are stateful.
  - Only one inbound rule is needed.
  - They see traffic and response as the same thing.
- Understand AWS logical resources so they're not limit to IP traffic only.
  - Can have a source and destination referencing the instance and not the IP.
- Default SG is created in a VPC to allow all traffic.
  - Does so by referencing itself. Anything this SG is attached to is matched
  by this rule.
- SGs have a hidden implicit **Deny**.
  - Anything that is not allowed in the rule set for the SG is implicitly denied.
- SG cannot explicit deny anything.
  - NACLs are used in conjunction with SGs to do explicit denys.

#### SGs vs NACL

- NACLs are used when products cannot use SGs, e.g. NAT Gateways.
- NACLs are used when adding explicit deny, such as bad IPs or bad actors.
- SGs is the default almost everywhere because they are stateful.
- NACLs are associated with a subnet and only filter traffic that crosses
that boundary. If the resource is in the same subnet, it will not do anything.

### Network Address Translation (NAT) Gateway

Set of different processes that can address IP packets by changing
their source or destination addresses.

**IP masquerading**, hides CIDR block behind one IP. This allows many IPv4
addresses to use one public IP for **outgoing** internet access.
Incoming connections don't work. Outgoing connections can get a response
returned.

- Must run from a public subnet to allow for public IP address.
  - Internet Gateway subnets configure to allocate public IPv4 addresses
  and default routes for those subnets pointing at the IGW.
- Uses Elastic IPs (Static IPv4 Public)
  - Don't change
  - Allocated to your account
- AZ resilient service , but HA in that AZ.
  - If that AZ fails, there is no recovery.
- For a fully region resilient service, you must deploy one NATGW in each AZ
with a Route Table in each AZ with NATGW as target.
- Managed service, scales up to 45 Gbps. Can deploy multiple NATGW to increase
bandwidth.
- AWS charges on usage per hour and data volume processed.

NATGW cannot do port forwarding or be a bastion server. In that case it might
be necessary to run a NAT EC2 instance instead.

---

## Elastic-Cloud-Compute-EC2

EC2 provides Infrastructure as a Service (IaaS Product)

### Virtualization 101

Servers are configured in three sections without virtualization.

- CPU hardware
- Kernel
  - Operating system
  - Runs in **privileged mode** and can interact with the hardware directly.
- User Mode
  - Runs applications.
  - Can make a **system call** to the Kernel to interact with the hardware.
  - If an app tries to interact with the hardware without a system call, it
  will cause a system error and can crash the server or at minimum the app.

#### Emulated Virtualization - Software Virtualization

Host OS operated on the HW and included a hypervisor (HV).
SW ran in privileged mode and had full access to the HW.
Guest OS wrapped in a VM and had devices mapped into their OS to emulate real
HW. Drivers such as graphics cards were all SW emulated to allow the process
to run properly.

The guest OS still believed they were running on real HW and tried
to take control of the HW. The areas were not real and only allocated
space to them for the moment.

The HV performs **binary translation**.
System calls are intercepted and translated in SW on the way. The guest OS needs
no modification, but slows down a lot.

#### Para-Virtualization

Guest OS are modified and run in HV containers, except they do not use slow
binary translation. The OS is modified to change the **system calls** to
**user calls**. Instead of calling on the HW, they call on the HV using
**hypercalls**. Areas of the OS call the HV instead of the HW.

#### Hardware Assisted Virtualization

The physical HW itself is virtualization aware. The CPU has specific
functions so the HV can come in and support. When guest OS tries to run
privileged instructions, they are trapped by the CPU and do not halt
the process. They are redirected to the HV from the HW.

What matters for a VM is the input and output operations such
as network transfer and disk IO. The problem is multiple OS try to access
the same piece of hardware but they get caught up on sharing.

#### SR-IOV (Singe Route IO virtualization)

Allows a network or any card to present itself as many mini cards.
As far as the HW is concerned, they are real dedicated cards for their
use. No translation needs to be done by the HV. The physical card
handles it all. In EC2 this feature is called **enhanced networking**.

### EC2 Architecture and Resilience

- EC2 instances are virtual machines run on EC2 hosts.
- Shared hosts: Every customer is isolated even on the same shared hardware.
- Dedicated hosts: Pay for entire host, don't pay for instances.
- AZ resilient service. They run within only one AZ system.
  - You can't access them cross zone.

 EC2 host contains

- Local hardware such as CPU and memory
- Also have temporary instance store
  - If instance moves hosts, the storage is lost.
- Can use remote storage, Elastic Block Store (EBS).
  - EBS allows you to allocate volumes of persistent storage to instances
within the same AZ.
- 2 types of networking
  - Storage networking
  - Data networking

EC2 Networking (ENI)

When instances are provisioned within a specific subnet within a VPC
A primary elastic network interface is provisioned in a subnet which
maps to the physical hardware on the EC2 host. Subnets are also within
one specific AZ. Instances can have multiple network interfaces, even within
different subnets so long as they're within the same AZ.

An instance runs on a specific host. If you restart the instance
it will stay on that host until either:

- The host fails or is taken down by AWS
- The instance is stopped and then started, different than restarted.

The instance will be relocated to another host in the same AZ. Instances
cannot move to different AZs. Everything about their hardware is locked within
one specific AZ.
A migration is taking a **copy** of an instance and moving it to a different AZ.

In general instances of the same type and generation will occupy the same host.
The only difference will generally be their size.

#### EC2 Strengths

Long running compute needs. Many other AWS services have run time limits.

Server style applications

- things waiting for network response
- burst or stead-load
- monolithic application stack
  - middle ware or specific run time components
- migrating application workloads or disaster recovery
  - existing applications running on a server and a backup system to intervene

### EC2 Instance Types

General Purpose - default steady state workloads with even resources
Compute Optimized - Media processing, scientific modeling and gaming
Memory Optimized - Processing large in-memory data sets
Accelerated Computing - Hardware GPU, FPGAs
Storage Optimized - Large amounts of super fast local storage. Massive amounts
of IO per second. Elastic search and analytic workloads.

#### Naming Scheme

R5dn.8xlarge - whole thing is the instance type. When in doubt give the
full instance type

- 1st char: Instance family.
- 2nd char: Instance generation. Generally always select the newest generation.
- char after period: Instance size. Memory and CPU considerations.
  - Often easier to scale system with a larger number of smaller instance sizes.
- 3rd char - before period: additional capabilities
  - a: amd cpu
  - d: NVMe storage
  - n: network optimized
  - e: extra capacity for ram or storage

### Storage Refresher

- **Instance Store**
  - Direct (local) attached storage
  - Super fast
  - Ephemeral storage or temporary storage
- **Elastic Block Store (EBS)**
  - Network attached storage
  - Volumes delivered over the network
  - Persistent storage lives on past the lifetime of the instance

#### Three types of storage

- Block Storage: Volume presented to the OS as a collection of blocks. No
structure beyond that. These are mountable and bootable. The OS will
create a file system on top of this, NTFS or EXT3 and then it mounts
it as a drive or a root volume on Linux. Spinning hard disks or SSD. This
could also be delivered by a physical volume. Has no built in structure.
You can mount an EBS volume or boot off an EBS volume.

- File Storage: Presented as a file share with a structure. You access the
files by traversing the storage. You cannot boot from storage, but you
can mount it.

- Object Storage: It is a flat collection of objects. An object can be anything
with or without attached metadata. To retrieve the object, you need to provide
the key and then the value will be returned. This is not mountable or
bootable. It scales very well and can have simultaneous access.

#### Storage Performance

- IO Block Size: Determines how to split up the data.
- IOPS: How many reads or writes a system can accommodate per second.
- Throughput: End rate achieved, expressed in MB/s (megabyte per second).

`Block Size * IOPS = Throughput`

This isn't the only part of the chain, but it is a simplification.
A system might have a throughput cap. The IOPS might decrease as the block
size increases.

### Elastic Block Store (EBS)

- Allocate block storage **volumes** to instances.
- Volumes are isolated to one AZ.
  - The data is highly available and resilient for that AZ.
  - All of the data is replicated within that AZ. The entire AZ must have
  a major fault to go down.
- Two physical storage types available (SSD/HDD)
- Varying level of performance (IOPS, T-put)
- Billed as GB/month.
  - If you provision a 1TB for an entire month, you're billed as such.
  - If you have half of the data, you are billed for half of the month.
- Four types of volumes, each with a dominant performance attribute.
  - General purpose SSD (gp2)
  - Provisioned IOPS SSD (io1)
    - maximum IOPS such as databases
  - T-put optimized HDD (st1)
    - maximum t-put for logs or media storage
  - Cold HDD (sc1)

#### General Purpose SSD (gp2)

Uses a performance bucket architecture based on the IOPS it can deliver.
The GP2 starts with 5,400,000 IOPS allocated. It is all available instantly.

You can consume the capacity quickly or slowly over the life of the volume.
The capacity is filled back based upon the volume size.
Min of 100 IOPS added back to the bucket per second.

Above that, there are 3 IOPS/GiB of volume size. The max is 16,000 IOPS.
This is the **baseline performance**

Default for boot volumes and should be the default for data volumes.
Can only be attached to one EC2 instance at a time.

#### Provisioned IOPS SSD (io1)

You pay for capacity and the IOPs set on the volume.
This is good if your volume size is small but need a lot of IOPS.

50:1 IOPS to GiB Ratio
64,000 is the max IOPS per volume assuming 16 KiB I/O.

Good for latency sensitive workloads such as mongoDB.
Multi-attach allows them to attach to multiple EC2 instances at once.

#### HDD Volume Types

- great value
- great for high throughput vs IOPs
- 500 GiB - 16 TiB
- Neither can be used for EC2 boot volumes.
- Good for streaming data on a hard disk.
  - Media conversion with large amounts of storage.
- Frequently accessed high throughput intensive workload
  - log processing
  - data warehouses
- The access patterns should be sequential
  - Massive inefficiency for small reads and writes

Two types

- st1
  - Starts at 1 TiB of credit per TiB of volume size.
  - 40 MB/s baseline per TiB
  - Burst of 250 MB/s per TiB
  - Max t-put of 500 MB/s
- sc1
  - Designed for less frequently accessed data, it fills slower.
  - 12 MB/s baseline per TiB
  - Burst of 80 MB/s per TiB
  - Max t-put of 250 MB/s

#### EBS Exam Power Up

- Volumes are created in an AZ, isolated in that AZ.
- If an AZ fails, the volume is impacted.
- Highly available and resilient in that AZ. The only reason for failure is
if the whole AZ fails.
- Generally one volume to one instance, except **io1** with multi-attach
- Has a GB/m fee regardless of instance state.
- EBS maxes at 80k IOPS per instance and 64k vol (io1)
- Max 2375 MB/s per instance, 1000 MiB/s (vol) (io1)

### EC2 Instance Store

- Local **block storage** attached to an instance.
- Physically connected to one EC2 host.
  - They are isolated to that one specific host.
  - Instances on that host can access them.
- Highest storage performance in AWS.
- Included in instance price, use it or lose it.
- Can be attached ONLY at launch. Cannot be attached later.

Each instance has a collection of volumes that are
locked to that specific host. If the instance moves, the data doesn't.

Instances can move between hosts for many reasons:

- If an instance is stopped and started, that migrates hosts.
- If a host undergoes AWS maintenance, it will be wiped.
- If you change the type of an instance, these will be lost.
- If a physical hardware fails, then the data is gone.

The number, size, and performance of instance store volumes vary based on the
type of instance used. Some instances do not have any instance store volumes
at all.

#### Instance Store Exam PowerUp

- Instance store volumes are local to EC2 host.
- Can only be added at launch time. Cannot be added later.
- Any data on instance store data is lost if it gets moved, or resized.
- Highest data performance in all of AWS.
- You pay for it anyway, it's included in the price.
- TEMPORARY

### EBS vs Instance Store

If the read/write can be handled by EBS, that should be default.

When to use EBS

- Highly available and reliable in an AZ. Can self correct against HW issues.
- Persist independently from EC2 instances.
  - Can be removed or reattached.
  - You can terminated instance and keep the data.
- Multi-attach feature of **io1**
  - Can create a multi shared volume.
- Region resilient backups.
- Require up to 64,000 IOPS and 1,000 MiB/s per volume
- Require up to 80,000 IOPS and 2,375 MB/s per instance

When to use Instance Store

- Great value, they're included in the cost of an instance.
- More than 80,000 IOPS and 2,375 MB/s
- If you need temporary storage, or can handle volatility.
- Stateless services, where the server holds nothing of value.
- Rigid lifecycle link between storage and the instance.
  - This ensures the data is erased when the instance goes down.

### EBS Snapshots, restore, and fast snapshot restore

- Efficient way to backup EBS volumes to S3.
  - The data becomes region resilient.
- Can be used to migrate data between hosts.

Snapshots are incremental volume copies to S3.
The first is a **full copy** of `data` on the volume. This can take some time.
EBS won't be impacted, but will take time in the background.
Future snaps are incremental, consume less space and are quicker to perform.

If you delete an incremental snapshot, it moves data to ensure subsequent
snapshots will work properly.

Volumes can be created (restored) from snapshots.
Snapshots can be used to move EBS volumes between AZs.
Snapshots can be used to migrate data between volumes.

#### Snapshot and volume performance

- When creating a new EBS volume without a snapshot, the performance is
available immediately.
- When restoring from S3, performs **Lazy Restore**
  - If you restore a volume, it will transfer it slowly in the background.
  - If you attempt to read data that hasn't been restored yet, it will
  immediately pull it from S3, but this will achieve lower levels of performance
  than reading from EBS directly.
  - You can force a read of every block all data immediately using DD.

Fast Snapshot Restore (FSR) allows for immediate restoration.
You can create 50 of these FSRs per region. When you enable it on
a snapshot, you pick the snapshot specifically and the AZ that you want to be
able to do instant restores to. Each combination of Snapshot and AZ counts
as one FSR set. You can have 50 FSR sets per region.
FSR is not free and can get expensive with lost of different snapshots.

#### Snapshot Consumption and Billing

Billed using a GB/month metric.
20 GB stored for half a month, represents 10 GB-month.

This is used data, not allocated data. If you have a 40 GB volume but only
use 10 GB, you will only be charged for the allocated data.
This is not how EBS itself works.

The data is incrementally stored which means doing a snapshot every 5 minutes
will not necessarily increase the charge as opposed to doing one every hour.

#### EBS Encryption

Provides at rest encryption for block volumes and snapshots.

When you don't have EBS encryption, the volume is not encrypted.
The physical hardware itself may be performing at rest encryption, but
that is a separate thing.

When you set up an EBS volume initially, EBS uses KMS and a customer master key.
This can be the EBS default (CMK) which is referred to as `aws/ebs` or it
could be a customer managed CMK which you manage yourself.

That key is used by EBS when an encrypted volume is created. The CMK
generates an encrypted data encryption key which is stored on the volume with
the physical disk. This key can only be encrypted by KMS when a role with
the proper permissions makes the request.

When the volume is first used, EBS asks CMS to decrypt the key and stores
the decrypted key in memory on the EC2 host while it's being used. At all
other times it's stored on the volume in encrypted form.

When the EC2 instance is using the encrypted volume, it can use the
decrypted data encryption key to move data on and off the volume. It is used
for all cryptographic operations when data is being used to and from the
volume.

When data is stored at rest, it is stored as ciphertext.

If the EBS volume is ever moved, the key is discarded.

If a snapshot is made of an encrypted EBS volume, the same data encryption
key is used for that snapshot. Anything made from this snapshot is also
encrypted in the same way.

Every time you create a new EBS volume from scratch, it creates a new
data encryption key.

##### EBS Encryption Exam Power Up

- AWS accounts can be set to encrypt EBS volumes by default.
  - It will use the default CMK unless a different one is chosen.
  - Each volume uses 1 unique DEK (data encryption key)
  - Snapshots and future volume use the same DEK
- Can't change a volume to NOT be encrypted.
  - You could mount an unencrypted volume and copy things over but you can't
  change the original volume.
- The OS itself isn't aware of the encryption, there is no performance loss.
  - The volume itself is encrypted using AES256
  - This occurs between the EC2 host and the EBS system itself.
  - The OS does not see any encryption. It simply writes data out and reads
  data in from a disk.
  - If an exam question does not use AES256, or it suggests you need an OS to
encrypt or hold the keys, then you need to perform full disk encryption
at the operating system level.

### EC2 Network Interfaces, Instance IPs and DNS

An EC2 instance starts with at least one ENI - elastic network interface.
An instance may have ENIs in separate subnets, but everything must be
within one AZ.

When you launch an instance with Security Groups, they are on the
network interface and not the instance.

#### Elastic Network Interface (ENI)

Has these properties

- MAC address
- Primary IPv4 private address
  - From the range of the subnet the ENI is within.
  - Will be static and not change for the lifetime of the instance
    - `10.16.0.10`
  - Given a DNS name that is associated with the address.
    - `ip-10-16-0-10.ec2.internal`
    - Only resolvable inside the VPC and always points at private IP address
- 0 or more secondary private IP addresses
- 0 or 1 public IPv4 address
  - The instance must manually be set to receive an IPv4 address or spun into a
subnet which automatically allocates an IPv4.
This is a dynamic IP that is not fixed.
If you stop an instance the address is removed.
When you start up again, it is given a brand new IPv4 address.
Restarting the instance will not change the IP address.
Changing between EC2 hosts will change the address.
This will be allocated a public DNS name. The Public DNS name will resolve to
the primary private IPv4 address of the instance.
Outside of the VPC, the DNS will resolve to the public IP address.
This allows one single DNS name for an instance, and allows traffic to resolve
to an internal address inside the VPC and the public will resolve to a public
IP address.
- 1 elastic IP per private IPv4 address
  - Can have 1 public elastic interface per private IP address on this interface.
This is allocated to your AWS account.
Can associate with a private IP on the primary interface or secondary interface.
If you are using a public IPv4 and assign an elastic IP, the original IPv4
address will be lost. There is no way to recover the original address.
- 0 or more IPv6 address on the interface
  - These are by default public addresses.
- Security groups
  - Applied to network interfaces.
  - Will impact all IP addresses on that interface.
  - If you need different IP addresses impacted by different security
  groups, then you need to make multiple interfaces and apply different
  security groups to those interfaces.
- Source / destination checks
  - If traffic is on the interface, it will be discarded if it is not
  from going to or coming from one of the IP addresses

Secondary interfaces function in all the same ways as primary interfaces except
you can detach interfaces and move them to other EC2 instances.

#### ENI Exam PowerUp

- Legacy software is licensed using a mac address.
  - If you provision a secondary ENI to a specific license, you can move
around the license to different EC2 instances.
- Multi homed (subnets) management and data.
- Different security groups are attached to different interfaces.
- The OS doesn't see the IPv4 public address.
- You always configure the private IPv4 private address on the interface.
- Never configure an OS with a public IPv4 address.
- IPv4 Public IPs are Dynamic, starting and stopping will kill it.

Public DNS for a given instance will resolve to the primary private IP
address in a VPC. If you have instance to instance communication within
the VPC, it will never leave the VPC. It does not need to touch the internet
gateway.

### Amazon Machine Image (AMI)

Images of EC2 instances that can launch more EC2 instance.

- When you launch an EC2 instance, you are using an Amazon provided AMI.
- Can be Amazon or community provided
- Marketplace (can include commercial software)
  - Will charge you for the instance cost and an extra cost for the AMI
- AMIs are regional with a unique ID.
- Controls permissions
  - Default only your account can use it.
  - Can be set to be public.
  - Can have specific AWS accounts on the AMI.
- Can create an AMI from an existing EC2 instance to capture the current config.

#### AMI Lifecycle

1. Launch: EBS volumes are attached to EC2 devices using block IDs.

   - BOOT /dev/xvda
   - DATA /dev/xvdf

2. Configure: customize the instance from applications or volume sizes.

3. Create Image or AMI

    - AMI contains:
      - Permissions: who can use it, is it public or private
      - EBS snapshots are created from attached EBS volumes
        - Snapshots are referenced inside the AMI using block device mapping.
        - Table of data that links the snapshot IDs that you've just
        created when making that AMI and it has for each one of those
        snapshots, a device ID that the original volumes had on the EC2
        instance.

4. Launch: When launching an instance, the snapshots are used to create new EBS
volumes in the AZ of the EC2 instance and contain the same block device mapping.

#### AMI Exam PowerUps

- AMI can only be used in one region
- AMI Baking: creating an AMI from a configuration instance.
- An AMI cannot be edited. If you need to update an AMI, launch an instance,
make changes, then make new AMI
- Can be copied between regions
- Remember permissions by default are your account only
- Billing is for the storage capacity for the EBS snapshots the AMI references.

### EC2 Pricing Models

#### On-Demand Instances

- Hourly rate based on OS, size, options, etc
- Billed in seconds (60s min) or hourly
  - Depends on the OS
- Default pricing model
- No long-term commitments or upfront payments
- New or uncertain application requirements
- Short-term, spiky, or unpredictable workloads which can't tolerate disruption.

#### Spot Instances

Up to 90% off on-demand, but depends on the spare capacity.
You can set a maximum hourly rate in a certain AZ in a certain region.
If the max price you set is above the spot price, you pay only that spot
price for the duration that you consume that instance.
As the spot price increases, you pay more.
Once this price increases past your maximum, it will terminate the instance.
Great for data analytics when the process can occur later at a lower use time.

#### Reserved Instance

Up to 75% off on-demand.
The trade off is commitment.
You're buying capacity in advance for 1 or 3 years.
Flexibility on how to pay

- All up front
- Partial upfront
- No upfront

Best discounts are for 3 years all up front.
Reserved in region, or AZ with capacity reservation.
Reserved instances takes priority for AZ capacity.
Can perform scheduled reservation when you can commit to specific time windows.

Great if you have a known stead state usage, email usage, domain server.
Cheapest option with no tolerance for disruption.

### Instance Status Checks and Autorecovery

Every instance has two high level status checks

- System Status Checks
  - Failure of this check could indicate SW or HW problems of the EC2
service or the host.
- Instance Status Checks
  - Specific to the file system or has a corrupted Kernel.

Autorecovery can kick in and help,

- Recover this instance
  - can be a number of steps depending on the failure
- Stop this instance
- Terminate this instance
  - useful in a cluster
- Reboot this instance

### Horizontal and Vertical Scaling

#### Vertical Scaling

As customer load increases, the server may need to grow to handle more data.
The server can increase in capacity, but this will require a reboot.

- Often times vertical scaling can only occur during planned outages.
- Larger instances also carry a $ premium compared to smaller instances.
- Instance size is an upper cap on performance.
- No application modification is needed.
  - Works for all applications, even monoliths (all code in one app)

#### Horizontal Scaling

As the customer load increases, this adds additional capacity.
Instead of one running copy of an application, you can have multiple versions
running on each server.
This requires a load balancer.
When customers try to access an application, the load balancer ensures the
servers get equal parts of the load.

- Sessions are everything.
- With horizontal scaling you can shift between instances equally.
- This requires either application support or off-host sessions.
- Servers are **stateless**, the app stores session data elsewhere.
- No disruption while scaling up or down.
- No real limits to scaling.
- Uses smaller instances so you pay less, allows for better granularity.

### Instance Metadata

EC2 service provides data to instances
Accessible inside all instances

Memorize `http://169.254.169.254/latest/meta-data/`

Meta-data contains information on the environment the instance is in.
You can find out about the networking or user-data among other things.
This is not authenticated or encrypted. Anyone who can gain access to the
instance can see the meta-data. This can be restricted by local firewall

---

## Containers-and-ECS

### Intro to Containers

Virtualization Problems

Using an EC2 virtual machine with Nitro Hypervisor, 4 GB ram, and 40 GB disk,
the OS can consume 60-70% of the disk and much of the available memory.
Containers leverage the similarities of multiple guest OS by removing duplicate
resources. This allows applications to run in their own isolated environments.

#### Image Anatomy

A Docker image is composed of multiple layers and not a monolithic disk image.
Each line of a Docker image creates a new filesystem layer on top of the
previous. Images are created from scratch or a base image.
Images contain read only layers, images are layer onto images.

Docker container is the same as a Docker image, except it
has an additional READ/WRITE layer of the container.

If you have lots of containers with very similar base
structures, they will share the parts that overlap.
The other layers are reused between containers.

#### Container Registry

Registry or hub of container images.
Dockerfile can create a container image where it gets stored
in the container registry.

Docker hosts can run many containers based on one or more images.
A single image can generate Containers on many different Docker hosts.

#### Container Key Concepts

- Docker files are used to build Docker images
- Containers are portable and always run as expected.
  - Anywhere there is a compatible host, it will run exactly as you intended.
- Containers are lightweight, use the host OS for the heavy lifting.
  - File system layers are shared when possible.
- Containers only run the application and environment it needs to run.
- Ports need to be **exposed** to allow outside access from the host and beyond.
- Application stacks can be multi container

### Elastic Container Service (ECS) Concepts

- Accepts containers and instructions you provide.
- ECS allows you to create a cluster.
  - Clusters are where containers run from.
- Container images will be located on a registry.
  - AWS provides ECR (elastic container registry)
  - Dockerhub can be used as well.
- **Container definition** gives ECS just enough info about the single container.
  - A pointer to which image to use and the ports that will be exposed.
- **Task definitions** store the resources used by the task.
  - Stores **task role**, an IAM role that allows the task access to other
  AWS resources.
- Task is not by itself highly available.

ECS **Service** is configured via Service Definition and represents
how many copies of a task you want to run for scaling and HA.

### ECS Cluster Types

ECS Cluster manages:

- Scheduling and Orchestration
- Cluster manager
- Placement engine

#### EC2 mode

ECS cluster is created within a VPC. It benefits from the multiple AZs that
are within that VPC.
You specify an initial size which will drive an **auto scaling group**.

ECS using EC2 mode is not a serverless solution, you need to worry about
capacity for your cluster.

The container instances are not delivered as a managed service, they
are managed as normal EC2 instances.
You can use spot pricing or prepaid EC2 servers.

#### Fargate mode

Removes more of the management overhead from ECS, no need to manage EC2.

**Fargate shared infrastructure** allows all customers
to access from the same pool of resources.

Fargate deployment still uses a cluster with a VPC where AZs are specified.

For ECS tasks, they are injected into the VPC. Each task is given an
elastic network interface which has an IP address within the VPC. They then
run like a VPC resource.

You only pay for the container resources you use.

#### EC2 vs ECS(EC2) vs Fargate

If you already are using containers, use **ECS**.

**EC2 mode** is good for a large workload if you are price conscious.
This allows for spot pricing and prepayment.

**Fargate** is great if you,

- Have a large workload but are overhead conscious.
- Have small or burst style workloads.
- Use batch or periodic workloads.

---

## Advanced-EC2

### Bootstrapping EC2 using User Data

Bootstrapping is a process where scripts or other config steps can be run when
an instance is first launched. This allows an instance to be brought to service
in a particular configured state.

In systems automation, bootstrapping allows the system to self configure.
In AWS this is **EC2 Build Automation**.

This could perform some software installs and post install configs.

Bootstrapping is done using **user data** and is injected into the instance
in the same way that meta-data is. It is accessed using the meta-data IP.

<http://169.254.169.254/latest/>

Anything you pass in is executed by the instance OS only once on launch!

EC2 doesn't validate the user data. You can tell EC2 to pass in trash data
and the data will be injected. The OS needs to understand the user data.

#### Bootstrapping Architecture

An AMI is used to launch an EC2 instance in the usual way to create
an EBS volume that is attached to the EC2 instance. This is based on the
block mapping inside the AMI.

Now the EC2 service provides some user data through to the EC2 instance.
There is SW within the OS designed to look at the metadata IP for any user data.
If it sees any user data, it executes this on launch of that instance.

This is treated like any other script the OS runs. At the end of running
the script, the instance will be in:

- Running state and ready for service.
- Bad config but still likely running.
  - The instance will probably still pass its checks.
  - It will not be configured as you expected.

#### User Data Key Points

EC2 doesn't know what the user data contains, it's just a block of data.
The user data is not secure, anyone can see what gets passed in. For this
reason it is important not to pass passwords or long term credentials.

User data is limited to 16 KB in size. Anything larger than this will
need to pass a script to download the larger set of data.

User data can be modified if you stop the instance, change the user
data, then restart the instance. This won't be executed since the instance
has already started.

#### Boot-Time-To-Service-Time

How quickly after you launch an instance is it ready for service?
This includes the time for EC2 to configure the instance and any software
downloads that are needed for the user.
When looking at an AMI, this can be measured in minutes.

AMI baking will front load the time needed by configuring as much as possible.

### AWS::CloudFormation::Init

**cfn-init** is a helper script installed on EC2 OS.
This is a simple configuration management system.

- User Data is procedural and run by the OS line by line.
- cfn-init can be procedural, but can also be desired state.
  - Can specify particular versions of packages. It will ensure things are
  configured to that end state.
  - Can manipulate OS groups and users.
  - Can download sources and extract them using authentication.

This is executed as any other command by being passed into the instance as part
of the user data and retrieves its directives from the CloudFormation
stack and you define this data in the CloudFormation template called
`AWS::CloudFormation::Init`.

#### cfn-init explained

Starts off with a **CloudFormation template**.
This has a logical resource within it which is to create an EC2 instance.
This has a specific section called `Metadata`.
This then passes in the information passed in as `UserData`.
cfn-init gets variables passed into the user data by CloudFormation.

It knows the desired state and can work towards a final configuration.
This can monitor the user data and change things as the EC2 data changes.

#### CreationPolicy and Signals

If you pass in user data, there is no way for CloudFormation to know
if the EC2 instance was provisioned properly. It may be marked as complete,
but the instance could be broken.

A **CreationPolicy** is something which is added to a logical resource
inside a CloudFormation template. You create it and supply a timeout value.

This waits for a signal from the resource itself before moving to a create
complete state.

### EC2 Instance Roles

IAM roles are the best practice ways for services to be granted permissions.
EC2 instance roles are roles that an instance can assume and anything
running in that instance has the permissions that role grants.

Starts with an IAM role with a permissions policy.
EC2 instance role allows the EC2 service to assume that role.

The **instance profile** is the item that allows the permissions to get
inside the instance. When you create an instance role in the console,
an instance profile is created with the same name.

When IAM roles are assumed, you are provided temporary roles based on the
permission assigned to that role. These credentials are passed through
instance **meta-data**.

EC2 and the secure token service ensure the credentials never expire.

Key facts

- Credentials are inside meta-data
  - iam/security-credentials/role-name
  - automatically rotated - always valid
  - Resources need to check the meta-data periodically
- Should always use roles compared to storing long term credentials
- CLI tools use role credentials automatically

### AWS System Manager Parameter Store

Passing secrets into an EC2 instance is bad practice because anyone
who has access to the meta-data has access to the secrets.

Parameter store allows for storage of **configuration** and **secrets**

- Strings
- StringList
- SecureString

Parameter Store:

- Can store license codes, database strings, and full configs and passwords.
- Allows for hierarchies and versioning.
- Can store plaintext and ciphertext.
  - This integrates with **kms** to encrypt passwords.
- Allows for public parameters such as the latest AMI parameter to be stored
and referenced for EC2 creating.
- Is a public service so any services needs access to the public sphere or
to be an AWS public service.
- Applications, EC2 instances, lambda functions can all request access to
parameter store.
- Tied closely to IAM, can use
  - Long term credentials such as access keys.
  - Short term use of IAM roles.

### System and Application Logging on EC2

CloudWatch and CloudWatch Logs cannot natively capture data inside an instance.

CloudWatch Agent is required for OS visible data. It sends this data into CW
For CW to function, it needs configuration and permissions in addition
to having the CW agent installed.
The CW agent needs to know what information to inject into CW and CW Logs.

The agent also needs some permissions to interact with AWS.
This is done with an IAM role as best practice.
The IAM role has permissions to interact with CW logs.
The IAM role is attached to the instance which provides the instance and
anything running on the instance, permissions to manage CW logs.

The data requested is then injected in CW logs.
There is one log group for each individual log we want to capture.
There is one log stream for each group for each instance that needs
management.

We can use parameter store to store the configuration for the CW agent.

### EC2 Placement Groups

#### Cluster Placement

Pack instances close together

Achieves the highest level of performance available with EC2.

Best practice is to launch all of the instances within that group at the
same time.
If you launch with 9 instances and AWS places you in a place with capacity
for 12, you are now limited in how many you can add.

Cluster placements need to be part of the same AZ. Cluster
placement groups are generally the same rack, but they can even be the same
EC2 host.

All members have direct connections to each other. They can achieve
**10 Gbps single stream** vs 5 Gbps normally. They also have the lowest
latency and max PPS possible in AWS.

If the hardware fails, the entire cluster will fail.

##### Cluster Placement Exam PowerUp

- Clusters can't span AZs. The first AZ used will lock down the cluster.
- They can span VPC peers.
- Requires a supported instance type.
- Best practice to use the same type of instance and launch all at once.
- This is the only way to achieve **10Gbps SINGLE stream**, other data metrics
assume multiple streams.

#### Spread Placement

Keep instances separated

This provides the best resilience and availability.
Spread groups can span multiple AZs. Information will be put on distinct
racks with their own network or power supply. There is a limit of 7 instances
per AZ. The more AZs in a region, the more instances inside a spread placement
group.

##### Spread Placement Exam PowerUp

- Provides the highest level of availability and resilience.
  - Each instance by default runs from a different rack.
- 7 instances per AZ is a hard limit.
- Not supported for dedicated instances or hosts.

Use case: small number of critical instances that need to be kept separated
from each other. Several mirrors of an application

#### Partition Placement

Groups of instances spread apart

If a problem occurs with one rack's networking or power, it will
at most take out one instance.

The main difference is you can launch as many instances in each partition
as you desire.

When you launch a partition group, you can allow AWS decide or you can
specifically decide.

##### Partition Placement Exam PowerUp

- 7 partitions maximum for each AZ
- Instances can be placed into a specific partition, or AWS can pick.
- This is not supported on dedicated hosts.
- Great for HDFS, HBase, and Cassandra

### EC2 Dedicated Hosts

EC2 host allocated to you in its entirety.
Pay for the host itself which is designed for a family of instances.
There are no instance charges.
You can pay for a host on-demand or reservation with 1 or 3 year terms.

The host hardware has physical sockets and cores. This dictates how
many instances can be run on the HW.

Hosts are designed for a specific size and family. If you purchase one host, you
configure what type of instances you want to run on it. With the older VM
system you cannot mix and match. The new Nitro system allows for mixing and
matching host size.

#### Dedicated Hosts Limitations

- AMI Limits, some versions can't be used
- Amazon RDS instances are not supported
- Placement groups are not supported for dedicated hosts.
- Hosts can be shared with other organization accounts using **RAM**
- This is mostly used for licensing problems related to ports.

### Enhanced Networking

Enhanced networking uses SR-IOV.
The physical network interface is aware of the virtualization.
Each instance is given exclusive access to one part of a physical network
interface card.

There is no charge for this and is available on most EC2 types.
It allows for higher IO and lower host CPU usage
This provides more bandwidth and higher packet per seconds.
In general this provides lower latency.

#### EBS Optimized

Historically network on EC2 was shared with the same network stack used
for both data networking and EBS storage networking.

EBS optimized instance means that some stack optimizations have taken place
and dedicated capacity has been provided for that instance for EBS usage.

Most new instances support this and have this enabled by default for no charge.

---

## Route-53

### Public Hosted Zones

A hosted zone is a DNS database for a given section of global DNS data.
A public hosted zone is a type of R53 hosted zone which is hosted on
R53 provided public DNS name servers. When creating a hosted zone, AWS provides
at least 4 DNS name servers which host the zone.

This is globally resilient service due to multiple DNS servers.

Hosted zones are created automatically when you register a domain using R53.

Hosted zones can be created separately. If you want to register a domain
elsewhere and use R53 to host the zone file and records for that domain, then
you can specifically create a hosted zone and point at an externally
registered domain at that zone.
There is a monthly fee to host each hosted zone within R53 and a fee for
any queries made to that service.

Hosted Zones are what the DNS system references via delegation and name server
records. A hosted zone, when referenced in this way by the DNS system, is known
as being authoritative for a domain.
It becomes the single source of truth for a domain.

### Route 53 Health Checks

Route checks will allow for periodic health checks on the servers.
If one of the servers has a bug, this will be removed from the list.

If the bug gets fixed, the health check will pass and the server will be
added back into a healthy state.

Health checks are separate from, but are used by records inside R53.
You don't create health checks inside records themselves.

These are performed by a fleet of global health checkers. If you think
they are bots and block them, this could cause alarms.

Checks occur every 30 seconds by default. This can be increased to 10 seconds
for additional costs. These checks are per health checker. Since there are many
you will automatically get one every few seconds. The 10 second option will
complete multiple checks per second.

There could be one of three checks

- TCP checks: R53 tries to establish TCP with end point within 10 seconds.
- HTTP/HTTPS: Same as TCP but within 4 seconds. The end point must respond
with a 200 or 300 status code within 3 seconds of checking.
- String matching: Same as above, the body must have a string within the first
5120 bytes. This is chosen by the user.

It will be deemed healthy or unhealthy.

There are three types of checks.

- Endpoint checks
- CloudWatch alarms
- Checks of checks

### Route 53 Routing Policies Examples

- Simple: Route traffic to a single resource. Client queries the resolver
which has one record. It will respond with 3 values and these get forwarded
back to the client. The client then picks one of the three at random.
This is a single record only. No health checks.

- Failover: Create two records of the same name and the same type. One
is set to be the primary and the other is the secondary. This is the same
as the simple policy except for the response. Route 53 knows the health of
both instances. As long as the primary is healthy, it will respond with
this one. If the health check with the primary fails, the backup will be
returned instead. This is set to implement active - passive failover.

- Weighted: Create multiple records of the same name within the hosted zone.
For each of those records, you provide a weighted value. The total weight
is the same as the weight of all the records of the same name. If all of the
parts of the same name are healthy, it will distribute the load based
on the weight. If one of them fails its health check, it will be skipped over
and over again until a good one gets hit. This can be used for migration
to separate servers.

- Latency-based: Multiple records in a hosted zone can be created with
the same name and same type. When a client request arrives, it knows which
region the request comes from. It knows the lowest latency and will respond
with the lowest latency.

- Geolocation: Focused to delivering results matching the query of your
customers. The record will first be matched based on the country if possible.
If this does not happen, the record will be checked based on the continent.
Finally, if nothing matches again it will respond with the default response.
This can be used for licensing rights. If overlapping regions occur,
the priority will always go to the most specific or smallest region. The US
will be chosen over the North America record.

- Multi-value: Simple records use one name and multiple values in this record.
These will be health checked and the unhealthy responses will automatically
be removed. With multi-value, you can have multiple records with the same
name and each of these records can have a health check. R53 using this method
will respond to queries with any and all healthy records, but it removes
any records that are marked as unhealthy from those responses. This removes
the problem with simple routing where a single unhealthy record can make it
through to your customers. Great alternative to simple routing when
you need to improve the reliability, and it's an alternative to failover
when you have more than two records to respond with, but don't want
the complexity or the overhead of weighted routing.
