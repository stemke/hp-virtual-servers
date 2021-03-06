---
copyright:
  years:  2017, 2020
lastupdated: "2020-04-14"

subcollection: hp-virtual-servers

keywords: public isolation for {{site.data.keyword.hpvs}}, compute isolation for {{site.data.keyword.hpvs}}, {{site.data.keyword.hpvs}} architecture, workload isolation in {{site.data.keyword.hpvs}}

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:tip: .tip}
{:important: .important}
{:note: .note}

# Learning about {{site.data.keyword.hpvs}} architecture and workload isolation

Review the following sample architecture for {{site.data.keyword.cloud}} {{site.data.keyword.hpvs}}, and learn more about different isolation levels so that you can choose the solution that best meets the requirements of the workloads that you want to run in the cloud.
{: shortdesc}

## {{site.data.keyword.hpvs}} architecture

![**{{site.data.keyword.hpvs}}** architecture](image/hpvs_architecture-overview.svg "**{{site.data.keyword.hpvs}}**  architecture overview")
*Figure 1. **{{site.data.keyword.hpvs}}** architecture overview*


The {{site.data.keyword.hpvs}} architecture is divided into two major areas:
1. The **Z as a Service (ZaaS)** infrastructure as shown in the upper half of the picture is used to host the {{site.data.keyword.hpvs}}s. This infrastructure is deployed as multi-zone regions, where each region consists of 3 zones. In every region there are multiple IBM LinuxONE systems available to deploy the virtual servers. These can be reached via SSH over the public network.
2. The lower half of the picture shows the **{{site.data.keyword.hpvs}} Management** stack. The key component is the {{site.data.keyword.hpvs}} service broker which is used to create and manage your virtual servers. You can use the {{site.data.keyword.cloud_notm}} catalog to interact with this component to provision a virtual server. After that you can use the {{site.data.keyword.hpvs}} dashboard to view details about the virtual server you just created.

## {{site.data.keyword.hpvs}} workload isolation
As shown in Figure 2, the IBM LinuxONE systems which are used for the {{site.data.keyword.hpvs}} are partitioned into several Logical Partitions (LPARs). A host system which is protected by the IBM Secure Service Container technology is installed on each partition. IBM Secure Service Container technology provides protection against insider threats, for example, IBM Cloud administrators. This is achieved by the following features:
* The host system supports interactions for administrators via well-defined encrypted REST APIs with only a limited set of secure configuration options. No other connection is available, for example, SSH is not available.
* The IBM Secure Service Container implements a secure boot procedure to detect potential attacks against the installation image, which ensures that only untampered images are booted.
* All disks which are attached to the IBM Secure Service Container host system are automatically encrypted using AES256. The encryption keys are stored in the protected host.
* Mechanisms to look into the processor or memory state from the IBM LinuxONE Hardware Management Console are not available for partitions protected by the IBM Secure Service Container technology.

![**{{site.data.keyword.hpvs}}** architecture](image/hpvs_architecture-isolation.svg "**{{site.data.keyword.hpvs}}**  architecture compute isolation")<br>

*Figure 2. **{{site.data.keyword.hpvs}}** architecture compute isolation*


Figure 2. shows that every host system is shared between multiple {{site.data.keyword.hpvs}} owned by multiple tenants. The KVM hypervisor isolates the virtual servers from each other, and each virtual server has its own virtualized network connection.

## Dependencies to other {{site.data.keyword.cloud_notm}} services
| Service name | Description|
| -----------|-------------------------------|
| Business Support Services for {{site.data.keyword.cloud_notm}} (BSS) | BSS is used to access information about the {{site.data.keyword.cloud_notm}} account, service subscription, service usage, and billing. |
| {{site.data.keyword.cloudcerts_full}} | This service manages the TLS certificates that are used for the service broker |
| {{site.data.keyword.loganalysisfull}} with LogDNA | The {{site.data.keyword.hpvs}} service broker sends service logs to {{site.data.keyword.loganalysisfull_notm}} with LogDNA. These logs are monitored and analyzed by the service team to detect service issues and malicious activities. |
| {{site.data.keyword.cloudaccesstrailfull}} | {{site.data.keyword.hpvs}} integrates with {{site.data.keyword.cloudaccesstrailfull_notm}} to forward audit events to the {{site.data.keyword.cloudaccesstrailfull_notm}} service. |
| {{site.data.keyword.mon_full}} | The {{site.data.keyword.hpvs}} service broker sends service metrics to {{site.data.keyword.mon_full_notm}}. These metrics are monitored by the service team to identify capacity and performance issues of the service. |
| {{site.data.keyword.iamlong}} | To authenticate requests to the service and authorize user actions, {{site.data.keyword.hpvs}} uses platform and service access roles in {{site.data.keyword.iamshort}} (IAM). For more information about the IAM permissions required to work with the service, see [Managing access for {{site.data.keyword.hpvs}}](/docs/services/hp-virtual-servers?topic=hp-virtual-servers-iam) |
| {{site.data.keyword.containerlong}} | {{site.data.keyword.hpvs}} is using the {{site.data.keyword.containerlong_notm}} to host the service broker and the dashboard UI. |
| {{site.data.keyword.databases-for-mongodb_full}} | {{site.data.keyword.hpvs}} uses the {{site.data.keyword.databases-for-mongodb_full_notm}} to store the metadata about virtual servers to manage those. |


## Dependencies on 3rd party services

Review the list of 3rd party services that {{site.data.keyword.hpvs}} connect to over the public network.

| Service name | Description|
| -----------|-------------------------------|
| Akamai, Cloudflare | Akamai and Cloudflare are used as the primary providers for DNS, global load balancing, and web firewall capabilities in {{site.data.keyword.hpvs}} |
| {{site.data.keyword.ghe_short}} | {{site.data.keyword.ghe_short}} is used to track service enhancements, features, and customer issues. When a customer issue is identified, the CRN, the name and the plan of the virtual server is documented here. This information is shared with the service team to start troubleshooting the issue. |
| Slack | Slack is used as the IBM-internal communication medium to troubleshoot issues and bring together internal SMEs to resolve customer issues. Diagnostic information about virtual servers is sent to a private Slack channel and includes the CRN, customer account ID and details about the virtual server. |
| Dockerhub | Dockerhub is used to host some Docker images which are used by the service. |
| ServiceNow | ServiceNow is used as Customer Support and Change Management Tool. It processes information that you provide in support tickets. |
| PagerDuty | PagerDuty is used as duty management and call-out system for customer incidents. Information from your support tickets is shared here. |
