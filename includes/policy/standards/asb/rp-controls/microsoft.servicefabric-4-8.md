---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 70bc92f5f760e227cfe3906f9896888d97902325
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112041164"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Service Fabric 클러스터는 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric은 기본 클러스터 인증서를 사용하여 노드 간 통신을 위한 3단계 보호(None, Sign 및 EncryptAndSign)를 제공합니다. 모든 노드 간 메시지가 암호화되고 디지털로 서명될 수 있게 보호 수준을 설정합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
