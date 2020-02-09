---
title: Azure에서 Ethereum 증명 기관 컨소시엄 솔루션 템플릿 배포
description: Ethereum Authority consortium 솔루션을 사용 하 여 Azure에서 다중 구성원 컨소시엄 Ethereum 네트워크를 배포 하 고 구성 합니다.
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75387682"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Azure에서 Ethereum 증명 기관 컨소시엄 솔루션 템플릿 배포

[Ethereum Authority 컨소시엄 Preview Azure 솔루션 템플릿](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) 을 사용 하 여 최소 Azure 및 Ethereum 지식으로 다중 구성원 컨소시엄 증명 기관 Ethereum 네트워크를 배포, 구성 및 제어할 수 있습니다.

솔루션 템플릿은 각 consortium 멤버가 Microsoft Azure 계산, 네트워킹 및 저장소 서비스를 사용 하 여 블록 체인 네트워크 공간을 프로 비전 하는 데 사용할 수 있습니다. 각 consortium 구성원의 네트워크 공간은 응용 프로그램 또는 사용자가 Ethereum 트랜잭션을 제출 하기 위해 상호 작용할 수 있는 부하 분산 된 유효성 검사기 노드 집합으로 구성 됩니다.

## <a name="choose-an-azure-blockchain-solution"></a>Azure 블록 체인 솔루션 선택

Ethereum authority consortium 솔루션 템플릿을 사용 하도록 선택 하기 전에 시나리오를 사용 가능한 Azure Blockchain 옵션의 일반적인 사용 사례와 비교 합니다.

옵션 | 서비스 모델 | 일반적인 사용 사례
-------|---------------|-----------------
솔루션 템플릿 | IaaS | 솔루션 템플릿은 완전히 구성 된 blockchain 네트워크 토폴로지를 프로 비전 하는 데 사용할 수 있는 Azure Resource Manager 템플릿입니다. 템플릿은 지정 된 blockchain 네트워크 유형에 대해 Microsoft Azure 계산, 네트워킹 및 저장소 서비스를 배포 하 고 구성 합니다.
[Azure Blockchain 서비스](../service/overview.md) | PaaS | Azure Blockchain 서비스 미리 보기는 컨소시엄 Blockchain 네트워크의 대형, 관리 및 관리를 간소화 합니다. PaaS, consortium 관리 또는 계약 및 트랜잭션 개인 정보를 요구 하는 솔루션에는 Azure Blockchain 서비스를 사용 합니다.
[Azure 블록 체인 워크 벤치](../workbench/overview.md) | IaaS 및 PaaS | Azure Blockchain Workbench 미리 보기는 비즈니스 프로세스 및 데이터를 다른 조직과 공유하기 위해 블록체인 애플리케이션을 만들고 배포할 수 있도록 설계된 Azure 서비스 및 기능 컬렉션입니다. 블록 체인 솔루션 프로토타입 또는 블록 체인 응용 프로그램 개념 증명을 위해 Azure Blockchain 워크 벤치를 사용 합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

Ethereum 솔루션 템플릿을 사용 하 여 단일 또는 다중 지역 기반 Ethereum (authority of authority) 컨소시엄 네트워크를 배포할 수 있습니다.

![배포 아키텍처](./media/ethereum-poa-deployment/deployment-architecture.png)

각 컨소시엄 멤버 배포에는 다음이 포함됩니다.

* PoA 유효성 검사기 실행용 가상 머신
* RPC, 피어 링 및 거 버 넌 스 요청 배포를 위한 Azure Load Balancer
* 유효성 검사기 ID 보안을 위한 Azure Key Vault
* 지속적인 네트워크 정보 호스팅 및 임대 조정을 위한 Azure Storage
* 로그 및 성능 통계를 집계하기 위한 Azure Monitor
* 프라이빗 VNet 간 VPN 연결을 허용하기 위한 VNet 게이트웨이(선택 사항)

기본적으로 RPC 및 피어링 엔드포인트는 공용 IP를 통해 액세스할 수 있으므로 구독 및 클라우드 간 연결을 간소화할 수 있습니다. 응용 프로그램 수준 액세스 제어의 경우 [패리티의 권한 부여 계약](https://wiki.parity.io/Permissioning)을 사용할 수 있습니다. 구독 간 연결에 VNet 게이트웨이를 활용 하는 Vpn을 통해 배포 된 네트워크를 지원 합니다. VPN 및 VNet 배포는 더 복잡 하기 때문에 솔루션을 프로토타입 할 때 공용 IP 모델을 시작 하는 것이 좋습니다.

Docker 컨테이너는 안정성 및 모듈화에 사용 됩니다. Azure Container Registry는 각 배포의 일부로 버전이 지정 된 이미지를 호스트 하 고 제공 하는 데 사용 됩니다. 컨테이너 이미지는 다음과 같이 구성됩니다.

* Orchestrator-id 및 거 버 넌 스 계약을 생성 합니다. Id 저장소에 id를 저장 합니다.
* 패리티 클라이언트-id 저장소에서 id를 임대 합니다. 피어를 검색 하 고 연결 합니다.
* EthStats Agent-RPC를 통해 로컬 로그 및 통계를 수집 하 고 Azure Monitor에 정보를 푸시합니다.
* 거 버 넌 스 계약과 상호 작용 하는 웹 인터페이스입니다.

### <a name="validator-nodes"></a>유효성 검사기 노드

인증 증명 프로토콜에서 유효성 검사기 노드는 기존의 채굴기 노드를 대신합니다. 각 유효성 검사기에는 블록 생성 프로세스에 참여할 수 있도록 하는 고유한 Ethereum id가 있습니다. 각 컨소시엄 멤버는 지리적 중복을 위해 5개 지역에 걸쳐 둘 이상의 유효성 검사기 노드를 프로비전할 수 있습니다. 유효성 검사기 노드는 다른 유효성 검사기 노드와 통신하여 기본 분산 원장의 상태에 관한 합의에 도달합니다. 네트워크에 대 한 참여를 보장 하기 위해 각 consortium 구성원은 네트워크의 첫 번째 구성원 보다 많은 유효성 검사기를 사용할 수 없습니다. 예를 들어 첫 번째 멤버가 세 가지 유효성 검사기를 배포 하는 경우 각 멤버는 유효성 검사기를 최대 3 개 까지만 포함할 수 있습니다.

### <a name="identity-store"></a>ID 저장소

Id 저장소는 생성 된 Ethereum id를 안전 하 게 보관 하는 각 멤버의 구독에 배포 됩니다. 각 유효성 검사기에 대해 오케스트레이션 컨테이너는 Ethereum 개인 키를 생성 하 고 Azure Key Vault에 저장 합니다.

## <a name="deploy-ethereum-consortium-network"></a>Ethereum consortium 네트워크 배포

이 연습에서는 다중 파티 Ethereum 컨소시엄 네트워크를 만드는 경우를 가정 하겠습니다. 다음 흐름은 다중 파티 배포의 예입니다.

1. 세 명의 멤버가 각각 MetaMask를 사용하여 Ethereum 계정을 생성합니다.
1. *Member a* 가 Ethereum poa를 배포 하 고 해당 Ethereum 공용 주소를 제공 합니다.
1. *멤버 A*가 *멤버 B* 및 *멤버 C*에게 컨소시엄 URL을 제공합니다.
1. *멤버 B* 및 *멤버 C*가 Ethereum PoA를 배포하여 해당 Ethereum 공용 주소 및 *멤버 A*의 컨소시엄 URL을 제공합니다.
1. *멤버 A*가 *멤버 B*에게 관리자로 투표합니다.
1. *멤버 A* 및 *멤버 B*가 모두 *멤버 C*에게 관리자로 투표합니다.

다음 섹션에서는 네트워크에서 첫 번째 구성원의 공간을 구성 하는 방법을 보여 줍니다.

### <a name="create-resource"></a>리소스 만들기

[Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.

**Blockchain** > **Ethereum-Authority Consortium (미리 보기)** 를 선택 합니다.

### <a name="basics"></a>기본 사항

**기본**에서 배포에 대 한 표준 매개 변수의 값을 지정 합니다.

![기본 사항](./media/ethereum-poa-deployment/basic-blade.png)

매개 변수 | Description | 예제 값
----------|-------------|--------------
새 네트워크를 만들거나 기존 네트워크에 가입 | 새 consortium 네트워크를 만들거나 기존 컨소시엄 네트워크에 가입할 수 있습니다. 기존 네트워크를 조인 하려면 추가 매개 변수가 필요 합니다. | 새로 만들기
메일 주소 | 배포가 완료 되 면 배포에 대 한 정보를 사용 하 여 전자 메일 알림을 받게 됩니다. | 유효한 전자 메일 주소
VM 사용자 이름 | 배포 된 각 VM의 관리자 사용자 이름 | 1-64 영숫자 문자
인증 유형 | 가상 머신을 인증하는 방법. | 암호
암호 | 배포된 각 가상 머신의 관리자 계정 암호. 모든 Vm은 처음에 동일한 암호를 가집니다. 프로 비전 한 후 암호를 변경할 수 있습니다. | 12-72자 
Subscription | 컨소시엄 네트워크를 배포하는 구독 |
리소스 그룹| 컨소시엄 네트워크를 배포하는 리소스 그룹. | myResourceGroup
위치 | 리소스 그룹에 대한 Azure 지역입니다. | 미국 서부 2

**확인**을 선택합니다.

### <a name="deployment-regions"></a>배포 지역

*배포 지역*에서 각에 대 한 지역 및 위치 수를 지정 합니다. 최대 5 개 지역에 배포할 수 있습니다. 첫 번째 지역은 *기본 사항* 섹션의 리소스 그룹 위치와 일치 해야 합니다. 개발 또는 테스트 네트워크의 경우에는 멤버 당 단일 지역을 사용할 수 있습니다. 프로덕션의 경우 고가용성을 위해 둘 이상의 지역에 배포 합니다.

![배포 지역](./media/ethereum-poa-deployment/deployment-regions.png)

매개 변수 | Description | 예제 값
----------|-------------|--------------
지역 수|컨소시엄 네트워크를 배포하는 지역 수| 2
첫 번째 지역 | 컨소시엄 네트워크를 배포하는 첫 번째 지역 | 미국 서부 2
두 번째 지역 | 컨소시엄 네트워크를 배포 하는 두 번째 지역입니다. 영역 수가 둘 이상인 경우에는 추가 지역이 표시 됩니다. | 미국 동부 2

**확인**을 선택합니다.

### <a name="network-size-and-performance"></a>네트워크 크기 및 성능

*네트워크 크기 및 성능*에서 consortium 네트워크 크기에 대 한 입력을 지정 합니다. 유효성 검사기 노드 저장소 크기는 블록 체인의 잠재적 크기를 결정 합니다. 배포 후에 크기를 변경할 수 있습니다.

![네트워크 크기 및 성능](./media/ethereum-poa-deployment/network-size-and-performance.png)

매개 변수 | Description | 예제 값
----------|-------------|--------------
부하가 분산된 유효성 검사기 노드 수 | 네트워크의 일부로 프로 비전 할 유효성 검사기 노드의 수입니다. | 2
유효성 검사기 노드 스토리지 성능 | 배포 된 각 유효성 검사기 노드에 대 한 관리 디스크의 유형입니다. 가격 책정에 대 한 자세한 내용은 [저장소 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) 을 참조 하세요. | 표준 SSD
유효성 검사기 노드 가상 머신 크기 | 유효성 검사기 노드에 사용되는 가상 머신 크기입니다. 가격 책정에 대 한 자세한 내용은 [가상 머신 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 을 참조 하세요. | 표준 D2 v3

가상 컴퓨터 및 저장소 계층은 네트워크 성능에 영향을 줍니다.  다음 표를 사용 하 여 비용 효율성을 선택할 수 있습니다.

가상 머신 SKU|스토리지 계층|가격|처리량|대기 시간
---|---|---|---|---
F1|표준 SSD|low|low|high
D2_v3|표준 SSD|중간|중간|중간
F16s|프리미엄 SSD|high|high|low

**확인**을 선택합니다.

### <a name="ethereum-settings"></a>Ethereum 설정

*Ethereum 설정*에서 Ethereum 관련 구성 설정을 지정 합니다.

![Ethereum 설정](./media/ethereum-poa-deployment/ethereum-settings.png)

매개 변수 | Description | 예제 값
----------|-------------|--------------
컨소시엄 멤버 ID | 컨소시엄 네트워크에 참여 하는 각 멤버와 연결 된 ID입니다. 이는 충돌을 방지 하기 위해 IP 주소 공간을 구성 하는 데 사용 됩니다. 개인 네트워크의 경우 구성원 ID는 동일한 네트워크에 있는 여러 조직에서 고유 해야 합니다.  고유 멤버 ID는 같은 조직이 여러 지역에 배포하는 경우에도 필요합니다. 충돌이 발생 하지 않도록 하기 위해 다른 조인 멤버와 공유 해야 하므로이 매개 변수의 값을 기록해 둡니다. 유효한 범위는 0에서 255 까지입니다. | 0
네트워크 ID | 배포하는 컨소시엄 Ethereum 네트워크의 네트워크 ID입니다. 각 Ethereum 네트워크에는 공용 네트워크에 대한 ID를 1로 하는 자체의 네트워크 ID가 있습니다. 유효한 범위는 5에서 999999999 까지입니다. | 10101010
관리자 Ethereum 주소 | PoA 거 버 넌 스에 참여 하는 데 사용 되는 Ethereum 계정 주소입니다. MetaMask를 사용 하 여 Ethereum 주소를 생성할 수 있습니다. |
고급 옵션 | Ethereum 설정에 대한 고급 옵션 | 사용
공용 IP를 사용 하 여 배포 | 개인 VNet을 선택 하면 네트워크는 VNet 게이트웨이 뒤에 배포 되 고 피어 링 액세스를 제거 합니다. 사설 VNet의 경우 연결을 호환 하려면 모든 멤버가 VNet 게이트웨이를 사용 해야 합니다. | 공용 IP
블록 가스 한도 | 네트워크의 시작 블록 가스 한도입니다. | 50000000
블록 다시 봉인 기간(초) | 네트워크에 트랜잭션이 없을 때 빈 블록이 작성되는 빈도입니다. 빈도가 높으면 블록이 더 빨리 작성되지만 스토리지 비용이 증가합니다. | 15
트랜잭션 권한 계약 | 트랜잭션 권한 지정 계약에 대한 바이트 코드입니다. 허용 되는 Ethereum 계정 목록으로 스마트 계약 배포 및 실행을 제한 합니다. |

**확인**을 선택합니다.

### <a name="monitoring"></a>모니터링

모니터링을 통해 네트워크에 대 한 로그 리소스를 구성할 수 있습니다. 모니터링 에이전트는 네트워크 상태를 신속 하 게 확인 하거나 문제를 디버그 하는 기능을 제공 하는 네트워크에서 유용한 메트릭과 로그를 수집 하 고 표시 합니다.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

매개 변수 | Description | 예제 값
----------|-------------|--------------
모니터링 | 모니터링을 사용 하도록 설정 하는 옵션 | 사용
기존 Azure Monitor 로그에 연결 | 새 Azure Monitor 로그 인스턴스를 만들거나 기존 인스턴스에 조인 하는 옵션 | 새로 만들기
위치 | 새 인스턴스가 배포 되는 지역입니다. | 미국 동부
기존 log analytics 작업 영역 ID (기존 Azure Monitor 로그에 연결 = 기존 조인)|기존 Azure Monitor logs 인스턴스의 작업 영역 ID||해당 없음
기존 log analytics 기본 키 (기존 Azure Monitor 로그에 연결 = 기존 조인)|기존 Azure Monitor logs 인스턴스에 연결 하는 데 사용 되는 기본 키입니다.||해당 없음

**확인**을 선택합니다.

### <a name="summary"></a>요약

요약을 클릭 하 여 지정 된 입력을 검토 하 고 기본 배포 전 유효성 검사를 실행 합니다. 을 배포 하기 전에 템플릿 및 매개 변수를 다운로드할 수 있습니다.

**만들기** 를 선택 하 여 배포 합니다.

배포에 VNet 게이트웨이가 포함 된 경우 배포는 45 ~ 50 분까지 걸릴 수 있습니다.

## <a name="deployment-output"></a>배포 출력

배포가 완료 되 면 Azure Portal를 사용 하 여 필요한 매개 변수에 액세스할 수 있습니다.

### <a name="confirmation-email"></a>확인 이메일

전자 메일 주소 ([기본 사항 섹션](#basics))를 제공 하면 배포 정보 및이 설명서에 대 한 링크가 포함 된 전자 메일이 전송 됩니다.

![배포 이메일](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>포털

배포가 성공적으로 완료 되 고 모든 리소스가 프로 비전 되 면 리소스 그룹에서 출력 매개 변수를 볼 수 있습니다.

1. 포털에서 리소스 그룹으로 이동 합니다.
1. **개요 > 배포**를 선택 합니다.

    ![리소스 그룹 개요](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Microsoft azure-blockchain **. 에테르 스코프-...** 배포를 선택 합니다.
1. **출력** 섹션을 선택 합니다.

    ![배포 출력](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>컨소시엄 확장

컨소시엄을 확장하려면 먼저 실제 네트워크를 연결해야 합니다. VPN 뒤에 배포 하는 경우 [VNet 게이트웨이 연결](#connecting-vnet-gateways) 새 구성원 배포의 일부로 네트워크 연결 구성 섹션을 참조 하세요. 배포가 완료 되 면 [거 버 넌 스 앱](#governance-dapp) 을 사용 하 여 네트워크 관리자가 됩니다.

### <a name="new-member-deployment"></a>새 멤버 배포

다음 정보를 조인 멤버와 공유합니다. 정보는 배포 후 전자 메일 또는 포털 배포 출력에서 찾을 수 있습니다.

* 컨소시엄 데이터 URL
* 배포한 노드 수
* VNet 게이트웨이 리소스 ID(VPN을 사용하는 경우)

배포 구성원은 다음 지침을 사용 하 여 네트워크 상태를 배포할 때 동일한 Ethereum 증명 기관 컨소시엄 솔루션 템플릿을 사용 해야 합니다.

* **기존 조인**을 선택합니다.
* 공평 하 게 표현할 수 있도록 네트워크에서 나머지 멤버와 동일한 수의 유효성 검사기 노드를 선택 합니다.
* 동일한 Admin Ethereum 주소를 사용 합니다.
* *Ethereum 설정* 에서 제공 된 *Consortium 데이터 Url* 사용
* 네트워크의 나머지 부분이 VPN 뒤에 있는 경우 고급 섹션에서 **개인 VNet** 을 선택 합니다.

### <a name="connecting-vnet-gateways"></a>VNet 게이트웨이 연결

이 섹션은 개인 VNet을 사용 하 여 배포 하는 경우에만 필요 합니다. 공용 IP 주소를 사용 하는 경우이 섹션을 건너뛸 수 있습니다.

개인 네트워크의 경우 다른 구성원은 VNet 게이트웨이 연결을 통해 연결 됩니다. 구성원이 네트워크에 연결 하 여 트랜잭션 트래픽을 볼 수 있으려면 기존 구성원은 VPN 게이트웨이에서 최종 구성을 수행 하 여 연결을 수락 해야 합니다. 조인 하는 멤버의 Ethereum 노드는 연결이 설정 될 때까지 실행 되지 않습니다. 단일 실패 지점이 발생 하는 가능성을 줄이려면 컨소시엄에서 중복 된 네트워크 연결을 만듭니다.

새 멤버가 배포되면 기존 멤버는 새 멤버에 대한 VNet 게이트웨이 연결을 설정하여 양방향 연결을 완료해야 합니다. 기존 멤버에는 다음이 필요 합니다.

* 연결 멤버의 VNet 게이트웨이 ResourceID입니다. [배포 출력](#deployment-output)을 참조 하세요.
* 공유 연결 키입니다.

기존 멤버는 다음 PowerShell 스크립트를 실행하여 연결을 완료해야 합니다. 포털에서 오른쪽 위에 있는 탐색 모음에 있는 Azure Cloud Shell를 사용할 수 있습니다.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>서비스 모니터링

배포 전자 메일의 링크를 따라 이동 하거나 배포 출력 [OMS_PORTAL_URL]에서 매개 변수를 찾으면 Azure Monitor 포털을 찾을 수 있습니다.

포털에서 먼저 고급 네트워크 통계와 노드 개요를 표시합니다.

![모니터 범주](./media/ethereum-poa-deployment/monitor-categories.png)

**노드 개요** 를 선택 하면 노드당 인프라 통계가 표시 됩니다.

![노드 통계](./media/ethereum-poa-deployment/node-stats.png)

**네트워크** 통계를 선택 하면 Ethereum 네트워크 통계가 표시 됩니다.

![네트워크 통계](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

모니터링 로그를 쿼리하여 실패를 조사 하거나 임계값 경고를 설정할 수 있습니다. 다음 쿼리는 *로그 검색* 도구에서 실행할 수 있는 예입니다.

둘 이상의 유효성 검사기 쿼리에서 보고 된 목록 블록은 체인 포크를 찾는 데 도움이 될 수 있습니다.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

5 분 버킷의 평균으로 지정 된 유효성 검사기 노드의 평균 피어 수를 가져옵니다.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH 액세스

보안상의 이유로 SSH 포트 액세스는 기본적으로 네트워크 그룹 보안 규칙에 의해 거부됩니다. PoA 네트워크의 가상 머신 인스턴스에 액세스 하려면 다음 보안 규칙을 *허용*으로 변경 해야 합니다.

1. Azure Portal에서 배포 된 리소스 그룹의 **개요** 섹션으로 이동 합니다.

    ![SSH 개요](./media/ethereum-poa-deployment/ssh-overview.png)

1. 액세스 하려는 VM의 지역에 대 한 **네트워크 보안 그룹** 을 선택 합니다.

    ![SSH NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

1. **허용-ssh** 규칙을 선택 합니다.

    ![allow-ssh](./media/ethereum-poa-deployment/ssh-allow.png)

1. **작업** 을 **허용** 으로 변경

    ![SSH 사용 허용](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. **저장**을 선택합니다. 변경 내용을 적용 하는 데 몇 분 정도 걸릴 수 있습니다.

제공 된 관리자 사용자 이름 및 암호/s s p 키를 사용 하 여 SSH를 통해 유효성 검사기 노드의 가상 컴퓨터에 원격으로 연결할 수 있습니다. 첫 번째 유효성 검사기 노드에 액세스 하는 SSH 명령이 템플릿 배포 출력에 나열 됩니다. 예:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

추가 트랜잭션 노드를 가져오려면 포트 번호를 1 씩 증가 시킵니다.

둘 이상의 지역에 배포 하는 경우 명령을 해당 지역에서 부하 분산 장치의 DNS 이름이 나 IP 주소로 변경 합니다. 다른 지역의 DNS 이름 또는 IP 주소를 찾으려면 명명 규칙을 사용 하 여 리소스 **\*\*\*\*\*-lbpip-reg\#** 를 찾고 해당 dns 이름 및 IP 주소 속성을 확인 합니다.

## <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager 부하 분산

Azure Traffic Manager는 들어오는 트래픽을 여러 지역의 여러 배포로 라우팅하여 가동 중지 시간을 줄이고 PoA 네트워크의 응답성을 향상시킬 수 있습니다. 기본 제공 되는 상태 검사 및 자동 경로 바꾸기는 RPC 엔드포인트과 거 버 넌 스 앱의 고가용성을 보장 하는 데 도움이 됩니다. 이 기능은 여러 지역에 배포하고 프로덕션을 준비하는 경우에 유용합니다.

자동 장애 조치 (failover)를 통해 PoA 네트워크 가용성을 향상 시키기 위해 Traffic Manager를 사용 합니다. 또한 Traffic Manager를 사용 하 여 네트워크 대기 시간이 가장 낮은 Azure 위치로 최종 사용자를 라우팅하여 네트워크 응답성을 높일 수 있습니다.

Traffic Manager 프로필을 만들려는 경우 네트워크에 액세스하기 위해 프로필의 DNS 이름을 사용할 수 있습니다. 다른 컨소시엄 멤버가 네트워크에 추가되면 Traffic Manager를 사용하여 배포된 유효성 검사기에서 부하를 분산시킬 수도 있습니다.

### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

1. [Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
1. **Traffic Manager 프로필**을 검색 합니다.

    ![Azure Traffic Manager 검색](./media/ethereum-poa-deployment/traffic-manager-search.png)

    프로필에 고유한 이름을 지정 하 고 PoA 배포에 사용 된 리소스 그룹을 선택 합니다.

1. **만들기** 를 선택 하 여 배포 합니다.

    ![Traffic Manager 만들기](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. 배포 된 후에는 리소스 그룹에서 인스턴스를 선택 합니다. Traffic manager에 액세스할 DNS 이름은 개요 탭에서 찾을 수 있습니다.

    ![Traffic Manager DNS 찾기](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. **엔드포인트** 탭을 선택 하 고 **추가** 단추를 선택 합니다.
1. 그런 다음 엔드포인트에 고유한 이름을 지정하고
1. **대상 리소스 종류**에 대해 **공용 IP 주소**를 선택 합니다.
1. 첫 번째 지역의 부하 분산 장치에 대 한 공용 IP 주소를 선택 합니다.

    ![Traffic Manager 라우팅](./media/ethereum-poa-deployment/traffic-manager-routing.png)

배포된 네트워크의 각 지역에 대해 반복합니다. 엔드포인트이 **활성화** 된 상태 이면 자동으로 로드 되 고, traffic MANAGER의 DNS 이름에서 지역 균형이 조정 됩니다. 이제 문서의 다른 단계에서 [CONSORTIUM_DATA_URL] 매개 변수 대신이 DNS 이름을 사용할 수 있습니다.

## <a name="data-api"></a>데이터 API

각 컨소시엄 멤버는 다른 멤버가 네트워크에 연결하는 데 필요한 정보를 호스팅합니다. 쉽게 연결할 수 있도록 각 멤버는 데이터 API 엔드포인트에서 연결 정보 집합을 호스팅합니다.

기존 멤버는 멤버의 배포 전에 [CONSORTIUM_DATA_URL]를 제공 합니다. 배포 시 조인 멤버는 다음 엔드포인트의 JSON 인터페이스에서 정보를 검색합니다.

`<CONSORTIUM_DATA_URL>/networkinfo`

응답에는 멤버 (Genesis block, Validator Set 계약 ABI, bootnodes)를 조인 하는 데 유용한 정보와 기존 멤버 (유효성 검사기 주소)에 유용한 정보가 포함 되어 있습니다. 이 표준화를 사용 하 여 클라우드 공급자 간에 컨소시엄을 확장할 수 있습니다. 이 API는 다음과 같은 구조를 사용 하 여 JSON 형식의 응답을 반환 합니다.

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>거버넌스 DApp

인증 증명의 중심에는 분산 거버넌스가 있습니다. 권한 증명은 네트워크를 정상적으로 유지 하기 위해 허용 되는 네트워크 인증 목록에 의존 하므로이 사용 권한 목록을 수정 하는 데에는 공평 한 메커니즘을 제공 하는 것이 중요 합니다. 각 배포에는이 허용 된 목록의 체인 거 버 넌 스에 대 한 일련의 스마트 계약과 포털이 제공 됩니다. 제안된 변경이 컨소시엄 멤버의 과반수 표결에 도달하면 해당 변경이 시행됩니다. 투표를 사용 하면 새 합의 참가자를 추가 하거나 손상 된 참가자를 사용 하는 투명 한 방식으로 참가자를 제거할 수 있습니다.

거버넌스 DApp는 네트워크에서 기관을 관리하는 데 사용되는 미리 배포된 [스마트 계약](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) 집합 및 웹 애플리케이션입니다. 기관은 관리 id 및 유효성 검사기 노드로 분류 됩니다.
관리자는 유효성 검사 노드 집합에 대 한 참여를 위임할 수 있습니다. 관리자는 네트워크 내부 또는 외부에서 다른 관리자에게 투표할 수도 있습니다.

![거버넌스 DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **분산 거 버 넌 스:** 네트워크 기관의 변경 내용은 선택한 관리자에의 한 체인 투표를 통해 관리 됩니다.
* **유효성 검사기 위임:** 기관은 각 PoA 배포에 설정 된 유효성 검사기 노드를 관리할 수 있습니다.
* **감사 가능한 변경 기록:** 각 변경 내용은 투명 및 auditability을 제공 하는 블록 체인에 기록 됩니다.

### <a name="getting-started-with-governance"></a>거버넌스 시작

거 버 넌 스를 통해 모든 종류의 트랜잭션을 수행 하려면 Ethereum을 사용 해야 합니다. 가장 간단한 방법은 [MetaMask](https://metamask.io)와 같은 브라우저 내 전자 지갑를 사용 하는 것입니다. 그러나 이러한 스마트 계약은 네트워크에 배포 되므로 거 버 넌 스 계약에 대 한 상호 작용을 자동화할 수도 있습니다.

MetaMask를 설치한 후 브라우저에서 거버넌스 DApp로 이동합니다.  배포 출력에서 Azure Portal를 통해 URL을 찾을 수 있습니다.  브라우저 내 전자 지갑이 설치 되어 있지 않은 경우 아무 작업도 수행할 수 없습니다. 그러나 관리자 상태를 볼 수 있습니다.  

### <a name="becoming-an-admin"></a>관리자 되기

네트워크에 배포 된 첫 번째 구성원 인 경우 자동으로 관리자가 되며 패리티 노드가 유효성 검사기로 나열 됩니다. 네트워크에 가입 하는 경우 과반수 (50% 이상)로 관리자에 게 투표 해야 합니다. 기존 관리 집합의입니다. 관리자가 되지 않도록 선택한 경우에도 노드는 블록 체인을 동기화 하 고 유효성을 검사 합니다. 그러나 블록 생성 프로세스에는 참여 하지 않습니다. 관리자가 될 투표 프로세스를 시작 하려면 **추천** 를 선택 하 고 Ethereum 주소와 별칭을 입력 합니다.

![추천](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>후보

**후보** 탭을 선택 하면 현재 후보 관리자 집합이 표시 됩니다.  현재 관리자의 과반수 투표에 도달 하면 후보는 관리자로 승격 됩니다.  후보에 투표 하려면 행을 선택 하 고 **투표**를 선택 합니다. 투표를 변경 하는 경우 후보를 선택 하 고 **Rescind 투표**를 선택 합니다.

![후보](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>관리자

**관리자** 탭은 현재 관리자 집합을 표시 하 고 투표 기능을 제공 합니다.  관리자가 50%를 초과 하면 네트워크에서 관리자 권한으로 제거 됩니다. 관리자가 소유 하는 모든 유효성 검사기 노드는 유효성 검사기 상태를 상실 하 고 네트워크에서 트랜잭션 노드가 됩니다. 관리자는 여러 가지 이유로 제거 될 수 있습니다. 그러나 미리 정책에 동의 하는 것은 컨소시엄의 것입니다.

![관리자](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>유효성 검사기

**유효성 검사기** 탭을 선택 하면 인스턴스에 대 한 현재 배포 된 패리티 노드와 현재 상태 (노드 유형)가 표시 됩니다. 이 보기는 현재 배포 된 컨소시엄 멤버를 나타내므로 각 consortium 구성원에는이 목록에 있는 다른 유효성 검사기 집합이 있습니다. 인스턴스를 새로 배포 하 고 유효성 검사기를 추가 하지 않은 경우에는 **유효성 검사기를 추가할**수 있는 옵션이 제공 됩니다. 유효성 검사기를 추가 하면 자동으로 지역적으로 분산 된 패리티 노드 집합이 선택 되 고 유효성 검사기 집합에 할당 됩니다. 허용 된 용량 보다 더 많은 노드를 배포한 경우 나머지 노드는 네트워크의 트랜잭션 노드가 됩니다.

각 유효성 검사기의 주소는 Azure의 [ID 저장소](#identity-store)를 통해 자동으로 할당됩니다.  노드가 다운 되 면 해당 id를 내어 줍니다 배포의 다른 노드가 해당 위치를 사용할 수 있습니다. 이 프로세스를 통해 합의 참여를 항상 사용할 수 있습니다.

![유효성 검사기](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>컨소시엄 이름

모든 관리자가 컨소시엄 이름을 업데이트할 수 있습니다.  왼쪽 위에서 기어 아이콘을 선택 하 여 컨소시엄 이름을 업데이트 합니다.

### <a name="account-menu"></a>계정 메뉴

오른쪽 위에서은 Ethereum 계정 별칭 및 identicon입니다.  관리자 인 경우 별칭을 업데이트할 수 있습니다.

![계정](./media/ethereum-poa-deployment/governance-dapp-account.png)

## Ethereum 개발<a id="tutorials"></a>

스마트 계약을 컴파일, 배포 및 테스트 하기 위해 Ethereum 개발에 고려할 수 있는 몇 가지 옵션은 다음과 같습니다.
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) -클라이언트 기반 Ethereum 개발 환경
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) -브라우저 기반 및 로컬 Ethereum 개발 환경

### <a name="compile-deploy-and-execute-smart-contract"></a>스마트 계약 컴파일, 배포 및 실행

다음 예제에서는 간단한 스마트 계약을 만듭니다. Truffle를 사용 하 여 스마트 계약을 컴파일하여 blockchain 네트워크에 배포 합니다. 배포 된 후에는 트랜잭션을 통해 스마트 계약 함수를 호출 합니다.

#### <a name="prerequisites"></a>필수 조건

* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)를 설치합니다. Python은 Truffle 및 Web3에 필요 합니다. 설치 옵션을 선택 하 여 경로에 Python을 포함 합니다.
* Truffle v 5.0.5 `npm install -g truffle@v5.0.5`를 설치 합니다. Truffle을 사용하려면 [Node.js](https://nodejs.org), [Git](https://git-scm.com/)를 포함한 여러 도구를 설치해야 합니다. 자세한 내용은 [Truffle 설명서](https://github.com/trufflesuite/truffle)를 참조 하세요.

### <a name="create-truffle-project"></a>Truffle 프로젝트 만들기

스마트 계약을 컴파일하고 배포 하려면 먼저 Truffle 프로젝트를 만들어야 합니다.

1. 명령 프롬프트 또는 셸을 엽니다.
1. `HelloWorld`라는 폴더를 만듭니다.
1. 새 `HelloWorld` 폴더로 디렉터리를 변경 합니다.
1. `truffle init`명령을 사용 하 여 새 Truffle 프로젝트를 초기화 합니다.

    ![새 Truffle 프로젝트 만들기](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>스마트 계약 추가

Truffle 프로젝트의 **계약** 하위 디렉터리에서 스마트 계약을 만듭니다.

1. Truffle 프로젝트의 **계약** 하위 디렉터리에 있는 명명 된 `postBox.sol`에 파일을 만듭니다.
1. **Postbox**에 다음 농담 코드를 추가 합니다.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Truffle를 사용 하 여 스마트 계약 배포

Truffle 프로젝트에는 blockchain 네트워크 연결 정보에 대 한 구성 파일이 포함 되어 있습니다. 네트워크에 대 한 연결 정보를 포함 하도록 구성 파일을 수정 합니다.

> [!WARNING]
> 네트워크를 통해 Ethereum 개인 키를 보내지 마세요. 각 트랜잭션을 로컬에서 먼저 서명한 다음, 네트워크를 통해 서명된 트랜잭션을 보내도록 합니다.

1. [Blockchain 네트워크를 배포할 때 사용 되는 Ethereum 관리자 계정](#ethereum-settings)에 대 한 니모닉 구가 필요 합니다. MetaMask를 사용 하 여 계정을 만든 경우 MetaMask에서 니모닉을 검색할 수 있습니다. MetaMask 확장의 오른쪽 위에 있는 관리자 계정 아이콘을 선택 하 고 **설정 > 보안 & 개인 정보 > 초기값 단어**를 표시 합니다.
1. Truffle 프로젝트의 `truffle-config.js` 내용을 다음 콘텐츠로 바꿉니다. 자리 표시자 엔드포인트 및 니모닉 값을 바꿉니다.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Truffle HD 전자 프로그램 공급자를 사용 하 고 있으므로 `npm install truffle-hdwallet-provider --save`명령을 사용 하 여 프로젝트에 모듈을 설치 합니다.

Truffle는 마이그레이션 스크립트를 사용 하 여 블록 체인 네트워크에 스마트 계약을 배포 합니다. 새 스마트 계약을 배포 하려면 마이그레이션 스크립트가 필요 합니다.

1. 새 마이그레이션을 추가 하 여 새 계약을 배포 합니다. Truffle 프로젝트의 migration 하위 디렉터리에 파일 **`2_deploy_contracts.js`를 만듭니다** .

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Truffle 마이그레이션 명령을 사용 하 여 PoA 네트워크에 배포 합니다. Truffle 프로젝트 디렉터리의 명령 프롬프트에서 다음을 실행 합니다.

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>스마트 계약 함수 호출

이제 스마트 계약이 배포 되었으므로 트랜잭션을 전송 하 여 함수를 호출할 수 있습니다.

1. Truffle 프로젝트 디렉터리에서 `sendtransaction.js`라는 새 파일을 만듭니다.
1. **Sendtransaction**에 다음 콘텐츠를 추가 합니다.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Truffle execute 명령을 사용 하 여 스크립트를 실행 합니다.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![트랜잭션을 통해 함수를 호출 하는 스크립트를 실행 합니다.](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>WebAssembly(WASM) 지원

WebAssembly 지원은 새로 배포된 PoA 네트워크에서 이미 사용하도록 설정되어 있습니다. 이를 통해 스마트 계약을 웹 어셈블리로 변환 컴파일되는 모든 언어(Rust, C, C++)로 개발할 수 있습니다. 자세한 내용은 패리티 기술 개요: 패리티 [기술](https://github.com/paritytech/pwasm-tutorial) [개요](https://wiki.parity.io/WebAssembly-Home) 를 참조 하세요.

## <a name="faq"></a>FAQ

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>전송 하지 않은 네트워크에 많은 트랜잭션이 있습니다. 이러한 트랜잭션은 어디서 오는 것인가요?

[개인 API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)를 잠금 해제하는 것은 안전하지 않습니다. 봇은 잠금 해제된 Ethereum 계정을 수신 대기하고 자금을 고갈시키려고 시도합니다. 봇은 이러한 계정에 실제 Ether가 있다고 가정하고 잔액을 가장 먼저 빼돌리려고 시도합니다. 개인 API는 네트워크에서 사용하도록 설정하지 않습니다. 대신 MetaMask 등의 전자 메일을 사용 하 여 수동으로 또는 프로그래밍 방식으로 트랜잭션을 미리 서명 합니다.

### <a name="how-to-ssh-onto-a-vm"></a>VM에 SSH를 연결하려면 어떻게 할까요?

SSH 포트는 보안상의 이유로 표시되지 않습니다. [이 가이드](#ssh-access)에 따라 SSH 포트를 사용하도록 설정합니다.

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>멤버 또는 트랜잭션 노드를 설정하고 감사하려면 어떻게 할까요?

트랜잭션 노드는 네트워크에 피어 링 합의에 참여 하지 않는 패리티 클라이언트 집합입니다. 이러한 노드는 여전히 Ethereum 트랜잭션을 제출하고 스마트 계약 상태를 읽는 데 사용할 수 있습니다. 이 메커니즘은 네트워크의 인증 기관이 아닌 컨소시엄 구성원에 게 원하게을 제공 하는 데 사용할 수 있습니다. 이를 위해서는 [컨소시엄 성장](#growing-the-consortium)의 단계를 따르세요.

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>MetaMask 트랜잭션이 오래 걸리는 이유는 무엇인가요?

트랜잭션이 올바른 순서로 수신되도록 하기 위해 각 Ethereum 트랜잭션에는 증분 논스(nonce)가 제공됩니다. 다른 네트워크에서 MetaMask의 계정을 사용한 경우 nonce 값을 다시 설정 해야 합니다. 설정 아이콘 (3 개 모음), 설정, 계정 다시 설정을 클릭 합니다. 트랜잭션 기록이 지워지고, 이제 트랜잭션을 다시 제출할 수 있습니다.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>MetaMask에서 가스 요금을 지정해야 하나요?

인증 증명 컨소시엄에서는 Ethereum이 사용되지 않습니다. 따라서 MetaMask에서 트랜잭션을 제출할 때 가스 요금을 지정할 필요가 없습니다.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Azure OMS로의 프로비전 실패로 인해 배포가 실패하면 어떻게 해야 하나요?

모니터링은 선택적 기능입니다. Azure Monitor 리소스를 성공적으로 프로 비전 할 수 없기 때문에 배포가 실패 하는 드문 경우 Azure Monitor 없이 다시 배포할 수 있습니다.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>공용 IP 배포는 사설망 배포와 호환되나요?

아닙니다. 피어 링에는 양방향 통신이 필요 하므로 전체 네트워크는 공용 또는 개인 중 하나 여야 합니다.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>인증 증명의 예상 트랜잭션 처리량은 어떻게 되나요?

트랜잭션 처리량은 네트워크 토폴로지 및 트랜잭션 유형에 따라 크게 달라집니다. 단순 트랜잭션을 사용한 벤치마크 테스트 결과 여러 지역에 걸쳐 배포된 네트워크에서 초당 평균 트랜잭션 수는 400개였습니다.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>스마트 계약 이벤트를 구독하려면 어떻게 해야 하나요?

Ethereum 인증 증명에서는 이제 웹 소켓을 지원합니다.  배포 출력을 확인 하 여 웹 소켓 URL 및 포트를 찾습니다.

## <a name="next-steps"></a>다음 단계

Azure Blockchain 솔루션에 대 한 자세한 내용은 [Azure blockchain 설명서](https://docs.microsoft.com/azure/blockchain/)를 참조 하세요.
