---
title: Power BI 작업 영역 컬렉션으로 행 수준 보안
description: Power BI 작업 영역 컬렉션으로 행 수준 보안에 대한 세부 정보
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 2d0c8629e4abf9e27cd204b9a13b79808b177501
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103635"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Power BI 작업 영역 컬렉션으로 행 수준 보안

보고서 또는 데이터 세트 내의 특정 데이터에 대한 사용자 액세스를 제한하는 데 RLS(행 수준 보안)를 사용하여 여러 다양한 사용자가 모두 서로 다른 데이터를 보면서 동일한 보고서를 사용할 수 있도록 합니다. Power BI 작업 영역 컬렉션은 RLS로 구성된 데이터 세트를 지원합니다.

![Power BI 작업 영역 컬렉션의 행 수준 보안 흐름](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

RLS를 활용하기 위해서는 사용자, 역할 및 규칙이라는 세 가지 주요 개념을 이해하는 것이 중요합니다. 이러한 개념 각각에 대해 조금 더 자세히 살펴보겠습니다.

**사용자** - 보고서를 보는 실제 최종 사용자입니다. Power BI 작업 영역 컬렉션에서 사용자는 앱 토큰의 사용자 이름 속성으로 식별됩니다.

**역할** - 사용자는 역할에 속합니다. 역할은 규칙에 대한 컨테이너로, "Sales Manager" 또는 "Sales Rep"와 같이 이름을 지정할 수 있습니다. Power BI 작업 영역 컬렉션에서 사용자는 앱 토큰의 역할 속성으로 식별됩니다.

**규칙** – 역할에는 규칙이 있고 해당 규칙은 데이터에 적용할 실제 필터입니다. "Country = USA"처럼 간단하거나 훨씬 동적일 수 있습니다.

### <a name="example"></a>예

이 문서의 나머지 부분에서는 RLS를 작성하는 예를 제공한 후 포함된 애플리케이션 내에서 이를 사용하는 것을 보여 줍니다. 이 예에서는 [소매 분석 샘플](https://go.microsoft.com/fwlink/?LinkID=780547) PBIX 파일을 사용합니다.

![예제 판매 보고서](media/row-level-security/scenario-2.png)

소매 분석 샘플은 특정 소매 체인에 속하는 모든 상점에 대한 판매를 보여 줍니다. RLS가 없다면 어떤 지역 관리자가 로그인하여 보고서를 보든지 동일한 데이터를 보게 됩니다. 고위 경영진은 각 지역 관리자가 자신이 관리하는 상점에 대한 판매만 보도록 결정했으며 이를 위해 RLS를 사용할 수 있습니다.

RLS는 Power BI Desktop으로 작성됩니다. 데이터 세트 및 보고서가 열리면 다이어그램 보기로 전환하여 스키마를 볼 수 있습니다.

![Power BI Desktop의 모델 다이어그램](media/row-level-security/diagram-view-3.png)

이 스키마로 알 수 있는 몇 가지 사항은 다음과 같습니다.

* **Total Sales**와 같은 모든 측정값은 **Sales** 팩트 테이블에 저장됩니다.
* 다음과 같은 4개의 추가 관련 차원 테이블이 있습니다. **Item**, **Time**, **Store** 및 **District**.
* 관계선의 화살표는 테이블 간에 필터가 흐를 수 있는 방향을 나타냅니다. 예를 들어, 필터가 **Time[Date]** 에 배치되면 현재 스키마에서 **Sales** 테이블의 값만 필터링합니다. 관계선에서 모든 화살표가 sales 테이블만 가리키므로 이 필터에 다른 테이블은 영향을 받지 않습니다.
* **District** 테이블은 각 지역에 대한 관리자가 누구인지를 나타냅니다.
  
  ![District 테이블 행](media/row-level-security/district-table-4.png)

이 스키마에 따라 District 테이블의 **District Manager** 열에 필터를 적용하는 경우, 해당 필터가 보고서를 보는 사용자와 일치하는 경우 이 필터는 **Store** 및 **Sales** 테이블을 필터링하여 특정 지역 관리자에 대한 데이터만 보여 줍니다.

방법을 알아보겠습니다.

1. 모델링 탭에서 **역할 관리**를 클릭합니다.  
   ![모델링 리본의 역할 관리 단추](media/row-level-security/modeling-tab-5.png)
2. **관리자**라는 새 역할을 만듭니다.  
   ![Power BI Desktop에서 역할 생성](media/row-level-security/manager-role-6.png)
3. **District** 테이블에서 다음 DAX 식을 입력합니다. **[District Manager] = USERNAME()**  
   ![역할의 테이블에 대한 DAX 필터 식](media/row-level-security/manager-role-7.png)
4. 규칙이 작동하는지 확인하려면 **모델링** 탭에서 **역할로 보기**를 클릭한 후 다음을 입력합니다.  
   ![역할로 보기](media/row-level-security/view-as-roles-8.png)

   이제 보고서는 **Andrew Ma**로 로그인한 것처럼 데이터를 보여 줍니다.

여기서 수행한 방향으로 필터를 적용하면 **District**, **Store** 및 **Sales** 테이블의 모든 레코드가 필터링됩니다. 그러나 **Sales** 및 **Time**, **Sales** 및 **Item** 간의 관계에서 필터 방향으로 인해 **Item** 및 **Time** 테이블은 필터링되지 않습니다.

![관계가 강조 표시된 다이어그램 뷰](media/row-level-security/diagram-view-9.png)

이 요구 사항의 경우 문제가 되지 않지만 관리자가 판매 항목이 없는 항목을 보고 싶어하지 않는 경우 관계에 대해 양방향 교차 필터링을 설정할 수 있으며 보안 필터가 두 방향으로 흐를 수 있습니다. 이 작업은 다음과 같이 **Sales** 및 **Item** 간의 관계를 편집하여 수행할 수 있습니다.

![관계에 대한 교차 필터 방향](media/row-level-security/edit-relationship-10.png)

이제 필터는 Sales 테이블에서 **Item** 테이블로도 흐를 수 있습니다.

![다이어그램 뷰의 관계에 대한 필터 방향 아이콘](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> 데이터에 대해 DirectQuery 모드를 사용 중인 경우 다음 두 옵션을 선택하여 양방향 교차 필터링을 활성화해야 합니다.

1. **파일** -> **옵션 및 설정** -> **미리 보기 기능** -> **DirectQuery에 대해 양방향 교차 필터링 활성화**.
2. **파일** -> **옵션 및 설정** -> **DirectQuery** -> **DirectQuery 모드에서 무제한 측정값 허용**.

양방향 교차 필터링에 대해 알아보려면 [SQL Server Analysis Services 2016 및 Power BI Desktop에서 양방향 교차 필터링](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx) 백서를 다운로드하세요.

그러면 Power BI Desktop에서 수행해야 할 모든 작업이 마무리되지만 Power BI Embedded에서 작업을 정의한 RLS 규칙을 만들기 위해 몇 가지 작업을 수행해야 합니다. 애플리케이션에서 사용자가 인증 및 권한 부여되고 특정 Power BI Embedded 보고서에 사용자 액세스를 부여하는 데 앱 토큰이 사용됩니다. Power BI Embedded는 사용자가 누구인지에 대한 어떠한 특정한 정보도 포함하지 않습니다. RLS가 작동하기 위해서는 앱 토큰의 일부로 추가 컨텍스트를 전달해야 합니다.

* **username** (선택 사항) – RLS에 사용되며 RLS 규칙을 적용할 때 사용자를 식별하는 데 사용할 수 있는 문자열입니다. Power BI Embedded를 사용하는 행 수준 보안 사용을 참조하세요.
* **역할** – 행 수준 보안 규칙을 적용할 때 선택할 역할이 들어 있는 문자열입니다. 둘 이상의 역할을 전달하는 경우 문자열 배열로 전달해야 합니다.

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) 메서드를 사용하여 토큰을 만듭니다. 사용자 이름 속성이 있으면 역할에 하나 이상의 값을 전달해야 합니다.

예를 들어 EmbedSample을 변경할 수 있습니다. DashboardController 줄 55는 다음과 같이 업데이트할 수 있습니다.

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

to

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

전체 앱 토큰은 다음과 같은 모양입니다.

![JSON Web Token 예제](media/row-level-security/app-token-string-12.png)

이제 종합해보면 누군가가 이 보고서를 보기 위해 애플리케이션에 로그인하면 행 수준 보안에 정의된 대로 자신에게 보기가 허용된 데이터만 표시됩니다.

![애플리케이션에 표시된 보고서](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>참고 항목

[Power를 사용하는 RLS(행 수준 보안)](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Embed 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](https://community.powerbi.com/)
