- [EC2](#ec2)
- [SSM](#ssm)
- [S3](#s3)
- [Cloudformation](#cloudformation)
- [ALB](#alb)
- [ASG](#asg)
- [Service Advisor](#service-advisor)
- [Beanstalk](#beanstalk)
- [CloudWatch](#cloudwatch)


## EC2
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

**Capacity Reservations**
- On-Demand Capacity Reservations enable you to reserve capacity for your Amazon EC2 instances in a specific Availability Zone for any duration.
- Capacity Reservations do not offer any billing discounts. You can combine Capacity Reservations with Savings Plans or Regional Reserved Instances to receive a discount.
- Capacity Reservations can be used with neither placement groups nor Dedicated Hosts.
- You can share Capacity Reservations with other AWS accounts.

## SSM
- Use the `AWSSupport-ExecuteEC2Rescue` document to recover impaired instances
- `AWS-UpdateCloudFormationStackWithApproval` document is used to update resources that were deployed by using CloudFormation template.
- Use the A`WS-UpdateWindowsAmi` document to recover impaired instances - You use the `AWS-UpdateLinuxAmi` and `AWS-UpdateWindowsAmi` documents to create golden AMIs from a source AMI.

**Systems Manager Automation** simplifies common maintenance and deployment tasks of EC2 instances and other AWS resources. Automation enables you to do the following: Build Automation workflows to configure and manage instances and AWS resources, Create custom workflows or use pre-defined workflows maintained by AWS, Receive notifications about Automation tasks and workflows by using Amazon EventBridge, Monitor Automation progress and execution details by using the Amazon EC2 or the AWS Systems Manager console

**AWS Systems Manager Patch Manager** automates the process of patching managed instances with both security-related and other types of updates. You can use Patch Manager to apply patches for both operating systems and applications. You can use Patch Manager to install Service Packs on Windows instances and perform minor version upgrades on Linux instances. You can patch fleets of EC2 instances or your on-premises servers and virtual machines (VMs) by operating system type.

## S3
**Retention Periods**

A `retention period` protects an object version for a fixed amount of time.After the retention period expires, the object version can be overwritten or deleted unless you also placed a legal hold on the object version. This is where the `lifecycle policy` kicks. You can place a retention period on an `object version` either explicitly or through a bucket default setting.

When you use bucket default settings, you don't specify a `Retain Until Date`. Instead, you specify a duration, in either days or years, for which every object version placed in the bucket should be protected.

`Object Lock` works **only in versioned buckets**, and `retention periods` and `legal holds` apply to individual object versions.

## Cloudformation
!["stack policies"](stack-policies.jpg)

!["Change Sets"](change-sets.png)

- `Parameter constraints` describe allowed input values so that AWS CloudFormation catches any invalid values before creating a stack. You can set constraints such as a minimum length, maximum length, and allowed patterns.
- `!GetAtt` - The Fn::GetAtt intrinsic function returns the value of an attribute from a resource in the template. This example snippet returns a string containing the DNS name of the load balancer with the logical name myELB - YML : `!GetAtt` myELB.DNSName JSON : "Fn::GetAtt" : [ "myELB" , "DNSName" ]

## ALB
- `HTTP 503: Service unavailable will be received as response` is returned if the target groups for the load balancer have no registered targets.
- `HTTP 403: Forbidden will be returned` is returned if you configured an AWS WAF web access control list (web ACL) to monitor requests to your Application Load Balancer and it blocked the request.

## ASG
`Client.InternalError: Client error on launch` is thrown when an Auto Scaling group attempts to launch an instance that has an encrypted EBS volume, but the service-linked role does not have access to the customer-managed CMK used to encrypt it.
!["ASG"](asg.jpg)

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