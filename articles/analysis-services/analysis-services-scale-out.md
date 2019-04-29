---
title: Azure Analysis Services 규모 확장 | Microsoft Docs
description: 규모 확장으로 Azure Analysis Services 서버 복제
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8c226608f6c1c776463aa05c02b1d3cc04b699ec
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766819"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 규모 확장

규모 확장을 사용 하 여 클라이언트 쿼리가 여러 배포 수 *쿼리 복제본* 에 *풀 쿼리*, 높은 쿼리 워크 로드 동안 응답 시간을 줄여 합니다. 또한 클라이언트 쿼리가 작업 처리에 의해 부정적인 영향을 받지 않도록 쿼리 풀에서 처리가 구분될 수도 있습니다. Azure Portal 또는 Analysis Services REST API를 사용하여 규모 확장을 구성할 수 있습니다.

규모 확장은 표준 가격 책정 계층의 서버에서 사용할 수 있습니다. 각 쿼리 복제본은 서버와 동일한 요금으로 청구됩니다. 모든 쿼리 복제본은 서버와 동일한 지역에 만들어집니다. 사용자가 구성할 수 있는 쿼리 복제본 수는 서버가 있는 지역에 따라 제한됩니다. 자세히 알아보려면 [지역별 가용성](analysis-services-overview.md#availability-by-region)을 참조하세요. 규모 확장은 서버의 사용 가능한 메모리 양을 증가시키지 않습니다. 메모리를 늘리려면 계획을 업그레이드해야 합니다. 

## <a name="why-scale-out"></a>이유는 스케일 아웃?

일반적인 서버 배포에서는 한 서버가 처리 서버 및 쿼리 서버 역할을 모두 수행합니다. 서버 모델에 대한 클라이언트 쿼리 수가 서버 계획에 대한 QPU(쿼리 처리 단위)를 초과하거나 높은 쿼리 워크로드와 동시에 모델 처리가 발생하면 성능이 저하될 수 있습니다. 

규모 확장을 사용 하면 최대 7 개의 추가 쿼리 복제본 리소스를 사용 하 여 쿼리 풀을 만들 수 있습니다 (8 합계를 포함 하 여 *기본* 서버). 중요 한 시간에 QPU 요구 사항에 맞게 쿼리 풀에 있는 복제본의 수를 조정할 수 있습니다 하 고 언제 든 지 쿼리 풀에서 처리 서버를 분리할 수 있습니다. 

쿼리 풀에 있는 쿼리 복제본의 수와 관계없이 처리 워크로드는 쿼리 복제본 간에 분산되지 않습니다. 주 서버가 처리 서버 역할도합니다. 쿼리 복제본에 주 서버와 쿼리 풀의 각 복제본 간에 동기화 모델 데이터베이스에 대 한 쿼리만 제공 합니다. 

를 확장 하는 경우 증분 방식으로 쿼리 풀에 추가할 새 쿼리 복제본에 대 한 최대 5 분 정도 걸릴 수 있습니다. 경우 모든 새 쿼리 복제본 구성 되며 실행 되는 새 클라이언트 연결 쿼리 풀의 리소스 간에 부하가 분산 됩니다. 기존 클라이언트 연결은 현재 연결되어 있는 리소스에서 변경되지 않습니다. 규모 감축 시 조정할 때 쿼리 풀에서 제거되는 쿼리 풀 리소스에 대한 기존 클라이언트 연결이 종료됩니다. 클라이언트는 나머지 쿼리 리소스 풀에 다시 연결할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

주 서버의 model 데이터베이스는 스케일 아웃 처음을 구성할 때 *자동으로* 새 쿼리 풀에서 새 복제본과 동기화 합니다. 자동 동기화가 한 번만 발생 합니다. 자동 동기화 하는 동안 기본 서버 데이터 파일 (blob storage에서 휴지 상태로 암호화)는 blob 저장소에서 미사용 암호화는 두 번째 위치에 복사 됩니다. 쿼리 풀에서 복제본 있다면 *하이드레이션* 파일의 두 번째 집합에서 데이터를 사용 하 여 합니다. 

하면 스케일 아웃 서버를 처음으로 경우에 자동 동기화를 수행 하는 동안에 수동 동기화를 수행할 수 있습니다. 동기화는 주 서버의 쿼리 풀의 복제본에서 데이터 일치를 보장 합니다. 동기화를 수행 해야 합니다 (새로 고침) 주 서버에는 모델을 처리할 때 *후* 처리 작업이 완료 됩니다. 이 동기화는 파일의 두 번째 집합에 blob storage의 주 서버의 파일에서 업데이트 된 데이터를 복사합니다. 쿼리 풀의 복제본은 blob 저장소에서 파일의 두 번째 집합에서 업데이트 된 데이터를 사용 하 여 다음 하이드레이션 됩니다. 

후속 스케일 아웃 작업을 수행할 때 예를 들어 2 ~ 5 쿼리 풀에 있는 복제본의 수를 늘리면 새 복제본은 하이드레이션 blob 저장소에서 파일의 두 번째 집합에서 데이터를 사용 하 여 합니다. 동기화가 없습니다. 다음을 수행 하려는 경우 규모를 쿼리 풀에서 새 복제본 후 동기화를 두 번 중복 하이드레이션을 하이드레이션 합니다. 후속 스케일 아웃 작업을 수행할 때 반드시 염두에서에 둡니다.

* 동기화를 수행 *스케일 아웃 작업 전에* 추가 복제본의 중복 하이드레이션 하지 않아도 됩니다.

* 모두 처리를 자동화 하는 경우 *고* 스케일 아웃 작업을 반드시 먼저 주 서버에서 데이터를 처리 하 고 동기화를 수행한 다음 스케일 아웃 작업을 수행 합니다. 이 시퀀스 보장 QPU 및 메모리 리소스에 미치는 영향을 최소화 합니다.

* 쿼리 풀의 복제본이 없는 경우에 동기화가 허용 됩니다. 주 서버에서 처리 작업에서 새 데이터를 사용 하 여 하나 이상의 복제본에 0에서 확장 하는 경우 쿼리 풀에서 복제본이 없는 동기화를 먼저 수행 하 고 스케일 아웃 합니다. 스케일 아웃 하기 전에 동기화 새로 추가 된 복제본의 중복 하이드레이션 피할 수 있습니다.

* 주 서버에서 모델 데이터베이스를 삭제 하는 경우이 자동으로 삭제 되지 쿼리 풀의 복제본에서. 사용 하 여 동기화 작업을 수행 해야 합니다 [동기화 AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) 복제본의 공유 blob 저장소 위치에서 해당 데이터베이스에 대 한 파일/s를 제거 하 고 다음 모델을 삭제 하는 PowerShell 명령 쿼리 풀에서 복제본의 데이터베이스입니다.

* 주 서버에서 데이터베이스의 이름을 바꾸면 데이터베이스 복제본을 동기화 제대로 확인 하는 데 필요한 추가 단계가 있습니다. 이름 바꾸기 후 동기화를 사용 하 여 수행 합니다 [동기화 AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) 명령을 지정 하는 `-Database` 이전 데이터베이스 이름 가진 매개 변수입니다. 이 동기화 모든 복제본에서 데이터베이스 및 이전 이름 가진 파일을 제거합니다. 그런 다음 다른 동기화 지정을 수행 합니다 `-Database` 매개 변수를 새 데이터베이스 이름입니다. 두 번째 동기화 파일의 두 번째 집합에 새로 명명된 된 데이터베이스를 복사 하 고 모든 복제본을 하이드레이션 합니다. 포털에서 동기화 모델 명령을 사용 하 여 이러한 동기화를 수행할 수 없습니다.

### <a name="separate-processing-from-query-pool"></a>쿼리 풀에서 처리 구분

처리 및 쿼리 작업 모두에서 최대 성능을 내기 위해서는 쿼리 풀에서 처리 서버를 분리하도록 선택할 수 있습니다. 분리되면 기존 및 새 클라이언트 연결이 쿼리 풀에서만 쿼리 복제본에 할당됩니다. 처리 작업에만 약간의 시간이 걸리는 경우 처리 및 동기화 작업을 수행하는 데 걸리는 약간의 시간 동안 쿼리 풀에서 처리 서버를 분리한 다음, 쿼리 풀에 다시 포함시키도록 선택할 수 있습니다. 

## <a name="monitor-qpu-usage"></a>QPU 사용량 모니터링

서버의 규모 확장이 필요한지 확인하려면 메트릭을 사용하여 Azure Portal에서 서버를 모니터링하십시오. QPU가 정기적으로 최대로 출력되는 지점까지 증가하는 경우 모델에 대한 쿼리 수가 계획의 QPU 한도를 초과하고 있음을 의미합니다. 쿼리 풀 작업 큐 길이 메트릭은 쿼리 스레드 풀 큐의 쿼리 수가 사용 가능한 QPU를 초과하면 증가합니다. 

보기를 다른 좋은 메트릭은 ServerResourceType에서 평균 QPU를입니다. 이 메트릭은 쿼리 풀의를 사용 하 여 주 서버에 대 한 평균 QPU를 비교합니다. 

### <a name="to-configure-qpu-by-serverresourcetype"></a>QPU ServerResourceType 여를 구성 하려면
1. 메트릭 꺾은선형 차트를 클릭 **메트릭을 추가**합니다. 
2. **리소스**, 선택한 서버 시작 후에 **메트릭 네임 스페이스**를 선택 **Analysis Services 표준 메트릭에**, 그런 다음 **메트릭**, 선택 **QPU**를 선택한 다음 **집계**를 선택 **Avg**합니다. 
3. 클릭 **분할 적용**합니다. 
4. **값**를 선택 **ServerResourceType**합니다.  

자세한 내용은 [서버 메트릭 모니터링](analysis-services-monitor.md)을 참조하세요.

## <a name="configure-scale-out"></a>규모 확장 구성

### <a name="in-azure-portal"></a>Azure Portal에서

1. Azure Portal에서 **규모 확장**을 클릭합니다. 슬라이더를 사용하여 쿼리 복제본 서버 수를 선택합니다. 선택한 복제본 수는 기존 서버에 추가됩니다.

2. 쿼리 서버에서 처리 서버를 제외하려면 **쿼리 풀에서 처리 서버 구분**에서 [예]를 선택합니다. 클라이언트 [연결](#connections) 기본 연결 문자열을 사용 하 여 (없이 `:rw`) 쿼리 풀에서 복제본으로 리디렉션됩니다. 

   ![규모 확장 슬라이더](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 새 쿼리 복제본 서버를 프로비전하려면 **저장**을 클릭합니다. 

스케일 아웃 서버에 대 한 첫 번째 시간을 구성할 때 주 서버에 대 한 모델은 쿼리 풀에서 복제본을 사용 하 여 자동으로 동기화 됩니다. 자동 동기화는 먼저 스케일 아웃 하나 이상의 복제본을 구성할 때 한 번만 발생 합니다. 동일한 서버에 복제본의 수에 대 한 후속 변경 *는 다른 자동 동기화를 트리거하지 않습니다*합니다. 서버를 복제본 0 및 임의 개수의 복제본에 규모 확장 한 다음 다시 설정 하는 경우에 자동 동기화가 다시 발생 하지 않습니다. 

## <a name="synchronize"></a>동기화 

수동으로 또는 REST API를 사용 하 여 동기화 작업을 수행 되어야 합니다.

### <a name="in-azure-portal"></a>Azure Portal에서

**개요** > 모델 > **모델 동기화**에서.

![규모 확장 슬라이더](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

**동기화** 작업을 사용합니다.

#### <a name="synchronize-a-model"></a>모델 동기화   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>동기화 상태 가져오기  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

상태 코드를 반환 합니다.


|코드  |설명  |
|---------|---------|
|-1     |  올바르지 않음       |
|0     | 복제 중        |
|1     |  리하이드레이션       |
|2     |   Completed       |
|3     |   실패      |
|4     |    종료하는 중     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 사용 하기 전에 [설치 하거나 최신 Azure PowerShell 모듈 업데이트](/powershell/azure/install-az-ps)합니다. 

사용 하 여 동기화를 실행 하려면 [동기화 AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)합니다.

쿼리 복제본 수를 설정 하려면 [집합 AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)합니다. 선택적 `-ReadonlyReplicaCount` 매개 변수를 지정합니다.

쿼리 풀에서 처리 서버 구분을 사용 하 여 [집합 AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)합니다. 선택적으로 지정할 `-DefaultConnectionMode` 매개 변수를 사용 하 여 `Readonly`입니다.

자세한 내용은 참조 하세요 [Az.AnalysisServices 모듈을 사용 하 여 서비스 주체를 사용 하 여](analysis-services-service-principal.md#azmodule)입니다.

## <a name="connections"></a>연결

서버의 [개요] 페이지에는 두 개의 서버 이름이 있습니다. 서버에 대한 규모 확장을 아직 구성하지 않은 경우 두 서버 이름은 동일하게 작동합니다. 서버에 대한 규모 확장을 구성한 후에는 연결 형식에 따라 적절한 서버 이름을 지정해야 합니다. 

Power BI Desktop, Excel 및 사용자 지정 앱과 같은 최종 사용자 클라이언트 연결의 경우 **서버 이름**을 사용합니다. 

SSMS, SSDT, PowerShell의 연결 문자열, Azure Function 앱 및 AMO의 경우 **관리 서버 이름**을 사용합니다. 관리 서버 이름에는 특별한 `:rw`(읽기/쓰기) 한정자가 포함됩니다. (기본) 관리 서버에서 모든 처리 작업을 수행 합니다.

![서버 이름](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>문제 해결

**문제:** 사용자는 **연결 모드 'ReadOnly'에서 '\<서버 이름>' 서버 인스턴스를 찾을 수 없습니다.** 라는 오류를 받게 됩니다.

**해결 방법:** 선택 하는 경우는 **쿼리 풀에서 처리 서버 분리** 옵션을 기본 연결 문자열을 사용 하 여 클라이언트 연결 (없이 `:rw`) 쿼리 풀 복제본으로 리디렉션됩니다. 동기화가 완료되지 않았기 때문에 쿼리 풀의 복제본이 아직 온라인 상태가 아니면 리디렉션된 클라이언트 연결이 실패할 수 있습니다. 연결 실패를 방지하려면 동기화를 수행할 때 쿼리 풀에 두 개 이상의 서버가 있어야 합니다. 다른 서버가 온라인 상태로 유지되는 동안 각 서버는 개별적으로 동기화됩니다. 처리 중에 쿼리 풀에 처리 서버가 없도록 선택한 경우 처리를 위한 풀에서 처리 서버를 제거한 다음, 처리가 완료된 후 동기화되기 전, 다시 풀에 추가하도록 선택할 수 있습니다. 메모리 및 QPU 메트릭을 사용하여 동기화 상태를 모니터링할 수 있습니다.

## <a name="related-information"></a>관련 정보

[서버 메트릭 모니터링](analysis-services-monitor.md)   
[Azure Analysis Services 관리](analysis-services-manage.md) 
