---
title: 개념 증명 제공-Azure DevTest Labs | Microsoft Docs
description: Azure DevTest Labs 엔터프라이즈 환경에 성공적으로 통합 될 수 있도록 개념 증명을 제공 하는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75643290"
---
# <a name="deliver-a-proof-of-concept"></a>개념 증명 제공 

Azure DevTest Labs에 대 한 주요 시나리오 중 하나는 클라우드에서 개발 및 테스트 환경을 사용 하도록 설정 하는 것입니다. 예를 들면 다음과 같습니다.

* 클라우드에서 개발자 데스크톱을 만드는 중입니다.
* 테스트 환경을 구성 하는 중입니다.
* 가상 컴퓨터 및 기타 Azure 리소스에 대 한 액세스를 허용 합니다.
* 개발자가 학습 하 고 실험해 볼 수 있는 샌드박스 영역을 설정 합니다.

또한 DevTest Labs는 기업에서 회사 보안, 규정 및 규정 준수 정책을 준수 하는 개발자에 게 "셀프 서비스 Azure"를 제공 하도록 지 원하는 정책 guardrails 및 비용 제어를 제공 합니다. 

모든 엔터프라이즈에는 Azure DevTest Labs를 해당 환경에 성공적으로 통합 하는 방법에 대 한 요구 사항이 다릅니다. 이 문서에서는 성공적인 개념 증명을 위해 엔터프라이즈에서 완료 해야 하는 가장 일반적인 단계에 대해 설명 합니다. 개념 증명은 성공적인 종단 간 배포를 위한 첫 번째 단계입니다. 

## <a name="getting-started"></a>시작 

개념 증명을 제공 하는 방법에 대해 알아봅니다. Azure 및 DevTest Labs에 대 한 자세한 내용은 시간을 할애 하는 것이 중요 합니다.  다음은 몇 가지 시작 리소스입니다. 

* [Azure Portal 이해](https://azure.microsoft.com/features/azure-portal/)
* [DevTest Labs의 기본 사항](devtest-lab-overview.md)
* [DevTest Labs 지원 시나리오](devtest-lab-guidance-get-started.md)
* [DevTest Labs enterprise 설명서](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure 네트워킹 소개](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>필수 조건 

DevTest Labs를 사용 하 여 파일럿 또는 개념 증명을 성공적으로 완료 하려면 몇 가지 필수 구성 요소가 있습니다. 

* **Azure 구독**: 기업은 azure에 대 한 액세스를 가능 하 게 하는 기존 [기업계약](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) 을 보유 하 고 있으며, DevTest Labs에 대 한 기존 또는 새로운 구독을 사용할 수 있습니다. 또는 기업에서 파일럿 중에 [Visual Studio 구독](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) 을 사용할 수 있습니다 (무료 Azure 크레딧 활용). 이러한 옵션을 모두 사용할 수 없는 경우 기업은 [무료 Azure 계정을](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)만들고 사용할 수 있습니다. 기업계약 있는 경우 [Enterprise 개발/테스트 구독](https://azure.microsoft.com/offers/ms-azr-0148p/) 을 사용 하면 Windows 10/Windows 8.1 클라이언트 운영 체제에 액세스 하 고 개발 및 테스트 작업에 대 한 할인 된 요금을 얻을 수 있습니다. 
* **Azure Active Directory 테넌트**: 사용자를 추가 하거나 랩 소유자를 추가 하는 등의 사용자 관리를 사용 하도록 설정 하려면 해당 사용자가 파일럿을 위해 Azure 구독에 사용 되는 [Azure Active Directory 테넌트](https://azure.microsoft.com/services/active-directory/) 의 일부 여야 합니다. 기업에서는 사용자가 클라우드에서 온-프레미스 id를 사용할 수 있도록 [하이브리드 id](../active-directory/hybrid/whatis-hybrid-identity.md) 를 설정 하지만, DevTest Labs 파일럿에는이 작업이 필요 하지 않습니다. 

## <a name="scoping-of-the-pilot"></a>파일럿의 범위 지정 

구현을 시작 하기 전에 파일럿을 계획 해야 합니다. 리소스를 지속적으로 유지 하지 않고 파일럿 사용자에 대 한 적절 한 기대치를 설정 합니다. 

> [!IMPORTANT]
> 파일럿을 crisply 하 고 기대치를 설정 하는 것이 얼마나 중요 한지를 충분히 강조할 수 없습니다.

파일럿을 시작 하기 전에 다음과 같은 주요 질문에 답변 합니다. 

* 학습 하고자 하는 내용 및 파일럿의 성공 여부는 어떻게 되나요? 
* 파일럿에서 다룰 워크 로드 또는 시나리오는 무엇 인가요? 파일럿의 범위를 지정 하 고 신속 하 게 완료할 수 있도록 작은 집합만 정의 하는 것이 중요 합니다. 
* 랩에서 사용할 수 있어야 하는 리소스는 무엇 인가요? 예: 사용자 지정 이미지, marketplace 이미지, 정책, 네트워크 토폴로지. 
* 환경을 확인 하기 위해 파일럿에 관여 하는 사용자 및 팀은 누구 인가요?  
* 파일럿 기간 이란 무엇 인가요? 2 주 또는 1 개월과 같이 계획 된 범위에 잘 맞는 기간을 선택 합니다. 
* 파일럿이 완료 되 면 파일럿 중에 사용 된 할당 된 리소스에서 어떻게 되나요? 파일럿 리소스를 삭제할 계획 인가요? 다음을 고려할 수 있습니다.
   
   "파일럿의 끝에서 가상 컴퓨터와 랩을 모두 제공 하도록 계획할 수 있는 경우 파일럿에 대 한 단일 구독을 설정 하 고이에 대 한 모든 작업을 병렬로 수행할 수 있습니다." 

파일럿을 "완벽 하 게" 하는 경향이 있으므로 서비스가 회사에서 롤아웃 된 후 최종 상태를 동일 하 게 나타낼 수 있습니다. 이는 거짓 가정입니다. "완벽" 하 게 되 면 파일럿을 시작 *하기 전에* 완료 해야 합니다. 파일럿의 목적은 최종 서비스의 확장 및 롤아웃에 대 한 적절 한 결정을 내리는 것입니다. 

파일럿의 초점은 Azure DevTest Labs가 기업에 적합 한 서비스 인지 여부에 대 한 질문에 대답 하기 위해 필요한 최소한의 워크 로드 및 종속성을 선택 하는 것입니다. 가장 낮은 종속성이 있는 가장 간단한 작업을 선택 하 여 신속 하 고 명확한 성공을 보장 하는 것이 좋습니다. 가능 하지 않은 경우에는 잠재적 복잡성을 노출 하는 가장 대표적인 워크 로드를 선택 하 여 파일럿 단계에서 성공이 수평 확장 단계로 복제 될 수 있도록 합니다. 

다음 예제에서는 올바른 범위의 개념 증명을 보여 줍니다. 

## <a name="example-proof-of-concept-plan"></a>예: 개념 증명 계획 

이 섹션에서는 DevTest Labs의 파일럿 개념 증명의 범위를 지정 하는 데 사용할 샘플을 보여 줍니다. 

> [!TIP]
> 오류에 대 한 프로젝트 설정의 가능성을 최소화 하려면이 섹션에 설명 된 예제를 건너뛰지 않는 것이 좋습니다. 

### <a name="overview"></a>개요 

Microsoft는 공급 업체가 회사 네트워크에서 격리 된 환경으로 사용 하기 위해 DevTest 랩을 기반으로 하는 새 환경을 Azure에서 개발할 계획입니다. 솔루션이 요구 사항을 충족 하는지 확인 하기 위해 개념 증명을 개발 하 여 종단 간 솔루션의 유효성을 검사 합니다. 경험을 사용해 볼 수 있는 몇 가지 공급 업체를 포함 했습니다. 

### <a name="outcomes"></a>결과 

개념 증명을 작성 하는 경우 결과에 먼저 집중 합니다 (수행 하려는 작업). 개념 증명의 끝에는 다음이 제공 됩니다. 

* 공급 업체에서 Azure Active Directory (Azure AD)의 게스트 계정을 사용 하 여 Azure의 격리 된 환경에 액세스할 수 있는 종단 간 작업 솔루션입니다. 환경에는 생산성을 유지 하는 데 필요한 리소스가 있습니다. 
* 광범위 한 사용 및 도입에 영향을 주는 잠재적 차단 문제는 열거 되 고 이해 됩니다.
* 개념 증명을 개발 하는 개인은 모든 코드를 이해 하는 데 유용 합니다. 또한 관련 된 참고 자료를 이해 하 고 광범위 하 게 채택 하는 것이 확실 합니다.

### <a name="open-questions-and-prerequisites"></a>질문 및 필수 구성 요소 열기 

* 이 프로젝트에 사용할 수 있는 구독을 만들었는지 확인 합니다. 
* Azure ad 테넌트와 azure ad와 관련 된 질문에 대 한 도움말을 제공할 수 있는 사용자를 식별 하는 azure AD 전역 관리자가 있나요? 
* 프로젝트에서 작업 하는 개인에 대해 공동 작업을 수행 하는 장소가 있나요? 

   * 소스 코드 및 스크립트 (예: Azure Repos) 
   * 문서 (예: Microsoft 팀 또는 SharePoint)  
   * 대화 (예: Microsoft 팀) 
   * 작업 항목 (예: Azure Boards) 
* 공급 업체에 필요한 리소스는 무엇 인가요? 여기에는 가상 컴퓨터 및 기타 필수 서버에서 로컬로 네트워크에서 사용할 수 있는 응용 프로그램이 포함 됩니다. 
* 가상 머신이 Azure의 도메인에 가입 됩니까? 그렇다면 이것이 Azure Active Directory Domain Services (Azure AD DS) 또는 다른 것 입니까? 
* 개념 증명의 대상이 되는 팀 또는 공급 업체를 식별 했습니까? 환경에 대 한 고객은 누구 인가요?
* 개념 증명에 사용 되는 Azure 지역은 무엇 인가요? 
* DevTest Labs를 통해 공급 업체가 IaaS (Vm) 외에도 사용할 수 있는 서비스 목록이 있나요? 
* 랩을 사용 하 여 공급 업체/사용자를 교육 하려면 어떻게 해야 하나요? 

### <a name="components-of-the-proof-of-concept-solution"></a>개념 증명 솔루션의 구성 요소 

솔루션에는 다음과 같은 구성 요소가 필요 합니다. 

* 다양 한 공급 업체 팀은 Azure에서 랩 집합을 사용 합니다.
* 랩은 요구 사항을 지 원하는 네트워크 인프라에 연결 됩니다.
* 공급 업체는 Azure AD 및 역할 할당을 통해 랩에 액세스할 수 있습니다.
* 공급 업체는 해당 리소스에 성공적으로 연결 하는 방법을 제공 합니다. 특히, 사이트 간 VPN을 사용 하면 공용 IP 주소 없이 직접 가상 컴퓨터에 액세스할 수 있습니다.
* 아티팩트 집합은 가상 컴퓨터에 공급 업체에 필요한 필수 소프트웨어를 포함 합니다.

## <a name="additional-planning-and-design-decisions"></a>추가 계획 및 디자인 결정 

전체 DevTest Labs 솔루션을 릴리스 하기 전에 몇 가지 중요 한 계획과 설계 결정을 내려야 합니다. 개념 증명을 사용 하 여 작업 하는 환경은 이러한 결정을 내리는 데 도움이 될 수 있습니다. 추가 고려 사항은 다음과 같습니다. 

* **구독 토폴로지**: Azure의 리소스에 대 한 엔터프라이즈 수준의 요구 사항은 [단일 구독 내에서 사용 가능한 할당량](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)을 초과 하 여 확장할 수 있습니다. 이렇게 하면 초기 구독 제한을 늘리기 위해 여러 Azure 구독 및/또는 서비스 요청이 요구 됩니다. 구독에서 리소스를 배포 하는 방법을 앞에서 결정 하는 것이 중요 합니다. 한 가지 중요 한 리소스는 나중에 다른 구독으로 리소스를 이동 하기 어렵기 때문에 [구독 의사 결정 가이드](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 입니다. 예를 들어 랩을 만든 후에는 다른 구독으로 이동할 수 없습니다.  
* **네트워크 토폴로지**: DevTest Labs에서 자동으로 만드는 [기본 네트워크 인프라](../app-service/networking-features.md) 는 엔터프라이즈 사용자에 대 한 요구 사항 및 제약 조건을 충족 하기에 충분 하지 않을 수 있습니다. 일반적으로 [Azure express 경로 연결 가상 네트워크](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), 구독 간 연결에 대 한 [허브 및 스포크](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 및 온-프레미스 연결에 대해서만 [강제 라우팅을](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) 확인 하는 것이 일반적입니다. DevTest Labs는 랩에서 새 가상 컴퓨터를 만들 때 기존 가상 네트워크를 랩에 연결 하 여 사용할 수 있도록 합니다. 
* **가상 컴퓨터 원격 액세스**: DevTest Labs에 있는 가상 컴퓨터에 원격으로 액세스 하는 다양 한 옵션이 있습니다. 가장 쉬운 방법은 공용 Ip 또는 공유 공용 Ip를 사용 하는 것입니다. 이러한 [설정은 랩에서 사용할 수](devtest-lab-shared-ip.md)있습니다. 이러한 옵션이 충분 하지 않은 경우에는 원격 액세스 게이트웨이를 사용 하는 것도 옵션입니다. 이 옵션은 [DevTest labs enterprise 참조 아키텍처](devtest-lab-reference-architecture.md) 에 표시 되며 [DevTest labs 원격 데스크톱 게이트웨이 설명서](configure-lab-remote-desktop-gateway.md)에 추가로 설명 되어 있습니다. 또한 기업에서는 Express 경로 또는 사이트 간 VPN을 사용 하 여 랩을 온-프레미스 네트워크에 연결할 수 있습니다. 이 옵션은 인터넷에 노출 되지 않은 개인 IP 주소를 기반으로 가상 컴퓨터에 대 한 직접 원격 데스크톱 또는 SSH 연결을 사용 하도록 설정 합니다. 
* **권한 처리**: DevTest Labs에서 일반적으로 사용 되는 두 가지 주요 권한은 [소유자 및 랩 사용자](devtest-lab-add-devtest-user.md)입니다. 랩에서 각 액세스 수준으로 위임 하는 DevTest Labs를 배포 하기 전에 결정 하는 것이 중요 합니다. 일반적인 모델은 예산 소유자 (예: 팀 리더)를 랩 소유자로, 팀 멤버를 랩 사용자로 하는 것입니다. 이 모델은 예산을 담당 하는 사람 (팀 리더)이 정책 설정을 조정 하 고 팀을 예산 내에 유지할 수 있도록 합니다.  

## <a name="completing-the-proof-of-concept"></a>개념 증명 완료 

예상 되는 학습가 적용 된 후 파일럿을 완료할 시간입니다. 사용자 로부터 피드백을 수집 하 고, 파일럿에 성공 했는지 확인 하 고, 조직이 엔터프라이즈에서 DevTest Labs의 규모 롤아웃에 앞서 이동 하는지 결정 하는 시간입니다. 또한 규모 롤아웃 전체에서 일관성을 유지 하기 위해 DevTest Labs 및 관련 리소스의 배포를 자동화 하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계 

* [DevTest Labs enterprise 설명서](devtest-lab-guidance-prescriptive-adoption.md)
* [엔터프라이즈에 대 한 참조 아키텍처](devtest-lab-reference-architecture.md)
* [DevTest Labs 배포 확장](devtest-lab-guidance-orchestrate-implementation.md)
* [Azure DevTest Labs의 구현 오케스트레이션](devtest-lab-guidance-orchestrate-implementation.md)
