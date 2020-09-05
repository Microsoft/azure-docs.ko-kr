---
title: Azure Security Center에서 보안 솔루션 통합 | Microsoft Docs
description: Azure Security Center를 파트너와 통합하여 Azure 리소스의 전반적인 보안을 강화하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 1af99f6317ac5888478c7df08fcc31478ef1ccef
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180850"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center에서 보안 솔루션 통합
이 문서를 통해 이미 Azure Security Center에 연결된 보안 솔루션을 관리하고 새로 추가할 수 있습니다.

## <a name="integrated-azure-security-solutions"></a>통합된 Azure 보안 솔루션
Security Center를 사용하면 Azure에서 통합된 보안 솔루션을 쉽게 사용할 수 있습니다. 이점은 다음과 같습니다.

- **배포 간소화**: Security Center에서는 통합된 파트너 솔루션의 간결한 프로비전을 제공합니다. 맬웨어 방지 및 취약성 평가와 같은 솔루션의 경우 가상 머신에서 에이전트를 프로 비전 할 수 Security Center. 방화벽 어플라이언스의 경우 필요한 네트워크 구성의 대부분을 처리할 수 Security Center.
- **통합**된 검색: 파트너 솔루션의 보안 이벤트는 자동으로 수집 되 고 집계 되며 Security Center 경고 및 인시던트의 일부로 표시 됩니다. 또한 이러한 이벤트는 다른 원본의 감지를 결합하여 고급 위협 감지 기능을 제공합니다.
- **통합된 상태 모니터링 및 관리**: 고객은 통합된 상태 이벤트를 사용하여 한 눈에 모든 파트너 솔루션을 모니터링할 수 있습니다. 기본 관리는 파트너 솔루션을 사용하여 고급 설정에 쉽게 액세스하여 사용할 수 있습니다.

현재 통합 보안 솔루션은 [Qualys](https://www.qualys.com/public-cloud/#azure) 및 [Rapid7](https://www.rapid7.com/products/insightvm/) 및 Microsoft Application Gateway 웹 응용 프로그램 방화벽에의 한 취약성 평가를 포함 합니다.

> [!NOTE]
> 대부분의 보안 공급 업체는 해당 어플라이언스에서 실행 되는 외부 에이전트를 금지 하므로 Security Center는 파트너 가상 어플라이언스에 Log Analytics 에이전트를 설치 하지 않습니다.

표준 계층 고객에 게 제공 되는 기본 제공 스캐너를 비롯 하 여 Qualys에서 취약성 검색 도구를 통합 하는 방법에 대 한 자세한 내용은 [Azure Virtual Machines에 대 한 취약성 평가](deploy-vulnerability-assessment-vm.md)를 참조 하세요.

Security Center는 다음에 대한 취약성 분석도 제공합니다.

* SQL 데이터베이스 - [취약성 평가 대시보드에서 취약성 평가 보고서 탐색](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)을 참조하세요.
* Azure Container Registry 이미지- [Security Center와 Azure Container Registry 통합 (미리 보기)](azure-container-registry-integration.md) 을 참조 하세요.

## <a name="how-security-solutions-are-integrated"></a>보안 솔루션을 통합하는 방법
Security Center에서 배포된 Azure 보안 솔루션은 자동으로 연결됩니다. 온-프레미스 또는 다른 클라우드에서 실행 되는 컴퓨터를 포함 하 여 다른 보안 데이터 원본에 연결할 수도 있습니다.

[![파트너 솔루션 통합](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>통합된 Azure 보안 솔루션 및 다른 데이터 원본 관리

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서 **Security Center**를 엽니다.

1. Security Center의 메뉴에서 **보안 솔루션**을 선택 합니다.

**보안 솔루션** 페이지에서 통합 된 Azure 보안 솔루션의 상태를 확인 하 고 기본 관리 작업을 실행할 수 있습니다.

### <a name="connected-solutions"></a>연결된 솔루션

**연결 된 솔루션** 섹션에는 현재 Security Center에 연결 된 보안 솔루션이 포함 되어 있습니다. 또한 각 솔루션의 상태를 보여 줍니다.  

![연결된 솔루션](./media/security-center-partner-integration/connected-solutions.png)

파트너 솔루션의 상태는 다음과 같을 수 있습니다.

* **정상** (녹색)-상태 문제가 없습니다.
* **비정상** (빨강)-즉각적인 주의가 필요한 상태 문제가 있습니다.
* **중지 된 보고** (주황)-솔루션이 상태 보고를 중지 했습니다.
* **보고 되지 않음** (회색)-솔루션이 아직 아무것도 보고 하지 않았으며 상태 데이터를 사용할 수 없습니다. 최근에 연결 되었고 여전히 배포 중인 솔루션의 상태가 보고 되지 않은 될 수 있습니다.

> [!NOTE]
> 상태 데이터를 사용할 수 없는 경우 Security Center에 마지막으로 수신된 이벤트의 날짜와 시간을 통해 솔루션이 보고 중인지 여부가 표시됩니다. 상태 데이터를 사용할 수 없고 지난 14 일 내에 경고가 수신 되지 않은 경우 Security Center는 솔루션이 비정상 이거나 보고 되지 않음을 나타냅니다.
>
>

**보기** 를 선택 하 여 다음과 같은 추가 정보 및 옵션을 선택 합니다.

   - **솔루션 콘솔** -이 솔루션에 대 한 관리 환경을 엽니다.
   - **VM 연결** -응용 프로그램 연결 페이지를 엽니다. 여기서 리소스를 파트너 솔루션에 연결할 수 있습니다.
   - **솔루션 삭제**
   - **구성**

   ![파트너 솔루션 세부 정보](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>검색된 솔루션

Security Center는 Azure에서 실행 되는 보안 솔루션을 자동으로 검색 하지만 Security Center에 연결 되지 않고 **검색 된 솔루션** 섹션에 솔루션을 표시 합니다. 이러한 솔루션에는 [Azure AD ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)및 파트너 솔루션과 같은 Azure 솔루션이 포함 됩니다.

> [!NOTE]
> Security Center 표준 계층은 검색 된 솔루션 기능의 구독 수준에서 필요 합니다. 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md) 을 참조 하세요.
>

Security Center와 통합 하기 위해 솔루션에서 **연결** 을 선택 하 고 보안 경고에 대 한 알림을 받습니다.

### <a name="add-data-sources"></a>데이터 원본 추가

**데이터 원본 추가** 섹션에는 연결할 수 있는 다른 사용 가능한 데이터 원본이 포함됩니다. 이러한 원본의 데이터를 추가하는 방법에 대한 지침은 **추가**를 클릭합니다.

![데이터 원본](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 파트너 솔루션을 통합하는 방법을 살펴보았습니다. 관련 정보는 다음 문서를 참조 하세요.

* [보안 경고 및 권장 사항을 내보냅니다](continuous-export.md). Azure 센티널 또는 다른 SIEM과의 통합을 설정 하는 방법에 대해 알아봅니다.
* [Security Center에서 보안 상태 모니터링](security-center-monitoring.md) Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.