# week3
# Week 3: Systems Manager and S3

## HarborTech Ticket Summary

TKT-2026-0003 is for Bright Path Community Services. The operational problem is that Dana performs the same maintenance task every Monday on five EC2 instances. Performing the task manually on each instance takes more time and creates a greater chance of inconsistent results. Bright Path also needs a simple public webpage containing program hours, contact information, images, and downloadable public forms.

The investigation focuses on using AWS Systems Manager for centralized EC2 management and Amazon S3 for hosting static website content.

## Client Impact

Repeated manual administration across five EC2 instances can affect consistency and efficiency because each system has to be handled separately. It also increases the chance that a command could be missed or performed differently on one instance.

Using centralized management can make repeated maintenance more consistent and reduce the amount of time spent performing the same task manually. This allows HarborTech to focus more on troubleshooting and other support responsibilities.

## AWS Services Involved

The AWS services and concepts involved in this investigation include:

- **AWS Systems Manager** — Provides centralized management capabilities for AWS resources and managed instances.
- **Run Command** — Allows commands to be executed on multiple managed instances without connecting to each instance individually.
- **Session Manager** — Provides interactive access to managed instances without requiring inbound SSH access.
- **Inventory** — Collects information about an instance, such as installed software and system information.
- **Parameter Store** — Provides a centralized location for storing configuration values and retrieving them by parameter name.
- **Amazon S3** — Provides object storage for files and other data.
- **Static Website Hosting** — Allows static files such as HTML, images, and public documents to be served as a website.

## Virtualization Connection

Systems Manager provides a centralized management layer for virtual machines such as EC2 instances. Instead of an administrator manually connecting to each virtual machine, Systems Manager provides management capabilities across multiple managed instances.

For Bright Path, this is useful because Dana has the same maintenance task on five EC2 instances. Run Command can be used for the repeated non-interactive maintenance task, while Session Manager can be used when an administrator needs an interactive session.

Amazon S3 provides a different service model. A static website does not need a traditional operating system and web server when it only contains files such as HTML, images, and downloadable forms. S3 can store and serve those static objects without HarborTech having to maintain a traditional web server.

## Evidence Reviewed

The investigation reviewed the following evidence and requirements:

- Dana performs the same maintenance task every Monday on five EC2 instances.
- The repeated maintenance task is a candidate for centralized execution.
- Run Command is appropriate for repeated, non-interactive commands.
- Session Manager is intended for interactive access when an administrator needs to work directly with a managed instance.
- Inventory can provide information about installed software and system state.
- Parameter Store can centralize environment-specific configuration values.
- Systems Manager capabilities require the instances to meet managed-node prerequisites, including the SSM Agent, appropriate IAM permissions, and required connectivity.
- Bright Path's public page requires program hours, contact information, images, and downloadable public forms.
- The Bright Path page was created as an `index.html` static webpage.
- **S3 Bucket Name:** `brightpath-as-39483`
- **Region:** `us-west-2`
- **Object Key:** `index.html`
- **Website Endpoint:** `http://brightpath-as-39483.s3-website-us-east-1.amazonaws.com/`
- The public-access test was denied because the Learner Lab restricted the public-access configuration needed for the website to be publicly readable.
- CloudShell was used to verify the AWS caller identity and update the website files.

The AWS identity was verified with:

```bash
aws sts get-caller-identity
```

The command returned the current AWS identity and confirmed that I was operating in the Learner Lab environment.

The S3 website files were updated using:

```bash
aws s3 sync ./brightpath-site s3://brightpath-as-39483/
```

## Operational Analysis

The repeated Monday maintenance task is better suited for centralized management and automation because the same command needs to be performed across five EC2 instances. Run Command fits this requirement because it can execute a command across managed instances without requiring Dana to connect to each instance separately.

Interactive troubleshooting is different. If Dana needs to investigate a specific instance or perform an interactive task, Session Manager is more appropriate because it provides an interactive session with the managed instance.

Inventory is useful when HarborTech needs information about the systems, such as installed software or system configuration. Parameter Store is useful when the same environment-specific value is hardcoded in multiple places. The application or automation would need to retrieve the value from Parameter Store by its parameter name because Parameter Store does not automatically rewrite existing configuration files.

The Bright Path webpage is suited for S3 static website hosting because its requirements are limited to static content. The page does not require server-side processing, a database, or user authentication.

## Recommendation

For Dana's repeated EC2 maintenance task, HarborTech should use **AWS Systems Manager Run Command**. This feature fits the need to execute the same non-interactive maintenance command across five EC2 instances.

Before using Run Command, HarborTech should verify that each instance is ready to be managed. The **SSM Agent must be installed and running**, the instances must have the required **IAM permissions through an appropriate instance role**, and they must have the necessary **network connectivity to Systems Manager endpoints**. HarborTech should verify that all five instances appear as managed nodes before depending on the automation.

For interactive administration, HarborTech should use **Session Manager**. For system information, **Inventory** can be used. For centralized configuration values, **Parameter Store** is appropriate.

For Bright Path's public resource page, **Amazon S3 static website hosting** is appropriate because the required content is static. If Bright Path later requires features such as user authentication, protected accounts, server-side processing, or database transactions, the architecture should be expanded or changed because static S3 hosting alone would not provide those capabilities.

## Escalation Notes

The S3 website test resulted in an access denial because the Learner Lab restricted the public-access configuration needed to make the website publicly readable. I documented the restriction instead of attempting to bypass the sandbox controls.

In a normal AWS account, the appropriate S3 public-access and permission configuration would need to be approved and configured for public website access.

HarborTech should also escalate any EC2 instance that does not meet the Systems Manager managed-node requirements. This includes missing or inactive SSM Agent configuration, missing IAM permissions, or connectivity problems that prevent the instance from communicating with Systems Manager.

## Lessons Learned

Week 3 taught me that centralized management can reduce repeated manual work and make operations more consistent. I learned that Systems Manager has different features for different situations instead of using one tool for every task. Run Command is useful for repeated commands, Session Manager is useful for interactive access, Inventory provides system information, and Parameter Store can centralize configuration values.

I also learned that not every workload needs a traditional server. A simple webpage made up of static files can use S3 static website hosting instead of requiring a server that HarborTech would have to maintain.

Another important lesson was that automation should not be used until the environment is ready for it. The managed instances need the correct agent, permissions, and connectivity. If the required access is restricted by the Learner Lab, the correct response is to document the limitation and escalate it rather than attempting to bypass the restriction.

## Professional Vocabulary

- **Systems Manager:** An AWS service that provides centralized management capabilities for AWS resources and managed instances.
- **Managed Node:** A machine that has been configured so Systems Manager can manage and communicate with it.
- **Run Command:** A Systems Manager feature used to execute commands on one or more managed nodes.
- **Session Manager:** A Systems Manager feature that provides interactive access to managed instances without requiring traditional inbound SSH access.
- **Inventory:** A Systems Manager capability that collects information about managed systems, including software and system details.
- **Parameter Store:** A Systems Manager capability that provides a centralized place to store and retrieve configuration values by parameter name.
- **Automation:** Using defined processes or tools to perform tasks consistently with less manual intervention.
- **Static Website Hosting:** Hosting website files such as HTML, images, and documents without requiring server-side application processing.
- **Object Storage:** A storage model that stores data as objects along with information such as the object's name and metadata. Amazon S3 uses object storage.
- **Management Plane:** The layer used to manage, configure, monitor, and control resources rather than directly serving the application's end-user content.
