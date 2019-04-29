---
title: Power BI 작업 영역 컬렉션에서 데이터 원본에 연결 | Microsoft Docs
description: Power BI 작업 영역 컬렉션 내에서 데이터 원본에 연결하는 방법을 알아봅니다.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 721458c5725e912d801b307ac05f3fde0776580e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110634"
---
# <a name="connect-to-a-data-source"></a>데이터 원본에 연결

**Power BI 작업 영역 컬렉션**을 사용하여 사용자 고유의 앱에 보고서를 포함할 수 있습니다. Power BI 보고서를 앱에 포함하면 데이터의 복사본을 **가져오거나** **DirectQuery**를 통해 데이터 원본에 **직접 연결**하여 기본 데이터에 보고서에 연결됩니다.

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

**가져오기**와 **DirectQuery** 사용 간의 차이점은 다음과 같습니다.

| 가져오기 | 직접 연결 |
| --- | --- |
| 테이블, 열 *및 데이터*를 보고서의 데이터 세트로 가져오거나 복사합니다. 기본 데이터에서 발생한 변경 내용을 보려면 현재 데이터 세트를 다시 새로 고치거나 가져오거나 완료해야 합니다. |*테이블 및 열*만 보고서의 데이터 세트로 가져오거나 복사합니다. 항상 최신 데이터가 표시됩니다. |

Power BI 작업 영역 컬렉션을 사용하면 클라우드 데이터 원본에서 DirectQuery를 사용할 수 있지만, 현재 온-프레미스 데이터 원본에서는 사용할 수 없습니다.

> [!NOTE]
> 현재 온-프레미스 데이터 게이트웨이는 Power BI 작업 영역 컬렉션에서 지원되지 않습니다. 즉, DirectQuery를 온-프레미스 데이터 원본과 사용할 수 없습니다.

## <a name="supported-data-sources"></a>지원되는 데이터 원본

**DirectQuery**
* Azure SQL 데이터베이스
* Azure SQL Data Warehouse

**가져오기**

Power BI Desktop 내에서 모든 사용 가능한 데이터 원본을 사용하여 가져올 수 있습니다. Power BI 작업 영역 컬렉션 내의 데이터는 새로 고칠 수 **없습니다**. Power BI 작업 영역 컬렉션에 대한 PBIX 파일에 변경 내용을 업로드해야 합니다. 이는 사용 가능한 게이트웨이가 없기 때문입니다.

## <a name="benefits-of-using-directquery"></a>DirectQuery 사용할 경우의 이점

**DirectQuery**를 사용할 경우 다음 두 가지 주요 이점이 있습니다.

* **DirectQuery**를 사용하면 대규모 데이터 세트에서 시각화를 빌드할 수 있습니다. 그렇지 않으면 먼저 모든 데이터를 가져올 수 없습니다.
* 기본 데이터 변경에는 데이터 새로 고침이 필요할 수 있으며, 일부 보고서의 경우 현재 데이터를 표시하려면 대용량 데이터 전송이 필요할 수 있습니다. 이 경우 데이터를 다시 가져올 수 없습니다. 반면, **DirectQuery** 보고서는 항상 현재 데이터를 사용합니다.

## <a name="limitations-of-directquery"></a>DirectQuery의 제한 사항

**DirectQuery**사용에 대한 몇 가지 제한 사항이 있습니다.

* 모든 테이블을 단일 데이터베이스에서 가져와야 합니다.
* 쿼리가 지나치게 복잡한 경우 오류가 발생합니다. 이 오류를 해결하려면 복잡하지 않도록 쿼리를 리팩터링해야 합니다. 쿼리가 복잡한 경우 **DirectQuery**를 사용하는 대신 데이터를 가져와야 합니다.
* 관계 필터링은 양방향이 아니라 단방향으로 제한됩니다.
* 열의 데이터 형식을 변경할 수 없습니다.
* 기본적으로 제한은 측정값에 허용되는 DAX 식에 적용됩니다. [DirectQuery 및 측정값](#measures)을 참조하세요.

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery 및 측정값
기본 데이터 원본으로 전송되는 쿼리가 허용되는 성능을 유지하도록 하기 위해 측정값에 대한 제한 사항이 적용됩니다. **Power BI Desktop**을 사용할 때 고급 사용자는 **파일 > 옵션 및 설정 > 옵션**을 선택하여 이 제한 사항을 무시하도록 선택할 수 있습니다. **옵션** 대화 상자에서 **DirectQuery**를 선택한 다음 **DirectQuery 모드에서 무제한 측정값 허용** 옵션을 선택합니다. 이 옵션을 선택하면 측정값에 유효한 모든 DAX 식을 사용할 수 있습니다. 그러나 데이터를 가져올 때 성능이 좋은 일부 식이 **DirectQuery** 모드에서는 백 엔드 원본에 대한 쿼리 속도가 느려질 수 있음을 사용자가 알고 있어야 합니다. 

## <a name="see-also"></a>관련 항목

* [Microsoft Power BI 작업 영역 컬렉션 시작](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](https://community.powerbi.com/)