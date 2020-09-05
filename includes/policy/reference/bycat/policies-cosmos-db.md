---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 43162aaf0293024d4e89e2caacfce44959375943
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298652"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |규정 또는 규정 준수 요구 사항인 경우 고객 관리형 키를 사용하여 Azure Cosmos DB에 저장된 데이터의 미사용 암호화를 제어할 수 있습니다. 또한 고객 관리형 키는 서비스 관리형 키를 사용하여 수행되는 기본 암호화 계층 위에 두 번째 암호화 계층을 추가하여 이중 암호화를 제공합니다. [https://aka.ms/cosmosdb-cmk](https://aka.ms/cosmosdb-cmk) 참조 |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |
|[Azure Cosmos DB 계정에 방화벽 규칙이 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |IP 규칙이 구성되지 않은 리소스를 감사 또는 거부하고 기본적으로 모든 네트워크를 허용합니다. 가상 네트워크 필터를 사용하도록 정의된 IP 규칙이 하나 이상 있는 계정은 규정을 준수하는 것으로 간주됩니다. 퍼블릭 액세스를 비활성화한 계정도 규정을 준수하는 것으로 간주됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Cosmos DB 허용되는 위치](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |이 정책을 사용하면 Azure Cosmos DB 조직에서 리소스를 배포할 때 지정할 수 있는 위치를 제한할 수 있습니다. 지역 규정 준수 요구 사항을 적용하는 데 사용합니다. |[parameters('policyEffect')] |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[Azure Cosmos DB 키 기반 메타데이터 쓰기 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4750c32b-89c0-46af-bfcb-2e4541a818d5) |이 정책을 통해 모든 Azure Cosmos DB 계정에서 키 기반 메타데이터 쓰기 액세스를 사용하지 않도록 설정할 수 있습니다. |추가 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_DisableMetadata_Append.json) |
|[Azure Cosmos DB 처리량을 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b7ef78e-a035-4f23-b9bd-aff122a1b1cf) |이 정책을 사용하면 리소스 공급자를 통해 Azure Cosmos DB 데이터베이스 및 컨테이너를 만들 때 조직에서 지정할 수 있는 최대 처리량을 제한할 수 있습니다. 자동 크기 조정 리소스 생성을 차단합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_MaxThroughput_Deny.json) |
|[Cosmos DB 계정에 대한 Advanced Threat Protection 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |이 정책은 Cosmos DB 계정에서 Advanced Threat Protection을 사용하도록 설정합니다. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |
