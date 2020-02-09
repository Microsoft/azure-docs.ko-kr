---
title: Azure Advisor 소개
description: Azure Advisor를 사용하여 Azure 배포를 최적화합니다.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443087"
---
# <a name="introduction-to-azure-advisor"></a>Azure Advisor 소개

Azure Advisor의 주요 기능에 대해 알아보고 자주 묻는 질문과 답변을 살펴봅니다.

## <a name="what-is-advisor"></a>Advisor란?
Advisor는 Azure 배포를 최적화하기 위한 모범 사례를 따르는 데 도움이 되는 개인 설정된 클라우드 컨설턴트입니다. 리소스 구성 및 사용량 원격 분석을 수행하고 Azure 리소스의 경제성, 성능, 고가용성 및 보안을 개선하는 데 도움이 되는 해결 방법을 권장합니다.

Advisor를 사용하면 다음과 같은 작업을 수행할 수 있습니다.
* 사전 대응이 가능하고, 실행 가능하고, 개인화된 모범 사례 권장 사항 
* 전체 Azure 사용을 줄일 수 있는 기회를 모색하면서 성능, 보안 및 고가용성 개선
* 온라인으로 작업이 제안되는 권장 사항 가져오기

[Azure Portal](https://aka.ms/azureadvisordashboard)을 통해 Advisor에 액세스할 수 있습니다. [Portal](https://portal.azure.com)에 로그인하고 탐색 메뉴에서 **Advisor**를 찾거나 **모든 서비스** 메뉴에서 검색합니다.

Advisor 대시보드에 모든 구독에 대한 개인화된 권장 사항이 표시됩니다.  필터를 적용하여 특정 구독 및 리소스 유형에 대한 권장 사항을 표시할 수 있습니다.  권장 사항은 다음 네 가지 범주로 나뉩니다. 

* **고가용성**: 업무상 중요한 애플리케이션의 연속성을 보장하고 향상시키는 데 도움이 됩니다. 자세한 내용은 [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)을 참조하세요.
* **보안**: 보안 위반으로 이어질 수 있는 위협 및 취약점을 검색합니다. 자세한 내용은 [Advisor 보안 권장 사항](advisor-security-recommendations.md)을 참조하세요.
* **성능**: 애플리케이션의 속도를 향상시킵니다. 자세한 내용은 [Advisor 성능 권장 사항](advisor-performance-recommendations.md)을 참조하세요.
* **비용**: 전체 Azure 사용을 최적화하고 사용량을 줄입니다. 자세한 내용은 [Advisor 비용 권장 사항](advisor-cost-recommendations.md)을 참조하세요.
* **운영 뛰어난**기능: 프로세스 및 워크플로 효율성, 리소스 관리 효율성 및 배포 모범 사례를 달성할 수 있도록 지원 합니다. . 자세한 내용은 Advisor 작동에 대한 [권장 사항](advisor-operational-excellence-recommendations.md)을 참조 하세요.

  ![Advisor 권장 사항 유형](./media/advisor-overview/advisor-dashboard.png)

범주를 클릭하여 해당 범주 내의 권장 사항 목록을 표시하고 권장 사항을 선택하여 자세한 내용을 볼 수 있습니다.  기회를 활용하거나 문제를 해결하기 위해 수행할 수 있는 작업에 대해 알아볼 수도 있습니다.

![Advisor 권장 사항 범주](./media/advisor-overview/advisor-ha-category-example.png) 

권장 사항을 구현하려면 권장 사항에 대해 권장되는 작업을 선택합니다.  간단한 인터페이스를 통해 권장 사항을 구현하거나 구현을 지원하는 설명서를 참조할 수 있습니다.  권장 사항을 구현하면 Advisor가 해당 권장 사항을 인식하는 데 최대 1일이 걸릴 수 있습니다.

권장 사항에 대해 즉각적인 작업을 수행하지 않으려는 경우 지정된 시간 후에 연기하거나 해제할 수 있습니다.  특정 구독 또는 리소스 그룹에 대한 권장 사항을 수신하지 않으려면 지정된 구독 및 리소스 그룹에 대한 권장 사항만 생성하도록 Advisor를 구성할 수 있습니다.

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

### <a name="how-do-i-access-advisor"></a>Advisor에 액세스하려면 어떻게 해야 하나요?
[Azure Portal](https://aka.ms/azureadvisordashboard)을 통해 Advisor에 액세스할 수 있습니다. [Portal](https://portal.azure.com)에 로그인하고 탐색 메뉴에서 **Advisor**를 찾거나 **모든 서비스** 메뉴에서 검색합니다.

가상 머신 리소스 인터페이스를 통해 Advisor 권장 사항을 볼 수도 있습니다. 가상 컴퓨터를 선택하고 메뉴에서 Advisor 권장 사항으로 스크롤합니다. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Advisor에 액세스하려면 어떤 권한이 필요하나요?
 
구독에 대한 *소유자*, *참여자* 또는 *읽기 권한자*로 Advisor 권장 사항에 액세스할 수 있습니다.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Advisor는 어떤 리소스에 대해 권장 사항을 제공하나요?

Advisor는 Application Gateway, App Services, 가용성 집합, Azure Cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure Express 경로, Azure Cosmos DB, Azure 공개에 대한 권장 사항을 제공 합니다. IP 주소, SQL Data Warehouse, SQL server, storage 계정, Traffic Manager 프로필 및 가상 머신.

Azure Advisor에는 추가 리소스 유형에 대한 권장 사항을 포함 하는 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) 의 권장 사항도 포함 되어 있습니다.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>권장 사항을 연기하거나 해제할 수 있나요?

권장 사항을 연기하거나 해제하려면 **연기** 링크를 클릭합니다. 연기 기간을 지정하거나, 권장 사항을 해제하려면 **안 함**을 선택합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.

* [Advisor 시작](advisor-get-started.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
