### AWS SYSOPS ADMINISTRATOR (SOA-C02) EXAM NOTES - David Galera, June 2025
- [EC2](#ec2)
- [ALB](#alb)
- [ASG](#asg)
- [EBS](#ebs)
- [Storage Gateway](#storage-gateway)
- [SSM](#ssm)
- [S3](#s3)
- [Cloudformation](#cloudformation)
- [Service Advisor](#service-advisor)
- [Beanstalk](#beanstalk)
- [CloudWatch](#cloudwatch)
- [Cloudfront](#cloudfront)
- [VPC](#vpc)


## EC2

**termination protection**:
- You can't enable **termination protection** for Spot Instances. A Spot Instance is terminated when the Spot price exceeds the amount you're willing to pay for Spot Instances
- To prevent instances that are part of an ASG from terminating on scale in, use **instance protection**.
- To prevent Amazon EC2 Auto Scaling from terminating unhealthy instances, suspend the `ReplaceUnhealthy` process.
- To specify which instances Amazon EC2 Auto Scaling should terminate first, choose a termination policy.

The `DisableApiTermination` attribute controls whether the instance can be terminated using the console, CLI, or API

`Status checks ` are performed **every minute** at no charge, returning a pass or a fail status. If all checks pass, the overall status of the instance is OK. If one or more checks fail, the overall status is impaired. `Status checks` are built into Amazon EC2, so they cannot be disabled or deleted.

**AMIs**
- You can only share AMIs that have **unencrypted volumes** and volumes that are **encrypted with a customer-managed CMK**. If you share an AMI with encrypted volumes, you must also share any CMKs used to encrypt them.
- You do not need to share the Amazon EBS snapshots that an AMI references in order to share the AMI. Only the AMI itself needs to be shared; the system automatically provides the access to the referenced Amazon EBS snapshots for the launch.
- To make an AMI available in a different Region, copy the AMI to the Region and then share it. Sharing an AMI from different Regions is not available.

- `No reboot` -> **crash-consistent**, the instance is not shut down while creating the AMI. This option is **not selected by default**.
- It isn't possible to restore or recover a deleted or deregistered AMI. However, you can create a new, identical AMI using one of the following: 
  - EBS snapshots that were created as backups.
  - EC2 instances that were launched from the deleted AMI

**Resizing**
- If your instance has a public IPv4 address, AWS releases the address and gives it a new public IPv4 address. The instance retains its private IPv4 addresses, any Elastic IP addresses, and any IPv6 addresses.
- Resizing of an instance is **only possible if the root device for your instance is an EBS volume** - If the root device for your instance is an EBS volume, you can change the size of the instance simply by changing its instance type, which is known as resizing it. If the root device for your instance is an instance store volume, you must migrate your application to a new instance with the instance type that you need.
- You must **stop your Amazon EBS–backed instance** before you can change its instance type. AWS moves the instance to new hardware; however, the instance ID does not change - You must stop your Amazon EBS–backed instance before you can change its instance type. When you stop and start an instance, AWS moves the instance to new hardware; however, the instance ID does not change.
- If your instance is in an Auto Scaling group, the Amazon EC2 Auto Scaling service marks the stopped instance as unhealthy, and may terminate it and launch a replacement instance - If your instance is in an Auto Scaling group, the Amazon EC2 Auto Scaling service marks the stopped instance as unhealthy, and may terminate it and launch a replacement instance. To prevent this, you can suspend the scaling processes for the group while you're resizing your instance.

**Enhanced Networking**

Consider using enhanced networking for the following scenarios:

- If your packets-per-second rate reaches its ceiling, consider moving to enhanced networking. If your rate reaches its ceiling, you've likely reached the upper thresholds of the virtual network interface driver.

- If your throughput is near or exceeding 20K packets per second (PPS) on the VIF driver, it's a best practice to use enhanced networking.

**Capacity Reservations**
- On-Demand Capacity Reservations enable you to reserve capacity for your Amazon EC2 instances in a specific Availability Zone for any duration.
- Capacity Reservations do not offer any billing discounts. You can combine Capacity Reservations with Savings Plans or Regional Reserved Instances to receive a discount.
- Capacity Reservations can be used with neither placement groups nor Dedicated Hosts.
- You can share Capacity Reservations with other AWS accounts.

## ALB
- `HTTP 503: Service unavailable will be received as response` is returned if the target groups for the load balancer have no registered targets.
- `HTTP 403: Forbidden will be returned` is returned if you configured an AWS WAF web access control list (web ACL) to monitor requests to your Application Load Balancer and it blocked the request.

## ASG
`Client.InternalError: Client error on launch` is thrown when an Auto Scaling group attempts to launch an instance that has an encrypted EBS volume, but the service-linked role does not have access to the customer-managed CMK used to encrypt it.
!["ASG"](asg.jpg)

## EBS

An EBS (**io1 or io2**) volume, when configured with the new **Multi-Attach** option, can be attached to a maximum of 16 EC2 instances in a single Availability Zone. Additionally, each Nitro-based EC2 instance can support the attachment of multiple Multi-Attach enabled EBS volumes

**RAID**
![raid](raid.jpg)
## Storage Gateway

![storage-gw](storage-gw.png)
AWS Storage Gateway uses SSL/TLS (Secure Socket Layers/Transport Layer Security) to encrypt data that is transferred between your gateway appliance and AWS storage. By default, Storage Gateway uses Amazon S3-Managed Encryption Keys (SSE-S3) to server-side encrypt all data it stores in Amazon S3. You have an option to use the Storage Gateway API to configure your gateway to encrypt data stored in the cloud using server-side encryption with AWS Key Management Service (SSE-KMS) customer master keys (CMKs).

File, Volume and Tape Gateway data is stored in Amazon S3 buckets by AWS Storage Gateway. Tape Gateway supports backing data to Amazon S3 Glacier apart from the standard storage.

## SSM
- Use the `AWSSupport-ExecuteEC2Rescue` document to recover impaired instances
- `AWS-UpdateCloudFormationStackWithApproval` document is used to update resources that were deployed by using CloudFormation template.
- Use the A`WS-UpdateWindowsAmi` document to recover impaired instances - You use the `AWS-UpdateLinuxAmi` and `AWS-UpdateWindowsAmi` documents to create golden AMIs from a source AMI.

**Systems Manager Automation** simplifies common maintenance and deployment tasks of EC2 instances and other AWS resources. Automation enables you to do the following: Build Automation workflows to configure and manage instances and AWS resources, Create custom workflows or use pre-defined workflows maintained by AWS, Receive notifications about Automation tasks and workflows by using Amazon EventBridge, Monitor Automation progress and execution details by using the Amazon EC2 or the AWS Systems Manager console

**AWS Systems Manager Patch Manager** automates the process of patching managed instances with both security-related and other types of updates. You can use Patch Manager to apply patches for both operating systems and applications. You can use Patch Manager to install Service Packs on Windows instances and perform minor version upgrades on Linux instances. You can patch fleets of EC2 instances or your on-premises servers and virtual machines (VMs) by operating system type.

## S3
**MFA delete** requires additional authentication for either of the following operations:
- Change the versioning state of your bucket
- Permanently delete an object version

The bucket owner (root account) can enable MFA Delete only via the **AWS CLI**

You cannot delete an S3 bucket if **delete markers** are present (bucket not empty error).

In-place switch from unencrypted to encrypted, encryption works at object level.

**Retention Periods**

A `retention period` protects an object version for a fixed amount of time.After the retention period expires, the object version can be overwritten or deleted unless you also placed a legal hold on the object version. This is where the `lifecycle policy` kicks. You can place a retention period on an `object version` either explicitly or through a bucket default setting.

When you use bucket default settings, you don't specify a `Retain Until Date`. Instead, you specify a duration, in either days or years, for which every object version placed in the bucket should be protected.

`Object Lock` works **only in versioned buckets**, and `retention periods` and `legal holds` apply to individual object versions.

## Cloudformation
!["stack policies"](stack-policies.jpg)

!["Change Sets"](change-sets.png)

- `Parameter constraints` describe allowed input values so that AWS CloudFormation catches any invalid values before creating a stack. You can set constraints such as a minimum length, maximum length, and allowed patterns.
- `!GetAtt` - The Fn::GetAtt intrinsic function returns the value of an attribute from a resource in the template. This example snippet returns a string containing the DNS name of the load balancer with the logical name myELB - YML : `!GetAtt` myELB.DNSName JSON : "Fn::GetAtt" : [ "myELB" , "DNSName" ]

The `cfn-init` helper script reads template metadata from the AWS::CloudFormation::Init key and acts accordingly to:

- Fetch and parse metadata from AWS CloudFormation

- Install packages

- Write files to disk

- Enable/disable and start/stop services

The `cfn-signal` helper script signals AWS CloudFormation to indicate whether Amazon EC2 instances have been successfully created or updated. If you install and configure software applications on instances, you can signal AWS CloudFormation when those software applications are ready.

You can use the wait condition handle to make AWS CloudFormation pause the creation of a stack and wait for a signal before it continues to create the stack. For example, you might want to download and configure applications on an Amazon EC2 instance before considering the creation of that Amazon EC2 instance complete.

AWS CloudFormation creates a wait condition just like any other resource. When AWS CloudFormation creates a wait condition, it reports the wait condition’s status as CREATE_IN_PROGRESS and waits until it receives the requisite number of success signals or the wait condition’s timeout period has expired. If AWS CloudFormation receives the requisite number of success signals before the time out period expires, it continues creating the stack; otherwise, it sets the wait condition’s status to CREATE_FAILED and rolls the stack back.

A `cfn-init` script failure is still be followed by the `cfn-signal` script. The **Timeout** property determines how long AWS CloudFormation waits for the requisite number of success signals. Timeout is a minimum-bound property, meaning the timeout occurs no sooner than the time you specify, but can occur shortly thereafter. The maximum time that you can specify is 43200 seconds (12 hours ). The **stack creation can fail** if CloudFormation fails to receive a signal from your EC2 instance if the **Timeout property is set to a low value**.

You cannot delete stacks that have termination protection enabled. The deletion fails and the stack - including its status - remains unchanged.

This includes nested stacks whose root stacks have termination protection enabled. Disable termination protection on the root stack, then perform the delete operation again. It is strongly recommended that you do not delete nested stacks directly, but only delete them as part of deleting the root stack and all its resources.

You must delete all objects in an Amazon S3 bucket or remove all instances in an Amazon EC2 security group before you can delete the bucket or security group. Otherwise, stack deletion fails and the stack will be in the DELETE_FAILED state.

If you created an AWS resource outside of AWS CloudFormation management, you can bring this existing resource into AWS CloudFormation management using `resource import`.

Performing a **drift detection** operation on a stack determines whether the stack has drifted from its expected template configuration, and returns detailed information about the drift status of each resource in the stack that supports drift detection.

To control how AWS CloudFormation handles the EBS volume when the stack is deleted, set a deletion policy for your volume. You can `delete`, `retain` or create a `snapshot`.

When you create a stack, all update actions are allowed on all resources. By default, anyone with stack update permissions can update all of the resources in the stack. You can prevent stack resources from being unintentionally updated or deleted during a stack update by using a `stack policy`. After you set a stack policy, all of the resources in the stack are protected by default. A **stack policy applies only during stack updates**.

You can use the `OnFailure` property of the CloudFormation CreateStack call for this use-case. The `OnFailure` property determines what action will be taken if stack creation fails. This must be one of `DO_NOTHING`, `ROLLBACK`, or `DELETE`.

## Service Advisor
!["Service Advisor"](service-advisor.jpg)

## Beanstalk
- ASG 2 default `cloudwatch alarms`: Average outbound network traffic (`NetworkOut`) from each instance is higher than 6 MB or lower than 2 MB over a period of five minutes.

## CloudWatch
You can retrieve custom metrics from your applications or services using the `StatsD` and `collectd` protocols. `StatsD` is supported on both Linux servers and servers running Windows Server. `collectd` is supported only on Linux servers.

**Canaries**

Canaries are Node.js scripts. They create Lambda functions in your account that use Node.js as a framework. Canaries work over both HTTP and HTTPS protocols.

UI canaries offer programmatic access to a headless Google Chrome Browser via Puppeteer. For more information about Puppeteer, see Puppeteer.

Canaries check the availability and latency of your endpoints and can store load time data and screenshots of the UI. They monitor your REST APIs, URLs, and website content, and they can check for unauthorized changes from phishing, code injection and cross-site scripting.

You can run a canary once or on a regular schedule. Scheduled canaries can run 24 hours a day, as often as once per minute

**Cloudwatch metrics**
- `StatusCheckFailed` - Reports whether the instance has passed both the instance status check and the system status check in the last minute.
- `StatusCheckFailed_Instance` - Reports whether the instance has passed the instance status check in the last minute.
- `StatusCheckFailed_System` - Reports whether the instance has passed the system status check in the last minute.
**CloudWatch Agent**: Any configuration files appended to the configuration **must have different file names** from each other and from the initial configuration file. If you use `append-config` with a configuration file with the same file name as a configuration file that the agent is already using, the append command overwrites the information from the first configuration file instead of appending to it. This is true even if the two configuration files with the same file name are on different file paths.

## Cloudfront
If your Amazon **S3 bucket is configured as a website endpoint**, you can't configure CloudFront to use HTTPS to communicate with your origin because Amazon S3 doesn't support HTTPS connections in that configuration.

## VPC
Troubleshooting flow logs:
- The IAM role for your flow log does not have sufficient permissions to publish flow log records to the CloudWatch log group
- The IAM role does not have a trust relationship with the flow logs service
- The trust relationship does not specify the flow logs service as the principal

After you've created a flow log, you cannot change its configuration or the flow log record format
