---
title: Azure Data Catalog에서 관련 데이터 자산을 보는 방법
description: 이 문서에서는 Azure Data Catalog에서 선택한 데이터 자산의 관련 데이터 자산을 보는 방법을 설명합니다.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: b01c328812113ad721b7632978ad28e54a6a3ef1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61000130"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Azure Data Catalog에서 관련 데이터 자산을 보는 방법
Azure Data Catalog를 사용하면 선택한 데이터 자산과 관련된 데이터 자산을 보고 관계를 확인할 수 있습니다. 

## <a name="supported-data-sources"></a>지원되는 데이터 원본 
다음 데이터 원본의 데이터 자산을 등록하면 Azure Data Catalog는 선택한 데이터 자산 간의 조인 관계에 대한 메타데이터를 자동으로 등록합니다. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> 데이터 카탈로그에서 두 데이터 자산 간의 관계를 가져오려면 동시에 두 자산을 등록해야 합니다. 그 중 하나를 별도로 추가한 경우 해당 자산 및 다른 데이터 자산을 다시 추가하여 둘 간의 관계를 가져옵니다.

## <a name="view-related-data-assets"></a>관련된 데이터 자산 보기
선택한 데이터 세트와 관련된 데이터 자산을 보려면 다음 그림과 같이 **관계** 탭을 사용합니다. 

![Azure Data Catalog - 관련 데이터 자산 보기](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

이 예제에는 선택한 **ProductSubcategory** 데이터 자산에 대해 2가지 관계가 설정되어 있습니다. 

- Product 테이블의 ProductSubcategoryID 열은 선택한 ProductSubcategory 테이블의 ProductSubcategoryID 열과 외래 키 관계에 있습니다. 
- ProductSubCategory 테이블의 ProductCategoryID 열은 선택한 ProductCategory 테이블의 ProductCategoryID 열과 외래 키 관계에 있습니다.

> [!NOTE]
> 관계 트리 보기의 화살표 방향을 확인하세요.  

열의 정규화된 이름 같은 자세한 정보를 보려면 마우스를 위로 가져갑니다. 그러면 다음 그림과 비슷한 팝업이 표시됩니다. 

![Azure Data Catalog - 관계 팝업](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

이미 등록된 자산 간의 관계를 포함하려면 해당 자산을 다시 등록합니다.

## <a name="next-steps"></a>다음 단계
- [데이터 자산을 관리하는 방법](data-catalog-how-to-manage.md)