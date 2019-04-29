---
title: Azure Portal을 사용하여 Azure Data Lake Analytics 관리
description: 이 문서에서는 Azure Portal을 사용하여 Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법을 설명합니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8b2f16f45be1d095e9be8042611de328af36f064
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813442"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Data Lake Analytics 관리
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

이 문서에서는 Azure Portal을 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법을 설명합니다.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics 계정 관리

### <a name="create-an-account"></a>계정 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 만들기** > **인텔리전스 + 분석** > **Data Lake Analytics**를 클릭합니다.
3. 다음 항목에 대한 값을 선택합니다. 
   1. **이름**: Data Lake Analytics 계정의 이름입니다.
   2. **구독**: 계정에 사용되는 Azure 구독입니다.
   3. **리소스 그룹**: 계정을 만들 Azure 리소스 그룹입니다. 
   4. **위치**: Data Lake Analytics 계정의 Azure 데이터 센터입니다. 
   5. **Data Lake Store**: Data Lake Analytics 계정에 사용할 기본 저장소입니다. Azure Data Lake Store 계정 및 Data Lake Analytics 계정은 같은 위치에 있어야 합니다.
4. **만들기**를 클릭합니다. 

### <a name="delete-a-data-lake-analytics-account"></a>Data Lake Analytics 계정 삭제

Data Lake Analytics 계정을 삭제하기 전에 먼저 해당 기본 Data Lake Store 계정을 삭제합니다.

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **삭제**를 클릭합니다.
3. 계정 이름을 입력합니다.
4. **삭제**를 클릭합니다.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>데이터 원본 관리

Data Lake Analytics는 다음 데이터 원본을 지원합니다.

* Data Lake Store
* Azure Storage

데이터 탐색기를 사용하여 데이터 원본을 찾아 기본 파일 관리 작업을 수행할 수 있습니다. 

### <a name="add-a-data-source"></a>데이터 원본 추가

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **데이터 원본**을 클릭합니다.
3. **데이터 원본 추가**를 클릭합니다.
    
   * Data Lake Store 계정을 추가하려면 계정 이름이 필요하고 쿼리를 할 수 있으려면 계정에 대한 액세스가 필요합니다.
   * Azure Blob Storage를 추가하려면 스토리지 계정 및 계정 키가 필요합니다. 저장소 계정 및 계정 키를 찾으려면 포털에서 저장소 계정으로 이동합니다.

## <a name="set-up-firewall-rules"></a>방화벽 규칙 설정

Data Lake Analytics를 사용하여 네트워크 수준에서 Data Lake Analytics 계정에 대한 액세스 잠금을 강화할 수 있습니다. 방화벽을 사용하도록 설정하고 IP 주소를 지정하거나 신뢰할 수 있는 클라이언트에 대한 IP 주소 범위를 정의할 수 있습니다. 이러한 조치를 사용하도록 설정한 후에는 IP 주소가 정의된 범위 내에 있는 클라이언트만 저장소에 연결할 수 있습니다.

다른 Azure 서비스(예: Azure Data Factory 또는 VM)에서 Data Lake Analytics 계정에 연결하는 경우 **Azure 서비스 허용**이 **켬**으로 설정되어 있는지 확인합니다. 

### <a name="set-up-a-firewall-rule"></a>방화벽 규칙 설정

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. 왼쪽 메뉴에서 **방화벽**을 클릭합니다.

## <a name="add-a-new-user"></a>새 사용자 추가

**사용자 추가 마법사**를 사용하여 손쉽게 새 Data Lake 사용자를 프로비전할 수 있습니다.

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. 왼쪽의 **시작** 아래에서 **사용자 추가 마법사**를 클릭합니다.
3. 사용자를 선택한 다음 **선택**을 클릭합니다.
4. 역할을 선택한 다음 **선택**을 클릭합니다. Azure Data Lake를 사용하도록 새 개발자를 설정하려면 **Data Lake Analytics 개발자** 역할을 선택합니다.
5. U-SQL 데이터베이스의 ACL(액세스 제어 목록)을 선택합니다. 선택 사항에 만족하면 **선택**을 클릭합니다.
6. 파일의 ACL을 선택합니다. 기본 저장소의 경우 루트 폴더 “/” 및 /system 폴더의 ACL을 변경하지 마세요. **선택**을 클릭합니다.
7. 선택한 변경 내용을 모두 검토한 다음 **실행**을 클릭합니다.
8. 마법사가 완료되면 **완료**를 클릭합니다.

## <a name="manage-role-based-access-control"></a>역할 기반 Access Control 관리

다른 Azure 서비스와 마찬가지로 RBAC(역할 기반 Access Control)를 사용하여 사용자가 서비스를 조작하는 방법을 제어할 수 있습니다.

표준 RBAC 역할은 다음 기능이 있습니다.
* **소유자**: 작업을 제출하고, 작업을 모니터링하고, 사용자의 작업을 취소하고, 계정을 구성할 수 있습니다.
* **기여자**: 작업을 제출하고, 작업을 모니터링하고, 사용자의 작업을 취소하고, 계정을 구성할 수 있습니다.
* **독자**: 작업을 모니터링할 수 있습니다.

Data Lake Analytics 개발자 역할을 사용하여 U-SQL 개발자가 Data Lake Analytics 서비스를 사용하도록 할 수 있습니다. Data Lake Analytics 개발자 역할을 사용하여 다음을 수행할 수 있습니다.
* 작업을 제출합니다.
* 작업 상태 및 모든 사용자가 제출한 작업의 진행률을 모니터링합니다.
* 모든 사용자가 제출한 작업에서 U-SQL 스크립트를 확인합니다.
* 자신의 작업만을 취소합니다.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Data Lake Analytics 계정에 사용자 또는 보안 그룹 추가

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **액세스 제어(IAM)** > **역할 할당 추가**를 클릭합니다.
3. 원하는 역할을 선택합니다.
4. 사용자를 추가합니다.
5. **확인**을 클릭합니다.

>[!NOTE]
>사용자 또는 보안 그룹이 작업을 제출해야 할 경우 저장소 계정에 대한 권한도 필요합니다. 자세한 내용은 [Data Lake Store에 저장된 데이터 보호](../data-lake-store/data-lake-store-secure-data.md)를 참조하세요.
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>작업 관리

### <a name="submit-a-job"></a>작업 제출

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.

2. **새 작업**을 클릭합니다. 각 작업에 대해 다음을 구성합니다.

    1. **작업 이름**: 작업의 이름입니다.
    2. **우선 순위**: 숫자가 낮을수록 우선 순위가 높습니다. 두 작업이 큐에 대기 중이면 우선 순위 값이 낮은 작업이 먼저 실행됩니다.
    3. **병렬 처리**: 이 작업에 대해 예약할 최대 컴퓨팅 프로세스 수입니다.

3. **작업 제출**을 클릭합니다.

### <a name="monitor-jobs"></a>작업 모니터링

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **모든 작업 보기**를 클릭합니다. 계정의 모든 활성 작업 및 최근에 완료된 작업 목록이 표시됩니다.
3. 필요에 따라 **필터**를 클릭하여 **시간 범위**, **작업 이름** 및 **작성자** 값을 기준으로 작업을 찾을 수 있습니다. 

### <a name="monitoring-pipeline-jobs"></a>파이프라인 작업 모니터링
파이프라인에 속하는 작업이 순차적으로 잘 진행되어 특정 시나리오를 완료합니다. 예를 들어, 고객 정보의 사용을 정리, 추출, 변환, 집계하는 파이프라인을 사용할 수 있습니다. 파이프라인 작업은 작업이 제출될 때 "파이프라인" 속성을 사용하여 식별됩니다. ADF V2를 사용하여 예약된 작업에는 이 속성이 자동으로 포함됩니다. 

파이프라인의 일부인 U-SQL 작업 목록을 보려면 

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **작업 정보**를 클릭합니다. "모든 작업" 탭에 기본값이 지정되고 실행 중, 대기 및 종료된 작업 목록이 표시됩니다.
3. **파이프라인 작업** 탭을 클릭합니다. 파이프라인 작업 목록에 각 파이프라인에 대해 집계된 통계가 함께 표시됩니다.

### <a name="monitoring-recurring-jobs"></a>되풀이 작업 모니터링
되풀이 작업은 동일한 비즈니스 논리를 갖지만 실행될 때마다 다른 입력 데이터를 사용하는 작업입니다. 이상적으로, 되풀이 작업은 항상 성공해야 하며 실행 시간도 비교적 안정적입니다. 이러한 동작을 모니터링하면 작업이 정상 상태인지 확인하는 데 도움이 됩니다. 되풀이 작업은 “되풀이” 속성을 사용하여 식별됩니다. ADF V2를 사용하여 예약된 작업에는 이 속성이 자동으로 포함됩니다.

되풀이되는 U-SQL 작업 목록을 보려면 

1. Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
2. **작업 정보**를 클릭합니다. "모든 작업" 탭에 기본값이 지정되고 실행 중, 대기 및 종료된 작업 목록이 표시됩니다.
3. **되풀이 작업** 탭을 클릭합니다. 되풀이 작업 목록에 각 되풀이 작업에 대해 집계된 통계가 함께 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Lake Analytics 개요](data-lake-analytics-overview.md)
* [Azure PowerShell을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-powershell.md)
* [정책을 사용하여 Azure Data Lake Analytics 관리](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-policies)
