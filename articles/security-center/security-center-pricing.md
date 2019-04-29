---
title: 보안 강화를 위해 Security Center의 표준 계층으로 업그레이드 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에 대한 가격 책정 정보를 제공합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2019
ms.author: monhaber
ms.openlocfilehash: 27acda2496adea39321e498868aebcc2f824df3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905740"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>보안 강화를 위해 Security Center의 표준 계층으로 업그레이드
Azure Security Center는 Azure, 온-프레미스 및 기타 클라우드용으로 통합 보안 관리 및 고급 위협 보호 기능을 제공합니다. 또한 하이브리드 클라우드 작업을 확인하고 제어하는 기능, 위협에 대한 노출을 줄이는 적극적인 방어 기능, 그리고 빠르게 발전하는 사이버 공격에 대응할 수 있는 지능형 검색 기능을 제공합니다.

## <a name="pricing-tiers"></a>가격 책정 계층
Security Center는 두 계층으로 제공됩니다.

- 모든 Azure 구독에서 자동으로 사용하도록 설정되는 **무료** 계층에서는 Azure 리소스를 보호할 수 있도록 보안 정책, 지속적인 보안 평가 및 실행 가능한 보안 권장 사항을 제공합니다.
- **표준** 계층의 경우 무료 계층의 기능이 사설 클라우드 및 기타 공용 클라우드에서 실행되는 작업으로 확장 적용되며, 하이브리드 클라우드 작업 전반에 걸쳐 통합 보안 관리 및 위협 방지 기능이 제공됩니다. 또한 표준 계층에서는 기본 제공 행동 분석 및 Machine Learning을 사용하여 공격 및 제로 데이 익스플로잇을 식별하는 고급 위협 탐지 기능, 네트워크 공격과 맬웨어에 대한 노출을 줄여 주는 액세스 및 애플리케이션 컨트롤 등도 추가로 제공합니다. 표준 계층을 무료로 시도할 수 있습니다. Security Center 표준 계층 지원 Azure Vm을 포함 하 여 리소스에서 VM 확장 집합, App Service, SQL 서버 및 저장소 계정입니다. Azure Security Center 표준을 사용하는 경우 리소스 종류에 따라 지원을 옵트아웃할 수 있습니다. 


자세한 내용은 Security Center [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

## <a name="try-standard-free-for-30-days"></a>30일간 표준 계층 사용
표준 계층은 처음 30일간 평가판으로 제공됩니다. 30일 종료 시 서비스를 계속 사용하기로 선택하는 경우 사용량에 대한 요금이 자동으로 부과되기 시작합니다.

전체 Azure 구독을 구독 내의 모든 리소스에서 상속되는 표준 계층으로 업그레이드할 수 있습니다.

표준 계층 얻기:

1. **Security Center** 주 메뉴에서 **보안 정책**을 선택합니다.
2. 표준으로 업그레이드하려는 경우 구독을 선택합니다.
3. **보안 정책** 블레이드에서 **가격 책정 계층**을 선택합니다.
4. **표준**을 선택하여 업그레이드합니다.
5. **저장**을 클릭합니다.

(이미지의 가격은 예를 들어 전용입니다.) ![Security Center 가격 책정](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> 모든 Security Center 기능을 사용 하려면 표준 가격 계층 해당 가상 머신이 포함 된 구독에 적용 해야 합니다. 작업 영역에 대해 가격 책정을 구성해도 Just-In-Time VM 액세스, 적응형 애플리케이션 제어 및 Azure 리소스에 대한 네트워크 검색 기능은 사용하도록 설정되지 않습니다.
>
>

## <a name="why-upgrade-to-standard"></a>표준으로 업그레이드하는 이유?
Security Center에서는 다음을 비롯하여 하이브리드 클라우드 작업을 위한 강화된 보안 및 위협 방지 기능을 제공합니다.

- **하이브리드 보안** – 모든 온-프레미스 및 클라우드 작업에 걸쳐 보안을 통합 확인할 수 있습니다. 또한 보안 정책을 적용하고 하이브리드 클라우드 작업의 보안을 지속적으로 평가하여 보안 표준을 준수할 수 있습니다. 뿐만 아니라 방화벽 및 기타 파트너 솔루션을 포함한 다양한 소스에서 보안 데이터를 수집, 검색 및 분석할 수 있습니다.
- **고급 위협 검색** - 고급 분석 및 Microsoft Intelligent Security Graph를 사용하여 갈수록 발전하는 사이버 공격을 효율적으로 대응할 수 있습니다.  기본 제공 행동 분석 및 Machine Learning을 활용하여 공격 및 제로 데이 익스플로잇을 식별할 수 있습니다. 또한 네트워크, 컴퓨터 및 클라우스 서비스에서 들어오는 공격 및 위반 후 활동을 모니터링할 수 있습니다. 대화형 도구 및 상황에 맞는 위협 인텔리전스를 사용하면 조사를 손쉽게 수행할 수 있습니다.
- **액세스 및 애플리케이션 컨트롤** - Machine Learning을 통해 제공되며 특정 작업에 맞게 조정되는 허용 목록 권장 사항을 적용하여 맬웨어 및 기타 원치 않는 애플리케이션을 차단할 수 있습니다. Azure VM의 관리 포트에 대한 제어되는 Just-In-Time 액세스를 사용하여 네트워크 공격에 대한 취약성을 낮춤으로써 무차별 암호 대입 공격(brute force attack) 및 기타 네트워크 공격에 대한 노출을 크게 줄일 수 있습니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center의 가격 책정 방식에 대해 알아보았습니다. 표준 계층의 강화된 보안 및 고급 위협 보호에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [고급 위협 감지](security-center-threat-report.md)
- [Just-In-Time VM 액세스 제어](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
