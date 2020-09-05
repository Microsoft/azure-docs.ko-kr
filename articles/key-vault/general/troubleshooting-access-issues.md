---
title: Azure 키 자격 증명 모음 액세스 정책 문제 해결
description: Azure 키 자격 증명 모음 액세스 정책 문제 해결
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 270e5ba1879b229fbe9f5e6c8692bd8b4e9eebc7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688643"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Azure 키 자격 증명 모음 액세스 정책 문제 해결

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>키 자격 증명 모음을 어떻게 언제 액세스하는지 어떻게 식별할 수 있나요?
하나 이상의 키 자격 증명 모음을 만든 후에는 키 자격 증명 모음에 액세스하는 방법, 시기 및 사용자를 모니터링하려고 할 수도 있습니다. Azure Key Vault에 대한 로깅을 사용하도록 설정하여 이 작업을 수행할 수 있습니다. 로깅을 사용하도록 설정하는 단계별 가이드는 [자세히 알아보세요](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>키 자격 증명 모음에 대한 자격 증명 모음 가용성, 서비스 대기 시간 기간 또는 기타 성능 메트릭을 어떻게 모니터링할 수 있나요?
서비스의 크기를 조정하기 시작하면 키 자격 증명 모음으로 전송되는 요청 수가 증가합니다. 이로 인해 요청 대기 시간이 길어질 수 있으며 극단적인 경우에는 서비스의 성능에 영향을 줄 수 있는 요청이 정체됩니다. 키 자격 증명 모음 성능 메트릭을 모니터링하고 특정 임계값에 대한 경고를 받을 수 있습니다. 모니터링을 구성하는 단계별 가이드는 [자세히 알아보세요](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>키 자격 증명 모음 개체별로 액세스 제어를 할당하려면 어떻게 해야 하나요? 
비밀/키/인증서 액세스 제어 기능별 가용성은 여기, [자세히 알아보기](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)에서 확인할 수 있습니다.

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>액세스 제어 정책을 사용하여 키 자격 증명 모음 인증을 제공하려면 어떻게 해야 하나요?
클라우드 기반 애플리케이션을 Key Vault에 인증하는 가장 간단한 방법은 관리 ID를 사용하는 것입니다. 자세한 내용은 [App Service 관리 ID를 사용하여 Azure Key Vault에 액세스]( https://docs.microsoft.com/azure/key-vault/general/managed-identity)를 참조하세요.
온-프레미스 애플리케이션을 만들거나 로컬 개발을 수행하거나 관리 ID를 사용할 수 없는 경우, 대신 서비스 주체를 수동으로 등록하고 액세스 제어 정책을 사용하여 키 자격 증명 모음에 액세스할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>AD 그룹에 키 자격 증명 모음에 대한 액세스 권한을 부여하려면 어떻게 해야 하나요?
Azure CLI az keyvault set-policy 명령 또는 Azure PowerShell Set-AzKeyVaultAccessPolicy cmdlet을 사용하여 키 자격 증명 모음에 대한 액세스 권한을 AD 그룹에 부여합니다. 예제는 [키 자격 증명 모음에 대한 액세스 권한을 애플리케이션, Azure AD 그룹 또는 사용자에 부여](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault)를 검토합니다.

또한 애플리케이션에는 Key Vault에 할당된 ID 및 액세스 관리(IAM) 역할이 하나 이상 필요합니다. 그렇지 않으면 로그인 할 수 없고 구독에 액세스할 수 있는 권한이 없어 실패합니다. 관리 ID가 있는 Azure AD 그룹은 토큰을 새로 고치고 적용하는 데 최대 8시간이 필요할 수 있습니다.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>기존 액세스 정책을 삭제하지 않고 ARM 템플릿을 사용하여 Key Vault를 재배포하려면 어떻게 해야 하나요?
현재 Key Vault ARM 재배포는 Key Vault에서 액세스 정책을 삭제하고 ARM 템플릿의 액세스 정책으로 바꿉니다. Key Vault 액세스 정책에 대한 증분 옵션은 없습니다. Key Vault에서 액세스 정책을 유지하려면 Key Vault에서 기존 액세스 정책을 읽고 이러한 정책을 사용하여 ARM 템플릿을 채워 액세스 중단을 방지해야 합니다.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>다음 오류 유형에 대한 권장되는 문제 해결 단계
* HTTP 401: 인증 되지 않은 요청 - [문제 해결 단계](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403: 권한 부족 - [문제 해결 단계](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429: 요청이 너무 많음 - [문제 해결 단계](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* 키 자격 증명 모음에 대한 삭제 액세스 권한이 있는지 확인합니다. [Key Vault 액세스 정책](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* 코드에서 Key Vault를 인증하는 데 문제가 있는 경우 [Authentication SDK](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)를 사용합니다.

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>키 자격 증명 모음이 제한될 때 구현해야 하는 최선의 방법은 무엇인가요?
[여기](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits)서 설명하는 모범 사례를 따르세요.

## <a name="next-steps"></a>다음 단계

키 자격 증명 모음 인증 오류 문제를 해결하는 방법을 알아봅니다. [Key Vault 문제 해결 가이드](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
