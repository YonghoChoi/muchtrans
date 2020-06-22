title: 해시코프 클라우드 플랫폼 발표
author: MITCHELL HASHIMOTO
source: https://www.hashicorp.com/blog/announcing-cloud-platform/

# 해시코프 클라우드 플랫폼 발표

드디어 어떠한 클라우드 제공업체에서든 해시코프의 제품들을 자동으로 배포할 수 있도록 완벽히 관리되는 해시코프 클라우드 플랫폼(HCP)을 발표하게 되어서 기쁩니다.

첫번째 HCP 서비스인 **HCP Consul**은 아직까지는 AWS만 지원하는 내부 베타 서비스입니다. 그 다음으로 AWS만 지원하는 **HCP Vault**가 서비스될 예정입니다. 아래 미리 사용해보기 위한 회원 가입 링크를 클릭해주세요.

![미리 사용해보기 위한 회원가입](https://www.hashicorp.com/cloud-platform/request-access)

해시코프 도구들은 많은 커뮤니티 회원들과 고객들로부터 사용되고 운용되고 있지만 클러스터를 관리하는 것과 자동화 된 유지보수 작업 절차는 여전히 복잡하고 비용이 많이 들 수 있습니다. 이는 소규모 조직에 새로 투입되는 직원이 적응하는데 오래 걸리게 만들고 많은 기업들을 터무니 없이 멀티 클라우드를 사용하게끔 만듭니다. HCP는 고객의 제품을 위한 사전 정의된 자동화된 배포와 완벽하게 관리되는 클러스터들로 하나의 팀이 적은 리소스로 클라우드에서 크리티컬한 워크로드를 수행할 수 있도록 하는 것이 도전 과제입니다. 

HCP의 비전과 어떻게 조직의 디지털 트랜스포메이션을 가속화 할 수 있는지, 어떻게 멀티 클라우드를 활용하고 싶은지 알고 싶다면 계속 읽어보시기 바랍니다.

## HCP의 비전

현재는 하나의 클라우드 업체와 하나의 서비스를 지원하는 HCP를 출시했지만 플랫폼의 비전은 어떠한 클라우드 업체 위에서 어떠한 해시코프의 제품도 배포될 수 있는 내장 워크플로우들로 관리되는 서비스들을 제공하는 것이고, 가상 네트워크 추상화로 cross-provider 클러스터링을 화성화하는 것입니다. 이 비전을 가능하게하는 HCP의 주요 기능들을 아래에서 소개합니다. 

HCP 서비스들로 사용자들은 준비된 제품 클러스트들을 자동으로 배포하여 클라우드에서 빠르게 어플리케이션을 실행할 수 있습니다.

![](https://www.datocms-assets.com/2885/1592602025-consulcreateshort.gif?fit=max&fm=gif&q=80&w=2000)

클러스터는 해시코프 핵심 제품 팀들로부터 검증된 최신의 베스트 프랙티스들을 기반으로하여 "기본적으로 안전하게" 사전 구성됩니다. 이는 암호화를 적용하고 TLS를 설정하고 인증서의 수명주기를 관리하고 자동 보안 업데이트를 수행하는 것을 포함합니다. 아래 Consul의 예에서 클라이언트의 설정파일과 ACL 토큰은 배포가 성공된 이후에 쉽게 생성될 수 있습니다.

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