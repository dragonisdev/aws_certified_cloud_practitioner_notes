# Amazon Machine Images

> CLF-C02 focus: using AMIs as reusable EC2 launch templates and automating image creation with EC2 Image Builder.

## What is an AMI?

An Amazon Machine Image (AMI) is a template used to launch EC2 instances. It can contain:

- An operating system
- Installed software and configuration
- A template for the root volume
- Block device mappings for additional volumes
- Launch permissions defining which accounts can use it

Many instances can be launched from the same AMI, which makes server configuration repeatable.

## Sources of AMIs

| AMI source | Description |
| --- | --- |
| AWS provided | Images maintained by AWS, such as Amazon Linux |
| AWS Marketplace | Images published by software vendors, sometimes with additional software charges |
| Community | Public images published by AWS customers or other publishers |
| Custom | Private or shared images created from your configured instances or image pipelines |

Verify the publisher and contents before using a public or Marketplace image. Detailed Marketplace purchasing belongs in the AWS Ecosystem chapter.

## Creating a custom AMI

A common workflow is:

1. Launch an EC2 instance from a trusted base AMI.
2. Install patches, applications, agents, and configuration.
3. Remove temporary files and sensitive credentials.
4. Create an AMI from the configured instance.
5. Launch and test new instances from the AMI.

For an EBS-backed instance, creating an AMI normally creates snapshots of its root volume and any included EBS data volumes.

Custom AMIs help with:

- Standardized server builds
- Faster replacement and recovery
- Scaling identical instances in an Auto Scaling group
- Moving a tested configuration between environments

## AMI scope and sharing

- An AMI belongs to one AWS Region.
- It can be copied to another Region when a workload needs the image elsewhere.
- A private AMI can be shared with selected AWS accounts.
- A public AMI can be launched by any AWS account, so it must not contain secrets.
- Copying and sharing encrypted AMIs also depends on permissions for the relevant AWS KMS keys.

IAM and encryption policy details remain in the identity and security chapters.

## AMI, snapshot, and instance

| Resource | What it represents |
| --- | --- |
| AMI | A reusable launch template for EC2 instances |
| EBS snapshot | A point-in-time copy of the blocks in an EBS volume |
| EC2 instance | A running or stopped virtual server launched from an AMI |

An AMI can reference EBS snapshots, but an AMI and a snapshot are not the same resource.

## EC2 Image Builder

EC2 Image Builder automates the creation, testing, and distribution of server images.

A typical image pipeline can:

1. Start with a base image.
2. Apply reusable build components such as updates and software installation.
3. Run validation and security tests.
4. Produce a new AMI.
5. Distribute the image to selected AWS Regions and accounts.

Automation reduces manual image-building work and makes images easier to reproduce consistently.

## Exam memory checks

1. **What is an AMI?** A reusable template used to launch EC2 instances.
2. **What can an AMI contain?** An OS, software, a root-volume template, launch permissions, and block device mappings.
3. **Is an AMI global?** No. It belongs to one Region but can be copied to another.
4. **Is an AMI the same as an EBS snapshot?** No. An EBS-backed AMI can reference snapshots, but the AMI is an instance launch template.
5. **What does EC2 Image Builder automate?** Image creation, testing, and distribution.
6. **Why does an Auto Scaling group benefit from a custom AMI?** It can launch many instances with the same tested configuration.

## Avoiding duplication in later chapters

- EBS mechanics and snapshots: [EBS and EC2 Storage](04-ebs-and-ec2-storage.md)
- Marketplace subscriptions and third-party offerings: AWS Ecosystem
- KMS keys and detailed encryption controls: Security and Encryption
- Launch templates and automatic scaling: the next chapter

## References

- [Amazon Machine Images in Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)
- [How EC2 Image Builder works](https://docs.aws.amazon.com/imagebuilder/latest/userguide/how-image-builder-works.html)
- [What is EC2 Image Builder?](https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html)
