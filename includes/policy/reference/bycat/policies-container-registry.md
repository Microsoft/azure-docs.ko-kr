---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8920752a6adfe5f3259c4bf701173d2d817aa7da
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291020"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[컨테이너 레지스트리는 CMK(고객 관리형 키)로 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |CMK(고객 관리형 키)로 암호화가 설정되지 않은 감사 컨테이너 레지스트리입니다. CMK 암호화에 대한 자세한 내용은 [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[컨테이너 레지스트리는 무제한 네트워크 액세스를 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |네트워크(IP 또는 VNET) 규칙이 구성되지 않은 감사 컨테이너 레지스트리는 기본적으로 모든 네트워크 액세스를 허용합니다. 하나 이상의 IP/방화벽 규칙 또는 구성된 가상 네트워크가 있는 컨테이너 레지스트리는 규정을 준수하는 것으로 간주됩니다. Container Registry 네트워크 규칙에 대한 자세한 내용은 [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)을 참조하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[컨테이너 레지스트리는 프라이빗 링크를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |승인된 프라이빗 엔드포인트 연결이 하나 이상 없는 컨테이너 레지스트리를 감사합니다. 가상 네트워크의 클라이언트는 프라이빗 링크를 통해 프라이빗 엔드포인트 연결이 있는 리소스에 안전하게 액세스할 수 있습니다. 자세한 내용은 [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
