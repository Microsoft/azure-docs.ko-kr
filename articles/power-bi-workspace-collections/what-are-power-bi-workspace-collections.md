---
title: Power BI 작업 영역 컬렉션이란?
description: Power BI Embedded를 사용하면 Power BI 보고서를 웹 또는 모바일 애플리케이션으로 통합할 수 있으므로 사용자 지정 솔루션을 빌드할 필요가 없습니다.
services: power-bi-embedded
author: rkarlin
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: rkarlin
ms.openlocfilehash: 7f23856363b337a361f329ed54e2152842faf26e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118945"
---
# <a name="what-are-power-bi-workspace-collections"></a>Power BI 작업 영역 컬렉션이란?

**Power BI 작업 영역 컬렉션**을 사용하면 Power BI 보고서를 웹 또는 모바일 애플리케이션에 통합할 수 있습니다.

![애플리케이션 다이어그램](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

Power BI 작업 영역 컬렉션은 ISV(독립 소프트웨어 공급업체) 및 앱 개발자가 애플리케이션 내에서 Power BI 데이터 환경을 표시할 수 있도록 하는 **Azure 서비스** 입니다. 개발자가 애플리케이션을 구축한 경우 이러한 애플리케이션에는 고유한 사용자 및 고유한 기능 집합이 있습니다. 이러한 앱에는 이제 Microsoft Power BI 작업 영역 컬렉션을 통해 강력한 기능을 가질 수 있는 차트 및 보고서와 같은 일부 기본 제공 데이터 요소가 있을 수도 있습니다. 앱을 사용하기 위한 Power BI 계정이 필요 없습니다. 이전처럼 애플리케이션에 계속 로그인할 수 있으며 추가 라이선스를 요구하지 않고 Power BI 보고 환경을 보고 조작할 수 있습니다.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Microsoft Power BI 작업 영역 라이선스

**Microsoft Power BI 작업 영역 컬렉션** 사용 모델에서 Power BI 라이선스에 대한 책임이 최종 사용자에게 있지 않습니다.  대신 시각적 개체를 사용하는 앱의 개발자가 **세션**을 구매하고 해당 리소스를 소유하는 구독에 요금이 부과됩니다. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Microsoft Power BI 작업 영역 컬렉션 개념적 모델

![작업 영역 컬렉션을 사용하는 애플리케이션 흐름](media/what-are-power-bi-workspace-collections/model.png)

Azure의 다른 서비스와 같이 Power BI 작업 영역 컬렉션의 리소스는 [Azure Resource Manager API](https://msdn.microsoft.com/library/mt712306.aspx)를 통해 프로비전됩니다. 이 경우에 프로비전하는 리소스는 **Power BI 작업 영역 컬렉션**입니다.

## <a name="workspace-collection"></a>작업 영역 컬렉션

**작업 영역 컬렉션**은 0개 이상의 **작업 영역**을 포함하는 리소스의 최상위 Azure 컨테이너입니다.  **작업 영역** **컬렉션**에는 모든 표준 Azure 속성과 다음 항목이 있습니다.

* **액세스 키** - Power BI API(다음 섹션에서 설명)를 안전하게 호출할 때 사용하는 키입니다.
* **사용자** - Azure Portal 또는 Azure Resource Manager API를 통해 Power BI 작업 영역 컬렉션을 관리하는 관리자 권한이 있는 AAD(Azure Active Directory) 사용자입니다.
* **지역** – **작업 영역 컬렉션** 프로비전의 일부이며 프로비전할 지역을 선택할 수 있습니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

## <a name="workspace"></a>작업 영역

**작업 영역** 은 데이터 세트 및 보고서를 포함할 수 있는 Power BI 콘텐츠의 컨테이너입니다. **작업 영역** 은 처음 만들 때 비어 있습니다. Power BI Desktop을 사용하여 콘텐츠를 작성하고 [Power BI REST API](https://msdn.microsoft.com/library/mt711504.aspx)를 사용하여 프로그래밍 방식으로 PBIX를 작업 영역에 배치합니다. 또한 Power BI Desktop을 사용하는 대신 프로그래밍 방식으로 데이터 집합을 만든 다음 애플리케이션 내에서 보고서를 만들 수 있습니다.

## <a name="using-workspace-collections-and-workspaces"></a>작업 영역 컬렉션 및 작업 영역 사용

**작업 영역 컬렉션** 및 **작업 영역**은 빌드할 애플리케이션의 디자인에 가장 적합한 방법으로 사용되거나 구성되는 콘텐츠의 컨테이너입니다. 컨테이너 내에서 콘텐츠를 정렬할 수 있는 다양한 방법이 있습니다. 하나의 작업 영역 내에 모든 콘텐츠를 배치한 다음 나중에 앱 토큰을 사용하여 고객 간 콘텐츠를 더 세분화하는 방법을 선택할 수 있습니다. 또한 고객이 어느 정도 구분이 되도록 모든 고객을 분할된 작업 영역에 배치하는 방법을 선택할 수 있습니다. 또는 고객이 아닌 지역으로 사용자를 구성하는 방법을 선택할 수 있습니다. 이 유연한 디자인을 사용하면 콘텐츠를 구성할 가장 좋은 방법을 선택할 수 있습니다.

## <a name="cached-datasets"></a>캐시된 데이터 세트

캐시된 데이터 세트를 사용할 수 있습니다.  그러나 캐시된 데이터가 **Microsoft Power BI 작업 영역 컬렉션**에 로드되면 새로 고칠 수 없습니다. 캐시된 데이터 세트는 DirectQuery를 사용하는 대신 Power BI Desktop으로 데이터를 가져왔다는 것을 의미합니다.

## <a name="authentication-and-authorization-with-app-tokens"></a>앱 토큰으로 인증 및 권한 부여

**Microsoft Power BI 작업 영역 컬렉션**은 애플리케이션에 따라 필요한 모든 사용자 인증 및 권한 부여를 수행합니다. 최종 사용자가 Azure AD(Azure Active Directory)의 고객이어야 한다는 명시적인 요구 사항은 없습니다.  대신, 애플리케이션은 **Microsoft Power BI 작업 영역 컬렉션** 권한 부여에서 **애플리케이션 인증 토큰(앱 토큰)** 을 사용하여 Power BI 보고서를 렌더링하도록 지시합니다.  이러한 **앱 토큰** 은 앱에서 보고서를 렌더링하려고 할 때 필요한 경우에 만들어집니다.

![앱 토큰 사용 다이어그램](media/what-are-power-bi-workspace-collections/app-tokens.png)

**애플리케이션 인증 토큰(앱 토큰)** 은 **Microsoft Power BI 작업 영역 컬렉션**에 대한 인증에 사용됩니다.  다음과 같은 세 가지 형식의 **앱 토큰**이 있습니다.

1. 프로비전 토큰 - 새 **작업 영역**을 **작업 영역 컬렉션**에 프로비전할 때 사용
2. 개발 토큰 - **Power BI REST API**
3. 포함 토큰 - 포함된 iframe에서 보고서를 렌더링하도록 호출할 때 사용

이러한 토큰은 **Microsoft Power BI 작업 영역 컬렉션**과 상호 작용하는 여러 단계에 사용됩니다.  앱에서 Power BI로 사용 권한을 위임할 수 있도록 토큰을 디자인합니다. 자세한 내용은 [앱 토큰 흐름](app-token-flow.md)을 참조하세요.

## <a name="create-or-edit-reports-within-your-application"></a>애플리케이션 내에서 보고서 만들기 또는 편집

이제 Power BI Desktop을 사용하지 않고도 애플리케이션에서 직접 기존 보고서를 편집하거나 새 보고서를 만들 수 있습니다. 이렇게 하려면 작업 영역 내에 데이터 세트가 있어야 합니다.

## <a name="see-also"></a>참고 항목

[일반적인 Microsoft Power BI 작업 영역 컬렉션 시나리오](scenarios.md)  
[Microsoft Power BI 작업 영역 컬렉션 시작](get-started.md)  
[샘플 시작](get-started-sample.md)  
[보고서 포함](embed-report.md)  
[Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)  
[JavaScript Embed 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git 리포지토리](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git 리포지토리](https://github.com/Microsoft/PowerBI-Node)  

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](https://community.powerbi.com/)
