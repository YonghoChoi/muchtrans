title: 해시코프 클라우드 플랫폼 발표
author: MITCHELL HASHIMOTO
source: https://www.hashicorp.com/blog/announcing-cloud-platform/

# 해시코프 클라우드 플랫폼 발표

드디어 어떠한 클라우드 제공업체에서든 해시코프의 제품들을 자동으로 배포할 수 있도록 완벽히 관리되는 해시코프 클라우드 플랫폼(HCP)을 발표하게 되었습니다.

첫번째 HCP 서비스인 **HCP Consul**은 아직까지는 AWS만 지원하는 내부 베타 서비스입니다. 그 다음으로 AWS만 지원하는 **HCP Vault**가 서비스될 예정입니다. 아래 미리 사용해보기 위한 회원 가입 링크를 클릭해주세요.

[미리 사용해보기 위한 회원가입](https://www.hashicorp.com/cloud-platform/request-access)

해시코프 도구들은 많은 커뮤니티 회원들과 고객들로부터 사용되고 운용되고 있지만 클러스터를 관리하는 것과 자동화 된 유지보수 작업 절차는 여전히 복잡하고 비용이 많이 들 수 있습니다. 이는 소규모 조직에 새로 투입되는 직원이 적응하는데 오래 걸리게 만들고 많은 기업들을 비효율적으로 멀티 클라우드를 사용하게끔 만듭니다. HCP는 고객의 제품을 위한 사전 정의로 자동화된 배포와 완벽하게 관리되는 클러스터들로 하나의 팀이 적은 리소스로 클라우드에서 중요한 워크로드를 수행할 수 있도록 하는 것이 도전 과제입니다. 

HCP의 비전과 어떻게 조직의 디지털 트랜스포메이션을 가속화 할 수 있는지, 어떻게 멀티 클라우드를 활용할 수 있는지 알고 싶다면 계속 읽어보시기 바랍니다.

## HCP의 비전

현재는 하나의 클라우드 공급자와 하나의 서비스만을 지원하는 HCP를 출시했지만 플랫폼의 비전은 여러 클라우드 공급자에서 해시코프의 제품들이 배포되어 내장 워크플로우들로 관리되는 서비스들을 제공하고, 가상 네트워크 추상화로 cross-provider 클러스터링을 활성화하는 것입니다. 이 비전을 가능하게하는 HCP의 주요 기능들을 아래에서 소개합니다. 

HCP 서비스들로 사용자들은 준비된 제품의 클러스트를 자동으로 배포하여 클라우드에서 빠르게 어플리케이션을 실행할 수 있습니다.

![](https://www.datocms-assets.com/2885/1592602025-consulcreateshort.gif?fit=max&fm=gif&q=80&w=2000)

클러스터는 해시코프 핵심 제품 팀들로부터 검증된 최신의 모범사례들을 기반으로하여 "기본적으로 안전하게" 사전 구성됩니다. 이는 암호화를 적용하고 TLS를 설정하고 인증서의 수명주기를 관리하고 자동 보안 업데이트를 수행하는 것을 포함합니다. 아래 Consul의 예에서 클라이언트의 설정파일과 ACL 토큰은 배포가 성공된 이후에 쉽게 생성될 수 있습니다.

![](https://www.datocms-assets.com/2885/1592672094-consul-config2.png?fit=max&fm=png&q=80&w=2000)

HCP는 저렴한 선불 비용과 적은 규모의 팀 또는 조직에게 낮은 진입장벽을 제공하기 위해 사용료를 먼저 지급하는 요금 정책을 제공합니다. 요금은 사업이 성장하거나 고객이 증가할 떄 워크로드의 규모에 따라 증가하게 될 것 입니다. 개발과 프로덕션 모두의 SKU(Stock Keeping Unit, 재고 관리 코드)는 GA(General Availability, 정식 출시) 시 사용 가능합니다.

HCP 서비스들은 해시코프로부터 SLA가 보증된 관리형 제품 리소스들로 배포됩니다. 핵심 제품의 업그레이드, 백업, 모니터링, 스케일링은 모두 유지보수를 담당하는 엔지니어링 팀에 의해 백그라운드에서 처리되고, 제품을 사용하는 고객을 위한 해시코프 도구들을 더 쉽게 사용할 수 도록 클라우드 네이티브 어플리케이션들을 지원합니다. 고객이 관리하는 디플로이먼트들이 여기저기 흩어지지 않도록 하기 위해 필수적으로 로그와 텔레메트리, 디버그 정보를 제공하여 모든 운영 이슈들을 해결할 수 있습니다. 가끔 제거되는 가상머신으로 인해 발생하는 주요 클라우드 공급업체의 이벤트들 또한 자동으로 고객의 입장에서 처리됩니다.

![](https://www.datocms-assets.com/2885/1592602085-fully-managed-status.png?fit=max&fm=png&q=80&w=2000)

HCP는 어떠한 클라우드 공급자에 어떠한 해시코프 제품이든 배포할 수 있도록 공통된 워크플로우와 하나의 API 모음을 제공하여 고객이 표준화 하는 것을 가능하게 합니다. HCP 워크플로우의 중요 컴포넌트 중 하나는 클라우드 공급자들의 공통 추상화를 통해 격리된 단일 테넌트 네트워크의 공통 추상화를 제공하는 해시코프 가상 네트워크(HVN) 입니다. 각 HVN은 그 안으로 배포되는 다수의 서비스들을 가질 수 있고, 고객들은 준비된 다이렉트 네트워크 피어링으로 연결할 수 있습니다. 추후 이 HVN 메커니즘으로 서로 다른 클라우드 공급자간의 피어링을 할 수 있고, 제품에 공급자 간의 클러스터링을 할 수 있게 됩니다.

![](https://www.datocms-assets.com/2885/1592602051-createhvnshort.gif?fit=max&fm=gif&q=80&w=2000)

고객들은 SSO를 사용하는 GitHub과 같은 신원 공급자들을 HCP로 통합할 수 있고, 하나의 포털에서 서로 다른 팀과 프로젝트, 클라우드 공급자의 자원에 접근할 수 있는 중앙화된 정책들을 사용합니다.

## AWS 기반 HCP Consul

오늘 [미리 사용](https://www.hashicorp.com/cloud-platform/request-access)해볼 수 있도록 HCP Consul이 플랫폼에 출시되었습니다. 첫번째 클라우드 공급자로 AWS를 지원하도록 출시하였고, 고객의 요청에 따라 차차 더 많은 클라우드 공급자들을 제공할 수 있도록 확장할 예정입니다.

![](https://www.datocms-assets.com/2885/1592605691-hcp-consul-in-blog.png?fit=max&fm=png&q=80&w=2000)

완전 관리형 서비스인 HCP Consul은 안전한 서비스 네트워킹과 EKS, EC2, AWS Lambda를 비롯한 그 외 AWS 서비스들 간의 워크로드를 위한 서비스 메시, 그리고 AWS 환경과 다른 클라우드 환경, Private 데이터 센터를 연결 할 수 있는 가장 쉬운 방법입니다.

HCP 플랫폼은 HCP Consul과 [Azure 기반 해시코프 Consul 서비스 (HCS)](https://www.hashicorp.com/hcs)로 동작하고, 둘 간에 공통적인 주요 서비스들의 모음으로 구성됩니다. 핵심 플랫폼 구성요소들은 [Cadence](https://github.com/uber/cadence)를 기반으로 동작하는 API 서비스 계층과 제품의 배포를 위한 해시코프 테라폼 통합을 포함합니다.
 
![](https://www.datocms-assets.com/2885/1592602092-hcp-architecture.png?fit=max&fm=png&q=80&w=2000)

핵심 서비스들은 각 클라우드 공급자를 위한 Consul의 특정 API 서비스 컴포넌트들을 사용합니다. 특정 제품을 위한 컴포넌트들은 HCP Vault와 앞으로 추가될 제품들을 지원하기 위해 재구현될 것입니다. (핵심 컴포넌트들은 재사용됩니다.)

HCP Consul은 기본적으로 유저별로 Consul 서버 리소스가 격리됩니다. 각 HCP 조직(테넌트)은 AWS 계정별로 격리됩니다. 그리고 나서 HCP에는 각 "해시코프 가상 네트워크" (HVN)을 위한 단일 테넌트 VPC로 배포됩니다. 모든 HCP 자원들은 테넌트 격리를 보장하는 HVN으로 배포됩니다. 이는 다른 사용자로부터의 트래픽이 인터페이스나 호스트로 라우트 될 수 없다는 것을 보장합니다. 게다가 서버들은 AWS의 모범사례에 따라 다중 가용영역으로 배포되고 기본적으로 HCP에서 높은 중복성(redundancy, 이중화)을 제공하고 있습니다.

![](https://www.datocms-assets.com/2885/1592602096-hcp-consul-on-aws-deployment-pattern.png?fit=max&fm=png&q=80&w=2000)

플랫폼에서 생성되는 관리형 VPC로 어플리케이션을 운영하기 위한 고객 VPC에 연결하려면 해시코프 가상 네트워크와 피어링 연결이 필수적입니다.

![](https://www.datocms-assets.com/2885/1592602102-peeringshort.gif?fit=max&fm=gif&q=80&w=2000)

보안그룹과 라우트는 권한 허용으로 직접 AWS통해 설정될 것인지, 피어링 연결을 통해 트래픽을 허용할지 고려해봐야합니다. 항상 그렇듯 AWS에서는 모든 포트가 기본적으로 막혀있어서 연결이 필요한 곳에 올바른 포트로 보안그룹을 생성하여 명시적으로 허용하고 엔드포인트를 설정해야합니다.

Consul 클러스터가 한번 HCP로 배포되고 나면, 고객은 루트 레벨의 토큰을 사용하여 상호작용할 수 있습니다. 토큰이 한번 발행되면 Consul은 일반적인 고객 관리형 Consul과 동일한 방법으로 사용할 수 있습니다. HCP에서는 엔터프라이즈 버전에서 사용할 수 있는 같은 바이너리이 사용됩니다. 오늘 발표된 HCP에서는 Consul 1.8 이상의 버전을 사용할 수 있습니다.

## 다음 절차

HCP Consul은 오늘부터 [미리 사용](https://www.hashicorp.com/cloud-platform/request-access)해볼 수 있고, 올해말 공식 오픈될 예정입니다. HCP Vault는 그 이후로 예정되어 있습니다.

HCP에 대해 더 알아보려면 이 [웹 페이지](https://www.hashicorp.com/cloud-platform)에 방문하시거나 [press release](https://www.globenewswire.com/NewsRoom/ReleaseNg/3556566)를 읽어보시기 바랍니다. 또한 [HCP announcement](https://www.hashicorp.com/resources/hashiconf-digital-keynote-hashicorp-cloud-platform-announcement)와 HashiConf Digital 2020의 [HCP Consul demo](https://www.hashicorp.com/resources/hashicorp-cloud-platform-consul-hashiconf-keynote-demo)를 참고하시기 바랍니다.