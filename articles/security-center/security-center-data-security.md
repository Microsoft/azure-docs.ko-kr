---
title: Azure Security Center 데이터 보안 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 데이터 관리하고 보호하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7a327c963f32d6bf5b0cf7751557c61327fd26e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60911547"
---
# <a name="azure-security-center-data-security"></a>Azure Security Center 데이터 보안
고객이 위협을 방지, 감지 및 대응하는 데 도움을 주기 위해 Azure Security Center에서는 구성 정보, 메타데이터, 이벤트 로그, 크래시 덤프 파일 등을 포함한 보안 관련 데이터를 수집하고 처리합니다. Microsoft는 코딩부터 서비스에 이르기까지 엄격한 규정 준수 및 보안 지침을 따릅니다.

이 문서에서는 Azure Security Center에서 데이터 관리하고 보호하는 방법을 설명합니다.

## <a name="data-sources"></a>데이터 원본
Azure Security Center에서는 다음 소스의 데이터를 분석하여 보안 상태에 대한 가시성을 제공하고, 취약점을 식별하고, 완화 방법을 권장하고, 활성 위협을 감지합니다.

- Azure 서비스: 해당 서비스의 리소스 공급자와 통신하여 배포한 Azure 서비스의 구성에 대한 정보를 사용합니다.
- 네트워크 트래픽: 원본/대상 IP/포트, 패킷 크기 및 네트워크 프로토콜과 같은 Microsoft의 인프라에서 샘플링된 네트워크 트래픽 메타데이터를 사용합니다.
- 파트너 솔루션: 방화벽 및 맬웨어 방지 솔루션과 같은 통합된 파트너 솔루션의 보안 경고를 사용합니다.
- Virtual Machines 및 서버: Windows 이벤트 및 감사 로그, IIS 로그, syslog 메시지 및 가상 머신의 크래시 덤프 파일과 같은 보안 이벤트에 대한 구성 정보 및 정보를 사용합니다. 또한 경고가 생성되면 Azure Security Center에서 포렌식(forensics) 용도로 관련 VM 디스크의 스냅숏을 생성하고 VM 디스크에서 레지스트리 파일과 같은 경고와 관련된 컴퓨터 아티팩트를 추출합니다.


## <a name="data-protection"></a>데이터 보호
**데이터 분리**: 데이터는 서비스 전체에서 각 구성 요소에 논리적으로 별도로 유지됩니다. 모든 데이터에는 조직별로 태그가 지정됩니다. 이 태그는 데이터 수명 주기 동안 유지되며 서비스의 각 계층에서 적용됩니다.

**데이터 액세스**: Microsoft 직원은 보안 권장 사항을 제공하고 잠재적 보안 위협을 조사하기 위해 Azure 서비스에서 수집하거나 분석한 정보에 액세스할 수 있습니다. 이러한 정보에는 크래시 덤프 파일, 프로세스 생성 이벤트, VM 디스크 스냅숏 및 아티팩트 등이 포함되며, 가상 머신의 고객 데이터 또는 개인 데이터가 의도치 않게 포함될 수도 있습니다. Microsoft는 [Microsoft Online Services 약관 및 개인정보처리방침](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)을 준수합니다. 즉, Microsoft는 고객 데이터를 사용하거나 광고 또는 유사한 상업적 목적에서 정보를 파생하지 않습니다. 필요에 따라 고객 데이터를 사용하여 해당 서비스를 제공하도록 호환할 수 있는 목적으로 Azure 서비스를 제공합니다. 고객 데이터에 대한 모든 권한을 유지합니다.

**데이터 사용**: Microsoft는 여러 테넌트에 발생하는 패턴 및 위협 인텔리전스를 사용하여 방지 및 검색 기능을 향상시킵니다. [개인정보처리방침](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)에 설명된 개인정보처리방침 약정에 따라 수행합니다.

## <a name="data-location"></a>데이터 위치

**작업 영역**: 작업 영역은 크래시 덤프 및 일부 유형의 경고 데이터를 포함하여 다음 지역 및 Azure 가상 머신에서 수집된 데이터에 대해 지정되며 가장 가까운 작업 영역에 저장됩니다.

| VM 지역                        | 작업 영역 지역 |
|-------------------------------|---------------|
| 미국, 브라질, 캐나다 | 미국 |
| 유럽, 영국        | 유럽        |
| 아시아 태평양, 일본, 인도    | 아시아 태평양  |
| 오스트레일리아                     | 오스트레일리아     |


VM 디스크 스냅숏은 VM 디스크와 동일한 저장소 계정에 저장됩니다.

다른 환경에서 실행되는 가상 머신 및 서버의 경우(예: 온-프레미스) 수집된 데이터가 저장되는 작업 영역 및 지역을 지정할 수 있습니다.

**Azure Security Center Storage**: 파트너 경고를 포함하는 보안 경고에 대한 정보는 관련된 Azure 리소스의 위치에 따라 지역적으로 저장되는 반면 보안 상태에 대한 정보 및 권장 사항은 고객의 위치에 따라 미국 또는 유럽에 중앙 집중식으로 저장됩니다.
Azure Security Center는 크래시 덤프 파일의 임시 복사본을 수집하고 이용 시도 및 손상 성공의 증거를 찾기 위해 분석합니다. Azure Security Center는 작업 영역과 동일한 지역 내에서 이 분석을 수행하고 분석이 완료되면 임시 복사본을 삭제합니다.

컴퓨터 아티팩트는 VM과 동일한 지역에 중앙 집중식으로 저장됩니다.


## <a name="managing-data-collection-from-virtual-machines"></a>가상 머신에서 데이터 컬렉션 관리

Azure에서 Security Center를 사용하는 경우 각 Azure 구독에 대해 데이터 수집이 활성화됩니다. 또한 Azure Security Center의 보안 정책 섹션에서 구독에 대한 데이터 수집을 설정할 수 있습니다. 데이터 수집이 활성화되면 Azure Security Center는 지원되는 모든 기존 가상 머신 및 새로 만든 Azure 가상 머신에 Microsoft Monitoring Agent를 프로비전합니다.
Microsoft Monitoring 에이전트는 다양한 보안 관련 구성 및 이벤트를 검사하여 ETW([Windows용 이벤트 추적](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx))로 보냅니다. 또한 운영 체제는 컴퓨터를 실행하는 동안 이벤트 로그 이벤트를 발생시킵니다. 이러한 데이터의 예: 운영 체제 유형 및 버전, 운영 체제 로그(Windows 이벤트 로그), 프로세스 실행, 컴퓨터 이름, IP 주소, 로그인된 사용자 및 테넌트 ID입니다. Microsoft Monitoring Agent는 이벤트 로그 항목을 읽으며 ETW는 이를 추적하고 분석을 위해 작업 영역에 복사합니다. 또한 Microsoft Monitoring Agent는 크래시 덤프 파일을 작업 영역으로 복사하고, 프로세스 생성 이벤트를 활성화하고, 명령줄 감사를 활성화합니다.

Azure Security Center를 무료로 사용하는 경우 보안 정책의 가상 머신에서 데이터 수집을 해제할 수도 있습니다. 데이터 수집은 표준 계층의 구독에 필요합니다. VM 디스크 스냅숏 및 아티팩트 컬렉션은 데이터 수집이 사용하지 않도록 설정된 경우에도 여전히 사용하도록 설정됩니다.

## <a name="data-consumption"></a>데이터 사용량

고객은 아래와 같이 다른 데이터 스트림에서 데이터와 관련된 Security Center를 사용할 수 있습니다.

* **Azure 작업**: 모든 보안 경고, 승인된 Security Center [just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) 요청 및 [적응 애플리케이션 컨트롤](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)에서 생성된 모든 경고입니다.
* **Azure Monitor 로그**: 모든 보안 경고 합니다.


> [!NOTE]
> REST API를 통해 보안 권장 사항도 사용할 수 있습니다. 자세한 정보는 [보안 리소스 공급자 REST API 참조](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx)를 참고하세요.

## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Security Center에서 데이터 관리하고 보호하는 방법을 알아봅니다. Azure Security Center에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center의 계획 및 운영 가이드](security-center-planning-and-operations-guide.md) — 디자인 고려 사항을 계획하고 이해하여 Azure Security Center를 채택하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) — Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) — 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) — 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) — Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
