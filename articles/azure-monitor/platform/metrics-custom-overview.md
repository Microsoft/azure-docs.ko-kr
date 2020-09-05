---
title: Azure Monitor의 사용자 지정 메트릭 (미리 보기)
description: Azure Monitor의 사용자 지정 메트릭 및 모델링하는 방법을 알아봅니다.
author: ancav
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 06/01/2020
ms.subservice: metrics
ms.openlocfilehash: 73c9b2bf8cf88ca5e8576c451c9d9ac5f0eae8a3
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639905"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Azure Monitor의 사용자 지정 메트릭 (미리 보기)

Azure에서 리소스 및 애플리케이션을 배포하는 동안 성능 및 상태에 대한 정보를 얻기 위해 원격 분석 수집을 시작할 수 있습니다. Azure는 몇 가지 메트릭을 기본적으로 제공합니다. 이러한 메트릭은 [표준 또는 플랫폼](./metrics-supported.md)이라고 합니다. 그러나 메트릭은 본질적으로 제한되어 있습니다. 

더욱 심층적인 인사이트를 제공하려면 일부 사용자 지정 성능 지표 또는 비즈니스 관련 메트릭을 수집하는 것이 좋습니다. 이러한 **사용자 지정** 메트릭은 애플리케이션 원격 분석, Azure 리소스에서 실행되는 에이전트 또는 외부 모니터링 시스템을 통해서도 수집되어 Azure Monitor로 직접 전송될 수 있습니다. Azure Monitor에 게시된 후에는 Azure에서 내보낸 표준 메트릭과 나란히, Azure 리소스 및 애플리케이션에 대한 사용자 지정 메트릭을 찾아보고 쿼리 및 경고할 수 있습니다.

Azure Monitor 사용자 지정 메트릭은 공개 미리 보기로 제공 됩니다. 

## <a name="methods-to-send-custom-metrics"></a>사용자 지정 메트릭을 보내는 방법

다음과 같은 여러 가지 방법으로 사용자 지정 메트릭을 Azure Monitor로 보낼 수 있습니다.
- Azure Application Insights SDK를 사용하여 애플리케이션을 계측하고 사용자 지정 원격 분석을 Azure Monitor로 보냅니다. 
- [Windows 또는 Linux AZURE VM](azure-monitor-agent-overview.md) 에 Azure Monitor 에이전트 (미리 보기)를 설치 하 고 [데이터 수집 규칙](data-collection-rule-azure-monitor-agent.md) 을 사용 하 여 성능 카운터를 Azure Monitor 메트릭에 보냅니다.
- [Azure VM](collect-custom-metrics-guestos-resource-manager-vm.md), [가상 머신 확장 집합](collect-custom-metrics-guestos-resource-manager-vmss.md), [클래식 VM](collect-custom-metrics-guestos-vm-classic.md) 또는 [클래식 Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md)에 WAD(Microsoft Azure Diagnostics) 확장을 설치하고 성능 카운터를 Azure Monitor로 보냅니다. 
- Azure Linux VM에 [InfluxData Telegraf 에이전트](collect-custom-metrics-linux-telegraf.md)를 설치하고 Azure Monitor 출력 플러그 인을 사용하여 메트릭을 보냅니다.
- 사용자 지정 메트릭을 [Azure Monitor REST API에 직접](./metrics-store-custom-rest-api.md)보냅니다 `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>가격 책정 모델 및 보존

사용자 지정 메트릭 및 메트릭 쿼리에 대 한 대금 청구를 사용 하도록 설정 하는 경우에 대 한 자세한 내용은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/) 를 확인 하세요. 사용자 지정 메트릭 및 메트릭 쿼리를 비롯 한 모든 메트릭에 대 한 특정 가격 정보는이 페이지에서 제공 됩니다. 요약 하자면, 표준 메트릭 (플랫폼 메트릭)을 Azure Monitor 메트릭 저장소에 수집 하는 비용은 없지만 사용자 지정 메트릭은 일반 공급으로 전환 될 때 비용이 발생 합니다. 메트릭 API 쿼리는 비용이 발생 합니다.

사용자 지정 메트릭은 [플랫폼 메트릭과 동일한 시간](data-platform-metrics.md#retention-of-metrics)동안 보존 됩니다. 

> [!NOTE]  
> Application Insights SDK를 통해 Azure Monitor로 전송 되는 메트릭은 수집 로그 데이터로 청구 됩니다. [사용자 지정 메트릭 차원에 대 한 경고를 사용 하도록 설정](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) 된 Application Insights 기능이 선택 된 경우에만 추가 메트릭 요금이 발생 합니다. 이 확인란은 사용자 지정 메트릭 API를 사용 하 여 Azure Monitor 메트릭 데이터베이스로 데이터를 전송 하 여 더 복잡 한 경고를 허용 합니다.  [지역에서](https://azure.microsoft.com/pricing/details/monitor/) [Application Insights 가격 책정 모델](../app/pricing.md#pricing-model) 및 가격에 대해 자세히 알아보세요.


## <a name="how-to-send-custom-metrics"></a>사용자 지정 메트릭을 보내는 방법

Azure Monitor에 사용자 지정 메트릭을 보낼 때 보고되는 각 데이터 요소 또는 값은 다음 정보를 포함해야 합니다.

### <a name="authentication"></a>인증
사용자 지정 메트릭을 Azure Monitor로 전송하려면 메트릭을 전송하는 엔터티에 유효한 Azure AD(Azure Active Directory) 토큰이 요청의 **전달자** 헤더에 있어야 합니다. 유효한 전달자 토큰을 획득하기 위한 지원되는 몇 가지 방법이 있습니다.
1. [Azure 리소스에 대 한 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)입니다. Azure 리소스 자체(예: VM)에 ID를 제공합니다. MSI(관리 서비스 ID)는 특정 작업을 수행할 수 있는 권한을 리소스에 제공하도록 설계되었습니다. 예를 들어, 리소스가 해당 지표를 내보낼 수 있도록 허용합니다. 리소스 또는 해당 MSI에 다른 리소스에 대한 **모니터링 메트릭 게시자** 권한을 부여할 수 있습니다. 이 권한을 사용하면 MSI가 다른 리소스에 대한 지표도 내보낼 수 있습니다.
2. [AZURE AD 서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)입니다. 이 시나리오에서는 Azure 리소스에 대한 메트릭을 내보내는 권한을 Azure AD 애플리케이션 또는 서비스에 할당할 수 있습니다.
요청을 인증하기 위해 Azure Monitor는 Azure AD 공개 키를 사용하여 애플리케이션 토큰의 유효성을 검사합니다. 기존 **모니터링 메트릭 게시자** 역할에는 이 사용 권한이 이미 있으며, Azure Portal에서 사용할 수 있습니다. 서비스 주체는 사용자 지정 메트릭을 내보낼 리소스에 따라 필요한 범위에서 **모니터링 메트릭 게시자** 역할을 부여받을 수 있습니다. 범위의 예로 구독, 리소스 그룹 또는 특정 리소스가 있습니다.

> [!TIP]  
> 사용자 지정 메트릭을 내보내기 위해 Azure AD 토큰을 요청하는 경우 토큰이 요청되는 대상 그룹 또는 리소스가 `https://monitoring.azure.com/`이어야 합니다. 후행 슬래시(‘/’)를 포함해야 합니다.

### <a name="subject"></a>제목
이 속성은 사용자 지정 메트릭이 보고되는 Azure 리소스 ID를 캡처합니다. 이 정보는 수행되는 API 호출의 URL에 인코딩됩니다. 각 API는 단일 Azure 리소스에 대한 메트릭 값만 전송할 수 있습니다.

> [!NOTE]  
> 리소스 그룹 또는 구독의 리소스 ID에 대해서는 사용자 지정 메트릭을 내보낼 수 없습니다.


### <a name="region"></a>지역
이 속성은 메트릭을 내보내는 리소스가 배포된 Azure 지역을 캡처합니다. 메트릭은 리소스가 배포되는 지역과 동일한 Azure Monitor 지역 엔드포인트에 배포되어야 합니다. 예를 들어, 미국 서부에 배포된 VM에 대한 사용자 지정 메트릭은 WestUS 지역별 Azure Monitor 엔드포인트에 전송되어야 합니다. 또한 지역 정보는 API 호출의 URL에 인코딩됩니다.

> [!NOTE]  
> 공개 미리 보기 중에는 일부 Azure 지역에서만 사용자 지정 메트릭을 사용할 수 있습니다. 지원되는 지역 목록은 이 문서의 뒷부분에 설명되어 있습니다.
>
>

### <a name="timestamp"></a>타임스탬프
Azure Monitor에 전송되는 각 데이터 요소는 타임스탬프를 사용하여 표시되어야 합니다. 이 타임스탬프는 메트릭 값이 측정 또는 수집된 날짜/시간을 캡처합니다. Azure Monitor는 과거 20분 및 미래 5분까지의 타임스탬프가 지정된 메트릭 데이터를 허용합니다. 타임 스탬프는 ISO 8601 형식 이어야 합니다.

### <a name="namespace"></a>네임스페이스
네임스페이스는 유사한 메트릭을 함께 분류 또는 그룹화하는 방법입니다. 네임스페이스를 사용하면 각기 다른 인사이트 또는 성능 지표를 수집할 수 있는 메트릭 그룹을 격리할 수 있습니다. 예를 들어 앱을 프로 파일링 하는 메모리 사용 메트릭을 추적 하는 **contosomemorymetrics** 라는 네임 스페이스가 있을 수 있습니다. **Contosoapptransaction** 라는 다른 네임 스페이스는 응용 프로그램의 사용자 트랜잭션에 대 한 모든 메트릭을 추적할 수 있습니다.

### <a name="name"></a>속성
**이름**은 보고되는 메트릭의 이름입니다. 일반적으로 이름은 측정 대상을 식별하기에 충분한 정보를 제공합니다. 예를 들어, 지정된 VM에서 사용된 메모리 바이트 수를 측정하는 메트릭이 있습니다. 메트릭 이름은 **사용 중인 메모리 바이트**일 수 있습니다.

### <a name="dimension-keys"></a>차원 키
차원은 수집하는 메트릭에 관한 추가적인 특성을 설명하는 데 유용한 키 또는 값 쌍입니다. 추가적인 특성을 사용하면 메트릭에 대한 자세한 정보를 수집하여 더욱 심층적인 인사이트를 얻을 수 있습니다. 예를 들어, **사용 중인 메모리 바이트** 메트릭에는 VM의 각 프로세스에서 사용 중인 메모리 바이트 수를 캡처하는 **프로세스**라는 차원 키가 있을 수 있습니다. 이 키를 사용 하 여 메트릭을 필터링 하 여 메모리 별 프로세스의 크기를 확인 하거나 메모리 사용량에 따라 상위 5 개 프로세스를 식별할 수 있습니다.
차원은 선택 사항이 며 모든 메트릭에 차원이 포함 되지 않을 수 있습니다. 사용자 지정 메트릭은 차원을 최대 10 개까지 포함할 수 있습니다.

### <a name="dimension-values"></a>차원 값
메트릭 데이터 요소를 보고하는 경우 보고되는 메트릭의 각 차원 키에 해당하는 차원 값이 있습니다. 예를 들어, VM의 ContosoApp에서 사용하는 메모리를 보고하려는 경우

* 메트릭 이름은 **사용 중인 메모리 바이트**가 됩니다.
* 차원 키는 **프로세스**가 됩니다.
* 차원 값은 **ContosoApp.exe**가 됩니다.

메트릭 값을 게시하는 경우 차원 키당 단일 차원 값만 지정할 수 있습니다. VM의 여러 프로세스에 대해 수집한 메모리 사용률이 동일한 경우 해당 타임스탬프에 여러 개의 메트릭 값을 보고할 수 있습니다. 각 메트릭 값은 **프로세스** 차원 키에 대해 다른 차원 값을 지정합니다.
차원은 선택 사항이 며 모든 메트릭에 차원이 포함 되지 않을 수 있습니다. 메트릭 게시에서 차원 키를 정의 하는 경우 해당 차원 값은 필수입니다.

### <a name="metric-values"></a>메트릭 값
Azure Monitor는 1분 단위 간격으로 모든 메트릭을 저장합니다. 지정된 1분 동안 메트릭을 여러 번 샘플링해야 하는 경우도 있습니다. 예를 들면 CPU 사용률이 있습니다. 또는 많은 불연속 이벤트에 대해 측정해야 할 수도 있습니다. 로그인 트랜잭션 대기 시간을 예로 들 수 있습니다. 내보내야 하는 원시 값의 수를 제한하고 Azure Monitor에 대한 요금을 지불하려면 다음과 같은 값을 로컬로 미리 집계하고 내보낼 수 있습니다.

* **최소**: 1분 동안 모든 샘플 및 측정값에서 관찰된 최솟값입니다.
* **최대**: 1분 동안 모든 샘플 및 측정값에서 관찰된 최댓값입니다.
* **합계**: 1분 동안 모든 샘플 및 측정값에서 관찰된 모든 값의 합계입니다.
* **개수**: 1분 동안 수행된 샘플 및 측정값 수입니다.

예를 들어 지정 된 시간 동안 앱에 4 개의 로그인 트랜잭션이 있는 경우 각각에 대해 측정 된 대기 시간을 다음과 같이 지정할 수 있습니다.

|트랜잭션 1|트랜잭션 2|트랜잭션 3|트랜잭션 4|
|---|---|---|---|
|7ms|4ms|13ms|16밀리초|
|

Azure Monitor에 대한 결과 메트릭 게시는 다음과 같습니다.
* 최소: 4
* 최대: 16
* 합계: 40
* 개수: 4

애플리케이션이 로컬로 미리 집계할 수 없고 각 불연속 샘플 또는 이벤트를 수집되는 즉시 내보내야 하는 경우 원시 측정 값을 내보낼 수 있습니다. 예를 들어, 앱에서 로그인 트랜잭션이 발생할 때마다 단일 측정값만 사용하여 메트릭을 Azure Monitor에 게시합니다. 따라서 12밀리초가 걸리는 로그인 트랜잭션의 경우 메트릭 게시는 다음과 같습니다.
* 최소: 12
* 최대: 12
* 합계: 12
* 개수: 1

이 프로세스에서는 지정된 1분 동안 동일한 메트릭 및 차원 조합에 대해 여러 개의 값을 내보낼 수 있습니다. 그런 다음, Azure Monitor에서 지정된 1분 동안 내보낸 모든 원시 값을 가져와 집계합니다.

### <a name="sample-custom-metric-publication"></a>샘플 사용자 지정 메트릭 게시
다음 예제에서는 가상 머신에 대한 **메모리 프로필** 메트릭 네임스페이스에 **사용 중인 메모리 바이트**라는 사용자 지정 메트릭을 만듭니다. 이 메트릭에는 **프로세스**라는 단일 차원이 있습니다. 지정된 타임스탬프에서 다음 두 가지 프로세스에 대한 메트릭 값을 내보냅니다.

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, 진단 확장 및 InfluxData Telegraf 에이전트는 올바른 지역별 엔드포인트에 대한 메트릭 값을 내보내고 각 내보내기에 위의 속성을 모두 포함하도록 이미 구성되어 있습니다.
>
>

## <a name="custom-metric-definitions"></a>사용자 지정 메트릭 정의
내보내기 전에 사용자 지정 메트릭을 Azure Monitor에서 미리 정의할 필요가 없습니다. 게시된 각 메트릭 데이터 요소에는 네임스페이스, 이름 및 차원 정보가 포함되어 있습니다. 따라서 사용자 지정 메트릭을 Azure Monitor에 처음 내보낼 때 메트릭 정의가 자동으로 생성됩니다. 그런 다음, 메트릭 정의를 통해 메트릭을 내보낸 모든 리소스에서 이 메트릭 정의를 검색할 수 있습니다.

> [!NOTE]  
> Azure Monitor는 사용자 지정 메트릭에 대 한 **단위** 정의를 아직 지원 하지 않습니다.

## <a name="using-custom-metrics"></a>사용자 지정 메트릭 사용
사용자 지정 메트릭을 Azure Monitor에 전송한 후 Azure Portal을 통해 검색하고 Azure Monitor REST API를 통해 쿼리할 수 있습니다. 특정 조건이 충족되면 알리도록 메트릭에 대한 경고를 만들 수도 있습니다.

> [!NOTE]
> 사용자 지정 메트릭을 보려면 독자 또는 참가자 역할을 수행 해야 합니다.

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Azure Portal을 통해 사용자 지정 메트릭 찾아보기
1.    [Azure Portal](https://portal.azure.com)로 이동합니다.
2.    **모니터** 창을 선택합니다.
3.    **메트릭**을 선택합니다.
4.    사용자 지정 메트릭을 내보낸 리소스를 선택합니다.
5.    사용자 지정 메트릭에 대한 메트릭 네임스페이스를 선택합니다.
6.    사용자 지정 메트릭을 선택합니다.

## <a name="supported-regions"></a>지원되는 지역
공개 미리 보기 중 사용자 지정 메트릭을 게시할 수 있는 기능은 일부 Azure 지역에서만 사용할 수 있습니다. 이 제한 사항은 지원되는 지역 중 하나의 리소스에 대해서만 메트릭을 게시할 수 있음을 의미합니다. 다음 표에는 사용자 지정 메트릭이 지원되는 Azure 지역 집합이 나와 있습니다. 이러한 지역의 리소스에 대한 메트릭이 게시되어야 하는 해당 엔드포인트도 나와 있습니다.

|Azure 지역 |지역별 엔드포인트 접두사|
|---|---|
| **미국과 캐나다** | |
|미국 중서부 | https: \/ /westcentralus.monitoring.azure.com |
|미국 서부 2       | https: \/ /westus2.monitoring.azure.com |
|미국 중북부 | https: \/ /northcentralus.monitoring.azure.com
|미국 중남부| https: \/ /southcentralus.monitoring.azure.com |
|미국 중부      | https: \/ /centralus.monitoring.azure.com |
|캐나다 중부 | https: \/ /canadacentral.monitoring.azure.com |
|미국 동부| https: \/ /eastus.monitoring.azure.com |
|미국 동부 2 | https: \/ /eastus2.monitoring.azure.com |
| **유럽** | |
|북유럽    | https: \/ /northeurope.monitoring.azure.com |
|서유럽     | https: \/ /westeurope.monitoring.azure.com |
|영국 남부 | https: \/ /uksouth.monitoring.azure.com
|프랑스 중부 | https: \/ /francecentral.monitoring.azure.com |
| **아프리카** | |
|남아프리카 북부 | https: \/ /southafricanorth.monitoring.azure.com |
| **아시아** | |
|인도 중부 | https: \/ /centralindia.monitoring.azure.com |
|오스트레일리아 동부 | https: \/ /australiaeast.monitoring.azure.com |
|일본 동부 | https: \/ /japaneast.monitoring.azure.com |
|동남아시아  | https: \/ /southeastasia.monitoring.azure.com |
|동아시아 | https: \/ /eastasia.monitoring.azure.com |
|한국 중부   | https: \/ /koreacentral.monitoring.azure.com |

## <a name="latency-and-storage-retention"></a>대기 시간 및 저장소 보존

새 메트릭 또는 메트릭에 추가 되는 새 차원을 추가 하는 것이 표시 되는 데 최대 2 ~ 3 분이 소요 될 수 있습니다. 시스템에서 데이터는 30 초 미만의 99%에 표시 되어야 합니다. 

메트릭을 삭제 하거나 차원을 제거 하는 경우 변경 내용은 시스템에서 삭제 되는 데 1 ~ 1 일 걸릴 수 있습니다.

## <a name="quotas-and-limits"></a>할당량 및 제한
Azure Monitor는 사용자 지정 메트릭에 대해 다음과 같은 사용량 한도를 적용합니다.

|범주|제한|
|---|---|
|활성 시계열/구독/지역|50,000|
|메트릭당 차원 키|10|
|메트릭 네임스페이스, 메트릭 이름, 차원 키 및 차원 값의 문자열 길이|256자|

활성 시계열은 지난 12시간 동안 메트릭 값이 게시된 메트릭, 차원 키 또는 차원 값의 고유한 조합으로 정의됩니다.

## <a name="next-steps"></a>다음 단계
다음과 같은 다양한 서비스의 사용자 지정 메트릭을 사용합니다. 
 - [Virtual Machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [가상 머신 크기 집합](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (클래식)](collect-custom-metrics-guestos-vm-classic.md)
 - [Telegraf 에이전트를 사용하는 Linux 가상 머신](collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [클래식 Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 

