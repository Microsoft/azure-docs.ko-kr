---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 345cc8d5c1bc362973c8fae0e1c5605faf6e6e2d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298561"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |이 정책은 모든 가상 머신에 Azure Backup 서비스를 사용할 수 있는지 감사하는 데 도움이 됩니다. Azure Backup은 비용 효과적인 원클릭 백업 솔루션으로 데이터 복구를 간소화하며, 다른 클라우드 백업 서비스보다 사용하도록 설정하기가 쉽습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[특정 위치에 있는 VM을 동일한 위치에 있는 기존 중앙 자격 증명 모음으로 백업하는 작업 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |이 정책은 지정된 위치에 있는 VM에 대한 Azure Backup 보호를 동일한 위치에 있는 기존 중앙 자격 증명 모음으로 구성합니다. 이 정책은 아직 백업이 구성되지 않은 VM에만 적용됩니다. 이 정책은 200개 이하의 VM에 할당하는 것이 좋습니다. 이 정책이 200개가 넘는 VM에 할당될 경우 정의된 스케줄보다 몇 시간 뒤에 백업이 트리거될 수 있습니다. 이 정책은 더 많은 VM 이미지를 지원할 수 있도록 향상될 예정입니다. |deployIfNotExists, auditIfNotExists, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
