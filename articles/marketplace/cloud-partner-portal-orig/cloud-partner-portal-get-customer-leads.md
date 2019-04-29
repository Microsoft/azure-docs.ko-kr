---
title: 잠재 고객 구성 | Microsoft Docs
description: 클라우드 파트너 포털에서 잠재 고객을 구성하는 방법을 설명합니다.
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
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a425e607ea7dac394ab90a3fed4d4026056bbc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61069616"
---
<a name="get-customer-leads"></a>리드 가져오기
==================

이 문서에서는 클라우드 파트너 포털을 사용하여 잠재 고객을 만드는 방법을 설명합니다. 이러한 잠재 고객은 CRM 시스템에 연결할 수 있으며 영업 라인에 통합할 수 있습니다.

## <a name="leads"></a>잠재 고객

잠재 고객은 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 또는 [AppSource](https://appsource.microsoft.com)에서 제품을 배포하고 있거나 제품에 관심이 있는 고객입니다.

### <a name="azure-marketplace"></a>Azure Marketplace

1.  고객이 제품을 “시험 사용”해 봅니다. 시험 사용은 진입 장벽 없이 회사와 잠재 고객을 즉시 공유할 수 있는 신속한 기회입니다. 모든 시험 사용에서는 제품에 대해 더 자세히 알아보려는 잠재 고객이 생성됩니다. [Azure Marketplace 시험 사용](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)에서 시험 사용에 대해 자세히 알아보세요.

    ![Marketplace 시험 사용 예제](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. 고객이 “지금 설치하기”를 선택하여 정보 공유에 동의합니다. 이 고객은 **초기 관심**을 나타내는 잠재 고객에 해당하며, 제품 구매에 관심을 표시한 고객 관련 정보가 공유됩니다. 해당 잠재 고객은 고객 유입 경로에서 최상위에 해당하는 고객입니다.

   ![지금 설치하기 옵션](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. 고객이 [Azure Portal](https://portal.azure.com/)에서 “구매”를 선택하여 제품을 구매합니다. 그러면 이 고객은 **활성** 잠재 고객이 되며, 제품 배포를 시작한 고객 관련 정보가 공유됩니다.

   ![구매 옵션](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  고객이 제품을 "시험 사용"해볼 수 있습니다. 시험 사용은 진입 장벽 없이 회사와 잠재 고객을 즉시 공유할 수 있는 신속한 기회입니다. 모든 시험 사용에서는 제품에 대해 더 자세히 알아보려는 잠재 고객이 생성됩니다. [AppSource 시험 사용](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive)에서 시험 사용에 대해 자세히 알아보세요.

    ![시험 사용 예제](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  고객이 “지금 설치하기”를 선택하여 정보 공유에 동의합니다. 이 고객은 **초기 관심**을 나타내는 잠재 고객에 해당하며, 제품 구매에 관심을 표시하는 고객 관련 정보가 공유됩니다. 해당 잠재 고객은 고객 유입 경로에서 최상위에 해당하는 고객입니다.

      ![지금 설치하기 옵션](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  고객이 제품에서 “문의처”를 선택합니다. 그러면 이 고객은 **활성** 잠재 고객이 되며, 제품에 대한 추가 정보를 요청하는 고객 관련 정보가 공유됩니다.

    ![문의처 옵션](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>리드 데이터
---------

고객 확보 프로세스에서 수신되는 각 잠재 고객에게는 특정 필드에 데이터가 있습니다. 여러 단계에서 잠재 고객이 생성되므로 잠재 고객을 가장 적절하게 처리하려면 추가 작업을 중복 제거하고 개인 설정해야 합니다. 이렇게 하면 각 고객이 적절한 메시지를 받으며 모든 고객과 고유한 관계를 구축할 수 있습니다.

### <a name="lead-source"></a>잠재 고객 확보 기회

잠재 고객 확보 기회의 형식은 **확보 기회**-**작업** |  **제품**입니다.

**원본**: "AzureMarketplace", "AzurePortal", "테스트" 및 "AppSource (SPZA)"

**작업**:
- "INS" -- 설치. 고객이 제품을 구매할 때 Azure Marketplace 또는 AppSource에서 이 작업이 수행됩니다.
- "PLT" -- Partner Led Trial(파트너 진행 체험)의 약어. 고객이가 문의처 옵션을 사용할 때 AppSource에서 이 작업이 수행됩니다.
- "DNC" -- Do Not Contact(연락 안 함)의 약어. 앱 페이지에서 교차 목록에 올라 있는 파트너가 연락 요청을 받으면 AppSource에서 이 작업이 수행됩니다. 이 고객이 앱의 교차 목록에 올라 있다는 사실을 공유하지만 고객에게 연락할 필요는 없습니다.
- “Create” -- 이 작업은 Azure Portal 내에서만 수행되며, 고객이 자신의 계정에서 제품을 구매하면 생성됩니다.
- “StartTestDrive” -– 이 작업은 시험 사용 시에만 수행되며, 고객이 시험 사용을 시작할 때 생성됩니다.

**제품**

다음 예제에서는 게시자 및 특정 제품에 할당 된 고유 식별자를 표시 합니다: checkpoint.check-지점-r77-10sg-byol, bitnami.openedxcypress, 및 docusign.3701c77e-1cfa-4c 56 91e6 3ed0b622145a 합니다.


### <a name="customer-info"></a>고객 정보

다음 예제의 필드에는 잠재 고객에 포함된 고객 정보가 나와 있습니다.
- FirstName: John
- 성: Smith
- Email: jsmith\@microsoft.com
- 전화: 1234567890
- 국가: US
- 회사: Microsoft
- 제목: CTO

>[!Note]
>위 예제의 모든 데이터가 각 잠재 고객에 대해 항상 제공되는 것은 아닙니다.

Microsoft는 리드 향상에 적극적으로 노력하고 있으며, 여기에 없지만 필요한 데이터 필드가 있는 경우 [의견을 보내주세요](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>CRM 시스템을 클라우드 파트너 포털에 연결하는 방법
------------------------------------------------------------

파트너가 잠재 고객 확보를 시작할 수 있도록 Microsoft는 클라우드 파트너 포털에서 잠재 고객 관리 커넥터를 구축했습니다. 따라서 파트너는 CRM 정보를 쉽게 입력할 수 있으며, 연결은 자동으로 설정됩니다. 이제는 외부 시스템과 통합하기 위해 상당한 엔지니어링 작업을 수행하지 않고도 Marketplace에서 생성된 리드를 쉽게 활용할 수 있습니다.

![잠재 고객 관리 커넥터](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

잠재 고객은 다양한 CRM 시스템에 작성하거나 Azure Storage 테이블에 직접 작성할 수 있으므로 파트너는 원하는 방법으로 잠재 고객을 관리할 수 있습니다. 다음의 각 링크에서는 사용 가능한 잠재 고객 작성 대상에 연결하는 지침을 제공합니다.

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) - 잠재 고객 확보용으로 Dynamics CRM Online을 구성하는 방법의 지침을 확인할 수 있습니다.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) - 잠재 고객 확보용으로 Marketo 잠재 고객 구성을 설정하는 방법의 지침을 확인할 수 있습니다.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) - 잠재 고객 확보를 위한 Salesforce 인스턴스를 설정하는 방법의 지침을 확인할 수 있습니다.
-    [Azure 테이블](./cloud-partner-portal-lead-management-instructions-azure-table.md) – Azure 테이블에서 잠재 고객 확보를 위한 Azure 저장소 계정을 설정하는 방법의 지침을 확인할 수 있습니다.
-   [Https 엔드포인트](./cloud-partner-portal-lead-management-instructions-https.md) - 잠재 고객 확보를 위한 Https 엔드포인트를 설정하는 방법의 지침을 확인할 수 있습니다.

잠재 고객 작성 대상을 구성하고 제품을 게시하면 Microsoft에서 연결 유효성을 검사하고 테스트 잠재 고객을 전송합니다. 제품을 라이브로 전환하기 전에 확인할 때 미리보기 환경에서 직접 제품을 구매하여 리드 연결을 테스트할 수도 있습니다. 잠재 고객 이탈을 방지하려면 잠재 고객 설정을 최신 상태로 유지해야 하므로, 파트너 정보가 변경될 때마다 이러한 연결을 업데이트해야 합니다.

<a name="what-next"></a>다음 단계
----------

기술 설정이 완료되면 이러한 리드를 현재 영업 및 마케팅 전략과 운영 프로세스에 통합해야 합니다. Microsoft는 전반적인 판매 프로세스를 보다 잘 이해하고 고품질 리드와 충분한 데이터를 제공하여 궁극적으로는 고객이 성공하도록 지원하기 위해 노력하고 있습니다. 이렇게 고객의 성공을 지원할 수 있도록, 추가 데이터를 제공하여 리드를 최적화하고 향상시킬 수 있는 방법에 대한 의견을 환영합니다. 영업 팀이 Marketplace 잠재 고객을 더 많이 확보하는 데 도움이 되는 [피드백과 제안 사항을 제공](mailto:AzureMarketOnboard@microsoft.com)하려면 전자 메일을 보내 주시기 바랍니다.
