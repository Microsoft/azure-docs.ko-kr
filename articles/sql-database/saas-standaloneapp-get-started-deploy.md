---
title: 단일 테넌트 SaaS 자습서 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database를 사용하는 독립형 단일 테넌트 SaaS 애플리케이션을 배포 및 탐색합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: 4dbf53df4d3f34e80757f9575981b4b053587d97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485154"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database를 사용하는 독립형 단일 테넌트 애플리케이션을 배포 및 탐색합니다.

이 자습서에서는 독립형 애플리케이션 패턴 또는 테넌트별 앱 패턴을 사용하여 개발한 Wingtip 티켓 SaaS 애플리케이션 예제를 배포하고 탐색합니다.  애플리케이션은 다중 테넌트 SaaS 활성화 시나리오를 간소화하는 Azure SQL Database의 기능을 보여 주도록 설계되었습니다.

독립형 애플리케이션 패턴 또는 테넌트별 앱 패턴은 각 테넌트에 대해 애플리케이션 인스턴스를 배포합니다.  각 애플리케이션은 특정 테넌트에 대해 구성되고 별도 Azure 리소스 그룹에 배포됩니다. 다중 테넌트 솔루션을 제공하기 위해 애플리케이션의 여러 인스턴스를 프로비전합니다. 이 패턴은 테넌트 격리가 최우선인 테넌트 수가 더 작은 경우에 가장 적합합니다. Azure에는 테넌트를 대신하여 서비스 공급자가 리소스를 테넌트 구독에 배포하고 관리하도록 허용하는 파트너 프로그램이 있습니다. 

이 자습서에서는 3개의 테넌트에 대한 3개의 독립형 애플리케이션을 Azure 구독에 배포합니다.  개별 애플리케이션 구성 요소를 탐색하고 작업할 수 있는 전체 액세스 권한이 있습니다.

애플리케이션 소스 코드 및 관리 스크립트는 [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub 리포지토리에서 사용할 수 있습니다. 애플리케이션은 Visual Studio 2015를 사용하여 만들어졌으며, 업데이트하지 않고는 Visual Studio 2017에서 성공적으로 열리고 컴파일되지 않습니다.


이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Wingtip 티켓 SaaS 독립형 애플리케이션 배포 방법
> * 애플리케이션 소스 코드 및 관리 스크립트를 가져올 위치.
> * 앱을 구성하는 서버 및 데이터베이스 정보.

추가 자습서가 제공될 예정입니다. 이러한 자습서를 통해 이 애플리케이션 패턴을 기준으로 다양한 관리 시나리오를 탐색할 수 있습니다.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wingtip 티켓 SaaS 독립형 애플리케이션 배포

제공된 3개 테넌트에 대한 앱을 배포합니다.

1. [Azure Portal](https://portal.azure.com)에서 배포 템플릿을 열려면 파란색의 각 **Azure에 배포** 단추를 클릭합니다. 각 템플릿에는 새 리소스 그룹의 이름 및 앱의 다른 배포와 이 배포를 구분하는 사용자 이름이 매개 변수 값으로 필요합니다. 다음 단계에서는 이러한 값을 설정하는 방법을 자세히 설명합니다.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso Concert Hall**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dogwood Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. 각각의 배포에 필요한 매개 변수 값을 입력합니다.

    > [!IMPORTANT]
    > 일부 인증 및 서버 방화벽은 데모 목적으로 의도적으로 보호되지 않습니다. 각각의 애플리케이션 배포에 대한 **새 리소스 그룹을 만듭니다**.  기존 리소스 그룹을 사용하지 마세요. 이 애플리케이션이나 여기에서 만든 리소스를 프로덕션에 사용하지 마세요. 관련된 결제를 중지하려면 애플리케이션을 완료할 때 모든 리소스 그룹을 삭제합니다.

    리소스 이름에는 소문자, 숫자 및 하이픈만 사용하는 것이 가장 좋습니다.
    * **리소스 그룹** - 새로 만들기를 선택한 후 리소스 그룹의 이름을 소문자로 입력합니다. 권장 패턴은 **wingtip-sa-\<venueName\>-\<user\>** 입니다.  \<venueName\>의 경우 장소 이름을 공백 없이 대체합니다. \<user\>의 경우 아래의 사용자 값을 대체합니다.  이 패턴을 사용하면 리소스 그룹 이름은 *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*이 될 수 있습니다.
    * 드롭다운 목록에서 **위치**를 선택합니다.

    * **사용자** - 이니셜에 숫자를 더한 짧은 사용자 값이 좋습니다(예: *af1*).


3. **애플리케이션을 배포**합니다.

    * 사용 약관에 동의하려면 클릭합니다.
    * **구매**를 클릭합니다.

4. **알림**(검색 상자 오른쪽의 벨 아이콘)을 클릭하여 세 가지 전체 배포의 배포 상태를 모니터링합니다. 앱 배포에 약 5분이 걸립니다.


## <a name="run-the-applications"></a>애플리케이션 실행

앱은 이벤트를 호스트하는 장소를 표시합니다.  장소는 애플리케이션의 테넌트입니다. 각 장소는 해당 이벤트를 나열하고 티켓을 판매하는 개인 설정된 웹 사이트를 얻게 됩니다. 장소 유형에는 콘서트 홀, 재즈 클럽 및 스포츠 클럽 등이 포함됩니다. 샘플에서 장소 유형에 따라 장소의 웹 사이트에 표시되는 배경 사진이 결정됩니다.   독립 실행형 앱 모델에서 각 장소에는 별도의 애플리케이션 인스턴스와 고유한 독립형 SQL 데이터베이스가 있습니다.

1. 별도의 브라우저 탭에서 세 개의 각 테넌트에 대한 이벤트 페이지를 엽니다.

   - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

     각 URL에서 &lt;user&gt;를 배포의 사용자 값으로 바꿉니다.

   ![이벤트](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

들어오는 요청의 배포를 제어하기 위해 앱에서는 [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md)를 사용합니다. 각 테넌트 관련 앱 인스턴스는 URL에 도메인 이름의 일부로 테넌트 이름을 포함합니다. 모든 테넌트 URL에는 특정 **사용자** 값이 포함됩니다. URL은 다음 형식을 따릅니다.
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

각 테넌트의 데이터베이스 **위치**는 배포된 해당 앱의 앱 설정에 포함됩니다.

프로덕션 환경에서는 일반적으로 CNAME DNS 레코드를 만들어 [*회사 인터넷 도메인*](../traffic-manager/traffic-manager-point-internet-domain.md)이 트래픽 관리자 프로필의 URL을 가리키도록 합니다.


## <a name="explore-the-servers-and-tenant-databases"></a>서버 및 테넌트 데이터베이스 탐색

배포된 리소스 중 일부를 살펴보겠습니다.

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹 목록으로 이동합니다.
2. 세 개의 테넌트 리소스 그룹이 표시됩니다.
3. Fabrikam Jazz Club 배포의 리소스를 포함하는 **wingtip-sa-fabrikam-&lt;user&gt;** 리소스 그룹을 엽니다.  **fabrikamjazzclub-&lt;user&gt;** 서버에 **fabrikamjazzclub** 데이터베이스가 포함되어 있습니다.

각 테넌트 데이터베이스는 50개의 DTU *독립형* 데이터베이스입니다.

## <a name="additional-resources"></a>추가 리소스

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](sql-database-elastic-jobs-overview.md)
-->

- 다중 테넌트 SaaS 애플리케이션에 대해 알아보려면 [다중 테넌트 SaaS 애플리케이션을 위한 디자인 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.

 
## <a name="delete-resource-groups-to-stop-billing"></a>리소스 그룹을 삭제하여 청구 중지 ##

샘플 사용을 마친 후에는 만든 모든 리소스 그룹을 삭제하여 관련 청구를 중지합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * Wingtip 티켓 SaaS 독립형 애플리케이션 배포 방법
> * 앱을 구성하는 서버 및 데이터베이스 정보.
> * 샘플 리소스를 삭제하여 관련 결제를 중지하는 방법

다음 단계로 [프로비전 및 카탈로그](saas-standaloneapp-provision-and-catalog.md) 자습서를 참고하여 스키마 관리, 테넌트 분석과 같은 다양한 교차 테넌트 시나리오를 지원하는 테넌트 카탈로그의 용도를 살펴보세요.
 

