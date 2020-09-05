---
title: Azure Analysis Services 규모 확장 | Microsoft Docs
description: 확장을 사용 하 여 Azure Analysis Services 서버를 복제 합니다. 그러면 클라이언트 쿼리를 스케일 아웃 쿼리 풀의 여러 쿼리 복제본 간에 배포할 수 있습니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ceed2a287fb210a421972e9c9f9e6c77c6cb1879
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716931"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 규모 확장

스케일 아웃을 사용 하면 쿼리 *풀*의 여러 *쿼리 복제본* 간에 클라이언트 쿼리를 분산 하 여 높은 쿼리 작업 중에 응답 시간을 줄일 수 있습니다. 또한 클라이언트 쿼리가 작업 처리에 의해 부정적인 영향을 받지 않도록 쿼리 풀에서 처리가 구분될 수도 있습니다. Azure Portal 또는 Analysis Services REST API를 사용하여 규모 확장을 구성할 수 있습니다.

규모 확장은 표준 가격 책정 계층의 서버에서 사용할 수 있습니다. 각 쿼리 복제본은 서버와 동일한 요금으로 청구됩니다. 모든 쿼리 복제본은 서버와 동일한 지역에 만들어집니다. 사용자가 구성할 수 있는 쿼리 복제본 수는 서버가 있는 지역에 따라 제한됩니다. 자세히 알아보려면 [지역별 가용성](analysis-services-overview.md#availability-by-region)을 참조하세요. 규모 확장은 서버의 사용 가능한 메모리 양을 증가시키지 않습니다. 메모리를 늘리려면 계획을 업그레이드해야 합니다. 

## <a name="why-scale-out"></a>규모를 확장 하는 이유

일반적인 서버 배포에서는 한 서버가 처리 서버 및 쿼리 서버 역할을 모두 수행합니다. 서버 모델에 대한 클라이언트 쿼리 수가 서버 계획에 대한 QPU(쿼리 처리 단위)를 초과하거나 높은 쿼리 워크로드와 동시에 모델 처리가 발생하면 성능이 저하될 수 있습니다. 

규모 확장을 사용 하면 최대 7 개의 추가 쿼리 복제본 리소스 ( *주* 서버 포함)를 포함 하는 쿼리 풀을 만들 수 있습니다. 중요 한 시간에 QPU 수요를 충족 하도록 쿼리 풀의 복제본 수를 확장 하 고 언제 든 지 쿼리 풀에서 처리 서버를 분리할 수 있습니다. 

쿼리 풀에 있는 쿼리 복제본의 수와 관계없이 처리 워크로드는 쿼리 복제본 간에 분산되지 않습니다. 주 서버는 처리 서버 역할을 합니다. 쿼리 복제본은 주 서버와 쿼리 풀의 각 복제본 간에 동기화 된 모델 데이터베이스에 대 한 쿼리만 제공 합니다. 

규모를 확장 하는 경우 새 쿼리 복제본을 쿼리 풀에 증분 추가 하는 데 최대 5 분이 걸릴 수 있습니다. 모든 새 쿼리 복제본을 실행 하는 동안 새 클라이언트 연결은 쿼리 풀의 여러 리소스에서 부하가 분산 됩니다. 기존 클라이언트 연결은 현재 연결되어 있는 리소스에서 변경되지 않습니다. 규모 감축 시 조정할 때 쿼리 풀에서 제거되는 쿼리 풀 리소스에 대한 기존 클라이언트 연결이 종료됩니다. 클라이언트는 나머지 쿼리 풀 리소스에 다시 연결할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

처음으로 규모 확장을 구성 하는 경우 주 서버의 model 데이터베이스가 새 쿼리 풀의 새 복제본과 *자동으로* 동기화 됩니다. 자동 동기화는 한 번만 발생 합니다. 자동 동기화를 수행 하는 동안 주 서버의 데이터 파일 (blob 저장소에 저장 된 상태로 암호화 됨)은 두 번째 위치에 복사 되 고 blob 저장소에 저장 된 상태로도 복사 됩니다. 그런 다음 쿼리 풀의 복제본은 두 번째 파일 집합의 데이터로 *하이드레이션* 됩니다. 

자동 동기화는 서버를 처음 확장 하는 경우에만 수행 되지만 수동 동기화를 수행할 수도 있습니다. 쿼리 풀의 복제본에 대 한 데이터 동기화가 주 서버의 복제본과 일치 하는지 확인 합니다. 주 서버에서 모델을 처리 (새로 고침) 하는 경우에는 처리 작업이 완료 된 *후* 동기화를 수행 해야 합니다. 이 동기화는 blob 저장소의 주 서버 파일에서 두 번째 파일 집합으로 업데이트 된 데이터를 복사 합니다. 그런 다음 쿼리 풀의 복제본이 blob 저장소에 있는 두 번째 파일 집합의 업데이트 된 데이터로 하이드레이션 됩니다. 

예를 들어 쿼리 풀의 복제본 수를 2 ~ 5로 늘려도 후속 스케일 아웃 작업을 수행할 때 새 복제본은 blob storage에 있는 두 번째 파일 집합의 데이터로 하이드레이션 됩니다. 동기화가 없습니다. 확장 후 동기화를 수행 하는 경우 쿼리 풀의 새 복제본은 중복 하이드레이션 두 번 하이드레이션 됩니다. 후속 스케일 아웃 작업을 수행할 때 다음 사항에 유의 해야 합니다.

* 추가 된 복제본의 중복 하이드레이션 방지 하기 위해 *스케일 아웃 작업 전에* 동기화를 수행 합니다. 동시에 동시에 실행 되는 동시 동기화 및 확장 작업은 허용 되지 않습니다.

* 처리 *및* 확장 작업을 모두 자동화 하는 경우 먼저 주 서버에서 데이터를 처리 한 다음 동기화를 수행 하 고 스케일 아웃 작업을 수행 하는 것이 중요 합니다. 이 시퀀스는 QPU 및 메모리 리소스에 대 한 최소한의 영향을 보장 합니다.

* 쿼리 풀에 복제본이 없는 경우에도 동기화가 허용 됩니다. 주 서버에서 처리 작업의 새 데이터를 사용 하 여 0에서 하나 이상의 복제본으로 확장 하는 경우 쿼리 풀에서 복제본 없이 먼저 동기화를 수행 하 고 스케일 아웃 합니다. 규모 확장 전 동기화는 새로 추가 된 복제본의 중복 하이드레이션을 방지 합니다.

* 주 서버에서 model 데이터베이스를 삭제할 때 쿼리 풀의 복제본에서 자동으로 삭제 되지 않습니다. [AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell 명령을 사용 하 여 복제본의 공유 blob 저장소 위치에서 해당 데이터베이스의 파일/s를 제거한 다음 쿼리 풀의 복제본에서 model 데이터베이스를 삭제 하는 동기화 작업을 수행 해야 합니다. 모델 데이터베이스가 주 서버가 아닌 쿼리 풀의 복제본에 존재 하는지 확인 하려면 **풀 쿼리에서 처리 서버를 분리** 합니다. 설정이 **예**인지 확인 합니다. 그런 다음, SSMS를 사용 하 여 `:rw` 데이터베이스의 존재 여부를 확인 하는 한정자를 사용 하 여 주 서버에 연결 합니다. 그런 다음 한정자 없이 연결 하 여 쿼리 풀의 복제본에 연결 하 여 `:rw` 동일한 데이터베이스가 있는지 확인 합니다. 데이터베이스가 쿼리 풀의 복제본에 있지만 주 서버에 있는 경우에는 동기화 작업을 실행 합니다.   

* 주 서버에서 데이터베이스의 이름을 바꿀 때 데이터베이스가 복제본에 올바르게 동기화 되었는지 확인 하는 데 필요한 추가 단계가 있습니다. 이름을 바꾼 후에는 [AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) 명령을 사용 하 여 동기화를 수행 하 고 `-Database` 이전 데이터베이스 이름을 사용 하 여 매개 변수를 지정 합니다. 이 동기화는 복제본에서 이전 이름의 데이터베이스와 파일을 제거 합니다. 그런 다음 `-Database` 새 데이터베이스 이름으로 매개 변수를 지정 하 여 다른 동기화를 수행 합니다. 두 번째 동기화는 새로 명명 된 데이터베이스를 두 번째 파일 집합에 복사 하 고 모든 복제본을 하이드레이션 하며 나중 합니다. 이러한 동기화는 포털의 모델 동기화 명령을 사용 하 여 수행할 수 없습니다.

### <a name="synchronization-mode"></a>동기화 모드

기본적으로 쿼리 복제본은 증분이 아닌 전체로 증가 됩니다. 리하이드레이션는 단계에서 발생 합니다. 한 번에 두 개 이상의 복제본을 분리 하 고 연결 하 여 지정 된 시간에 하나 이상의 복제본이 온라인 상태로 유지 되도록 합니다. 경우에 따라이 프로세스가 수행 되는 동안 클라이언트에서 온라인 복제본 중 하나에 다시 연결 해야 할 수도 있습니다. 이제 **ReplicaSyncMode** 설정을 사용 하 여 쿼리 복제본 동기화가 병렬로 발생 하도록 지정할 수 있습니다. 병렬 동기화는 다음과 같은 이점을 제공 합니다. 

- 동기화 시간이 크게 감소 했습니다. 
- 복제본 간의 데이터는 동기화 프로세스 중에 일관 될 가능성이 높습니다. 
- 데이터베이스는 동기화 프로세스 전체에서 모든 복제본에 대해 온라인으로 유지 되므로 클라이언트는 다시 연결할 필요가 없습니다. 
- 메모리 내 캐시는 변경 된 데이터만으로 증분 업데이트 되며 모델을 완전히 리하이드레이션 수 있습니다. 

#### <a name="setting-replicasyncmode"></a>ReplicaSyncMode 설정

SSMS를 사용 하 여 고급 속성에서 ReplicaSyncMode를 설정 합니다. 가능한 값은 다음과 같습니다. 

- `1` (기본값): 전체 복제본 데이터베이스 리하이드레이션 단계 (증분). 
- `2`: 동기화가 병렬로 최적화 되었습니다. 

![모델 설정/설정](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

업데이트 해야 하는 캐시의 양에 따라 **ReplicaSyncMode = 2**를 설정 하는 경우 쿼리 복제본에서 추가 메모리를 소비할 수 있습니다. 데이터베이스를 온라인 상태로 유지 하 고 쿼리에 사용할 수 있도록 하려면 변경 된 데이터의 양에 따라 작업에서 이전 세그먼트와 새 세그먼트를 동시에 메모리에 유지 하기 때문에 복제본의 *메모리를 배가* 해야 할 수 있습니다. 복제본 노드의 메모리 할당은 주 노드와 동일 하며, 일반적으로 새로 고침 작업을 위한 기본 노드에 추가 메모리가 있으므로 복제본 메모리가 부족 하지 않을 수 있습니다. 또한 일반적인 시나리오는 데이터베이스를 주 노드에서 증분 업데이트 하는 것 이므로 메모리를 이중으로 요구 하는 것은 일반적이 지 않습니다. 동기화 작업에 메모리 부족 오류가 발생 하는 경우 기본 기술을 사용 하 여 다시 시도 합니다 (한 번에 두 번 연결/분리). 

### <a name="separate-processing-from-query-pool"></a>쿼리 풀에서 처리 구분

처리 및 쿼리 작업 모두에서 최대 성능을 내기 위해서는 쿼리 풀에서 처리 서버를 분리하도록 선택할 수 있습니다. 분리 되 면 새 클라이언트 연결은 쿼리 풀의 쿼리 복제본에만 할당 됩니다. 처리 작업에만 약간의 시간이 걸리는 경우 처리 및 동기화 작업을 수행하는 데 걸리는 약간의 시간 동안 쿼리 풀에서 처리 서버를 분리한 다음, 쿼리 풀에 다시 포함시키도록 선택할 수 있습니다. 쿼리 풀에서 처리 서버를 분리 하거나 쿼리 풀에 다시 추가 하는 경우 작업이 완료 될 때까지 최대 5 분이 걸릴 수 있습니다.

## <a name="monitor-qpu-usage"></a>QPU 사용량 모니터링

서버에 대 한 확장이 필요한 지 확인 하려면 메트릭을 사용 하 여 Azure Portal에서 [서버를 모니터링](analysis-services-monitor.md) 합니다. QPU가 정기적으로 최대로 출력되는 지점까지 증가하는 경우 모델에 대한 쿼리 수가 계획의 QPU 한도를 초과하고 있음을 의미합니다. 쿼리 풀 작업 큐 길이 메트릭은 쿼리 스레드 풀 큐의 쿼리 수가 사용 가능한 QPU를 초과하면 증가합니다. 

관찰할 또 다른 좋은 메트릭은 ServerResourceType의 평균 QPU입니다. 이 메트릭은 주 서버에 대 한 평균 QPU를 쿼리 풀과 비교 합니다. 

![규모 확장 메트릭 쿼리](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**ServerResourceType에서 QPU을 구성 하려면**

1. 메트릭 꺾은선형 차트에서 **메트릭 추가**를 클릭 합니다. 
2. **리소스**에서 서버를 선택 하 고 **메트릭 네임 스페이스**에서 **Analysis Services 표준 메트릭**을 선택한 다음 **메트릭**에서 **QPU**를 선택 하 고 **집계**에서 **Avg**를 선택 합니다. 
3. **분할 적용**을 클릭 합니다. 
4. **값**에서 **serverresourcetype**를 선택 합니다.  

### <a name="detailed-diagnostic-logging"></a>자세한 진단 로깅

확장 된 서버 리소스의 자세한 진단을 보려면 Azure Monitor 로그를 사용 합니다. 로그를 사용 하면 Log Analytics 쿼리를 사용 하 여 서버 및 복제본 별로 QPU 및 메모리를 중단할 수 있습니다. 자세히 알아보려면 [Analysis Services 진단 로깅](analysis-services-logging.md#example-queries)의 예제 쿼리를 참조 하세요.


## <a name="configure-scale-out"></a>규모 확장 구성

### <a name="in-azure-portal"></a>Azure Portal에서

1. 포털에서 **확장**을 클릭 합니다. 슬라이더를 사용 하 여 쿼리 복제본 서버 수를 선택 합니다. 선택한 복제본 수는 기존 서버에 추가됩니다.  

2. 쿼리 서버에서 처리 서버를 제외하려면 **쿼리 풀에서 처리 서버 구분**에서 [예]를 선택합니다. 기본 연결 문자열 (제외)을 사용 하는 클라이언트 [연결은](#connections) `:rw` 쿼리 풀의 복제본으로 리디렉션됩니다. 

   ![규모 확장 슬라이더](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 새 쿼리 복제본 서버를 프로비전하려면 **저장**을 클릭합니다. 

서버에 대 한 수평 확장을 처음 구성 하는 경우 주 서버의 모델이 쿼리 풀의 복제본과 자동으로 동기화 됩니다. 자동 동기화는 하나 이상의 복제본에 대 한 확장을 처음 구성할 때 한 번만 수행 됩니다. 이후에 동일한 서버에 있는 복제본 수를 변경 *해도 다른 자동 동기화는 트리거되지 않습니다*. 서버를 0 개의 복제본으로 설정 하 고 복제본을 원하는 수 만큼 확장 한 후에도 자동 동기화는 다시 발생 하지 않습니다. 

## <a name="synchronize"></a>동기화 

동기화 작업은 수동으로 수행 하거나 REST API를 사용 하 여 수행 해야 합니다.

### <a name="in-azure-portal"></a>Azure Portal에서

**개요** > 모델 > **모델 동기화**에서.

![규모 확장 슬라이더](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

**동기화** 작업을 사용합니다.

#### <a name="synchronize-a-model"></a>모델 동기화   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>동기화 상태 가져오기  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

반환 상태 코드:


|코드  |Description  |
|---------|---------|
|-1     |  올바르지 않음       |
|0     | Replicating        |
|1     |  리하이드레이션       |
|2     |   완료됨       |
|3     |   실패      |
|4     |    중     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 사용 하기 전에 [최신 Azure PowerShell 모듈을 설치 하거나 업데이트](/powershell/azure/install-az-ps)합니다. 

동기화를 실행 하려면 [AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)를 사용 합니다.

쿼리 복제본 수를 설정 하려면 [AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)를 사용 합니다. 선택적 `-ReadonlyReplicaCount` 매개 변수를 지정합니다.

쿼리 풀에서 처리 서버를 분리 하려면 [AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)을 사용 합니다. `-DefaultConnectionMode`사용할 선택적 매개 변수를 지정 `Readonly` 합니다.

자세한 내용은 [Az. microsoft.analysisservices.sharepoint.integration.dll 모듈을 사용 하 여 서비스 주체 사용](analysis-services-service-principal.md#azmodule)을 참조 하세요.

## <a name="connections"></a>Connections

서버의 [개요] 페이지에는 두 개의 서버 이름이 있습니다. 서버에 대한 규모 확장을 아직 구성하지 않은 경우 두 서버 이름은 동일하게 작동합니다. 서버에 대한 규모 확장을 구성한 후에는 연결 형식에 따라 적절한 서버 이름을 지정해야 합니다. 

Power BI Desktop, Excel 및 사용자 지정 앱과 같은 최종 사용자 클라이언트 연결의 경우 **서버 이름**을 사용 합니다. 

SSMS, Visual Studio 및 PowerShell, Azure 함수 앱 및 AMO의 연결 문자열에는 **관리 서버 이름을**사용 합니다. 관리 서버 이름에는 특별한 `:rw`(읽기/쓰기) 한정자가 포함됩니다. 모든 처리 작업은 (기본) 관리 서버에서 발생 합니다.

![서버 이름](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>수직 확장, 규모 축소 및 규모 확장

여러 복제본이 있는 서버의 가격 책정 계층을 변경할 수 있습니다. 모든 복제본에 동일한 가격 책정 계층이 적용 됩니다. 크기 조정 작업을 수행 하면 모든 복제본이 항상 한 번에 표시 된 다음 새 가격 책정 계층의 모든 복제본을 가져옵니다.

## <a name="troubleshoot"></a>문제 해결

**문제:** 사용자가 ** \<Name of the server> 연결 모드 ' ReadOnly '에서 서버 ' ' 인스턴스를 찾을 수 없습니다.**

**해결 방법:** **풀 쿼리 옵션에서 처리 서버를 분리** 하는 경우 기본 연결 문자열 (없음)을 사용 하는 클라이언트 연결이 `:rw` 쿼리 풀 복제본으로 리디렉션됩니다. 동기화가 완료되지 않았기 때문에 쿼리 풀의 복제본이 아직 온라인 상태가 아니면 리디렉션된 클라이언트 연결이 실패할 수 있습니다. 연결 실패를 방지하려면 동기화를 수행할 때 쿼리 풀에 두 개 이상의 서버가 있어야 합니다. 다른 서버가 온라인 상태로 유지되는 동안 각 서버는 개별적으로 동기화됩니다. 처리 중에 쿼리 풀에 처리 서버가 없도록 선택한 경우 처리를 위한 풀에서 처리 서버를 제거한 다음, 처리가 완료된 후 동기화되기 전, 다시 풀에 추가하도록 선택할 수 있습니다. 메모리 및 QPU 메트릭을 사용하여 동기화 상태를 모니터링할 수 있습니다.



## <a name="related-information"></a>관련 정보

[서버 메트릭 모니터링](analysis-services-monitor.md)   
[Azure Analysis Services 관리](analysis-services-manage.md) 
