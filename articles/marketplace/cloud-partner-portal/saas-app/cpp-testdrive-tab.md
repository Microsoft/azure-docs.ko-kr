---
title: Azure SaaS 애플리케이션 제안에 대한 시험 사용 구성 | Microsoft Docs
description: Azure Marketplace의 SaaS 애플리케이션 제안에 대한 시험 사용을 구성합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pbutlerm
ms.openlocfilehash: b12ba53f847b46479b3100c088c29372b58c1b8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594220"
---
# <a name="saas-application-test-drive-tab"></a>SaaS 애플리케이션 시험 사용 탭

[시험 사용] 탭을 사용하여 고객에게 체험 환경을 제공합니다.

## <a name="test-drive-benefits"></a>시험 사용의 이점

고객이 확신을 가지고 구매할 수 있도록 고객의 평가판 환경을 만드는 것이 좋습니다. 사용 가능한 평가판 옵션 중에서 시험 사용은 우수한 잠재 고객 생성 및 이러한 잠재 고객의 전환율을 높이는 데 가장 효과적입니다.

시험 사용은 실제 구현 시나리오에서 입증된 제품의 주요 기능과 이점에 대한 실습 중심의 자기 주도형 평가판을 고객에게 제공합니다.


## <a name="how-a-test-drive-works"></a>시험 사용의 작동 방식

잠재 고객은 Marketplace에서 애플리케이션을 검색합니다. 고객은 로그인하여 사용 약관에 동의합니다. 이때 고객은 일정 시간 동안 사용해 보도록 미리 구성된 환경을 받고 여러분은 향후 연락할 우수한 잠재 고객을 얻게 됩니다. 자세한 내용은 [시험 사용이란?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)을 참조하세요.


## <a name="publishing-steps"></a>게시 단계

시험 사용 추가를 위한 기본 게시 단계는 다음과 같습니다.

1. 시험 사용 시나리오 정의
2. Resource Manager 템플릿 빌드 및/또는 수정
3. 시험 사용 단계별 설명서 만들기
4. 제품 다시 게시


## <a name="setting-up-a-test-drive"></a>시험 사용 설정

네 가지 유형의 시험 사용을 사용할 수 있으며, 각각은 현재 사용 중인 제품, 시나리오 및 마켓플레이스의 유형을 기반으로 합니다.

|  **형식**          |  **설명**  |  **설치 지침**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure 리소스 관리자               |    Azure Resource Manager 시험 사용은 게시자가 빌드한 솔루션을 구성하는 모든 Azure 리소스가 포함된 배포 템플릿입니다. 이 유형의 시험 사용에 맞는 제품은 Azure 리소스만 사용하는 제품입니다.               |       [Azure Resource Manager 시험 사용](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       호스트형             |       호스트형 시험 사용을 진행하면 시험 사용 사용자 프로비저닝 및 프로비전 해제를 수행하는 서비스를 Microsoft에서 호스팅하고 유지 관리하므로 복잡한 설정을 수행할 필요가 없습니다.             |         [호스팅된 시험 사용](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      논리 앱              |       논리 앱 시험 사용은 모든 복잡한 솔루션 아키텍처가 포함된 배포 템플릿입니다. 모든 Dynamics 애플리케이션 또는 사용자 지정 제품은 이 유형의 시험 사용을 사용해야 합니다.            |      [논리 앱 시험 사용](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         Power BI 시험 사용은 사용자 지정 빌드 대시보드에 포함된 링크로 구성됩니다. 대화형 Power BI 시각적 개체를 보여 주려는 제품은 이 유형의 시험 사용을 사용해야 합니다. 포함된 Power BI URL만 업로드하면 됩니다.          |        [Power BI 시험 사용](#power-bi-test-drive)           |
|   |   |   |


### <a name="power-bi-test-drive"></a>Power BI 시험 사용

다음 단계에 따라 시험 사용을 구성합니다.

1. [새 제안] 아래에서 **시험 사용**을 선택합니다.
2. [시험 사용]에서 **예**를 선택합니다.

   ![시험 사용](./media/saas-enable-test-drive.png)

   시험 사용을 사용하도록 설정하면 다음 화면 캡처에 표시된 [세부 정보] 및 [기술 구성] 양식을 확인합니다.

   ![시험 사용 구성 양식](./media/saas-test-drive-yes.png)

3. **세부 정보** 아래에서 다음 필드에 대한 정보를 제공합니다.
  
   - 설명 – 시험 사용 및 사용자가 수행할 수 있는 작업을 설명합니다. 기본 HTML 태그를 사용하여 이 설명의 서식을 지정할 수 있습니다.
   - 사용자 설명서 – 시험 사용을 수행할 때 고객이 사용할 수 있는 사용자 설명서를 업로드합니다. 이 설명서는 pdf 파일이어야 합니다.
   - 시험 사용 데모 비디오(선택 사항) - 고객이 시험 사용을 수행하기 전에 시청할 수 있는 비디오(YouTube 또는 Vimeo)를 제공할 수 있습니다. 비디오에 대한 URL을 제공합니다.

4. **기술 구성** 아래에서 다음 필드에 대한 정보를 제공합니다.

   - 시험 사용 유형 – 드롭다운 목록에서 **Power BI**를 선택합니다.
   - Power BI 대시보드에 연결 – 대시보드에 대한 링크를 제공합니다.

5. 시험 사용 구성이 완료되면 **저장**을 선택합니다.


## <a name="next-steps"></a>다음 단계

[상점 세부 정보 탭](./cpp-storefront-tab.md)
