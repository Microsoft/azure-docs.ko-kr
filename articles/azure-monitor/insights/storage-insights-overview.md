---
title: Storage용 Azure Monitor를 사용하여 Azure Storage 서비스 모니터링 | Microsoft Docs
description: 이 문서에서는 스토리지 관리자가 Azure Storage 계정의 성능 및 사용 문제를 신속하게 이해하도록 도와주는 Storage용 Azure Monitor 기능에 대해 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 6d6754594a321d297d983f9bd7e90ce074857919
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961978"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage"></a>Storage용 Azure Monitor를 사용하여 스토리지 서비스 모니터링

Storage용 Azure Monitor는 Azure Storage 서비스 성능, 용량 및 가용성에 대한 통합 보기를 제공하여 Azure Storage 계정의 포괄적인 모니터링을 제공합니다. 스토리지 용량과 성능을 두 가지 방법으로 관찰할 수 있습니다. 스토리지 계정에서 직접 보거나 Azure Monitor 뷰를 통해 스토리지 계정의 그룹을 볼 수도 있습니다. 

이 문서는 Storage용 Azure Monitor가 제공하는 환경을 파악하는 데 도움을 줍니다. 이 환경에는 핫스팟에 집중하고 대기 시간, 제한 및 가용성 문제를 진단하는 기능이 있으며, 대규모로 Storage 계정의 상태 및 성능에 대한 실용적인 정보를 얻을 수 있습니다.

## <a name="introduction-to-azure-monitor-for-storage"></a>Storage용 Azure Monitor 소개

환경을 살펴보기 전에, 환경에서 어떤 방식으로 정보가 제공되고 시각화되는지 이해해야 합니다. 스토리지 계정에서 직접 또는 Azure Monitor에서 Storage 기능을 선택하는 경우 Storage용 Azure Monitor는 일관된 환경을 나타냅니다. 

다음을 제공합니다.

* **대규모 큐브 뷰**는 스토리지 서비스 또는 API 작업의 상태에 따라 가용성에 대한 스냅샷 뷰를 표시하고, 사용률은 스토리지 서비스에서 받는 총 요청 수를 표시하고, 대기 시간은 스토리지 서비스 또는 API 작업 유형이 요청을 처리하는 데 걸리는 평균 시간을 표시합니다. BLOB, 파일, 테이블 및 큐를 기준으로 용량을 확인할 수도 있습니다.

* 특정 스토리지 계정의 **드릴다운 분석** - 문제를 진단하거나 범주(가용성, 성능, 실패 및 용량)별로 자세한 분석을 수행할 수 있습니다. 이러한 옵션 중 하나를 선택하면 메트릭을 자세히 볼 수 있습니다.  

* **사용자 지정이 가능**하므로 표시할 메트릭을 변경하고, 제한과 일치하는 임계값을 수정하거나 설정하고, 사용자 고유의 통합 문서로 저장할 수 있습니다. 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.  

스토리지 계정의 스토리지 메트릭은 기본적으로 수집되므로 이 기능을 사용하기 위해 아무것도 사용하도록 설정하거나 구성할 필요가 없습니다. Azure Storage에서 사용할 수 있는 메트릭에 익숙하지 않은 경우 [Azure Storage 메트릭](../../storage/common/monitor-storage.md)을 검토하여 Azure Storage 메트릭의 설명 및 정의를 확인합니다.

>[!NOTE]
>이 기능은 무료로 사용할 수 있으며 [Azure Monitor 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 설명된 것처럼 Azure Monitor 필수 기능을 구성하거나 설정하는 경우에만 요금이 부과됩니다.

>[!NOTE]
>Storage용 Azure Monitor는 [범용 v1 계정](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)을 지원하지 않습니다.
>

## <a name="view-from-azure-monitor"></a>Azure Monitor에서 보기

Azure Monitor에서 구독의 여러 스토리지 계정의 트랜잭션, 대기 시간 및 용량 세부 정보를 볼 수 있으며 성능, 용량 문제 및 오류를 식별하는 데 도움이 됩니다.

모든 구독의 스토리지 계정 사용률 및 가용성을 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal의 왼쪽 창에서 **모니터**를 선택하고 **인사이트** 섹션에서 **스토리지 계정**을 선택합니다.

    ![여러 스토리지 계정 보기](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>개요 통합 문서

선택한 구독의 **개요** 통합 문서에서 테이블은 구독 내에 그룹화 된 최대 5 개의 저장소 계정에 대 한 대화형 저장소 메트릭 및 서비스 가용성 상태를 표시 합니다. 다음 드롭다운 목록에서 선택하는 옵션을 기준으로 결과를 필터링할 수 있습니다.

* **구독** - 스토리지 계정이 있는 구독만 나열됩니다.  

* **저장소 계정** -기본적으로 5 개의 저장소 계정이 미리 선택 되어 있습니다. 범위 선택기에서 모든 스토리지 계정 또는 여러 스토리지 계정을 선택하는 경우 최대 200개의 스토리지 계정이 반환됩니다. 예를 들어 선택한 3개의 구독에 대해 총 573개의 스토리지 계정이 있는 경우 200개 계정만 표시됩니다. 

* **시간 범위** - 기본적으로는 선택한 항목을 기준으로 지난 4시간의 정보를 표시합니다.

드롭다운 목록 아래의 카운터 타일은 구독의 총 스토리지 계정 수를 롤업하고 선택된 총 수를 반영합니다. 통합 문서에는 트랜잭션 메트릭 또는 오류를 보고하는 열에 대한 조건부 컬러 코딩 또는 열 지도가 있습니다. 가장 진한 색이 가장 높은 값을 가지며 밝은 색은 가장 작은 값을 기준으로 합니다. 오류 기반 열의 경우 값이 빨간색이고 메트릭 기반 열의 경우 값이 파란색으로 되어 있습니다.

**가용성**, **E2E 대기 시간**, **서버 대기 시간** 및 **트랜잭션 오류 유형/오류** 열에서 값을 선택하면 해당 스토리지 계정에 대해 선택한 열과 일치하는 특정 유형의 스토리지 메트릭에 맞게 조정된 보고서로 연결됩니다. 각 범주의 통합 문서에 대한 자세한 내용은 아래의 [자세한 스토리지 통합 문서](#detailed-storage-workbooks) 섹션을 참조하세요. 

>[!NOTE]
>보고서에 표시될 수 있는 오류에 대한 자세한 내용은 [응답 유형 스키마](../../storage/common/monitor-storage-reference.md#metrics-dimensions)를 참조하고 **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**와 같은 응답 유형을 찾습니다. 선택한 스토리지 계정에 따라 세 가지가 넘는 오류 유형이 보고되는 경우 다른 모든 오류는 **기타** 범주에 표시됩니다.

기본 **가용성** 임계값은 다음과 같습니다.

* 경고 - 99%
* 위험 - 90%

관찰 결과 또는 요구 사항에 따라 가용성 임계값을 설정하려면 [가용성 임계값 수정](#modify-the-availability-threshold)을 검토합니다. 

### <a name="capacity-workbook"></a>용량 통합 문서

페이지 맨 위에서 **용량**을 선택하면 **용량** 통합 문서가 열립니다. 계정에 사용된 총 스토리지의 양과 계정의 각 데이터 서비스에서 사용된 용량이 표시되어 사용률이 초과되거나 미달된 스토리지를 식별하는 데 도움이 됩니다.

![여러 스토리지 계정 용량 통합 문서](./media/storage-insights-overview/storage-account-capacity-02.png) 

통합 문서에는 파란색 값을 사용하여 용량 메트릭을 보고하는 열에 대한 조건부 컬러 코딩 또는 열 지도가 있습니다. 가장 진한 색이 가장 높은 값을 가지며 밝은 색은 가장 작은 값을 기준으로 합니다.

통합 문서의 열 중 하나에서 값을 선택하면 스토리지 계정에 대한 **용량** 통합 문서로 드릴다운됩니다. 드릴다운 보고서에 대한 자세한 내용은 아래의 [자세한 스토리지 통합 문서](#detailed-storage-workbooks) 섹션에 설명되어 있습니다. 

## <a name="view-from-a-storage-account"></a>스토리지 계정에서 보기

스토리지 계정에서 직접 VM용 Azure Monitor에 액세스하려면 다음을 수행합니다.

1. Azure Portal에서 스토리지 계정을 선택합니다.

2. 목록에서 스토리지 계정을 선택합니다. 모니터링 섹션에서 인사이트를 선택합니다.

    ![선택한 스토리지 계정 개요 페이지](./media/storage-insights-overview/storage-account-direct-overview-01.png)

스토리지 계정에 대한 **개요** 통합 문서에는 신속하게 평가하는 데 도움이 되는 몇 가지 스토리지 성능 메트릭이 표시됩니다.

* 제어할 수 없는 문제가 배포된 지역의 스토리지 서비스에 영향을 주는지 즉시 확인할 수 있는 스토리지 서비스의 상태. **요약** 열 아래에 명시되어 있습니다.

* 스토리지 용량, 가용성, 트랜잭션 및 대기 시간과 관련된 가장 중요한 정보를 보여 주는 대화형 성능 차트  

* 서비스 가용성, 스토리지 서비스에 대한 총 트랜잭션 수, E2E 대기 시간 및 서버 대기 시간이 강조 표시된 메트릭 및 상태 타일

**오류**, **성능**, **가용성** 및 **용량** 단추 중 하나를 선택하면 해당 통합 문서가 열립니다. 

![선택한 스토리지 계정 개요 페이지](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>자세한 스토리지 통합 문서

여러 스토리지 계정 **개요** 통합 문서의 **가용성**, **E2E 대기 시간**, **서버 대기 시간** 및 **트랜잭션 오류 유형/오류** 열의 값을 선택하거나 특정 스토리지 계정의 **개요** 통합 문서에서 **오류**, **성능**, **가용성** 및 **용량** 단추 중 하나를 선택하면 각각 해당 범주에 맞게 조정된 일련의 대화형 스토리지 관련 정보를 제공합니다.  

* **가용성**을 선택하면 **가용성** 통합 문서가 열립니다. Azure Storage 서비스의 현재 상태, 선택된 시간 범위를 나타내는 추세선을 사용하여 스토리지 계정에 정의된 데이터 서비스로 분류된 사용 가능한 각 개체의 상태를 나타내는 표, 계정의 각 데이터 서비스에 대한 가용성 추세 차트가 표시됩니다.  

    ![가용성 보고서 예](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E 대기 시간** 및 **서버 대기 시간**을 선택하면 **성능** 통합 문서가 열립니다. E2E 대기 시간 및 서버 대기 시간을 보여 주는 롤업 상태 타일, E2E 및 서버 대기 시간의 성능 차트, 스토리지 계정에 정의된 데이터 서비스로 분류된 API의 성공적인 호출 대기 시간을 분석한 표가 포함되어 있습니다.

    ![성능 보고서 예](./media/storage-insights-overview/storage-account-performance-01.png)

* 그리드에 나열된 오류 범주 중 하나를 선택하면 **오류** 통합 문서가 열립니다. 이 보고서에는 설명된 오류를 제외한 다른 모든 클라이언트 쪽 오류 및 성공적인 요청에 대한 메트릭 타일, 클라이언트 제한 오류, ClientOtherError 특성과 관련된 트랜잭션 **응답 유형** 차원 메트릭에 대한 성능 차트와 **API 이름별 트랜잭션** 및 **응답 유형별 트랜잭션**에 대한 두 개의 표가 표시됩니다.

   ![오류 보고서 예](./media/storage-insights-overview/storage-account-failures-01.png)

* **용량**을 선택하면 **용량** 통합 문서가 열립니다. 타일 및 차트에서 계정의 각 스토리지 데이터 개체에 사용되는 총 스토리지 크기와 계정에 저장된 데이터 개체 수가 표시됩니다.  

    ![선택한 스토리지 계정 용량 페이지](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>고정 및 내보내기

섹션의 오른쪽 위에 있는 압정 아이콘을 선택하여 메트릭 섹션 중 하나를 Azure 대시보드에 고정할 수 있습니다.

![메트릭 섹션 대시보드에 고정 예제](./media/storage-insights-overview/workbook-pin-example.png)

여러 구독 및 스토리지 계정 **개요** 또는 **용량** 통합 문서는 압핀 아이콘 오른쪽에 있는 아래쪽 화살표 아이콘을 선택하여 결과를 Excel 형식으로 내보낼 수 있도록 지원합니다.

![통합 문서 그리드 결과 내보내기 예](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage"></a>Storage용 Azure Monitor 사용자 지정

이 섹션에서는 데이터 분석 요구 사항을 지원하도록 사용자 지정하기 위해 통합 문서를 편집하는 일반적인 시나리오를 중점적으로 설명합니다.

* 항상 특정 구독 또는 스토리지 계정을 선택하도록 통합 문서 범위를 지정합니다.
* 그리드에서 메트릭 변경
* 가용성 임계값 변경
* 색 렌더링 변경

사용자 지정 항목은 게시된 통합 문서에서 기본 구성을 덮어쓰지 않도록 사용자 지정 통합 문서에 저장됩니다. 통합 문서는 리소스 그룹 내에서 프라이빗 위치인 **내 보고서** 섹션 또는 리소스 그룹에 대한 액세스 권한을 갖고 있는 모든 사용자가 액세스할 수 있는 **공유 보고서** 섹션에 저장됩니다. 사용자 지정 통합 문서를 저장한 후에는 통합 문서 갤러리로 이동하여 통합 문서를 시작해야 합니다.

![명령 모음에서 통합 문서 갤러리 시작](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>구독 또는 스토리지 계정 지정

다음 단계를 수행하여 실행할 때마다 범위를 특정 구독 또는 스토리지 계정으로 지정하도록 여러 구독 및 스토리지 계정 **개요** 또는 **용량** 통합 문서를 구성할 수 있습니다.

1. 포털에서 **모니터**를 선택한 다음 왼쪽 창에서 **스토리지 계정**을 선택합니다.

2. **개요** 통합 문서의 명령 모음에서 **편집**을 선택합니다.

3. **구독** 드롭다운 목록에서 기본값으로 지정할 하나 이상의 구독을 선택합니다. 통합 문서에서는 최대 총 10개의 구독을 선택할 수 있습니다.  

4. **스토리지 계정** 드롭다운 목록에서 기본값으로 지정할 하나 이상의 계정을 선택합니다. 통합 문서에서는 최대 총 200개의 스토리지 계정을 선택할 수 있습니다. 

5. 명령 모음에서 **다른 이름으로 저장**을 선택하여 사용자 지정 항목이 있는 통합 문서의 복사본을 저장한 다음 **편집 완료**를 클릭하여 읽기 모드로 돌아갑니다.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>통합 문서의 메트릭 및 색 수정

미리 작성된 통합 문서에는 메트릭 데이터가 포함되어 있으며, 시각화 중 하나를 수정 또는 제거하고 팀의 특정 요구에 맞게 사용자 지정할 수 있습니다.

이 예에서는 여러 구독 및 스토리지 계정 용량 통합 문서를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* 메트릭 제거
* 색 렌더링 변경

미리 작성된 **오류**, **성능**, **가용성** 및 **용량** 통합 문서 중 하나에 대해 동일한 변경 내용을 수행할 수 있습니다.

1. 포털에서 **모니터**를 선택한 다음 왼쪽 창에서 **스토리지 계정**을 선택합니다.

2. 용량 통합 문서로 전환하려면 **용량**을 선택하고 명령 모음에서 **편집**을 선택합니다.

    ![편집을 선택하여 통합 문서 수정](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 메트릭 섹션 옆에 있는 **편집**을 선택합니다.

    ![편집을 선택하여 용량 통합 문서 메트릭 수정](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. **계정 사용 용량 타임라인** 열을 제거할 예정이므로 메트릭 그리드에서 **열 설정**을 선택합니다.

    ![열 설정 편집](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. **열 설정 편집** 창의 **열** 섹션에서 **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$|Account used capacity Timeline$** 을 선택하고 **열 렌더러** 드롭다운 목록에서 **숨김**을 선택합니다.

6. **저장 후 닫기**를 선택하여 변경 내용을 커밋합니다.

이제 보고서의 용량 메트릭에 대한 색 테마를 파란색 대신 녹색을 사용하도록 변경하겠습니다.

1. 메트릭 그리드에서 **열 설정**을 선택합니다.

2. **열 설정 편집** 창의,/storageaccounts ** `|` /Blobservices-capacity-blobservices $ microsoft. storage/Storageaccounts/fileservices-FileCapacity $ microsoft. storage/storageaccounts/ `|` `|` `|` QueueCapacity-** tableservices $ **열** 아래에서을 (를) 선택 합니다 (예를 들면)를 선택 하 여. **색상표** 드롭다운 목록에서 **녹색**을 선택합니다.

3. **저장 후 닫기**를 선택하여 변경 내용을 커밋합니다.

4. 명령 모음에서 **다른 이름으로 저장**을 선택하여 사용자 지정 항목이 있는 통합 문서의 복사본을 저장한 다음 **편집 완료**를 클릭하여 읽기 모드로 돌아갑니다.  

### <a name="modify-the-availability-threshold"></a>가용성 임계값 수정

이 예에서는 스토리지 계정 용량 통합 문서를 사용하여 가용성 임계값을 수정하는 방법을 보여 줍니다. 기본적으로, 백분율 가용성을 보고하는 타일 및 그리드는 최소 임계값 90 및 최대 임계값 99로 구성됩니다. **API 이름별 가용성** 그리드에서 **가용성 %** 의 최소 임계값을 85%로 변경하려고 합니다. 이는 임계값이 85% 미만인 경우 상태가 위험으로 변경됨을 의미합니다. 

1. 포털에서 **스토리지 계정**을 선택한 다음 목록에서 스토리지 계정을 선택합니다.

2. 왼쪽 창에서 **인사이트**를 선택합니다.

3. 통합 문서에서 **가용성**을 선택하여 가용성 통합 문서로 전환한 다음 명령 모음에서 **편집**을 선택합니다. 

4. 페이지 아래쪽으로 스크롤한 다음 **API별 가용성** 그리드의 왼쪽에서 **편집**을 선택합니다.

    ![API 이름별 가용성 그리드 설정 편집](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. **열 설정**을 선택한 다음 **열 설정 편집** 창의 **열** 섹션에서 **가용성(%)(임계값 + 형식 지정)** 을 선택합니다.

6. **위험** 상태의 값을 **90**에서 **85**로 변경한 다음 **저장 후 닫기**를 클릭합니다.

    ![위험 상태에 대한 가용성 임계값 수정](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 명령 모음에서 **다른 이름으로 저장**을 선택하여 사용자 지정 항목이 있는 통합 문서의 복사본을 저장한 다음 **편집 완료**를 클릭하여 읽기 모드로 돌아갑니다.

## <a name="troubleshooting"></a>문제 해결

일반적인 문제 해결 지침은 전용 통합 문서 기반 insights [문제 해결 문서](troubleshoot-workbooks.md)를 참조 하세요.

이 섹션에서는 Storage용 Azure Monitor를 사용할 때 발생할 수 있는 몇 가지 일반적인 문제를 진단하고 해결하는 데 도움이 되는 정보를 제공합니다. 아래 목록에서 특정 문제와 관련된 정보를 찾을 수 있습니다.

### <a name="resolving-performance-capacity-or-availability-issues"></a>성능, 용량 또는 가용성 문제 해결

Storage용 Azure Monitor로 식별한 스토리지 관련 문제를 해결하려면 Azure Storage [문제 해결 지침](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)을 참조하세요.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>200개의 스토리지 계정만 볼 수 있는 이유는 무엇인가요?

선택한 구독 수에 관계없이 선택한 스토리지 계정의 수는 200개로 제한됩니다.

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>가용성에 대한 색 지정 및 임계값을 변경하려면 어떻게 하나요?

가용성에 대한 색 지정 및 임계값을 변경하는 방법에 대한 자세한 단계는 [가용성 임계값 수정](#modify-the-availability-threshold) 섹션을 참조하세요.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Storage용 Azure Monitor에 표시된 데이터를 분석하고 문제를 해결하려면 어떻게 하나요?

 Storage용 Azure Monitor에 표시된 Azure Storage 데이터를 분석하고 문제를 해결하는 방법에 대한 자세한 내용은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md) 문서를 참조하세요.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>메트릭에 모든 오류 유형이 표시되지 않는 이유는 무엇인가요?

현재 세 가지 유형의 오류가 표시되고 나머지 오류는 단일 버킷에 함께 그룹화됩니다. splitByLimit를 사용하여 제어되며 수정 가능합니다. 이 속성을 변경하려면 다음을 수행합니다.

1. 통합 문서 편집을 클릭합니다.
2. 메트릭으로 이동하고 편집을 클릭한 다음 **트랜잭션, 합계** 또는 편집하려는 메트릭을 선택합니다.

    ![메트릭으로 이동하고 편집을 클릭한 다음 "트랜잭션, 합계"를 클릭합니다.](./media/storage-insights-overview/fqa7.png)

3. 그런 다음 분할 수를 변경합니다.

    ![메트릭 매개 변수 선택](./media/storage-insights-overview/fqa7-2.png)

n개의 서로 다른 오류 유형을 확인하려면 splitByLimit를 나머지 오류에 대해 1을 추가하여 n+1로 지정합니다.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>어떤 스토리지 계정에서 내 통합 문서를 저장했습니다. 지금 찾을 수 없는 이유는 무엇인가요?

각 통합 문서는 저장한 스토리지 계정에 저장됩니다. 사용자가 통합 문서를 저장한 특정 스토리지 계정을 찾아 보세요. 그렇지 않으면 리소스(스토리지 계정)를 모르고 특정 통합 문서를 찾을 수 있는 방법이 없습니다.

## <a name="next-steps"></a>다음 단계

* [메트릭 경고](../platform/alerts-metric.md) 및 [서비스 상태 알림](../../service-health/alerts-activity-log-service-notifications-portal.md)을 구성하여 문제 발견에 도움이 되는 자동 경고를 설정합니다.

* [Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../platform/workbooks-overview.md)를 검토하여 통합 문서에서 지원하도록 디자인된 시나리오, 새 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등을 알아보세요.

* 스토리지 분석 및 기타 도구를 사용하여 Azure Storage 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 지침은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.

