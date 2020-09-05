---
title: 공유 키를 사용 하 여 권한 부여 방지 (미리 보기)
titleSuffix: Azure Storage
description: 클라이언트가 Azure AD를 사용 하 여 요청에 권한을 부여 하도록 하려면 공유 키 (미리 보기)로 인증 된 저장소 계정에 대 한 요청을 허용 하지 않을 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/20/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 3d46df8847a5865c42438ea36245ead0f1e6e528
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951120"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Azure Storage 계정에 대 한 공유 키 권한 부여 방지 (미리 보기)

Azure Storage 계정에 대 한 모든 보안 요청에는 권한이 있어야 합니다. 기본적으로 요청은 Azure Active Directory (Azure AD) 자격 증명을 사용 하거나 공유 키 인증에 대 한 계정 액세스 키를 사용 하 여 권한을 부여할 수 있습니다. 이러한 두 가지 유형의 권한 부여 중 Azure AD는 공유 키를 통해 뛰어난 보안과 사용 편의성을 제공 하며 Microsoft에서 권장 합니다. 클라이언트가 Azure AD를 사용 하 여 요청에 권한을 부여 하도록 하려면 공유 키 (미리 보기)로 인증 된 저장소 계정에 대 한 요청을 허용 하지 않을 수 있습니다.

저장소 계정에 대 한 공유 키 인증을 허용 하지 않을 경우 Azure Storage는 계정 액세스 키로 권한이 부여 된 해당 계정에 대 한 모든 후속 요청을 거부 합니다. Azure AD로 인증 된 보안 요청만 성공 합니다. Azure AD 사용에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](storage-auth-aad.md)를 참조 하세요.

> [!WARNING]
> Azure Storage는 Blob 및 큐 저장소에 대 한 요청에 대해서만 Azure AD 인증을 지원 합니다. 저장소 계정에 대 한 공유 키를 사용 하 여 권한 부여를 허용 하지 않는 경우 공유 키 인증을 사용 하는 Azure Files 또는 테이블 저장소에 대 한 요청이 실패 합니다.
>
> 미리 보기 중에 계정 액세스 키를 사용 하 여 생성 된 SAS (공유 액세스 서명) 토큰을 사용 하는 Azure Files 또는 테이블 저장소에 대 한 요청은 공유 키 권한 부여를 허용 하지 않을 때 성공 합니다. 자세한 내용은 [미리 보기 정보](#about-the-preview)를 참조 하세요.
>
> 저장소 계정에 대 한 공유 키 액세스를 허용 하지 않으면 Azure Files에 대 한 SMB 연결에 영향을 주지 않습니다.
>
> 공유 키를 통해 계정에 대 한 액세스를 허용 하지 않거나 Azure Files 또는 테이블 저장소 작업을 지 원하는 저장소 계정에이 설정을 적용 하지 않으려면 Azure Files 또는 테이블 저장소 데이터를 별도의 저장소 계정으로 마이그레이션하는 것이 좋습니다.

이 문서에서는 공유 키 인증을 사용 하 여 보낸 요청을 검색 하는 방법 및 저장소 계정에 대 한 공유 키 인증을 수정 하는 방법을 설명 합니다. 미리 보기에 등록 하는 방법을 알아보려면 [미리 보기 정보](#about-the-preview)를 참조 하세요.

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>클라이언트 응용 프로그램에서 사용 하는 권한 부여 유형 검색

저장소 계정에 대 한 공유 키 인증을 허용 하지 않으면 공유 키 권한 부여에 대 한 계정 액세스 키를 사용 하는 클라이언트의 요청이 실패 합니다. 이러한 변경을 수행 하기 전에 공유 키 권한 부여가 클라이언트 응용 프로그램에 영향을 줄 수 있는 방법을 이해 하려면 저장소 계정에 대 한 로깅 및 메트릭을 사용 하도록 설정 합니다. 그런 다음 일정 시간 동안 계정에 대 한 요청 패턴을 분석 하 여 요청을 인증 하는 방법을 결정할 수 있습니다.

메트릭을 사용 하 여 공유 키 또는 SAS (공유 액세스 서명)로 인증 된 저장소 계정이 수신 하는 요청 수를 확인 합니다. 로그를 사용 하 여 해당 요청을 보내는 클라이언트를 확인 합니다.

미리 보기 중에 공유 액세스 서명으로 수행 된 요청을 해석 하는 방법에 대 한 자세한 내용은 [미리 보기 정보](#about-the-preview)를 참조 하세요.

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>공유 키로 권한이 부여 된 요청 수 모니터링

저장소 계정에 대 한 요청을 인증 하는 방법을 추적 하려면 Azure Portal에서 Azure 메트릭 탐색기를 사용 합니다. 메트릭 탐색기에 대 한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../azure-monitor/platform/metrics-getting-started.md)하기를 참조 하세요.

공유 키 또는 SAS를 사용 하 여 만든 요청을 추적 하는 메트릭을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다. **모니터링** 섹션에서 **메트릭**을 선택 합니다.
1. **메트릭 추가**를 선택합니다. **메트릭** 대화 상자에서 다음 값을 지정 합니다.
    1. **범위** 필드를 저장소 계정의 이름으로 설정 된 상태로 둡니다.
    1. **메트릭 네임 스페이스** 를 *계정*으로 설정 합니다. 이 메트릭은 저장소 계정에 대 한 모든 요청을 보고 합니다.
    1. **메트릭** 필드를 *트랜잭션으로*설정 합니다.
    1. **집계** 필드를 *Sum*으로 설정 합니다.

    새 메트릭은 지정 된 시간 간격 동안 저장소 계정에 대 한 트랜잭션 수의 합계를 표시 합니다. 결과 메트릭은 다음 이미지와 같이 표시 됩니다.

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="공유 키 또는 SAS를 사용 하 여 만든 트랜잭션을 합산 하는 메트릭을 구성 하는 방법을 보여 주는 스크린샷":::

1. 그런 다음 **필터 추가** 단추를 선택 하 여 인증 유형에 대 한 메트릭에 대 한 필터를 만듭니다.
1. **필터** 대화 상자에서 다음 값을 지정 합니다.
    1. **속성** 값을 *Authentication*으로 설정 합니다.
    1. **연산자** 필드를 등호 (=)로 설정 합니다.
    1. **값** 필드에서 *계정 키* 및 *SAS*를 선택 합니다.
1. 오른쪽 위 모서리에서 메트릭을 볼 시간 범위를 선택 합니다. 1 분에서 1 개월 간격으로 간격을 지정 하 여 요청 집계가 얼마나 세분화 되었는지 나타낼 수도 있습니다. 예를 들어, 지난 30 일 동안 날짜별로 집계 된 요청을 보려면 **시간 범위** 를 30 일로 설정 하 고 **시간 세분성** 을 1 일로 설정 합니다.

메트릭을 구성한 후에는 저장소 계정에 대 한 요청이 그래프에 표시 되기 시작 합니다. 다음 이미지는 공유 키로 권한이 부여 되었거나 SAS 토큰을 사용 하 여 만든 요청을 보여 줍니다. 요청은 지난 30 일 동안 하루에 집계 됩니다.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="공유 키로 권한이 부여 된 집계 된 요청을 보여 주는 스크린샷":::

또한 공유 키로 권한이 부여 된 특정 개수의 요청이 저장소 계정에 대해 수행 되는 경우 알리도록 경고 규칙을 구성할 수 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-metric.md)를 참조하세요.

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>로그를 분석 하 여 공유 키 또는 SAS를 사용 하 여 요청에 대 한 권한을 부여 하는 클라이언트 식별

Azure Storage 로그는 요청에 대 한 권한이 부여 된 방법을 포함 하 여 저장소 계정에 대해 수행 된 요청에 대 한 세부 정보를 캡처합니다. 로그를 분석 하 여 공유 키 또는 SAS 토큰을 사용 하 여 요청을 인증 하는 클라이언트를 확인할 수 있습니다.

권한 부여 방법을 평가 하기 위해 Azure Storage 계정에 요청을 기록 하려면 Azure Monitor (미리 보기)에서 Azure Storage 로깅을 사용할 수 있습니다. 자세한 내용은 [Azure Storage 모니터링](../common/monitor-storage.md)을 참조 하세요.

Azure Monitor Azure Storage 로깅은 로그 쿼리를 사용 하 여 로그 데이터를 분석 합니다. 로그를 쿼리하려면 Azure Log Analytics 작업 영역을 사용할 수 있습니다. 로그 쿼리에 대해 자세히 알아보려면 [자습서: Log Analytics 쿼리 시작](../../azure-monitor/log-query/get-started-portal.md)을 참조 하세요.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Azure Portal에서 진단 설정 만들기

Azure Monitor를 사용 하 여 Azure Storage 데이터를 기록 하 고 Azure Log Analytics를 사용 하 여 분석 하려면 먼저 요청 유형과 데이터를 기록할 저장소 서비스를 나타내는 진단 설정을 만들어야 합니다. Azure Portal에서 진단 설정을 만들려면 다음 단계를 수행 합니다.

1. [Azure Monitor 미리 보기에서 Azure Storage 로그인](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)에 등록 합니다.
1. 구독에서 Azure Storage 계정을 포함 하는 새 Log Analytics 작업 영역을 만들거나 기존 Log Analytics 작업 영역을 사용 합니다. 저장소 계정에 대 한 로깅을 구성한 후 Log Analytics 작업 영역에서 로그를 사용할 수 있습니다. 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요.
1. Azure Portal의 스토리지 계정으로 이동합니다.
1. 모니터링 섹션에서 **진단 설정 (미리 보기)** 을 선택 합니다.
1. 요청을 기록 하려는 Azure Storage 서비스를 선택 합니다. 예를 들어 blob 저장소에 요청을 기록 하려면 **blob** 을 선택 합니다.
1. **진단 설정 추가**를 선택 합니다.
1. 진단 설정의 이름을 제공 합니다.
1. **범주 세부 정보**의 **로그** 섹션에서 **StorageRead**, **storagewrite**및 **storagewrite** 를 선택 하 여 선택한 서비스에 모든 데이터 요청을 기록 합니다.
1. **대상 세부 정보**에서 **Log Analytics 보내기를**선택 합니다. 다음 이미지와 같이 앞에서 만든 구독 및 Log Analytics 작업 영역을 선택 합니다.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="요청 로깅에 대 한 진단 설정을 만드는 방법을 보여 주는 스크린샷":::

저장소 계정의 각 Azure Storage 리소스 유형에 대 한 진단 설정을 만들 수 있습니다.

진단 설정을 만든 후에는 해당 설정에 따라 저장소 계정에 대 한 요청이 나중에 기록 됩니다. 자세한 내용은 [Azure에서 리소스 로그 및 메트릭을 수집 하는 진단 설정 만들기](../../azure-monitor/platform/diagnostic-settings.md)를 참조 하세요.

Azure Monitor의 Azure Storage 로그에서 사용할 수 있는 필드에 대 한 참조는 [리소스 로그 (미리 보기)](../common/monitor-storage-reference.md#resource-logs-preview)를 참조 하세요.

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>공유 키 또는 SAS를 사용 하 여 수행 된 요청에 대 한 로그 쿼리

Azure Storage 로그 Azure Monitor에는 저장소 계정에 요청을 수행 하는 데 사용 된 권한 부여 유형이 포함 됩니다. 공유 키 또는 SAS로 권한이 부여 된 지난 7 일 동안 발생 한 요청에 대 한 로그를 검색 하려면 Log Analytics 작업 영역을 엽니다. 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행 합니다. 이 쿼리는 공유 키 또는 SAS를 사용 하 여 권한이 부여 된 요청을 가장 자주 보낸 10 개의 IP 주소를 표시 합니다.

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

이 쿼리를 기반으로 하는 경고 규칙을 구성 하 여 공유 키 또는 SAS로 인증 된 요청에 대해 알릴 수도 있습니다. 자세한 내용은 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-log.md)를 참조 하세요.

## <a name="remediate-authorization-via-shared-key"></a>공유 키를 통해 권한 부여 재구성

저장소 계정에 대 한 요청을 인증 하는 방법을 분석 한 후에는 공유 키를 통해 액세스 하지 못하도록 조치를 취할 수 있습니다. 그러나 먼저 공유 키 인증을 사용 하는 응용 프로그램을 업데이트 하 여 Azure AD를 대신 사용 해야 합니다. [클라이언트 응용 프로그램에서 전환을 추적 하는 데 사용 하는 권한 부여 유형 검색](#detect-the-type-of-authorization-used-by-client-applications) 에 설명 된 대로 로그 및 메트릭을 모니터링할 수 있습니다. Blob 및 큐 데이터에 Azure AD를 사용 하는 방법에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](storage-auth-aad.md)를 참조 하세요

공유 키를 사용 하 여 권한이 부여 된 요청을 안전 하 게 거부할 수 있는 경우 저장소 계정에 대 한 **Allowsharedkeyaccess** 속성을 **false**로 설정할 수 있습니다.

**Allowsharedkeyaccess** 속성은 기본적으로 설정 되지 않으며 명시적으로 설정할 때까지 값을 반환 하지 않습니다. 저장소 계정은 속성 값이 **null** 이거나 **True**인 경우 공유 키로 권한이 부여 된 요청을 허용 합니다.

> [!WARNING]
> 현재 클라이언트에서 공유 키를 사용 하 여 저장소 계정의 데이터에 액세스 하는 경우 해당 클라이언트를 Azure AD로 마이그레이션하여 저장소 계정에 대 한 공유 키 액세스를 허용 하지 않는 것이 좋습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 저장소 계정에 대 한 공유 키 인증을 허용 하지 않으려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정**에서 **구성** 설정을 찾습니다.
1. **공유 키 액세스 허용** 을 **사용 안 함**으로 설정 합니다.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="계정에 대 한 공유 키 액세스를 허용 하지 않는 방법을 보여 주는 스크린샷":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 된 저장소 계정에 대 한 공유 키 인증을 허용 하지 않으려면 Azure CLI 버전 2.9.1 이상을 설치 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 다음으로 새 또는 기존 저장소 계정에 대해 **Allowsharedkeyaccess** 속성을 구성 합니다.

다음 예에서는 Azure CLI를 사용 하 여 **Allowsharedkeyaccess** 속성을 설정 하는 방법을 보여 줍니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

공유 키 인증을 허용 하지 않으면 공유 키 권한 부여를 사용 하 여 저장소 계정에 대 한 요청을 수행할 수 없습니다. 오류 코드 403 (사용할 수 없음)이 표시 됩니다. Azure Storage는 저장소 계정에서 키 기반 권한 부여가 허용 되지 않음을 나타내는 오류를 반환 합니다.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>공유 키 액세스가 허용 되지 않는지 확인 합니다.

공유 키 권한 부여가 더 이상 허용 되지 않는지 확인 하기 위해 계정 액세스 키를 사용 하 여 데이터 작업을 호출할 수 있습니다. 다음 예에서는 액세스 키를 사용 하 여 컨테이너를 만들려고 시도 합니다. 저장소 계정에 대해 공유 키 권한이 허용 되지 않는 경우이 호출은 실패 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> 익명 요청은 허가 되지 않으며 익명의 공용 읽기 액세스를 위해 저장소 계정 및 컨테이너를 구성한 경우에도 계속 됩니다. 자세한 내용은 [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 구성](../blobs/anonymous-read-access-configure.md)을 참조 하세요.

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>여러 계정에 대 한 공유 키 액세스 설정 확인

최적의 성능을 가진 일련의 저장소 계정에서 공유 키 액세스 설정을 확인 하려면 Azure Portal에서 Azure 리소스 그래프 탐색기를 사용할 수 있습니다. 리소스 그래프 탐색기를 사용 하는 방법에 대해 자세히 알아보려면 [빠른 시작: Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리 실행](/azure/governance/resource-graph/first-query-portal)을 참조 하세요.

리소스 그래프 탐색기에서 다음 쿼리를 실행 하면 저장소 계정 목록이 반환 되 고 각 계정에 대 한 공유 키 액세스 설정이 표시 됩니다.

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>공유 키를 허용 하지 않는 경우 SAS 토큰에 미치는 영향 이해

저장소 계정에 대해 공유 키가 허용 되지 않는 경우 Azure Storage는 SAS 유형 및 요청의 대상인 서비스를 기반으로 SAS 토큰을 처리 합니다. 다음 표에서는 각 유형의 SAS에 권한이 부여 되는 방법과 저장소 계정의 **Allowsharedkeyaccess** 속성이 **false**인 경우 Azure Storage sas를 처리 하는 방법을 보여 줍니다.

| SAS 유형 | 권한 부여 유형 | AllowSharedKeyAccess가 false 인 경우의 동작 |
|-|-|-|
| 사용자 위임 SAS (Blob 저장소에만 해당) | Azure AD | 요청이 허용 됩니다. 뛰어난 보안을 위해 가능한 경우 사용자 위임 SAS를 사용 하는 것이 좋습니다. |
| 서비스 SAS | 공유 키 | Blob 저장소에 대 한 요청이 거부 되었습니다. 큐 및 테이블 저장소와 Azure Files에 대 한 요청이 허용 됩니다. 자세한 내용은 **미리 보기 정보** 섹션에서 [AllowSharedKeyAccess가 false 인 경우 큐, 테이블 및 파일에 대해 SAS 토큰이 있는 요청 허용](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) 을 참조 하세요. |
| 계정 SAS | 공유 키 | Blob 저장소에 대 한 요청이 거부 되었습니다. 큐 및 테이블 저장소와 Azure Files에 대 한 요청이 허용 됩니다. 자세한 내용은 **미리 보기 정보** 섹션에서 [AllowSharedKeyAccess가 false 인 경우 큐, 테이블 및 파일에 대해 SAS 토큰이 있는 요청 허용](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) 을 참조 하세요. |

공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)를 참조하세요.

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>다른 Azure 도구 및 서비스와의 호환성 고려

여러 Azure 서비스는 공유 키 인증을 사용 하 여 Azure Storage와 통신 합니다. 저장소 계정에 대 한 공유 키 인증을 허용 하지 않는 경우 이러한 서비스는 해당 계정의 데이터에 액세스할 수 없으며 응용 프로그램에 부정적인 영향을 줄 수 있습니다.

일부 Azure 도구에서는 Azure AD 권한 부여를 사용 하 여 Azure Storage에 액세스할 수 있는 옵션을 제공 합니다. 다음 표에서는 Azure AD를 사용 하 여 Azure Storage에 대 한 요청에 권한을 부여할 수 있는지에 대 한 몇 가지 인기 있는 Azure 도구를 보여 줍니다.

| Azure 도구 | Azure Storage에 대 한 Azure AD 권한 부여 |
|-|-|
| Azure portal | 지원됨. Azure Portal에서 Azure AD 계정을 사용 하 여 권한을 부여 하는 방법에 대 한 자세한 내용은 [Azure Portal에서 blob 또는 큐 데이터에 대 한 액세스 권한을 부여 하는 방법 선택](storage-access-blobs-queues-portal.md)을 참조 하세요. |
| AzCopy | Blob 저장소에 대해 지원 됩니다. AzCopy 작업에 권한을 부여 하는 방법에 대 한 자세한 내용은 AzCopy 설명서에서 [권한 부여 자격 증명을 제공 하는 방법 선택](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) 을 참조 하세요. |
| Azure Storage Explorer | Blob 저장소 및 Azure Data Lake Storage Gen2에만 지원 됩니다. 큐 저장소에 대 한 Azure AD 액세스는 지원 되지 않습니다. 올바른 Azure AD 테 넌 트를 선택 해야 합니다. 자세한 내용은 [Storage 탐색기 시작](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#sign-in-to-azure) 을 참조 하세요. |
| Azure PowerShell | 지원됨. Blob 및 큐 데이터에 액세스 하기 위해 Azure AD를 사용 하 여 PowerShell 명령에 권한을 부여 하는 방법에 대 한 자세한 내용은 [AZURE ad 자격 증명을 사용 하 여 powershell 명령 실행을](authorize-active-directory-powershell.md)참조 하세요. |
| Azure CLI | 지원됨. Blob 및 큐 데이터에 액세스 하기 위해 Azure AD를 사용 하 여 Azure CLI 명령에 권한을 부여 하는 방법에 대 한 자세한 내용은 [AZURE ad 자격 증명을 사용 하 여 Azure CLI 명령 실행](authorize-data-operations-cli.md)을 참조 하세요. |
| Azure IoT Hub | 지원됨. 자세한 내용은 [가상 네트워크에 대 한 지원 IoT Hub](../../iot-hub/virtual-network-support.md)를 참조 하세요. |
| Azure Cloud Shell | Azure Cloud Shell은 Azure Portal의 통합 셸입니다. Azure Cloud Shell는 저장소 계정의 Azure 파일 공유에서 지 속성 파일을 호스트 합니다. 이러한 파일은 해당 저장소 계정에 대해 공유 키 권한이 허용 되지 않는 경우 액세스할 수 없게 됩니다. 자세한 내용은 [연결 Microsoft Azure 파일 저장소](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage)를 참조 하세요. <br /><br /> 공유 키 액세스가 허용 되지 않는 저장소 계정을 관리 하기 위해 Azure Cloud Shell에서 명령을 실행 하려면 먼저 RBAC (역할 기반 액세스 제어)를 통해 이러한 계정에 필요한 사용 권한이 부여 되었는지 확인 합니다. 자세한 내용은 [azure 역할 기반 액세스 제어 (AZURE RBAC) 란?](../../role-based-access-control/overview.md)을 참조 하세요. |

## <a name="about-the-preview"></a>미리 보기 정보

공유 키 권한 부여를 허용 하지 않는 미리 보기는 Azure 공용 클라우드에서 사용할 수 있습니다. Azure Resource Manager 배포 모델만 사용 하는 저장소 계정에 대해서만 지원 됩니다. Azure Resource Manager 배포 모델을 사용 하는 저장소 계정에 대 한 자세한 내용은 [저장소 계정 유형](storage-account-overview.md#types-of-storage-accounts)을 참조 하세요.

미리 보기에 등록 하려면 [공유 키 액세스 제한 된 공개 미리 보기 Azure Storage](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u)를 참조 하세요.

> [!IMPORTANT]
> 이 미리 보기는 프로덕션 이외 용도로 사용해야 합니다.

미리 보기에는 다음 섹션에서 설명 하는 제한 사항이 포함 되어 있습니다.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>메트릭 및 로깅은 승인 된 방법에 관계 없이 SAS로 생성 된 모든 요청을 보고 합니다.

Azure Monitor Azure 메트릭 및 로깅은 미리 보기에서 서로 다른 유형의 공유 액세스 서명 간을 구분 하지 않습니다. Azure 메트릭 탐색기의 **sas** 필터 및 Azure Monitor Azure Storage **로그인의 sas 필드는** 모두 모든 유형의 sas로 인증 된 요청을 보고 합니다. 그러나 다른 유형의 공유 액세스 서명은 다른 방식으로 권한이 부여 되며 공유 키 액세스가 허용 되지 않는 경우 다르게 동작 합니다.

- 서비스 SAS 토큰 또는 계정 SAS 토큰은 공유 키로 권한이 부여 되며 **Allowsharedkeyaccess** 속성이 **false**로 설정 된 경우 Blob 저장소에 대 한 요청에 허용 되지 않습니다.
- 사용자 위임 SAS는 Azure AD를 사용 하 여 권한을 부여 받고 **Allowsharedkeyaccess** 속성이 **false**로 설정 된 경우 Blob 저장소에 대 한 요청에서 허용 됩니다.

저장소 계정에 대 한 트래픽을 평가할 때 [클라이언트 응용 프로그램에서 사용 하는 권한 부여 유형 검색](#detect-the-type-of-authorization-used-by-client-applications) 에 설명 된 메트릭과 로그에 사용자 위임 SAS를 사용 하 여 만든 요청이 포함 될 수 있다는 점에 유의 하세요. **Allowsharedkeyaccess** 속성이 **false**로 설정 된 경우 Azure Storage sas에 응답 하는 방법에 대 한 자세한 내용은 [공유 키 허용 안 함이 Sas 토큰에 주는 영향 이해](#understand-how-disallowing-shared-key-affects-sas-tokens)를 참조 하세요.

### <a name="requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false"></a>AllowSharedKeyAccess가 false 인 경우 큐, 테이블 및 파일에 대해 SAS 토큰이 있는 요청이 허용 됩니다.

미리 보기 중에 저장소 계정에 대 한 공유 키 액세스가 허용 되지 않는 경우에는 큐, 테이블 또는 Azure Files 리소스를 대상으로 하는 공유 액세스 서명이 계속 허용 됩니다. 이러한 제한 사항은 서비스 SAS 토큰과 계정 SAS 토큰에 모두 적용 됩니다. 두 유형의 SAS는 모두 공유 키로 인증 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 데이터에 대 한 액세스 권한 부여](storage-auth.md)
- [Azure Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](storage-auth-aad.md)
- [공유 키로 권한 부여](/rest/api/storageservices/authorize-with-shared-key)