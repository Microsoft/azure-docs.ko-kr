---
title: Azure Batch 서비스 작업
titleSuffix: Azure Machine Learning Studio
description: Machine Learning Studio 작업에 대 한 Azure Batch 서비스의 개요입니다. Batch 풀 처리를 사용하면 배치 작업을 제출할 수 있는 풀을 만들 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: 24efa3caba3918a38c09b1c921c600b117dedbc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751169"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-jobs"></a>Azure Machine Learning Studio 작업에 대한 Azure Batch 서비스

Machine Learning Batch 풀 처리는 Azure Machine Learning Batch 실행 서비스의 고객 관리 규모를 제공합니다. Machine Learning에 대한 클래식 Batch 처리는 제출할 수 있는 동시 작업 수를 제한하는 다중 테넌트 환경에서 수행되며, 작업은 선입 선출 기준으로 큐에 보관됩니다. 이 불확실성은 작업이 실행되는 시기를 예측할 수 없다는 것입니다.

Batch 풀 처리를 사용하면 배치 작업을 제출할 수 있는 풀을 만들 수 있습니다. 풀의 크기와 작업이 제출되는 풀을 제어합니다. BES 작업은 예측 가능한 처리 성능과 제출한 처리 부하에 해당하는 리소스 풀을 만드는 기능을 제공하는 자체 처리 공간에서 실행됩니다.

> [!NOTE]
> 풀을 만들려면 새 Resource Manager 기반 Machine Learning 웹 서비스가 있어야 합니다. 생성되면 풀에서 BES 웹 서비스(새 Resource Manager 기반 및 클래식 모두)를 실행할 수 있습니다.

## <a name="how-to-use-batch-pool-processing"></a>Batch 풀 처리를 사용하는 방법

Batch 풀 처리의 구성은 현재 Azure Portal을 통해 사용할 수 없습니다. Batch 풀 처리를 사용하려면:

-   Batch 풀 계정을 만들고 풀 서비스 URL 및 권한 부여 키를 가져오기 위해 CSS 호출
-   새 Resource Manager 기반 웹 서비스 및 청구 계획 만들기

계정을 만들려면 Microsoft 고객 서비스 및 지원 센터(CSS)에 전화하여 구독 ID를 제공합니다. CSS에서는 사용자와 협의하여 시나리오에 적합한 용량을 결정합니다. 그런 다음 CSS에서 만들 수 있는 최대 풀 수와 각 풀에 배치할 수 있는 VM(가상 머신)의 최대 수를 사용하여 계정을 구성합니다. 계정이 구성되면 풀 서비스 URL과 인증 키가 제공됩니다.

계정을 만든 후에 풀 서비스 URL과 인증 키를 사용하여 Batch 풀에 대한 풀 관리 작업을 수행합니다.

![Batch 풀 서비스 아키텍처](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

CSS에서 제공한 풀 서비스 URL에서 풀 만들기 작업을 호출하여 풀을 만듭니다. 풀을 만들 때 새 Resource Manager 기반의 Machine Learning 웹 서비스의 swagger.json에 VM 수와 URL을 지정합니다. 이 웹 서비스는 청구 연결을 설정하는 데 제공됩니다. Batch 풀 서비스는 swagger.json을 사용하여 풀과 청구 계획을 연결합니다. 풀에서 BES 웹 서비스(새 Resource Manager 기반 및 클래식 모두)를 실행할 수 있습니다.

새 Resource Manager 기반 웹 서비스를 사용할 수 있지만, 작업에 대한 요금 청구가 해당 서비스와 연결된 청구 계획과 대조하여 부과된다는 점에 유의하세요. 특히 Batch 풀 작업을 실행하기 위한 웹 서비스와 새로운 청구 계획을 만들 수 있습니다.

웹 서비스 만들기에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.

풀을 생성하면 웹 서비스의 Batch 요청 URL을 사용하여 BES 작업을 제출합니다. 풀 또는 클래식 일괄 처리에 제출하도록 선택할 수 있습니다. Batch 풀 처리에 작업을 제출하려면 작업 제출 요청 본문에 다음 매개 변수를 추가합니다.

"AzureBatchPoolId":"&lt;pool ID&gt;"

매개 변수를 추가하지 않으면 작업이 클래식 일괄 처리 환경에서 실행됩니다. 풀에 사용 가능한 리소스가 있는 경우 작업이 즉시 실행됩니다. 풀에 사용 가능한 리소스가 없는 경우 작업은 리소스를 사용할 수 있을 때까지 대기합니다.

정기적으로 풀 용량에 도달하고 용량을 늘려야 하는 경우 CSS에 전화하고 담당자와 협력하여 할당량을 늘릴 수 있습니다.

요청 예제:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Batch 풀 처리 사용 시 고려 사항

Batch 풀 처리는 항상 청구 가능한 서비스이며, 이를 Resource Manager 기반 청구 계획과 연결해야 합니다. 시간 풀에서 실행되는 작업 수에 관계없이 해당 풀이 실행되는 컴퓨팅 시간에 대해서만 청구됩니다. 풀을 만드는 경우 풀에서 실행되는 일괄 처리 작업이 없더라도 해당 풀을 삭제할 때까지 풀에 있는 각 가상 머신의 컴퓨팅 시간에 대해 요금이 청구됩니다. 가상 머신에 대한 청구는 프로비전을 완료하면 시작되고, 해당 프로비전을 삭제하면 중지됩니다. [Machine Learning 가격](https://azure.microsoft.com/pricing/details/machine-learning/) 페이지에 있는 계획 중 하나를 사용할 수 있습니다.

청구 예제:

2개의 가상 머신으로 Batch 풀을 만들고 24시간 후에 삭제하는 경우 해당 기간 동안 실행된 작업 수에 관계없이 48 컴퓨팅 시간에 대한 청구 계획이 계상됩니다.

4개의 가상 머신으로 Batch 풀을 만들고 12시간 후에 삭제하는 경우에도 48 컴퓨팅 시간에 대한 청구 계획이 계상됩니다.

작업 상태를 폴링하여 작업 완료 시기를 결정하는 것이 좋습니다. 모든 작업의 실행을 완료하면 풀 크기 조정 작업을 호출하여 풀의 가상 머신 수를 0으로 설정합니다. 예를 들어 풀 리소스가 부족하고 다른 청구 계획에 대해 청구하도록 새 풀을 만들어야 하는 경우 모든 작업의 실행을 완료하는 대신 해당 풀을 삭제할 수 있습니다.


| **Batch 풀 처리를 사용하는 경우**    | **클래식 일괄 처리를 사용하는 경우**  |
|---|---|
|많은 수의 작업을 실행해야 합니다.<br>또는<br/>자신의 작업이 즉시 실행된다는 것을 알아야 합니다.<br/>또는<br/>처리량을 보장해야 합니다. 예를 들어 지정된 시간 프레임 내에서 여러 작업을 실행해야 하며, 요구 사항에 맞게 컴퓨팅 리소스를 확장하려고 합니다.    | 몇 가지 작업만 실행하고 있습니다.<br/>AND<br/> 작업을 즉시 실행할 필요가 없습니다. |
