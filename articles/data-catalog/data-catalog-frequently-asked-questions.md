---
title: Azure 데이터 카탈로그에 대한 질문과 대답
description: 데이터 원본 검색, 주석 및 관리에 대한 기능을 포함한 Azure Data Catalog에 대한 질문과 대답입니다.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 7c5241b9df23bb0334a39f2c684fd1bdff40b4c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61004044"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure 데이터 카탈로그에 대한 질문과 대답
이 문서는 Azure Data Catalog 서비스와 관련된 질문에 대한 대답을 제공합니다.

## <a name="what-is-azure-data-catalog"></a>Azure 데이터 카탈로그란?
데이터 카탈로그는 등록 시스템 및 기업 데이터 원본을 위한 검색 시스템 역할을 하는 Microsoft Azure에서 호스팅되는 완전히 관리되는 서비스입니다. 데이터 카탈로그를 사용하여 분석가에서 데이터 과학자, 개발자에 이르는 모든 사용자는 데이터 원본을 등록, 검색, 이해 및 소비할 수 있습니다.

## <a name="what-customer-challenges-does-it-solve"></a>어떠한 고객 문제를 해결하나요?
데이터 카탈로그는 사용자가 엔터프라이즈 데이터 원본을 검색 및 이해할 수 있도록 하여 데이터 원본 검색 및 “다크 데이터”의 과제를 다룹니다.

## <a name="what-are-its-target-audiences"></a>해당 대상 고객층은 무엇입니까?
데이터 카탈로그는 다음을 비롯하여 기술자 및 일반 사용자를 위해 설계되었습니다.

* 데이터 개발자, BI 및 분석 전문가: 사용자를 이용할 다른 사람에 대 한 데이터 및 분석 콘텐츠를 생성 하는 일을 담당 합니다.
* 데이터 관리자: 데이터, 의미 및 사용할 의도 하는 방법에 대 한 지식이 있는 사람입니다.
* 데이터 소비자: 쉽게 검색할 수는 사용자를 이해 하 고 선택한 도구를 사용 하 여 해당 작업 수행에 필요한 데이터에 연결 합니다.
* 중앙 IT: 비즈니스 사용자가 수백 개의 데이터 소스를 가능 하 게 해야 하는 한 사람 및 데이터가 사용 되는 방법을 통해 감독을 유지 해야 하는 사람입니다.

## <a name="what-is-its-availability-by-region"></a>지역별 가용성은 무엇입니까?
데이터 카탈로그 서비스는 현재 다음과 같은 데이터 센터에서 사용할 수 있습니다.

* 미국 서부
* 미국 동부
* 서유럽
* 유럽 북부
* 오스트레일리아 동부
* 동남아시아

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>데이터 자산 수에 대한 제한은 무엇입니까?
데이터 카탈로그 무료 버전은 등록된 데이터 자산 5,000개로 제한됩니다.

데이터 카탈로그 표준 버전은 최대 100,000개의 등록된 데이터 자산을 지원합니다.

테이블, 뷰, 파일, 보고서 등, 데이터 카탈로그에 등록된 모든 개체는 데이터 자산으로 간주됩니다.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>지원되는 데이터 원본과 자산 형식은 무엇인가요?
현재 지원되는 데이터 원본 목록은 [데이터 카탈로그 DSR](data-catalog-dsr.md)을 참조하세요.

## <a name="how-do-i-request-support-for-another-data-source"></a>다른 데이터 원본에 대한 지원을 어떻게 요청하나요?
기능 요청 및 기타 피드백을 제출하려면 [Azure Feedback Forums의 데이터 카탈로그](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources)으로 이동합니다.

## <a name="how-do-i-get-started-with-data-catalog"></a>데이터 카탈로그를 시작하려면 어떻게 하나요?
시작하는 가장 좋은 방법은 [데이터 카탈로그 시작](data-catalog-get-started.md)으로 이동하는 것입니다. 이 문서는 서비스의 기능에 대한 종단간 개요입니다.

## <a name="how-do-i-register-my-data"></a>내 데이터를 어떻게 등록하나요?
데이터 카탈로그에 데이터를 등록하려면:
1. Azure Data Catalog 포털의 **게시** 영역에서 Azure Data Catalog 등록 도구를 시작합니다. 
2. 데이터 카탈로그 데이터 원본 등록 도구에서 데이터 카탈로그 포털에 액세스하는 데 사용하는 동일한 자격 증명으로 로그인합니다.
3. 등록하려는 데이터 원본 및 특정 자산을 선택합니다.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>등록되는 데이터 자산에서 어떤 속성을 추출하나요?
데이터 원본과 데이터 원본 사이의 특정 속성은 다르지만 일반적으로 데이터 카탈로그 게시 서비스는 다음 정보를 추출합니다.

* 자산 이름
* 자산 형식
* 자산 설명
* 특성/열 이름
* 특성/열 데이터 형식
* 특성/열 설명

> [!IMPORTANT]
> 데이터 카탈로그를 사용한 데이터 자산 등록은 클라우드에 데이터를 옮기거나 복사하지 않습니다. 데이터 원본에서 자산을 등록하면 자산의 메타데이터를 Azure로 복사하지만 해당 데이터는 기존 데이터 원본 위치에 유지됩니다. 이 규칙에서 유일한 예외는 자산 등록 시 사용자가 미리 보기 레코드 또는 데이터 프로필을 업로드하기로 선택하는 경우입니다. 미리 보기를 포함하는 경우 최대 20개의 레코드가 각 자산에서 복사되며 데이터 카탈로그에 스냅숏으로 저장됩니다. 데이터 프로필을 포함하는 경우 집계 정보가 계산되며 카탈로그에 저장된 메타데이터에 포함됩니다. 집계 정보는 테이블의 크기, 열당 null 값의 비율 또는 열에 대한 최소, 최대 및 평균 값을 포함할 수 있습니다. 
>
>

> [!NOTE]
> 최우선 **Description** 속성이 있는 SQL Server Analysis Services와 같은 데이터 원본의 경우, 데이터 카탈로그 데이터 원본 등록 도구가 해당 속성 값을 추출합니다. 최우선 **Description** 속성이 없는 SQL Server 관계형 데이터베이스의 경우, 데이터 카탈로그 데이터 원본 등록 도구가 개체 및 열에 대한 **ms_description** 확장 속성에서 해당 값을 추출합니다. 자세한 내용은 [데이터베이스 개체의 확장 속성 사용](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)을 참조하세요.
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>새로 등록된 자산이 카탈로그에 나타나려면 얼마나 걸리나요?
데이터 카탈로그를 사용하여 자산을 등록하면 데이터 카탈로그 포털에 나타나기까지 5~10초 정도 걸릴 수 있습니다.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>등록된 데이터 자산에 대한 메타데이터에 주석을 추가하고 보강하려면 어떻게 하나요?
등록된 자산에 대한 메타데이터를 제공하는 가장 간단한 방법은 데이터 카탈로그 포털에서 자산을 선택하여 선택한 개체의 속성 창이나 스키마 창에 값을 입력하는 것입니다.

등록하는 과정 동안 전문가 및 태그와 같은 일부 메타데이터를 제공할 수도 있습니다. 데이터 카탈로그 게시 서비스에 제공된 값은 당시에 등록되는 모든 자산에 적용됩니다. 추가 주석에 대해 포털에서 최근에 등록된 개체를 보려면 데이터 카탈로그 데이터 원본 등록 도구의 마지막 화면에서 **포털 보기** 단추를 선택합니다.

## <a name="how-do-i-delete-my-registered-data-objects"></a>등록된 데이터 개체를 삭제하려면 어떻게 하나요?
포털에서 개체를 선택한 다음 **삭제** 단추를 클릭하여 데이터 카탈로그에서 개체를 삭제할 수 있습니다. 개체를 제거하면 데이터 카탈로그에서 해당 메타데이터를 제거하지만 기본 데이터 원본에는 영향을 주지 않습니다.

## <a name="what-is-an-expert"></a>전문가란 무엇인가요?
전문가는 데이터 개체에 대해 정보에 근거한 관점을 가진 사람입니다. 개체에는 여러 전문가가 있을 수 있습니다. 전문가가 개체에 대한 “소유자”가 될 필요는 없습니다. 쉽게 말해 전문가는 데이터 사용 방법과 사용 목적을 아는 사람입니다.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>문제가 발생했을 때 데이터 카탈로그 팀과 정보를 공유하려면 어떻게 하나요?
문제를 보고하고, 정보를 공유하며, 질문을 하려면 [Azure Data Catalog 포럼](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)으로 이동합니다.

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>카탈로그에서 관심이 있는 다른 데이터 원본과 함께 작업하나요?
Microsoft는 데이터 카탈로그에 더 많은 데이터 원본을 추가하는 작업을 활발히 하고 있습니다. 특정 데이터 원본이 지원되는지 확인하려는 경우 [Azure 피드백 포럼의 Data Catalog](https://feedback.azure.com/forums/906052-data-catalog)로 이동하여 제안하시기 바랍니다. 또는 이미 제안되어 있는 경우 제안에 의견을 더해 주시기 바랍니다.

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Azure Data Catalog는 어떻게 Office 365용 Power BI의 데이터 카탈로그와 연결되나요?
Azure Data Catalog를 Power BI의 데이터 카탈로그가 진화한 것으로 생각할 수 있습니다. 2017년 봄 기준으로 Azure Data Catalog는 Excel 2016 및 Excel용 파워 쿼리에서 쿼리 공유 및 검색을 활성화하는 데 사용됩니다. Excel의 데이터 카탈로그 기능은 Power BI Pro 라이선스를 갖고 있는 사용자에게 제공됩니다.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>데이터 카탈로그로 자산을 등록하는 데 필요한 권한은 무엇인가요?
데이터 카탈로그 등록 도구를 실행하려면 사용자는 원본에서 메타데이터를 읽을 수 있도록 허용하는 데이터 원본에 대한 권한이 필요합니다. 미리 보기를 포함하려면 등록되는 개체에서 데이터를 읽을 수 있도록 허용하는 권한이 있어야 합니다.

데이터 카탈로그에서는 카탈로그 관리자가 메타데이터를 카탈로그에 추가할 수 있는 사용자 및 그룹을 제한할 수도 있습니다. 자세한 내용은 [데이터 카탈로그 및 데이터 자산에 대한 액세스를 보호하는 방법](data-catalog-how-to-secure-catalog.md)을 참조하세요.

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>데이터 카탈로그는 온-프레미스 배포에도 사용할 수 있나요?
데이터 카탈로그는 하이브리드 데이터 원본 검색 솔루션을 제공하는 클라우드 및 온-프레미스 데이터 원본과도 작업할 수 있는 클라우드 서비스입니다. 현재 온-프레미스를 실행하는 데이터 카탈로그 서비스 버전에 대한 계획은 없습니다.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>등록하는 데이터 원본에서 더 많거나 풍부한 메타데이터를 추출할 수 있나요?
데이터 카탈로그의 기능을 확장하는 작업을 활발히 진행 중입니다. 등록하는 동안 데이터 원본에서 추출된 추가 메타데이터를 포함하려는 경우, [Azure Feedback Forums의 데이터 카탈로그](https://feedback.azure.com/forums/906052-data-catalog)에서 이를 제안하시기 바랍니다. 또는 이미 제안되어 있는 경우 투표하시기 바랍니다. 

데이터 원본 등록 도구가 해당 메타데이터를 추출하지 않은 데이터 원본에 대해 열/스키마 메타데이터, 미리 보기 도는 데이터 프로필을 포함하려면 데이터 카탈로그 API를 사용하여 이 메타데이터를 추가할 수 있습니다.  자세한 내용은 [Azure Data Catalog REST API](https://docs.microsoft.com/rest/api/datacatalog/)를 참조하세요.

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>등록된 데이터 자산의 표시 여부를 제한하여 특정인만 검색하도록 하려면 어떻게 하나요?
데이터 카탈로그에서 해당 데이터 자산을 선택한 다음 **소유권 가져오기** 단추를 클릭합니다. 데이터 카탈로그의 데이터 자산에 대한 소유권은 표시 여부 설정을 변경하여 모든 사용자가 소유한 자산을 검색하거나 특정 사용자에게 표시 여부를 제한하도록 할 수 있습니다. 자세한 내용은 [Azure Data Catalog의 데이터 자산 관리](data-catalog-how-to-manage.md)를 참조하세요.

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>데이터 원본의 변경 사항이 카탈로그에 반영되도록 데이터 자산 등록을 업데이트하려면 어떻게 하나요?
카탈로그에 이미 등록된 데이터 자산에 대한 메타데이터를 업데이트하려면 단순히 해당 자산을 포함하는 데이터 원본을 다시 등록하면 됩니다. 테이블이나 뷰에서 추가 또는 제거되는 열과 같이 데이터 원본의 변경 사항은 카탈로그에서 업데이트되지만, 사용자가 제공한 주석은 유지됩니다.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>내 질문에 대답이 없습니다. 어디에서 답변을 찾을 수 있나요?
[Azure Data Catalog 포럼](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)으로 이동합니다. 게시된 질문을 여기에서 찾을 수 있습니다.
