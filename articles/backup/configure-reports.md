---
title: Azure Backup 보고서 구성
description: Log Analytics 및 Azure 통합 문서를 사용하여 Azure Backup에 대한 보고서 구성 및 보기
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0ede2c8507032811eda26cfcb4d90f18578f3700
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180289"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup 보고서 구성

백업 관리자의 일반적인 요구 사항은 장기간에 걸친 데이터를 기반으로 백업에 대한 인사이트를 획득하는 것입니다. 이러한 솔루션에 대한 사용 사례는 다음과 같습니다.

- 사용된 클라우드 스토리지를 할당하고 예측합니다.
- 백업 및 복원을 감사합니다.
- 여러 세분성 수준에서 주요 추세를 확인합니다.

현재 Azure Backup은 [Azure Monitor 로그](../azure-monitor/log-query/get-started-portal.md) 및 [Azure 통합 문서](../azure-monitor/platform/workbooks-overview.md)를 사용하는 보고 솔루션을 제공합니다. 이러한 리소스를 통해 전체 백업 자산의 백업에 대한 풍부한 인사이트를 얻을 수 있습니다. 이 문서에서는 Azure Backup 보고서를 구성하고 보는 방법을 설명합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

- Azure VM, Azure VM의 SQL, Azure VM의 SAP HANA, MARS(Microsoft Azure Recovery Services) 에이전트, MABS(Microsoft Azure Backup Server) 및 System Center DPM(Data Protection Manager)에서 Backup 보고서가 지원됩니다. Azure 파일 공유 백업의 경우 2020 년 6 월 1 일 이후에 생성 된 모든 레코드에 대해 데이터가 표시 됩니다.
- DPM 워크로드의 경우 DPM 버전 5.1.363.0 이상 및 에이전트 버전 2.0.9127.0 이상에서 Backup 보고서가 지원됩니다.
- MABS 워크로드의 경우 MABS 버전 13.0.415.0 이상 및 에이전트 버전 2.0.9170.0 이상에서 Backup 보고서가 지원됩니다.
- 사용자가 액세스할 수 있는 Log Analytics 작업 영역으로 데이터가 전송되는 모든 백업 항목, 자격 증명 모음, 구독 및 지역에서 Backup 보고서를 볼 수 있습니다. 자격 증명 모음 집합에 대한 보고서를 보려면 자격 증명 모음이 데이터를 보내는 Log Analytics 작업 영역에 대한 읽기 권한만 있으면 됩니다. 개별 자격 증명 모음에 대한 액세스 권한은 필요 없습니다.
- 고객의 구독에 대한 위임된 액세스 권한이 있는 [Azure Lighthouse](../lighthouse/index.yml) 사용자인 경우 Azure Lighthouse에서 이러한 보고서를 사용하여 모든 테넌트의 보고서를 볼 수 있습니다.
- 현재는 최대 100개 Log Analytics 작업 영역(테넌트 전체)에서 Backup 보고서를 통해 데이터를 볼 수 있습니다.
- 로그 백업 작업에 대한 데이터는 현재 보고서에 표시되지 않습니다.

## <a name="get-started"></a>시작하기

다음 단계에 따라 보고서 사용을 시작합니다.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Log Analytics 작업 영역을 만들거나 기존 작업 영역을 사용합니다.

Backup 보고 데이터를 저장할 Log Analytics 작업 영역을 하나 이상 설정합니다. 이 Log Analytics 작업 영역을 만들 수 있는 위치 및 구독은 자격 증명 모음이 있는 위치 및 구독과는 독립적입니다.

Log Analytics 작업 영역을 설정하려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md)를 참조하세요.

기본적으로 Log Analytics 작업 영역의 데이터는 30일 동안 보존됩니다. 더 긴 시간 동안 데이터를 보려면 Log Analytics 작업 영역의 보존 기간을 변경합니다. 보존 기간을 변경하려면 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](../azure-monitor/platform/manage-cost-storage.md)를 참조하세요.

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. 자격 증명 모음의 진단 설정 구성

Recovery Services 자격 증명 모음과 같은 Azure Resource Manager 리소스는 예약된 작업과 사용자가 트리거한 작업에 대한 정보를 진단 데이터로 기록합니다.

Recovery Services 자격 증명 모음의 모니터링 섹션에서 **진단 설정**을 선택하고 Recovery Services 자격 증명 모음의 진단 데이터 대상을 지정합니다. 진단 이벤트 사용에 대한 자세한 내용은 [Recovery Services 자격 증명 모음에 진단 설정 사용](./backup-azure-diagnostic-events.md)을 참조하세요.

![진단 설정 창](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

또한 Azure Backup은 지정된 범위에서 모든 자격 증명 모음의 진단 설정 구성을 자동화하는 기본 제공 Azure Policy 정의를 제공합니다. 이 정책을 사용하는 방법은 [대규모로 자격 증명 모음 진단 설정 구성](./azure-policy-configure-diagnostics.md)을 참조하세요.

> [!NOTE]
> 진단을 구성한 후 초기 데이터 푸시가 완료될 때까지 최대 24시간이 걸릴 수 있습니다. 데이터가 Log Analytics 작업 영역으로 이동하기 시작해도 당일 데이터는 보고서에 표시되지 않으므로 보고서에 데이터가 즉시 표시되지 않을 수 있습니다. 자세한 내용은 [Backup 보고서에 사용되는 규칙](#conventions-used-in-backup-reports)을 참조하세요. Log Analytics로 데이터를 보내도록 자격 증명 모음을 구성하고 이틀이 지난 후부터 보고서를 보는 것이 좋습니다.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Azure Portal에서 보고서 보기

Log Analytics로 데이터를 보내도록 자격 증명 모음을 구성한 후에는 자격 증명 모음 창으로 이동하고 **Backup 보고서**를 선택하여 Backup 보고서를 봅니다.

![자격 증명 모음 대시보드](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

이 링크를 선택하면 Backup 보고서 통합 문서가 열립니다.

> [!NOTE]
>
> - 현재는 보고서를 처음 로드하는 경우 최대 1분이 소요될 수 있습니다.
> - Recovery Services 자격 증명 모음은 Backup 보고서의 진입점일 뿐입니다. 자격 증명 모음 창에서 Backup 보고서 통합 문서를 연 후에는 적절한 Log Analytics 작업 영역 세트를 선택하여 모든 자격 증명 모음에서 집계된 데이터를 확인합니다.

보고서에는 다음과 같은 탭이 있습니다.

##### <a name="summary"></a>요약

이 탭을 사용 하 여 백업 공간에 대 한 개략적인 개요를 볼 수 있습니다. 총 백업 항목 수, 사용된 총 클라우드 스토리지, 보호된 인스턴스 수, 워크로드 유형별 작업 성공률을 한눈에 볼 수 있습니다. 특정 백업 아티팩트 형식에 대한 자세한 내용을 보려면 해당 탭으로 이동하세요.

   ![요약 탭](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Backup 항목

이 탭을 사용 하 여 백업 항목 수준에서 사용 되는 클라우드 저장소에 대 한 정보 및 추세를 볼 수 있습니다. 예를 들어 Azure VM 백업에서 SQL을 사용하는 경우 백업 중인 각 SQL 데이터베이스에 사용되는 클라우드 스토리지를 볼 수 있습니다. 특정 보호 상태의 백업 항목에 대한 데이터를 표시하도록 선택할 수도 있습니다. 예를 들어 탭의 맨 위에서 **보호 중지됨** 타일을 선택하면 [보호 중지 됨] 상태의 Backup 항목에 대한 데이터만 표시하도록 그 아래의 모든 위젯이 필터링됩니다.

   ![Backup 항목 탭](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>사용량

이 탭을 사용 하 여 백업에 대 한 주요 청구 매개 변수를 볼 수 있습니다. 이 탭에 표시되는 정보는 청구 엔터티(보호된 컨테이너) 수준에 있습니다. 예를 들어 DPM 서버를 Azure에 백업 하는 경우 DPM 서버에 사용 되는 보호 된 인스턴스 및 클라우드 저장소의 추세를 볼 수 있습니다. 마찬가지로 Azure Backup에서 SQL을 사용하거나 Azure Backup에서 SAP HANA를 사용하는 경우 이 탭은 이러한 데이터베이스가 들어 있는 가상 머신 수준에서 사용 관련 정보를 제공합니다.

   ![사용량 탭](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> DPM 워크 로드의 경우 사용자는 Recovery Services 자격 증명 모음 **개요** 탭에 표시 된 것과 같이 집계 사용량 값과 비교 하 여 보고서에 표시 되는 사용량 값 사이에 약간의 차이가 있습니다 (dpm 서버당 20mb). 이러한 차이는 백업에 대해 등록 되는 모든 DPM 서버에 보고에 대 한 아티팩트로 표시 되지 않는 연결 된 ' metadata ' 데이터 원본이 있다는 사실에 의해 고려 됩니다.

##### <a name="jobs"></a>작업

이 탭을 사용 하 여 일별 실패 한 작업 수 및 작업 실패의 상위 원인 등의 장기 실행 추세를 볼 수 있습니다. 이 정보는 집계 수준 및 Backup 항목 수준에서 볼 수 있습니다. 그리드에서 특정 Backup 항목을 선택하면 선택한 시간 범위의 해당 Backup 항목에서 트리거된 각 작업의 자세한 정보를 볼 수 있습니다.

   ![작업 탭](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>정책

이 탭을 사용 하 여 연결 된 항목 수 및 지정 된 정책에 따라 백업 된 항목에서 사용 되는 총 클라우드 저장소와 같은 모든 활성 정책에 대 한 정보를 볼 수 있습니다. 특정 정책을 선택하면 관련된 각 Backup 항목에 대한 정보를 볼 수 있습니다.

   ![정책 탭](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>최적화

이 탭을 사용 하 여 백업의 잠재적 비용 최적화 기회를 파악할 수 있습니다. 현재 최적화 탭에서 정보를 제공 하는 시나리오는 다음과 같습니다.

###### <a name="inactive-resources"></a>비활성 리소스

이 보기를 사용 하 여 상당한 시간 동안 성공적으로 백업 되지 않은 백업 항목을 식별할 수 있습니다. 이는 백업 중인 기본 컴퓨터가 더 이상 존재 하지 않거나 (따라서 백업에 실패 한 경우), 백업이 안정적으로 수행 되지 않도록 하는 컴퓨터에 문제가 있는 것일 수 있습니다.

비활성 리소스를 보려면 **최적화** 탭으로 이동 하 여 **비활성 리소스** 타일을 선택 합니다. 선택한 범위에 있는 모든 비활성 리소스의 세부 정보를 포함 하는 표를 표시 합니다. 기본적으로 그리드는 지난 7 일 동안 복구 지점이 없는 항목을 표시 합니다. 다른 시간 범위에 대 한 비활성 리소스를 찾으려면 탭 위쪽에서 **시간 범위** 필터를 조정 하면 됩니다.

비활성 리소스를 확인 한 후에는 해당 리소스에 대 한 Azure 리소스 창 (해당 하는 경우)으로 이동 하 여 문제를 자세히 조사할 수 있습니다. 시나리오에 따라 컴퓨터에 대 한 백업을 중지 하 고 (더 이상 존재 하지 않는 경우) 불필요 한 백업을 삭제 하 여 비용을 절감 하거나, 컴퓨터의 문제를 해결 하 여 백업이 안정적으로 수행 되도록 할 수 있습니다.

![최적화 탭-비활성 리소스](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>보존 기간이 긴 백업 항목

이 보기를 사용 하 여 조직에서 요구 하는 것 보다 더 긴 기간 동안 백업이 보존 된 항목을 식별할 수 있습니다.

**정책 최적화** 타일을 선택 하 고 나 서 **보존 최적화** 타일을 선택 하면 매일, 매주, 매월 또는 매년 보존 지점 (RP)의 보존이 지정 된 값 보다 큰 모든 백업 항목이 포함 된 표가 표시 됩니다. 기본적으로 그리드는 선택한 범위의 모든 백업 항목을 표시 합니다. 매일, 매주, 매월 및 매년 RP 보존에 대 한 필터를 사용 하 여 그리드를 추가로 필터링 하 고 백업 저장소 비용을 절약 하기 위해 보존 가능성이 감소할 수 있는 항목을 식별할 수 있습니다.

SQL 및 SAP HANA 같은 데이터베이스 작업의 경우 표에 표시 된 보존 기간은 차등 백업 지점이 아닌 전체 백업 지점의 보존 기간에 해당 합니다. 보존 필터에도 동일 하 게 적용 됩니다.  

![탭 보존 최적화 최적화](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>매일 전체 백업을 위해 구성된 데이터베이스 

이 뷰를 사용 하 여 매일 전체 백업에 대해 구성 된 데이터베이스 작업을 식별할 수 있습니다. 주로 주간 전체 백업과 함께 매일 차등 백업을 사용 하는 것이 더 비용 효율적입니다.

**정책 최적화** 타일, **백업 일정 최적화** 타일을 차례로 선택 하면 매일 전체 백업 정책이 적용 된 모든 데이터베이스가 포함 된 표가 표시 됩니다. 특정 백업 항목으로 이동 하 고 주간 전체 백업을 사용 하 여 매일 차등 백업을 사용 하도록 정책을 수정 하도록 선택할 수 있습니다.

탭 맨 위에 있는 **Backup 관리 유형** 필터에는 **Azure vm의 SQL** 항목과 **azure vm의 SAP HANA** 가 선택 되어 있어야 합니다 .이를 위해 그리드는 데이터베이스 작업을 예상 대로 표시할 수 있습니다.

![최적화 탭-백업 일정 최적화](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

## <a name="export-to-excel"></a>Excel로 내보내기

테이블이나 차트와 같은 위젯의 오른쪽 위에서 아래쪽 화살표 단추를 선택하면 기존 필터가 적용된 상태로 해당 위젯의 콘텐츠를 Excel 시트로 내보낼 수 있습니다. 테이블의 행을 더 많이 Excel로 내보내려면 각 그리드의 맨 위에 있는 **페이지당 행 수** 드롭다운 화살표를 사용하여 페이지에 표시할 행 수를 늘리면 됩니다.

## <a name="pin-to-dashboard"></a>대시보드에 고정

각 위젯의 맨 위에 있는 고정 단추를 선택하면 위젯이 Azure Portal 대시보드에 고정됩니다. 이 기능을 사용하면 본인에게 필요한 가장 중요한 정보를 표시하는 맞춤형 대시보드를 만들 수 있습니다.

## <a name="cross-tenant-reports"></a>테넌트 간 보고서

여러 테넌트 환경의 구독에 대한 액세스 권한을 위임받은 [Azure Lighthouse](../lighthouse/index.yml)를 사용하는 경우 기본 구독 필터를 사용할 수 있습니다. Azure Portal의 오른쪽 위 모서리에서 필터 단추를 선택하고, 데이터를 보고 싶은 모든 구독을 선택합니다. 이렇게 하면 모든 테넌트의 Log Analytics 작업 영역을 선택하여 다중 테넌트 보고서를 볼 수 있습니다.

## <a name="conventions-used-in-backup-reports"></a>Backup 보고서에 사용되는 규칙

- 필터는 각 탭의 왼쪽에서 오른쪽으로, 위쪽에서 아래쪽으로 적용됩니다. 즉, 필터는 해당 필터의 오른쪽 또는 해당 필터의 아래쪽에 있는 모든 위젯에 적용됩니다.
- 색이 지정된 타일을 선택하면 해당 타일의 값과 관련된 레코드에 대한 타일 아래의 위젯이 필터링됩니다. 예를 들어 **Backup 항목** 탭에서 **보호 중지됨** 타일을 선택하면 [보호 중지 됨] 상태의 백업 항목에 대한 데이터를 표시하도록 그 아래의 그리드와 차트가 필터링됩니다.
- 색이 지정 되지 않은 타일은 선택할 수 없습니다.
- 당일 데이터는 보고서에 표시되지 않습니다. 따라서 **시간 범위** 값을 **지난 7일**로 선택하면 날짜가 완전히 지난 7일의 레코드가 보고서에 표시됩니다. 당일은 포함되지 않습니다.
- 보고서에는 선택한 시간 범위에서 *트리거된* 작업(로그 작업과 별개)의 세부 정보가 표시됩니다.
- **클라우드 스토리지** 및 **보호된 인스턴스**에 대해 표시되는 값은 선택한 시간 범위의 *끝*에 있습니다.
- 보고서에 표시되는 Backup 항목은 선택한 시간 범위의 *끝*에 있는 항목입니다. 선택한 시간 범위 도중에 삭제된 Backup 항목은 표시되지 않습니다. Backup 정책에도 동일한 규칙이 적용됩니다.

## <a name="query-load-times"></a>쿼리 로드 시간

Backup 보고서의 위젯은 사용자의 Log Analytics 작업 영역에서 실행되는 Kusto 쿼리로 구동됩니다. 이 쿼리는 일반적으로 대량의 데이터를 처리하며, 풍부한 인사이트를 사용할 수 있도록 여러 조인이 포함되어 있습니다. 따라서 사용자가 대량의 백업 자산에 대한 보고서를 볼 때 위젯이 즉시 로드되지 않을 수 있습니다. 다음 표에서는 보고서가 표시되는 Backup 항목의 수와 시간 범위를 기준으로 여러 위젯을 로드하는 데 걸리는 대략적인 예상 시간을 제공합니다.

| **데이터 원본 수**                         | **시간 범위** | **대략적인 로드 시간**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5K                       | 1개월          | 타일: 5-10초 <br> 그리드: 5-10초 <br> 차트: 5-10초 <br> 보고서 수준 필터: 5-10초|
| ~5K                       | 3개월          | 타일: 5-10초 <br> 그리드: 5-10초 <br> 차트: 5-10초 <br> 보고서 수준 필터: 5-10초|
| ~10K                       | 3개월          | 타일: 15-20초 <br> 그리드: 15-20초 <br> 차트: 1-2분 <br> 보고서 수준 필터: 25-30초|
| ~15K                       | 1개월          | 타일: 15-20초 <br> 그리드: 15-20초 <br> 차트: 50-60초 <br> 보고서 수준 필터: 20-25초|
| ~15K                       | 3개월          | 타일: 20-30초 <br> 그리드: 20-30초 <br> 차트: 2-3분 <br> 보고서 수준 필터: 50-60초 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 보고서는 어떻게 되었나요?

- Azure 스토리지 계정의 데이터를 원본으로 하는 보고용 이전 Power BI 템플릿 앱은 사용 중단 절차를 밟고 있습니다. 이제부터 보고서를 보려면 자격 증명 모음 진단 데이터를 Log Analytics로 보내는 것이 좋습니다.

- 진단 데이터를 스토리지 계정 또는 LA 작업 영역으로 보내는 [V1 스키마](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) 역시 사용 중단 절차를 밟고 있습니다. 즉, V1 스키마를 기반으로 사용자 지정 쿼리 또는 자동화를 작성 한 경우 현재 지원 되는 V2 스키마를 사용 하도록 이러한 쿼리를 업데이트 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[Azure Backup을 사용하여 모니터링 및 보고하는 방법에 대한 자세한 정보](./backup-azure-monitor-alert-faq.md)
