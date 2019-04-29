---
title: Azure Data Catalog에서 데이터 원본에 연결하는 방법
description: Azure 데이터 카탈로그를 사용하여 검색된 데이터 원본에 연결하는 방법을 강조 표시한 방법 문서
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: c64340491dba11870364610a6c2ff62e25c1328a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001834"
---
# <a name="how-to-connect-to-data-sources"></a>데이터 원본에 연결하는 방법
## <a name="introduction"></a>소개
**Microsoft Azure 데이터 카탈로그** 는 등록 시스템 및 기업 데이터 원본을 위한 검색 시스템 역할을 하는 완전히 관리되는 클라우드 서비스입니다. 다시 말해서 **Azure 데이터 카탈로그** 는 사람들이 데이터 원본을 검색하고 이해하고 사용하도록 도우면서 조직의 기존 데이터로부터 더 많은 가치를 얻어내도록 돕는 역할을 합니다. 이 시나리오의 중요한 측면은 데이터를 사용하는 것입니다. 사용자가 데이터 원본을 검색하고 용도를 이해하면 다음 단계는 해당 데이터를 사용할 수 있도록 데이터 원본에 연결하는 것입니다.

## <a name="data-source-locations"></a>데이터 원본 위치
데이터 원본을 등록하는 동안 **Azure 데이터 카탈로그** 는 데이터 원본에 대한 메타데이터를 받습니다. 이 메타데이터는 데이터 원본 위치에 대한 세부 정보를 포함합니다. 위치에 대한 세부 정보는 데이터 원본에 따라 달라지지만 연결에 필요한 정보를 항상 포함합니다. 예를 들어 SQL Server Reporting Services 보고서에 대한 위치는 서버 이름 및 보고서에 대한 경로를 포함하는 한편 SQL Server 테이블에 대한 위치는 서버 이름, 데이터베이스 이름, 스키마 이름 및 테이블 이름을 포함합니다. 다른 데이터 원본 유형에는 원본 시스템의 구조 및 기능을 반영하는 위치가 있습니다.

## <a name="integrated-client-tools"></a>통합된 클라이언트 도구
데이터 원본에 연결하는 가장 간단한 방법은 **Azure Data Catalog** 포털에서 "다음에서 열기..." 메뉴를 사용하는 것입니다. 이 메뉴는 선택한 데이터 자산에 연결하기 위한 옵션의 목록을 표시합니다.
기본 타일 보기를 사용하는 경우 이 메뉴는 각 타일에서 사용 가능합니다.

 ![데이터 자산 타일에서 Excel로 SQL Server 테이블 열기](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

목록 보기를 사용하는 경우 메뉴는 포털 창의 위쪽에 있는 검색 표시줄에서 사용 가능합니다.

 ![검색 표시줄에서 보고서 관리자로 SQL Server Reporting Services 보고서 열기](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>지원되는 클라이언트 애플리케이션
Azure Data Catalog 포털에서 데이터 원본에 대한 "열기..." 메뉴를 사용하는 경우 클라이언트 컴퓨터에 올바른 클라이언트 애플리케이션이 설치되어 있어야 합니다.

| 애플리케이션에서 열기 | 파일 확장명 / 프로토콜 | 지원되는 애플리케이션 버전 |
| --- | --- | --- |
| Excel |.odc |Excel 2010 이상 |
| Excel (상위 1000) |.odc |Excel 2010 이상 |
| 파워 쿼리 |.xlsx |Excel용 파워 쿼리 추가 기능이 설치된 Excel 2016 또는 Excel 2010 또는 Excel 2013 |
| Power BI Desktop |.pbix |Power BI Desktop 2016년 7월 이상 |
| SQL Server Data Tools |vsweb:// |SQL Server 도구가 설치된 Visual Studio 2013 업데이트 4 이상 |
| 보고서 관리자 |http:// |[SQL Server Reporting Services에 대한 브라우저 요구 사항](https://technet.microsoft.com/library/ms156511.aspx)을 참조하세요. |

## <a name="your-data-your-tools"></a>데이터, 도구
메뉴에서 사용 가능한 옵션은 현재 선택한 데이터 자산 유형에 따라 달라집니다. 물론 모든 가능한 도구는 "다음에서 열기..." 메뉴에 포함되지 않지만 모든 클라이언트 도구를 사용하여 데이터 원본에 여전히 쉽게 연결할 수 있습니다. 데이터 자산이 **Azure Data Catalog** 포털에서 선택된 경우 전체 위치가 속성 창에 표시됩니다.

 ![SQL Server 테이블에 대한 연결 정보](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

연결 세부 정보는 데이터 원본 유형에 따라 다르지만 포털에 포함된 정보는 모든 클라이언트 도구에서 데이터 원본에 연결하는데 필요한 모든 것을 제공합니다. 사용자는 **Azure 데이터 카탈로그**를 사용하여 검색한 데이터 원본에 대한 연결 세부 정보를 복사하여 선택한 자신의 도구에서 데이터를 작업할 수 있도록 합니다.

## <a name="connecting-and-data-source-permissions"></a>연결 및 데이터 원본 사용 권한
**Azure 데이터 카탈로그** 를 통해 데이터 원본을 검색할 수 있지만 데이터 자체에 대한 액세스는 데이터 원본 소유자 또는 관리자의 통제 하에 있습니다. **Azure 데이터 카탈로그** 에서 데이터 원본을 검색하는 것은 사용자에게 데이터 원본 자체에 대한 액세스 권한을 부여하지 않습니다.

데이터 원본을 검색하지만 해당 데이터에 대한 액세스 권한이 없는 사용자를 쉽게 하려면 사용자가 데이터 원본에 주석을 지정할 때 액세스 요청 속성의 정보를 제공할 수 있습니다. 데이터 원본 액세스 획득에 대한 프로세스 링크 또는 연락처 지점을 포함한 여기에서 제공되는 정보는 포털에서 데이터 원본 위치 정보와 함께 제공됩니다.

 ![제공된 액세스 요청 지침을 사용한 연결 정보](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>요약
데이터 원본을 **Azure Data Catalog**에 등록하면 구조적 메타데이터 및 설명이 포함된 메타데이터를 데이터 원본에서 카탈로그 서비스로 복사하여 데이터를 검색할 수 있게 합니다. 데이터 원본이 등록되고 검색되면 사용자는 **Azure Data Catalog** 포털 "다음에서 열기..." "메뉴 또는 선택한 자신의 데이터 도구를 사용하여 데이터 원본에 연결할 수 있습니다.

## <a name="see-also"></a>참고 항목
* [Azure Data Catalog 시작](data-catalog-get-started.md) 자습서.
