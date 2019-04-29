---
title: Azure Data Lake Analytics 정책 관리
description: 정책을 사용하여 Data Lake Analytics 계정 사용을 제어하는 방법을 알아봅니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 64095f6706bb978cd33b8fe7833fe4e65fc3b0f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813416"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>정책을 사용하여 Azure Data Lake Analytics 관리

계정 정책을 사용하여 리소스 및 Azure Data Lake Analytics 계정이 사용되는 방식을 제어할 수 있습니다. 이러한 정책을 통해 Azure Data Lake Analytics 사용 비용을 제어할 수 있습니다. 예를 들어, 이러한 정책을 통해 계정에서 동시에 사용할 수 있는 AU 수를 제한하여 예기치 않은 비용 급증을 방지할 수 있습니다.

## <a name="account-level-policies"></a>계정 수준 정책

이러한 정책은 Data Lake Analytics 계정의 모든 작업에 적용됩니다.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Data Lake Analytics 계정의 최대 AU 수
Data Lake Analytics 계정에서 사용할 수 있는 총 AU(분석 단위) 수를 제어하는 정책입니다. 기본적으로 이 값은 250으로 설정됩니다. 예를 들어 이 값을 250 AU로 설정한 경우 250 AU가 할당되어 실행 중인 작업 한 개 또는 각각 25 AU가 할당되어 실행 중인 작업 10개가 있을 수 있습니다. 제출된 추가 작업은 실행 중인 작업이 완료될 때까지 큐에 대기됩니다. 실행 중인 작업이 완료되면 대기 작업이 실행되도록 AU가 확보됩니다.

Data Lake Analytics 계정의 AU 수를 변경하려면:

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **속성**을 클릭합니다.
3. **최대 AU** 아래에서 슬라이더를 움직여 값을 선택하거나 텍스트 상자에 값을 입력합니다. 
4. **저장**을 클릭합니다.

> [!NOTE]
> 기본(250) AU보다 더 많이 필요한 경우 포털에서 **도움말 + 지원**을 클릭하여 지원 요청을 제출합니다. Data Lake Analytics 계정에서 사용할 수 있는 AU 수를 늘릴 수 있습니다.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>동시에 실행될 수 있는 최대 작업 수
동시에 실행될 수 있는 작업 수를 제어하는 정책입니다. 기본적으로 이 값은 20으로 설정됩니다. Data Lake Analytics에 사용할 수 있는 AU가 있는 경우 새 작업은 실행 중인 작업 총수가 이 정책 값에 도달할 때까지 즉시 실행되도록 예약됩니다. 동시에 실행될 수 있는 최대 작업 수에 도달하면 이후 작업은 실행 중인 작업이 하나 이상 완료될 때까지 우선 순위에 따라 큐에 대기됩니다(AU 가용성에 따라 다름).

동시에 실행될 수 있는 작업 수를 변경하려면:

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **속성**을 클릭합니다.
3. **최대 실행 중인 작업 수** 아래에서 슬라이더를 움직여 값을 선택하거나 텍스트 상자에 값을 입력합니다. 
4. **저장**을 클릭합니다.

> [!NOTE]
> 기본(20) 작업 수보다 더 많이 실행해야 할 경우 포털에서 **도움말 + 지원**을 클릭하여 지원 요청을 제출합니다. Data Lake Analytics 계정에서 동시에 실행할 수 있는 작업 수를 늘릴 수 있습니다.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>작업 메타데이터 및 리소스를 유지할 기간 
사용자가 U-SQL 작업을 실행하면 Data Lake Analytics 서비스에서 모든 관련 파일을 유지합니다. 관련 파일로는 U-SQL 스크립트, U-SQL 스크립트에서 참조되는 DLL 파일, 컴파일된 리소스 및 통계가 있습니다. 파일은 기본 Azure Data Lake Storage 계정의 /system/ 폴더에 있습니다. 이 정책은 이러한 리소스가 자동으로 삭제되기 전에 저장되는 기간을 제어합니다(기본값: 30일). 이러한 파일은 디버깅 및 나중에 다시 실행할 작업의 성능 튜닝에 사용할 수 있습니다.

작업 메타데이터 및 리소스를 유지할 기간을 변경하려면:

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **속성**을 클릭합니다.
3. **작업 쿼리를 유지할 일 수** 아래에서 슬라이더를 움직여 값을 선택하거나 텍스트 상자에 값을 입력합니다.  
4. **저장**을 클릭합니다.

## <a name="job-level-policies"></a>작업 수준 정책

작업 수준 정책을 사용하여 개별 사용자(또는 특정 보안 그룹의 구성원)가 제출하는 작업에 설정할 수 있는 최대 AU 및 최대 우선 순위를 제어할 수 있습니다. 이 정책을 사용하여 사용자가 발생시키는 비용을 제어할 수 있습니다. 또한 예약된 작업이 같은 Data Lake Analytics 계정에서 실행되고 있는 우선 순위가 더 높은 프로덕션 작업에 줄 수 있는 효과를 제어할 수 있습니다.

Data Lake Analytics에는 작업 수준에서 설정할 수 있는 두 가지 정책이 있습니다.

* **작업당 AU 한도**: 사용자는이 Au 수가 해야 하는 작업을 제출만 수 있습니다. 기본적으로 이 한도는 계정의 최대 AU 한도와 같습니다.
* **우선 순위**: 사용자는 우선 순위가이 값 보다 작거나는 작업을 제출만 있습니다. 숫자가 높을수록 우선 순위가 낮습니다. 기본적으로 이 한도는 가능한 가장 높은 우선 순위인 1로 설정됩니다.

모든 계정에는 설정된 기본 정책이 있습니다. 기본 정책은 계정의 모든 사용자에게 적용됩니다. 특정 사용자 및 그룹에 대한 추가 정책을 설정할 수 있습니다. 

> [!NOTE]
> 계정 수준 정책 및 작업 수준 정책은 동시에 적용됩니다.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>특정 사용자 또는 그룹에 대한 정책 추가

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **속성**을 클릭합니다.
3. **작업 제출 한도** 아래에서 **정책 추가** 단추를 클릭합니다. 그런 후 다음 설정을 선택하거나 입력합니다.
    1. **계산 정책 이름**: 정책의 목적은 알려 정책 이름을 입력 합니다.
    2. **사용자 또는 그룹 선택**: 사용자 또는이 정책을 적용할 그룹을 선택 합니다.
    3. **작업 AU 한도 설정**: 선택한 사용자 또는 그룹에 적용 되는 AU 한도 설정 합니다.
    4. **Set the Priority Limit**: 선택한 사용자 또는 그룹에 적용 되는 우선 순위 제한을 설정 합니다.

4. **Ok**를 클릭합니다.

5. 새 정책은 **기본** 정책 테이블에서 **작업 제출 한도** 아래에 나열됩니다. 

### <a name="delete-or-edit-an-existing-policy"></a>기존 정책 삭제 또는 편집

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **속성**을 클릭합니다.
3. **작업 제출 한도** 아래에서 편집할 정책을 찾습니다.
4.  **삭제** 및 **편집** 옵션을 보려면 테이블의 맨 오른쪽 열에서 `...`을 클릭합니다.

## <a name="additional-resources-for-job-policies"></a>정책 작업에 대한 추가 리소스
* [Policy overview blog post](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)(정책 개요 블로그 게시물)
* [Account-level policies blog post](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)(계정 수준 정책 블로그 게시물)
* [Job-level policies blog post](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)(작업 수준 정책 블로그 게시물)

## <a name="next-steps"></a>다음 단계

* [Azure Data Lake Analytics 개요](data-lake-analytics-overview.md)
* [Azure Portal을 사용하여 Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-powershell.md)

