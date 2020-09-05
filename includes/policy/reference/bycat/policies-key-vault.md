---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 70146cdedccb35c061a47157275f1272d7c11d1b
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298671"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault의 진단 설정을 Event Hub에 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fed7c8c13-51e7-49d1-8a43-8490431a0da2) |이 진단 설정이 누락된 Key Vault를 만들거나 업데이트할 때 Key Vault의 진단 설정을 배포하여 지역별 Event Hub로 스트림합니다. |deployIfNotExists |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |
|[Key Vault의 진단 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Key Vault 개체를 복구할 수 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |이 정책은 Key Vault 개체를 복구할 수 없는 경우 감사합니다. Soft Delete 기능은 DELETE 작업 후에도 지정된 보존 기간(90일) 동안 리소스를 효율적으로 유지하는 동시에 개체가 삭제된 것처럼 보이게 합니다. '제거 보호'가 켜져 있으면 보존 기간인 90일이 지날 때까지 삭제된 상태의 자격 증명 모음이나 개체를 제거할 수 없습니다. 이러한 자격 증명 모음 및 개체는 계속 복구할 수 있으며, 고객에게 보존 정책을 준수하도록 합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|[허용되는 인증서 키 유형 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1151cede-290b-4ba0-8b38-0ad145ac888f) |이 정책은 인증서에 허용되는 키 유형을 관리합니다. |감사, 거부, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_AllowedKeyTypes.json) |
|[타원 곡선 암호화 인증서에 허용되는 커브 이름 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd78111f-4953-4367-9fd5-7e08808b54bf) |이 정책은 타원 곡선 암호화 인증서에 허용되는 커브 이름을 관리합니다. |감사, 거부, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_EC_AllowedCurveNames.json) |
|[인증서 수명 작업 트리거 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12ef42cb-9903-4e39-9c26-422d29570417) |이 정책은 인증서 만료 전에 인증서 수명 작업 트리거에 대한 구성을 관리합니다. |감사, 거부, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_LifetimeAction.json) |
|[인증서 유효 기간 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |이 정책은 인증서의 최대 유효 기간을 월 단위로 관리합니다. |감사, 거부, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[비통합 CA에서 발급한 인증서 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa22f4a40-01d3-4c7d-8071-da157eeff341) |이 정책은 지정된 비통합 인증 기관에서 발급한 인증서를 관리합니다. |감사, 거부, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_CustomCAs.json) |
|[통합 CA에서 발급한 인증서 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e826246-c976-48f6-b03e-619bb92b3d82) |이 정책은 지정된 Key Vault 통합 인증 기관에서 발급한 인증서를 관리합니다. |감사, 거부, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_SupportedCAs.json) |
|[지정된 만료 기간(일) 내에 있는 인증서 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff772fb64-8e40-40ad-87bc-7706e1949427) |이 정책은 만료 날짜까지 지정된 기간(일) 내에 있는 인증서를 관리합니다. |감사, 거부, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Expiry_ByDays.json) |
|[RSA 인증서의 최소 키 크기 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee51871-e572-4576-855c-047c820360f0) |이 정책은 RSA 인증서의 최소 키 크기를 관리합니다. |감사, 거부, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_RSA_MinimumKeySize.json) |
