title: Announcing the HashiCorp Cloud Platform
author: MITCHELL HASHIMOTO
source: https://www.hashicorp.com/blog/announcing-cloud-platform/

# Announcing the HashiCorp Cloud Platform

Today we are pleased to announce the HashiCorp Cloud Platform (HCP), a fully managed cloud offering to automate deployment of HashiCorp products on any cloud provider.

Our first HCP service — **HCP Consul** — is now in private beta with support for AWS. **HCP Vault** with support for AWS will follow next. Use the link below to sign up for early access.

![Sign Up for Early Access]](https://www.hashicorp.com/cloud-platform/request-access)

HashiCorp tools are used and operated by many community members and customers, but managing clusters and maintaining automated workflows can still be complicated and costly. This can increase onboarding times for smaller organizations and make multi-cloud objectives impractical for many enterprises. With automated deployments and fully managed clusters pre-configured for production, HCP enables a team to get critical workloads running in the cloud faster with fewer resources in order to meet today’s challenges.

Read on to learn more about our vision for HCP and how it can help your organization accelerate digital transformation and bring multi-cloud within reach.

## Our Vision for HCP

We are launching HCP with support for a single service and a single cloud provider. The vision for the platform, however, is to provide a suite of managed services with built-in workflows to deploy any HashiCorp product on any cloud provider, and to enable cross-provider clustering using a virtual network abstraction. The key HCP features that make this vision possible are outlined below.

HCP services enable a user to deploy production-ready clusters automatically and get applications up and running in the cloud quickly.

![](https://www.datocms-assets.com/2885/1592602025-consulcreateshort.gif?fit=max&fm=gif&q=80&w=2000)

Clusters are pre-configured to be “secure by default” based on the latest best practices as determined by the HashiCorp core product teams. This includes enabling encryption, configuring TLS, managing the lifecycle of certificates, and handling security updates automatically. In the Consul example below, the client configuration file and an ACL token can be easily generated after a deployment completes.

![](https://www.datocms-assets.com/2885/1592672094-consul-config2.png?fit=max&fm=png&q=80&w=2000)

HCP will also offer pay-as-you-go pricing to minimize upfront costs and lower the barrier to entry for smaller teams and organizations. Costs will increase only when workloads scale in response to increased business or customer demand. Both development and production SKUs will be available at GA.

HCP services deploy dedicated product resources that are fully managed with an SLA backed by HashiCorp. Upgrades, backups, monitoring, and scaling are all handled in the background by the engineering teams that build and maintain the core products, enabling a customer to support the cloud-native applications that depend on HashiCorp tools more easily. Operational issues can be resolved efficiently since logs, telemetry, and debug information are all readily available to HashiCorp operators, avoiding the back-and-forth required for customer-managed deployments. Cloud provider maintenance events that often cause virtual machines to be decommissioned are also automatically dealt with on the customer’s behalf.

![](https://www.datocms-assets.com/2885/1592602085-fully-managed-status.png?fit=max&fm=png&q=80&w=2000)

HCP enables a customer to standardize on a unified workflow and a single set of APIs to deploy any HashiCorp product on any cloud provider. A key component of the HCP workflow is the HashiCorp Virtual Network (HVN), which offers a common abstraction across cloud providers around an isolated single tenant network. Each HVN can have multiple services deployed within it, and enable customers to establish direct network peering arrangements. In the future, the HVN mechanism will also enable peering across cloud providers and cross-provider clustering for our products.

![](https://www.datocms-assets.com/2885/1592602051-createhvnshort.gif?fit=max&fm=gif&q=80&w=2000)

Customers can integrate HCP with identity providers like GitHub using SSO and use centralized policies to govern access to resources across teams, projects, and cloud providers from a single portal.

## HCP Consul on AWS

HCP Consul is available on the platform today for [early access](https://www.hashicorp.com/cloud-platform/request-access). We are launching with support for AWS as the first cloud provider and will expand to support additional cloud providers over time based on customer input.

![](https://www.datocms-assets.com/2885/1592605691-hcp-consul-in-blog.png?fit=max&fm=png&q=80&w=2000)

As a fully managed service, HCP Consul will be the easiest way to enable secure service networking and service mesh for workloads across EKS, EC2, AWS Lambda, and other AWS services, and to connect AWS environments to other cloud environments and private data centers.

The HCP platform powers both HCP Consul and [HashiCorp Consul Service (HCS) on Azure](https://www.hashicorp.com/hcs), and consists of a set of core services that are common across the two offerings. Core platform components include an API services layer, a workflow manager powered by [Cadence](https://github.com/uber/cadence), and a HashiCorp Terraform integration to handle product deployments.

![](https://www.datocms-assets.com/2885/1592602092-hcp-architecture.png?fit=max&fm=png&q=80&w=2000)

The core services are consumed by the Consul specific API service components for each cloud provider. The product-specific components will be re-implemented to support HCP Vault and future products as they are introduced (the core components will be reused).

HCP Consul isolates Consul server resources on a per-customer basis. Each HCP Organization (i.e. tenant) is isolated into a separate AWS account. HCP then deploys a single tenant VPC for each “HashiCorp Virtual Network” (HVN). All HCP resources are deployed into that HVN, which ensures tenant isolation. This also guarantees that traffic cannot be routed to an interface or a host that belongs to another customer. In addition, servers are deployed across multiple availability zones, in line with AWS best practices and providing for a high level of redundancy by default in HCP.

![](https://www.datocms-assets.com/2885/1592602096-hcp-consul-on-aws-deployment-pattern.png?fit=max&fm=png&q=80&w=2000)

A HashiCorp Virtual Network and a peering connection are required to join the customer VPC that houses the applications to the managed VPC hosted by the platform. This uses the standard AWS peering mechanism and should be familiar to AWS customers.

![](https://www.datocms-assets.com/2885/1592602102-peeringshort.gif?fit=max&fm=gif&q=80&w=2000)

A security group and a route will need to be configured through AWS directly to authorize access and enable traffic to flow over the peering connection. As is normal in AWS, all ports are off by default and connectivity needs to be explicitly allowed by creating a security group with the correct ports and endpoints configured.

Once the Consul cluster is deployed by HCP, customers can interact with it using a generated root level token. Once the token is generated, Consul will work in the same way as normal customer-managed Consul. The same binaries are used in HCP as are available in the enterprise version. As announced today, we will be using Consul 1.8 and above for HCP.

## Next Steps

HCP Consul is available for [early access](https://www.hashicorp.com/cloud-platform/request-access) today and will open up for public access later this year. HCP Vault will follow next.

To learn more about HCP, please visit our [web page](https://www.hashicorp.com/cloud-platform) or read the [press release](https://www.globenewswire.com/NewsRoom/ReleaseNg/3556566). You can also view the recordings of the [HCP announcement](https://www.hashicorp.com/resources/hashiconf-digital-keynote-hashicorp-cloud-platform-announcement) and the [HCP Consul demo](https://www.hashicorp.com/resources/hashicorp-cloud-platform-consul-hashiconf-keynote-demo) from HashiConf Digital 2020.