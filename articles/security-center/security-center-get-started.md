---
title: 표준 계층으로 업그레이드 - Azure Security Center
description: 이 빠른 시작에서는 보안 강화를 위해 Security Center의 표준 가격 책정 계층으로 업그레이드하는 방법을 보여줍니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 550c9ff57b9c558f2f175165c7f06ead45991be9
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226018"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>빠른 시작: Security Center 표준에 Azure 구독 온보딩
Azure Security Center는 하이브리드 클라우드 워크로드에 통합 보안 관리 및 위협 방지 기능을 제공합니다. 체험 계층은 Azure 리소스에 대한 제한된 보안만 제공하지만 표준 계층은 이러한 기능을 온-프레미스 및 기타 클라우드로 확장합니다. Security Center 표준을 사용하면 보안 취약성을 찾아서 수정하고, 액세스 및 애플리케이션 제어를 적용하여 악성 활동을 차단하고, 분석 및 인텔리전스를 사용하여 위협을 검색하고, 공격을 받을 때 신속하게 대응할 수 있습니다. 비용 없이 Security Center 표준을 사용해 볼 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

이 문서에서는 보안 강화를 위해 표준 계층으로 업그레이드하고 가상 머신에 Log Analytics 에이전트를 설치하여 보안 취약성과 위협을 모니터링합니다.

## <a name="prerequisites"></a>사전 요구 사항
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)으로 등록할 수 있습니다.

구독을 표준 계층으로 업그레이드하려면 구독 소유자, 구독 기여자 또는 보안 관리자의 역할이 할당되어야 합니다.

## <a name="enable-your-azure-subscription"></a>Azure 구독 사용

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.

1. **Microsoft Azure** 메뉴에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

   ![Security Center 개요][2]

**Security Center - 개요**에서는 하이브리드 클라우드 워크로드의 보안 태세를 총체적으로 확인하여 워크로드의 보안을 검색 및 평가하고 위험을 파악 및 완화할 수 있습니다. Security Center는 이전에 사용자 또는 다른 구독 사용자가 체험 계층에 온보딩하지 않은 Azure 구독을 자동으로 사용하도록 설정합니다.

**구독** 메뉴 항목을 클릭하여 구독 목록을 보고 필터링할 수 있습니다. 이제 Security Center에서 이러한 구독의 보안 평가를 시작하여 보안 취약성을 식별합니다. 평가 유형을 사용자 지정하기 위해 보안 정책을 수정할 수 있습니다. 보안 정책은 워크로드에서 원하는 구성을 정의하고 회사 또는 규정 보안 요구 사항을 준수하는 데 도움이 됩니다.

Security Center를 처음 시작하면 수분 내에 다음이 표시될 수 있습니다.

- **권장 사항** - Azure 구독의 보안을 향상하는 방법을 제공합니다. **권장 사항** 타일을 클릭하면 우선 순위가 지정된 목록이 시작됩니다.
- 현재 Security Center에서 평가 중인 **컴퓨팅 및 앱**, **네트워킹**, **데이터 보안** 및 **ID 및 액세스** 리소스 인벤토리 및 각 리소스의 보안 상태입니다.

Security Center를 최대한 활용하려면 아래 단계를 완료하여 표준 계층으로 업그레이드하고 Log Analytics 에이전트를 설치해야 합니다.


## <a name="upgrade-to-the-standard-tier"></a>표준 계층으로 업그레이드

Security Center 빠른 시작 및 자습서를 위해 표준 계층으로 업그레이드해야 합니다. Security Center 표준의 평가판이 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요. 

1. Security Center의 사이드바에서 **시작**을 선택합니다.
 
   ![시작](./media/security-center-get-started/get-started-upgrade-tab.png)

    **업그레이드** 탭에는 온보딩에 적합한 구독 및 작업 영역이 나열되어 있습니다.

1. **표준 계층을 사용하도록 설정할 작업 영역 선택** 목록에서 업그레이드할 작업 영역을 선택합니다.


    > [!TIP]
    > 평가판에 적합한 작업 영역을 선택하면 다음 단계에서 평가판이 시작됩니다. 작업 영역이 평가판에 적합하지 않은 경우 업그레이드되고 요금이 부과됩니다.

1. **업그레이드**를 선택하여 선택한 작업 영역을 표준 계층으로 업그레이드합니다.


## <a name="automate-data-collection"></a>데이터 수집 자동화
Security Center는 Azure VM 및 비 Azure 컴퓨터에서 데이터를 수집하여 보안 취약성과 위협을 모니터링합니다. 데이터는 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석용으로 작업 영역에 데이터를 복사하는 Log Analytics 에이전트를 사용하여 수집됩니다. 기본적으로 Security Center는 새 작업 영역을 만듭니다.

자동 프로비저닝을 사용하는 경우 Security Center에서 지원되는 모든 Azure VM 및 새로 만든 Azure VM에 Log Analytics 에이전트를 설치합니다. 자동 프로비저닝을 사용하는 것이 좋습니다.

Log Analytics 에이전트의 자동 프로비저닝을 사용하려면 다음을 수행합니다.

1. Security Center 주 메뉴에서 **가격 책정 및 설정**을 선택합니다.
1. 구독의 행에서 설정을 변경하려는 구독을 클릭합니다.
1. **데이터 컬렉션** 탭에서 **자동 프로비전**을 **켜짐**으로 설정합니다.
1. **저장**을 선택합니다.
---
  ![자동 프로비저닝 사용][6]

Azure VM에 대한 이 새로운 정보를 활용하여 Security Center는 시스템 업데이트 상태, OS 보안 구성, 엔드포인트 보호와 관련된 추가 권장 사항을 제공하고 추가 보안 경고를 생성할 수 있습니다.

  ![권장 사항][8]

## <a name="clean-up-resources"></a>리소스 정리
이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 이후의 빠른 시작과 자습서를 계속 사용하려면 표준 계층을 계속 실행하고 자동 프로비저닝을 설정된 상태로 유지합니다. 계속하지 않거나 체험 계층으로 되돌리려면 다음을 수행합니다.

1. Security Center 주 메뉴로 돌아가서 **가격 책정 및 설정**을 선택합니다.
2. 무료 계층으로 변경하려는 구독을 클릭합니다.
3. **가격 책정 계층**을 선택하고 **체험**을 선택하여 표준 계층에서 체험 계층으로 구독을 변경합니다.
5. **저장**을 선택합니다.

자동 프로비저닝을 사용하지 않도록 설정하려면 다음을 수행합니다.

1. Security Center 주 메뉴로 돌아가서 **가격 책정 및 설정**을 선택합니다.
2. 자동 프로비저닝을 사용하지 않도록 설정할 구독을 정리합니다.
3. **데이터 컬렉션** 탭에서 **자동 프로비전**을 **꺼짐**으로 설정합니다.
4. **저장**을 선택합니다.

>[!NOTE]
> 자동 프로비저닝을 해제해도 에이전트가 프로비저닝된 Azure VM에서 Log Analytics 에이전트가 제거되지는 않습니다. 자동 프로비저닝을 사용하지 않도록 설정하면 리소스에 대한 보안 모니터링이 제한됩니다.
>

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 표준 계층으로 업그레이드하고 하이브리드 클라우드 워크로드에서 통합된 보안 관리 및 위협 방지를 위해 Log Analytics 에이전트를 프로비저닝했습니다. Security Center 사용 방법을 자세히 알아보려면 온-프레미스 및 다른 클라우드에 있는 Windows 컴퓨터를 온보딩하기 위한 빠른 시작을 계속 진행하세요.

> [!div class="nextstepaction"]
> [빠른 시작: Azure Security Center에 Windows 컴퓨터 온보딩](quick-onboard-windows-computer.md)

클라우드 비용을 최적화하여 비용을 절감하고 싶습니까?

> [!div class="nextstepaction"]
> [Cost Management를 통한 비용 분석 시작](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
