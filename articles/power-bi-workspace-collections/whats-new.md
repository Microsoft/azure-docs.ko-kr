---
title: Power BI 작업 영역 컬렉션의 새로운 기능
description: Power BI 작업 영역 컬렉션의 새로운 기능에 대한 최신 정보 얻기
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 9ef9416abb4aaf0927d1e072286ef83a4d8c39cb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118810"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Power BI 작업 영역 컬렉션의 새로운 기능

**Power BI 작업 영역 컬렉션**에 대한 업데이트는 정기적으로 릴리스됩니다. 그러나 일부 릴리스에서 백 엔드 서비스 기능에 초점을 맞추므로 모든 릴리스는 새로운 사용자용 기능을 포함하지 않습니다. 새로운 사용자용 기능은 여기에 강조 표시됩니다.

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

## <a name="march-2017"></a>2017년 3월

**셀프 서비스 기능**

* [새 보고서 만들기](create-report-from-dataset.md)
* [다른 이름으로 보고서 저장](save-reports.md)
* 읽기/편집/새로 만들기 모드에서 보고서 포함 
* [편집/읽기 모드 간 보고서 전환](toggle-mode.md)

**REST API를 사용한 데이터 연결**

* [데이터 세트 만들기](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* 데이터 푸시 

**관리 API**

* 보고서 및 데이터 세트 복제
* 다른 데이터 세트에 보고서 바인딩

**샘플**

* 업데이트된 [JavaScript Report Embed 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>2016년 12월

* [새로운 JavaScript 포함 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>2016년 10월

* [Power BI 작업 영역 컬렉션 및 R을 사용한 고급 분석](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>2016년 8월 31일
이 릴리스에 포함된 내용:

* [고급 필터링 및 페이지 탐색](interact-with-reports.md)을 지원하도록 JavaScript SDK가 완전히 새롭게 바뀌었습니다.
* 이제 캐나다 중부 데이터 센터에서 Power BI 작업 영역 컬렉션이 지원됩니다. [데이터 센터 상태](https://azure.microsoft.com/status/)를 확인하세요.

## <a name="july-11-2016"></a>2016년 7월 11일
이 릴리스에 포함된 내용:

* **좋은 소식** Power BI 작업 영역 컬렉션 서비스는 더 이상 미리 보기가 아니며 이제 일반 공급(GA)됩니다.  
* 모든 REST API가 **/beta**에서 **/v1.0**으로 이동되었습니다.
* .NET 및 JavaScript SDK가 **v1.0**용으로 업데이트되었습니다.
* 이제 API 키를 사용하여 Power BI API 호출을 직접 인증할 수 있습니다. 포함에는 앱 토큰만 필요합니다. 이 과정의 일부로 프로비전 및 개발 토큰이 v1.0 API에서 사용 중단되었지만 2016년 12월 30일까지 베타 버전으로 계속 작동됩니다. 자세한 내용은 [Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)를 참조하세요.
* RLS(행 수준 보안)는 앱 토큰 및 포함된 보고서에 대해 지원됩니다. 자세한 내용은 [Power BI 작업 영역 컬렉션을 사용하는 행 수준 보안](row-level-security.md)을 참조하세요.
* 모든 **v1.0** API 호출에 사용할 수 있도록 샘플 애플리케이션이 업데이트되었습니다.
* Power BI 작업 영역 컬렉션은 Azure SDK, PowerShell 및 CLI에 대해 지원됩니다.
* 사용자가 시각화 데이터를 **.csv**로 내보낼 수 있습니다.
* 이제 동일한 모든 언어/로캘에서 Microsoft Azure로 Power BI 작업 영역 컬렉션이 지원됩니다. 자세한 내용은 [Azure - 언어](https://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx)를 참조하세요.

