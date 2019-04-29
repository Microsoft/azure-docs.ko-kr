---
title: Azure의 Hyperledger 패브릭 컨소시엄 네트워크
description: Hyperledger 패브릭 컨소시엄 네트워크를 배포하고 구성하기 위한 솔루션 템플릿
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/23/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: ce1afbd5499e798888e77f52d7b652e7e1f548fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690662"
---
# <a name="hyperledger-fabric-consortium-network"></a>Hyperledger 패브릭 컨소시엄 네트워크

Hyperledger 패브릭 컨소시엄 솔루션 템플릿을 사용하여 Azure에서 Hyperledger 패브릭 컨소시엄 네트워크를 배포하고 구성할 수 있습니다.

이 문서를 읽고 나면:

- 블록체인, Hyperledger 패브릭 및 복잡한 컨소시엄 네트워크 아키텍처에 대한 실무 지식을 얻을 수 있습니다.
- Azure Portal에서 Hyperledger 패브릭 컨소시엄 네트워크를 배포하고 구성하는 방법을 알아볼 수 있습니다.

## <a name="about-blockchain"></a>블록체인 정보

블록체인 커뮤니티를 처음 접하는 경우 이 솔루션 템플릿은 Azure에서 이 기술을 쉽고 구성 가능한 방법으로 배울 수 있는 좋은 기회입니다. 블록체인은 비트코인의 근간이 되는 기술이지만, 단순히 가상 화폐를 구현하는 것보다 훨씬 많은 가능성을 갖고 있습니다. 블록체인은 기존 데이터베이스, 분산 시스템 그리고 공격에 대한 불변성, 안정성, 감사 및 복원력을 보장하는 보안 다자간 계산을 지원하는 암호화 기술의 복합체입니다. 여러 프로토콜이 여러 메커니즘을 사용하여 이러한 특성을 제공합니다. [Hyperledger 패브릭](https://github.com/hyperledger/fabric)은 이러한 프로토콜 중 하나입니다.

## <a name="consortium-architecture-on-azure"></a>Azure의 응용 프로그램 아키텍처

Azure에서 Hyperledger 패브릭을 사용할 수 있도록 지원되는 두 가지 기본 배포 유형이 있습니다. 이러한 배포는 원하는 대상에 따라 다양한 토폴로지를 수용하도록 설계되었습니다.

- **단일 가상 머신, 개발자 서버** - 이 배포 유형은 Hyperledger 패브릭에 빌드된 솔루션을 빌드하고 테스트하는 데 사용되는 개발 환경으로 설계되었습니다.
- **다중 가상 머신, 스케일 아웃 배포** - 이 배포 유형은 공유 환경을 활용하는 여러 참가자의 컨소시엄을 모델링하는 환경용으로 설계되었습니다.

두 배포에서 Hyperledger 패브릭의 핵심을 이루는 구성 요소는 동일합니다.  배포의 차이점은 이러한 구성 요소의 확장 방법입니다.

- **CA 노드**: 네트워크의 ID에 사용되는 인증서를 생성하는 데 사용되는 인증 기관을 실행하는 노드입니다.
- **정렬자 노드**: 총 명령 브로드캐스트 또는 원자성 트랜잭션 같은 전송 보증을 구현하는 통신 서비스를 실행하는 노드입니다.
- **피어 노드**: 트랜잭션을 커밋하고 분산 원장의 상태와 복사본을 유지하는 노드입니다.
- **CouchDB 노드**: 상태 데이터베이스를 보유할 수 있는 CouchDB 서비스를 실행하고 chaincode 데이터의 풍부한 쿼리를 제공하여 단순 키/값에서 JSON 형식 스토리지로 확장할 수 있는 노드입니다.

### <a name="single-virtual-machine-architecture"></a>단일 가상 머신 아키텍처

앞에서 언급한 대로 단일 가상 머신 아키텍처는 개발자가 애플리케이션 개발에 사용되는 작은 메모리 공간 서버를 보유할 수 있도록 빌드되었습니다. 표시된 모든 컨테이너가 단일 가상 머신에서 실행되고 있습니다. 정렬 서비스는 이 구성을 위해 [SOLO](https://github.com/hyperledger/fabric/tree/master/orderer)를 사용하고 있습니다. 이 구성은 ‘내결함성 정렬 서비스가 아니라’ 개발 용도의 경량으로 설계되었습니다.

![단일 가상 머신 아키텍처](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>다중 가상 머신 아키텍처

다중 가상 머신, 스케일 아웃 아키텍처는 각 구성 요소의 고가용성 및 크기 조정을 핵심으로 하여 빌드되었습니다. 이 아키텍처는 프로덕션 등급 배포에 훨씬 더 적합합니다.

![다중 가상 머신 아키텍처](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>시작

시작하려면 여러 가상 머신과 표준 스토리지 계정의 배포를 지원할 수 있는 Azure 구독이 필요합니다. Azure 구독이 없는 경우 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

구독이 있으면 [Azure Portal](https://portal.azure.com)로 이동합니다. **리소스 만들기 > 블록체인 > Hyperledger 패브릭 컨소시엄**을 선택합니다.

![Hyperledger 패브릭 단일 멤버 블록체인 마켓플레이스 템플릿](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>배포

**Hyperledger 패브릭 컨소시엄** 템플릿에서 **만들기**를 선택합니다.

템플릿 배포에서 다중 노드 [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) 네트워크를 구성하는 과정을 안내합니다. 배포 흐름은 기본 사항, 컨소시엄 네트워크 설정, 패브릭 구성 및 선택적 구성 요소의 네 단계로 구분됩니다.

### <a name="basics"></a>기본 사항

**기본 사항**에서 모든 배포에 대해 표준 매개 변수 값을 지정합니다. 예를 들어 구독, 리소스 그룹 및 기본 가상 머신 속성입니다.

![기본 사항](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| 매개 변수 이름 | 설명 | 허용되는 값 |
|---|---|---|
**리소스 접두사** | 배포의 일부로 프로비전된 리소스의 이름 접두사 |6자 이하 |
**사용자 이름** | 이 멤버에게 배포된 각 가상 머신의 관리자 사용자 이름 |1-64자 |
**인증 유형** | 가상 머신에 인증하는 방법 |암호 또는 SSH 공개 키|
**암호(인증 형식 = 암호)** |배포된 각 가상 머신의 관리자 계정 암호. 암호는 다음 문자 유형 중 세 가지를 포함해야 합니다. 대문자 1개, 소문자 1개, 숫자 1개, 특수 문자 1개<br /><br />처음에는 모든 VM의 암호가 동일하지만, 프로비전 후 암호를 변경할 수 있습니다.|12-72자|
**SSH 키(인증 유형 = SSH 공개 키)** |원격 로그인에 사용되는 보안 셸 키 ||
**구독** |배포할 구독 ||
**리소스 그룹** |컨소시엄 네트워크를 배포할 리소스 그룹 ||
**위치**: |첫 번째 멤버를 배포할 Azure 지역 ||

**확인**을 선택합니다.

### <a name="consortium-network-settings"></a>컨소시엄 네트워크 설정

**네트워크 설정**에서 기존 컨소시엄 네트워크를 만들거나 연결하는 데 필요한 입력을 지정하고 조직 설정을 구성합니다.

![컨소시엄 네트워크 설정](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| 매개 변수 이름 | 설명 | 허용되는 값 |
|---|---|---|
**네트워크 구성** |새 네트워크를 만들거나 기존 네트워크를 연결하도록 선택할 수 있습니다. ‘기존 네트워크 연결’을 선택하는 경우 추가로 값을 제공해야 합니다. |새 네트워크 <br/> 기존 네트워크 연결 |
**HLF CA 암호** |배포의 일부로 생성되는 인증 기관에서 생성한 인증서에 사용되는 암호입니다. 암호는 다음 문자 유형 중 세 가지를 포함해야 합니다. 대문자 1개, 소문자 1개, 숫자 1개, 특수 문자 1개.<br /><br />처음에는 모든 가상 머신의 암호가 동일하지만, 프로비전 후 암호를 변경할 수 있습니다.|1-25자 |
**조직 설정** |조직 이름 및 인증서를 사용자 지정하거나 사용할 기본값을 설정할 수 있습니다.|기본값 <br/> 고급 |
**VPN 네트워크 설정** | VM에 액세스하기 위한 VPN 터널 게이트웨이를 프로비전합니다. | 예 <br/> 아닙니다. |

**확인**을 선택합니다.

### <a name="fabric-specific-settings"></a>Fabric별 설정

**패브릭 구성**에서 네트워크 크기 및 성능을 구성하고 네트워크 가용성에 대한 입력을 지정합니다. 예를 들어 정렬자 및 피어 노드 수, 각 노드에서 사용되는 지속성 엔진, VM 크기 등이 있습니다.

![패브릭 설정](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| 매개 변수 이름 | 설명 | 허용되는 값 |
|---|---|---|
**크기 조정 유형** |여러 개의 컨테이너를 포함하는 단일 가상 머신 또는 스케일 아웃 모델의 다중 가상 머신 중 하나인 배포 유형입니다.|단일 VM 또는 다중 VM |
**VM 디스크 유형** |배포된 각 노드를 지원하는 저장소 형식. <br/> 사용 가능한 디스크 유형에 대한 자세한 내용은 [디스크 유형 선택](../../virtual-machines/windows/disks-types.md)을 참조하세요.|표준 SSD <br/> 프리미엄 SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>다중 VM 배포(추가 설정)

![다중 VM 배포에 대한 패브릭 설정](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| 매개 변수 이름 | 설명 | 허용되는 값 |
|---|---|---|
**정렬자 노드 수** |트랜잭션을 블록으로 정렬(구성)하는 노드 수. <br />정렬 서비스에 대한 자세한 내용은 Hyperledger [문서](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html)를 참조하세요. |1-4 |
**정렬자 노드 가상 머신 크기** |네트워크의 정렬자 노드에 사용되는 가상 머신 크기|표준 Bs,<br />표준 Ds,<br />표준 FS |
**피어 노드 수** | 트랜잭션을 실행하고 원장의 상태와 복사본을 유지하는 컨소시엄 멤버 소유의 노드.<br />정렬 서비스에 대한 자세한 내용은 Hyperledger [설명서](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)를 참조하세요.|1-4 |
**노드 상태 지속성** |피어 노드에서 사용되는 지속성 엔진. 피어 노드별로 이 엔진을 구성할 수 있습니다. 다중 피어 노드에 대해서는 아래 세부 정보를 참조하세요.|CouchDB <br />LevelDB |
**피어 노드 가상 머신 크기** |네트워크의 모든 노드에 사용되는 가상 머신 크기|표준 Bs,<br />표준 Ds,<br />표준 FS |

### <a name="multiple-peer-node-configuration"></a>다중 피어 노드 구성

이 템플릿을 사용하여 피어 노드별로 지속성 엔진을 선택할 수 있습니다. 예를 들어 세 개의 피어 노드가 있는 경우 한 노드에서 CouchDB를 사용하고 다른 두 노드에서 LevelDB를 사용할 수 있습니다.

![다중 피어 노드 구성](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

**확인**을 선택합니다.

### <a name="deploy"></a>배포

**요약** 블레이드에서는 지정한 입력을 검토하고 기본 배포 전 유효성 검사를 실행합니다.

![요약](./media/hyperledger-fabric-consortium-blockchain/summary.png)

법률 정보 및 개인정보처리방침을 검토하고 **구매**를 선택하여 배포합니다. 프로비전되는 VM 수에 따라 배포 시간이 몇 분부터 수십 분까지 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Hyperledger 컨소시엄 블록체인 네트워크에 대한 애플리케이션 및 체인코드 개발에 집중할 준비가 되었습니다.
