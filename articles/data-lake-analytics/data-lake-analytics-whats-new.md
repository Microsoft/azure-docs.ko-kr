---
title: Data Lake Analytics 최근 변경 내용
description: 이 문서에서는 Data Lake Analytics에 적용된 최근 변경 내용에 대한 지속적인 목록을 제공합니다.
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: 6f4885594e4f67d91c595d8afdddc15e8ed4ae75
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263183"
---
# <a name="whats-new-in-data-lake-analytics"></a>Data Lake Analytics의 새로운 기능

Azure Data Lake Analytics는 특정 구성 요소에 대해 불규칙적으로 업데이트됩니다. 최신 업데이트 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

- 주요 구성 요소 베타 미리 보기 알림
- 중요한 구성 요소 버전 정보(예: 사용 가능한 구성 요소 버전 목록, 현재 기본 버전 등).


## <a name="notification-of-key-component-beta-preview"></a>주요 구성 요소 베타 미리 보기 알림

미리 보기에 제공되는 주요 구성 요소 베타 버전이 없습니다. 

## <a name="u-sql-runtime"></a>U-SQL 런타임

컴파일러, 최적화 프로그램 및 작업 관리자를 비롯한 Azure Data Lake U-SQL 런타임은 U-SQL 코드를 처리합니다.

도구에서 Azure Data Lake 분석 작업을 제출하면 해당 작업은 프로덕션 환경에서 현재 사용 가능한 기본 런타임을 사용합니다. 

런타임 버전은 불규칙적으로 업데이트됩니다. 그리고 이전 런타임을 일정 기간 동안 사용할 수 있습니다. 새 베타 버전의 미리 보기가 준비되면 마찬가지로 곧 제공됩니다.

> [!CAUTION]
> - 기본값과 다른 런타임을 선택하면 U-SQL 작업이 중단될 수 있습니다. 이러한 기본이 아닌 버전은 프로덕션용으로 사용하지 말고 테스트용으로만 사용하는 것이 좋습니다.
> - 기본이 아닌 런타임 버전에는 고정된 수명 주기가 있습니다. 자동으로 만료됩니다.

다음 버전은 현재 기본 런타임 버전입니다.

- release-20200124live_adl_16283022_2

U-SQL 런타임 오류를 해결하는 방법을 이해하려면 [U-SQL 런타임 오류 해결](runtime-troubleshoot.md)을 참조하세요.

## <a name="net-framework"></a>.NET Framework

이제 Azure Data Lake Analytics는 **.NET Framework v4.7.2** 버전을 사용합니다. 

Azure Data Lake Analytics U-SQL 스크립트 코드에서 사용자 지정 어셈블리를 사용하고, 해당 사용자 지정 어셈블리에서 .NET 라이브러리를 사용하는 경우 코드의 유효성을 검사하여 손상이 있는지 확인합니다.

.NET 업그레이드 문제 해결 방법을 이해하려면 [.NET 업그레이드](runtime-troubleshoot.md)를 사용하세요.

## <a name="release-note"></a>릴리스 정보

최신 업데이트에 대한 자세한 내용은 [Azure Data Lake Analytics 릴리스 정보](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes)를 참조하세요.


## <a name="next-steps"></a>다음 단계

* [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md)를 사용하여 Data Lake Analytics 시작

