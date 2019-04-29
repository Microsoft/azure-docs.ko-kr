---
title: SQL Data Warehouse용 Visual Studio 및 SSDT 설치 | Microsoft Docs
description: Azure SQL Data Warehouse용 Visual Studio 및 SSDT(SQL Server 개발 도구) 설치
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/05/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f67c2a4547ee923e5c1b49302c38693e9ffe87c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474252"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>SQL Data Warehouse용 Visual Studio 및 SSDT 설치
SQL Data Warehouse에 대 한 응용 프로그램을 개발 하려면 Visual Studio 2017을 사용 합니다. 현재 Visual Studio 2019 SSDT는 SQL Data Warehouse에 대 한 지원 되지 않습니다. 

SSDT와 함께 Visual Studio를 사용하면 SQL Server 개체 탐색기를 사용하여 SQL Data Warehouse의 테이블, 뷰, 저장 프로시저를 비롯한 많은 개체를 시각적으로 탐색하고 쿼리를 실행할 수 있습니다.

> [!NOTE]
> SQL Data Warehouse는 아직 Visual Studio 데이터베이스 프로젝트를 지원하지 않습니다. 이 기능에 대한 주기적인 업데이트를 수신하려면 [UserVoice]에서 투표해주세요.
> 
> 

## <a name="step-1-install-visual-studio"></a>1단계: Visual Studio 설치
이 링크를 따라 Visual Studio를 다운로드하고 설치합니다. Visual Studio 2013 이상이 설치되어 있는 경우 2단계로 건너뛰어 SSDT를 설치할 수 있습니다.

1. [Visual Studio를 다운로드][]합니다.
2. MSDN의 [Visual Studio 설치][Installing Visual Studio] 지침을 따르고 기본 구성을 선택합니다.

## <a name="step-2-install-ssdt"></a>2단계: SSDT 설치
Visual Studio용 SSDT를 설치하려면 먼저 다음 단계에 따라 Visual Studio 내에서 SSDT 업데이트를 확인합니다.

1. Visual Studio에서 **도구** / **확장 및 업데이트...** 를 클릭합니다. / **업데이트**
2. **제품 업데이트**를 선택한 후 **데이터베이스 도구용 Microsoft SQL Server 업데이트**를 찾습니다.

업데이트가 없는 경우 최신 버전을 설치했어야 합니다.  SSDT가 설치되었는지 확인하려면 **도움말** / **Microsoft Visual Studio 정보**를 클릭하여 목록에서 SQL Server 데이터 도구를 찾아봅니다. 설치 옵션을 Visual Studio에서 사용할 수 없는 경우 [SSDT 다운로드][SSDT Download] 페이지를 방문하여 SSDT를 수동으로 다운로드하고 설치할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 최신 버전의 SSDT가 설치되었으므로 SQL Data Warehouse에 [연결][connect]할 수 있습니다.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Visual Studio를 다운로드]: https://www.visualstudio.com/downloads/합니다.
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
