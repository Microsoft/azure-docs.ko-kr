---
title: Azure Resource Manager 리소스 공급자 작업 | Microsoft Docs
description: Microsoft Azure Resource Manager 리소스 공급자에서 사용할 수 있는 작업을 나열합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f19649e9033eabef8b7151f90af706bb6cb54321
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264902"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager 리소스 공급자 작업

이 문서에는 각 Azure Resource Manager 리소스 공급자에서 사용할 수 있는 작업 목록이 나열되어 있습니다. [사용자 지정 역할](custom-roles.md)에서 이러한 작업을 사용하여 Azure의 리소스에 대해 세분화된 [RBAC(역할 기반 액세스 제어)](overview.md)를 제공할 수 있습니다. 작업 문자열의 형식은 `{Company}.{ProviderName}/{resourceType}/{action}`입니다. 리소스 공급자 네임 스페이스를 Azure 서비스에 매핑하는 방법에 대 한 목록은 [리소스 공급자와 서비스의 일치](../azure-resource-manager/management/azure-services-resource-providers.md)를 참조 하세요.

리소스 공급자 작업은 계속 업데이트됩니다. 최신 작업을 가져오려면 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 또는 [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list)를 사용합니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.AAD/domainServices/delete | 도메인 서비스를 삭제합니다. |
> | 실행력 | Microsoft.AAD/domainServices/oucontainer/delete | Ou 컨테이너 삭제 |
> | 실행력 | Microsoft.AAD/domainServices/oucontainer/read | Ou 컨테이너 읽기 |
> | 실행력 | Microsoft.AAD/domainServices/oucontainer/write | Ou 컨테이너 쓰기 |
> | 실행력 | Microsoft.AAD/domainServices/read | 도메인 서비스를 읽습니다. |
> | 실행력 | Microsoft.AAD/domainServices/write | 도메인 서비스를 작성합니다. |
> | 실행력 | Microsoft.AAD/locations/operationresults/read |  |
> | 실행력 | Microsoft.AAD/Operations/read |  |
> | 실행력 | Microsoft.AAD/register/action | 도메인 서비스를 등록합니다. |
> | 실행력 | Microsoft.AAD/unregister/action | 도메인 서비스를 등록 취소합니다. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | microsoft.aadiam/diagnosticsettings/delete | 진단 설정을 삭제합니다. |
> | 실행력 | microsoft.aadiam/diagnosticsettings/read | 진단 설정을 읽습니다. |
> | 실행력 | microsoft.aadiam/diagnosticsettings/write | 진단 설정을 작성합니다. |
> | 실행력 | microsoft.aadiam/diagnosticsettingscategories/read | 진단 설정 범주를 읽습니다. |
> | 실행력 | aadiam/metricDefinitions/읽기 | 테넌트 수준 메트릭 정의 읽기 |
> | 실행력 | aadiam/메트릭/읽기 | 테넌트 수준 메트릭 읽기 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Addons/operations/read | 지원되는 RP 작업을 가져옵니다. |
> | 실행력 | Microsoft.Addons/register/action | 지정된 구독을 Microsoft.Addons에 등록합니다. |
> | 실행력 | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 지정한 구독에 대한 현재 지원 플랜 정보를 나열합니다. |
> | 실행력 | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 지정된 Canonical 지원 플랜을 제거합니다. |
> | 실행력 | Microsoft.Addons/supportProviders/supportPlanTypes/read | 지정된 Canonical 지원 플랜 상태를 가져옵니다. |
> | 실행력 | Microsoft.Addons/supportProviders/supportPlanTypes/write | 지정된 Canonical 지원 플랜 유형을 추가합니다. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/action | 테넌트에 대한 새 포리스트를 만듭니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 지정된 서비스 이름에 대한 모든 서버를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/alerts/read | 경고 ID, 경고 발생 날짜, 경고 마지막으로 검색된 경고, 경고 설명, 마지막 업데이트, 경고 수준, 경고 상태, 경고 문제 해결 링크 등과 같은 포리스트에 대한 경고 정보를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/configuration/read | 포리스트에 대한 서비스 구성을 가져옵니다. 예: 포리스트 이름, 기능 수준, 도메인 명명 마스터 FSMO 역할, 스키마 마스터 FSMO 역할 등 |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/delete | 서비스 및 해당 서버(상태 데이터 포함)를 삭제합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/dimensions/read | 포리스트에 대한 도메인 및 사이트 정보를 가져옵니다. 예: 상태, 활성 경고, 해결된 경고, 속성(도메인 기능 수준, 포리스트, 인프라 마스터, PDC, RID 마스터 등)  |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | 포리스트에 대한 사용자 기본 설정을 가져옵니다.<br>예: MetricCounterName(ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches)<br>UI 차트 등에 대한 설정입니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | 지정된 포리스트에 대한 포리스트 요약을 가져옵니다(포리스트 이름, 이 포리스트에 속한 도메인 수, 사이트 수 및 사이트 정보 등). |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 지정된 서비스에 지원되는 메트릭 목록을 가져옵니다.<br>예: ADFS 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomainService의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>ADSync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | 서비스가 지정되면 이 API는 메트릭 정보를 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | 이 API는 프리미엄 테넌트에 등록된 모든 ADDomainServices의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/read | 지정된 서비스 이름에 대한 서비스 정보를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 지정된 서비스 이름의 모든 서버에 대한 복제 정보를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | 도메인 컨트롤러 수 및 해당 복제 오류가 있는 경우 이 오류를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 지정된 포리스트에 대한 복제 정보와 함께 전체 도메인 컨트롤러 목록을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | 서버 인스턴스를 서비스에 추가합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | ADDomainService의 서버를 등록하는 동안 이 API가 호출되어 새 서버를 등록하기 위한 자격 증명을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 지정된 서비스와 테넌트에 대한 서버를 삭제합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/addsservices/write | 테넌트에 대한 ADDomainService 인스턴스를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/configuration/action | 테넌트 구성을 업데이트합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/configuration/read | 테넌트 구성을 읽습니다. |
> | 실행력 | Microsoft.ADHybridHealthService/configuration/write | 테넌트 구성을 만듭니다. |
> | 실행력 | Microsoft.ADHybridHealthService/logs/contents/read | Blob에 저장된 에이전트 설치 및 등록 로그의 내용을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/logs/read | 테넌트에 대한 에이전트 설치 및 등록 로그를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/operations/read | 시스템에서 지원하는 작업 목록을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/register/action | ADHybrid Health Service 리소스 공급자를 등록하고 ADHybrid Health Service 리소스를 만들 수 있도록 합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/reports/availabledeployments/read | DevOps에서 고객 인시던트를 지원하는 데 사용할 수 있는 지역 목록을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/reports/badpassword/read | Active Directory Federation Service의 모든 사용자에 대한 잘못된 암호 시도 목록을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 지정된 테넌트에 대한 일별 IPAddress당 UserId당 잘못된 사용자 이름/암호 시도 빈도와 관련하여 큐에 새로 넣어진 보고 작업의 상태 및 최종 결과가 포함된 Blob SAS URI를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | DevOps에서 동의한 테넌트 목록을 가져옵니다. 일반적으로 고객 지원에 사용됩니다. |
> | 실행력 | Microsoft.ADHybridHealthService/reports/isdevops/read | DevOps에서 동의한 테넌트인지 여부를 나타내는 값을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 선택한 DevOps 테넌트에 대한 userid(objectid)를 업데이트합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 지정된 테넌트에 대해 선택한 배포를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | 테넌트 ID가 지정되면 테넌트 스토리지 위치를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | 액세스할 데이터가 있는 지리적 위치를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/action | 테넌트의 서비스 인스턴스를 업데이트합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/alerts/read | 서비스에 대한 경고를 읽습니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/alerts/read | 서비스에 대한 경고를 읽습니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 기능 이름이 지정되면 해당 기능을 사용하는 데 필요한 모든 항목이 서비스에 있는지 확인합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/delete | 테넌트의 서비스 인스턴스를 삭제합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/exporterrors/read | 지정된 동기화 서비스에 대한 내보내기 오류를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/exportstatus/read | 지정된 서비스에 대한 내보내기 상태를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 지정된 서비스 및 서버에 대한 경고 피드백을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/metricmetadata/read | 지정된 서비스에 지원되는 메트릭 목록을 가져옵니다.<br>예: ADFS 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomainService의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>ADSync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> | 실행력 | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | 서비스가 지정되면 이 API는 지정된 서비스의 메트릭에 대한 평균을 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> | 실행력 | Microsoft.ADHybridHealthService/services/metrics/groups/read | 서비스가 지정되면 이 API는 메트릭 정보를 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> | 실행력 | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | 서비스가 지정되면 이 API는 지정된 서비스의 메트릭에 대한 집계 보기를 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> | 실행력 | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | 서비스에 대한 모니터링 구성을 추가하거나 업데이트합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 지정된 서비스에 대한 모니터링 구성을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | 서비스에 대한 모니터링 구성을 추가하거나 업데이트합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/premiumcheck/read | 이 API는 프리미엄 테넌트에 등록된 모든 서비스의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/read | 테넌트의 서비스 인스턴스를 읽습니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/reports/blobUris/read | 지난 7 일 동안 위험한 모든 IP 보고서 Uri를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/reports/details/read | 지난 7일간 잘못된 암호 오류가 있는 상위 50명의 사용자에 대한 보고서를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | 위험한 IP 보고서를 생성 하 고이를 가리키는 URI를 반환 합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/action | 서비스에 서버 인스턴스를 만듭니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | 서버에 대한 경고를 읽습니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | 서버를 등록하는 동안 이 API가 호출되어 새 서버를 등록하기 위한 자격 증명을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | 서버가 지정되면 이 API는 서버에서 업로드하는 데이터 형식 목록과 각 업로드에 대한 최신 시간을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/delete | 서비스의 서버 인스턴스를 삭제합니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 지정된 동기화 서비스에 대한 동기화 내보내기 오류 정보를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | 서비스가 지정되면 이 API는 메트릭 정보를 가져옵니다.<br>예를 들어, 이 API는 다음과 관련된 정보를 가져오는 데 사용할 수 있습니다. ADFederation 서비스의 경우 엑스트라넷 계정 잠금 횟수, 총 실패한 요청 수, 처리 중인 토큰 요청 수(프록시), 토큰 요청 수/초 등.<br>ADDomain 서비스의 경우 NTLM 인증 횟수/초, 성공한 LDAP 바인드 횟수/초, LDAP 바인드 시간, LDAP 활성 스레드 수, Kerberos 인증 횟수/초, 총 ATQ 스레드 수 등.<br>Sync 서비스의 경우 실행 프로필 대기 시간, 설정된 TCP 연결 수, Insights 에이전트 프라이빗 바이트 수, Azure AD로 내보내기 통계. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | 지정 된 서비스 및 서비스 멤버의 커넥터 및 실행 프로필 이름 목록을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/read | 서비스의 서버 인스턴스를 읽습니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 지정된 테넌트에 대한 서비스 구성을 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 지정된 테넌트에 대한 기능 허용 목록 상태를 가져옵니다. |
> | 실행력 | Microsoft.ADHybridHealthService/services/write | 테넌트의 서비스 인스턴스를 만듭니다. |
> | 실행력 | Microsoft.ADHybridHealthService/unregister/action | ADHybrid Health Service 리소스 공급자에 대한 구독을 등록 취소합니다. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Advisor/configurations/read | 구성 가져오기 |
> | 실행력 | Microsoft.Advisor/configurations/write | 구성을 만듭니다/업데이트합니다. |
> | 실행력 | Microsoft.Advisor/generateRecommendations/action | 권장 사항 생성 상태를 가져옵니다. |
> | 실행력 | Microsoft.Advisor/generateRecommendations/read | 권장 사항 생성 상태를 가져옵니다. |
> | 실행력 | Microsoft.Advisor/metadata/read | 메타데이터 가져오기 |
> | 실행력 | Microsoft.Advisor/operations/read | Microsoft Advisor에 대한 작업을 가져옵니다. |
> | 실행력 | Microsoft.Advisor/recommendations/available/action | Microsoft Advisor에서 새로운 권장 사항을 사용할 수 있습니다. |
> | 실행력 | Microsoft.Advisor/recommendations/read | 권장 사항을 읽습니다. |
> | 실행력 | Microsoft.Advisor/recommendations/suppressions/delete | 비표시 오류(Suppression)를 삭제합니다. |
> | 실행력 | Microsoft.Advisor/recommendations/suppressions/read | 비표시 오류(Suppression)를 가져옵니다. |
> | 실행력 | Microsoft.Advisor/recommendations/suppressions/write | 비표시 오류(Suppression)를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Advisor/register/action | Microsoft Advisor에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.Advisor/suppressions/delete | 비표시 오류(Suppression)를 삭제합니다. |
> | 실행력 | Microsoft.Advisor/suppressions/read | 비표시 오류(Suppression)를 가져옵니다. |
> | 실행력 | Microsoft.Advisor/suppressions/write | 비표시 오류(Suppression)를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Advisor/unregister/action | Microsoft Advisor에 대한 구독을 등록 취소합니다. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.AlertsManagement/actionRules/delete | 지정된 구독에서 작업 규칙을 삭제합니다. |
> | 실행력 | Microsoft.AlertsManagement/actionRules/read | 입력 필터에 대한 모든 작업 규칙을 가져옵니다. |
> | 실행력 | Microsoft.AlertsManagement/actionRules/write | 지정된 구독에서 작업 규칙을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.AlertsManagement/alerts/changestate/action | 경고 상태를 변경합니다. |
> | 실행력 | Microsoft.AlertsManagement/alerts/diagnostics/read | 경고에 대한 모든 진단을 가져옵니다. |
> | 실행력 | Microsoft.AlertsManagement/alerts/history/read | 경고 기록을 가져옵니다. |
> | 실행력 | Microsoft.AlertsManagement/alerts/read | 입력 필터에 대한 모든 경고를 가져옵니다. |
> | 실행력 | Microsoft.AlertsManagement/alertsList/read | 전체 구독에서 입력 필터에 대한 모든 경고를 가져옵니다. |
> | 실행력 | AlertsManagement/alertsMetaData/읽기 | 입력 매개 변수에 대 한 경고 메타 데이터를 가져옵니다. |
> | 실행력 | Microsoft.AlertsManagement/alertsSummary/read | 경고의 요약 정보를 가져옵니다. |
> | 실행력 | Microsoft.AlertsManagement/alertsSummaryList/read | 구독 전체의 경고에 대한 요약을 가져옵니다. |
> | 실행력 | Microsoft.AlertsManagement/Operations/read | 제공된 작업을 읽습니다. |
> | 실행력 | Microsoft.AlertsManagement/register/action | Microsoft 경고 관리에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | 지정 된 구독의 스마트 탐지기 경고 규칙 삭제 |
> | 실행력 | Microsoft.AlertsManagement/smartDetectorAlertRules/read | 입력 필터에 대 한 모든 스마트 탐지기 경고 규칙을 가져옵니다. |
> | 실행력 | Microsoft.AlertsManagement/smartDetectorAlertRules/write | 지정 된 구독에서 스마트 탐지기 경고 규칙을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.AlertsManagement/smartGroups/changestate/action | 스마트 그룹의 상태를 변경합니다. |
> | 실행력 | Microsoft.AlertsManagement/smartGroups/history/read | 스마트 그룹 기록을 가져옵니다. |
> | 실행력 | Microsoft.AlertsManagement/smartGroups/read | 입력 필터에 대한 모든 스마트 그룹을 가져옵니다. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 지정된 Analysis Server 이름이 올바른지와 사용되고 있지 않은지 확인합니다. |
> | 실행력 | Microsoft.AnalysisServices/locations/operationresults/read | 지정된 작업 결과에 대한 정보를 검색합니다. |
> | 실행력 | Microsoft.AnalysisServices/locations/operationstatuses/read | 지정된 작업 상태에 대한 정보를 검색합니다. |
> | 실행력 | Microsoft.AnalysisServices/operations/read | 작업 정보를 검색합니다. |
> | 실행력 | Microsoft.AnalysisServices/register/action | Analysis Services 리소스 공급자를 등록합니다. |
> | 실행력 | Microsoft.AnalysisServices/servers/delete | Analysis Server를 삭제합니다. |
> | 실행력 | Microsoft.AnalysisServices/servers/listGatewayStatus/action | 서버와 연결된 게이트웨이 상태를 나열합니다. |
> | 실행력 | Microsoft.AnalysisServices/servers/read | 지정된 Analysis Server의 정보를 검색합니다. |
> | 실행력 | Microsoft.AnalysisServices/servers/resume/action | Analysis Server를 계속합니다. |
> | 실행력 | Microsoft.AnalysisServices/servers/skus/read | 서버에 사용 가능한 SKU 정보를 검색합니다. |
> | 실행력 | Microsoft.AnalysisServices/servers/suspend/action | Analysis Server를 일시 중단합니다. |
> | 실행력 | Microsoft.AnalysisServices/servers/write | 지정된 Analysis Server를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.AnalysisServices/skus/read | SKU 정보를 검색합니다. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ApiManagement/checkNameAvailability/read | 제공된 서비스 이름을 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.ApiManagement/operations/read | Microsoft.ApiManagement 리소스에 제공되는 모든 API 작업을 읽습니다. |
> | 실행력 | Microsoft.ApiManagement/register/action | Microsoft.ApiManagement 리소스 공급자에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.ApiManagement/reports/read | 기간, 지역, 개발자, 제품, API, 작업, 구독 및 byRequest별로 집계된 보고서를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/delete | API Management 서비스 인스턴스의 지정 된 API를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/diagnostics/delete | API에서 지정 된 진단을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/diagnostics/read | API의 모든 진단을 나열 합니다. 또는 해당 식별자로 지정 된 API에 대 한 진단 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/diagnostics/write | API에 대 한 새 진단을 만들거나 기존 API를 업데이트 합니다. 또는는 해당 식별자로 지정 된 API에 대 한 진단 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 문제에서 지정 된 설명을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/issues/attachments/read | 지정 된 API와 관련 된 문제에 대 한 모든 첨부 파일을 나열 합니다. 또는 해당 식별자로 지정 된 API에 대 한 문제 첨부 파일의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/issues/attachments/write | API의 문제에 대 한 새 첨부 파일을 만들거나 기존 문제를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/issues/comments/delete | 문제에서 지정 된 설명을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/issues/comments/read | 지정 된 API와 관련 된 문제에 대 한 모든 설명을 나열 합니다. 또는 해당 식별자로 지정 된 API에 대 한 문제 설명의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/issues/comments/write | API의 문제에 대 한 새 주석을 만들거나 기존 주석을 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/issues/delete | API에서 지정 된 문제를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/issues/read | 지정 된 API와 관련 된 모든 문제를 나열 합니다. 또는 해당 식별자로 지정 된 API의 문제에 대 한 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/issues/write | API에 대 한 새 문제를 만들거나 기존 문제를 업데이트 합니다. 또는는 API에 대 한 기존 문제를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/delete | API에서 지정 된 작업을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/policies/delete | Api 작업에서 정책 구성을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/policies/read | API 작업 수준에서 정책 구성 목록을 가져옵니다. 또는 API 작업 수준에서 정책 구성을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/policies/write | API 작업 수준에 대 한 정책 구성을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/policy/delete | 작업 수준에서 정책 구성을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/policy/read | 작업 수준에서 정책 구성 가져오기 |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/policy/write | 작업 수준에서 정책 구성 만들기 |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/read | 지정 된 API에 대 한 작업의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 API 작업에 대 한 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/tags/delete | 작업에서 태그를 분리 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/tags/read | 작업과 연결 된 모든 태그를 나열 합니다. 또는 작업과 연결 된 태그를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/tags/write | 작업에 태그를 할당 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operations/write | API에서 새 작업을 만들거나 기존 작업을 업데이트 합니다. 또는는 해당 식별자로 지정 된 API의 작업 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/operationsByTags/read | 태그와 연결 된 작업의 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/policies/delete | Api에서 정책 구성을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/policies/read | API 수준에서 정책 구성을 가져옵니다. 또는 API 수준에서 정책 구성을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/policies/write | API에 대 한 정책 구성을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/policy/delete | API 수준에서 정책 구성을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/policy/read | API 수준에서 정책 구성 가져오기 |
> | 실행력 | Microsoft.ApiManagement/service/apis/policy/write | API 수준에서 정책 구성 만들기 |
> | 실행력 | Microsoft.ApiManagement/service/apis/products/read | API의 일부인 모든 제품을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/read | API Management 서비스 인스턴스의 모든 Api를 나열 합니다. 또는 해당 식별자로 지정 된 API의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/releases/delete | Api의 모든 릴리스를 제거 하거나 API에서 지정 된 릴리스를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/releases/read | API의 모든 릴리스를 나열 합니다.<br>Api 릴리스는 현재 API 수정을 만들 때 생성 됩니다.<br>릴리스는 이전 수정 버전으로 롤백하는 데도 사용 됩니다.<br>결과는 페이징할 수 있으며 $top 및 $skip 매개 변수에 의해 제한 될 수 있습니다.<br>또는 API 릴리스의 세부 정보를 반환 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/releases/write | API에 대 한 새 릴리스를 만듭니다. 또는는 해당 식별자로 지정 된 API 릴리스의 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/revisions/delete | API의 모든 수정 버전을 제거합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/revisions/read | API의 모든 수정 버전을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/schemas/delete | Api에서 스키마 구성을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/schemas/read | API 수준에서 스키마 구성을 가져옵니다. 또는 API 수준에서 스키마 구성을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/schemas/write | API에 대 한 스키마 구성을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Api에 대 한 태그 설명을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/tagDescriptions/read | API의 범위에서 모든 태그 설명을 나열 합니다. Swagger-tagDescription와 유사한 모델은 API 수준에서 정의 되지만 태그는 작업에 할당 되거나 API 범위에서 태그 설명을 가져올 수 있습니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Api의 범위에서 태그 설명을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/tags/delete | Api에서 태그를 분리 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/tags/read | API와 연결 된 모든 태그를 나열 합니다. 또는 API와 연결 된 태그를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/tags/write | 태그를 Api에 할당 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apis/write | API Management 서비스 인스턴스의 지정 된 기존 API를 새로 만들거나 업데이트 합니다. 또는 API Management 서비스 인스턴스의 지정 된 API를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apisByTags/read | 태그와 연결 된 api 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apiVersionSets/delete | 특정 Api 버전 집합을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/apiVersionSets/read | 지정 된 서비스 인스턴스의 API 버전 집합 컬렉션을 나열 합니다. 또는 식별자로 지정 된 Api 버전 집합의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apiVersionSets/versions/read | 버전 엔터티 목록을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/apiVersionSets/write | Api 버전 집합을 만들거나 업데이트 합니다. 또는는 해당 식별자로 지정 된 Api VersionSet의 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Virtual Network에서 실행되는 Microsoft.ApiManagement 리소스를 업데이트하여 업데이트된 네트워크 설정을 선택합니다. |
> | 실행력 | Microsoft.ApiManagement/service/authorizationServers/delete | 특정 권한 부여 서버 인스턴스를 삭제 합니다. |
> | 실행력 | Microsoft.apimanagement/service/authorizationServers/listSecrets/작업 | 권한 부여 서버에 대 한 암호를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/authorizationServers/read | 서비스 인스턴스 내에 정의 된 권한 부여 서버 컬렉션을 나열 합니다. 또는 암호 없이 권한 부여 서버의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/authorizationServers/write | 새 권한 부여 서버를 만들거나 기존 권한 부여 서버를 업데이트 합니다. 또는는 해당 식별자로 지정 된 권한 부여 서버의 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/backends/delete | 지정 된 백 엔드를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/backends/read | 지정 된 서비스 인스턴스의 백 엔드 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 백 엔드의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/backends/reconnect/action | 지정 된 시간 제한 후에 백 엔드에 대 한 새 연결을 만들도록 APIM 프록시에 알립니다. 제한 시간을 지정 하지 않으면 2 분의 제한 시간이 사용 됩니다. |
> | 실행력 | Microsoft.ApiManagement/service/backends/write | 백 엔드를 만들거나 업데이트 합니다. 또는 기존 백 엔드를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/backup/action | 사용자가 제공한 스토리지 계정의 지정된 컨테이너로 API Management 서비스를 백업합니다. |
> | 실행력 | Microsoft.ApiManagement/service/caches/delete | 특정 캐시를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/caches/read | 지정 된 서비스 인스턴스에 있는 모든 외부 캐시의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 캐시의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/caches/write | Api Management 인스턴스에 사용할 외부 캐시를 만들거나 업데이트 합니다. 또는는 해당 식별자로 지정 된 캐시의 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/certificates/delete | 특정 인증서를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/certificates/read | 지정 된 서비스 인스턴스에 있는 모든 인증서의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 인증서의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/certificates/write | 백 엔드와의 인증에 사용 되는 인증서를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | 지정된 콘텐츠 항목을 제거합니다. |
> | 실행력 | Microsoft.ApiManagement/service/contentTypes/contentItems/read | 콘텐츠 항목 목록을 반환하거나 콘텐츠 항목 세부 정보를 반환합니다. |
> | 실행력 | Microsoft.ApiManagement/service/contentTypes/contentItems/write | 새 콘텐츠 항목을 만들거나 지정된 콘텐츠 항목을 업데이트합니다. |
> | 실행력 | Microsoft.ApiManagement/service/contentTypes/read | 콘텐츠 형식을 반환합니다. |
> | 실행력 | Microsoft.ApiManagement/service/delete | API Management 서비스 인스턴스를 삭제합니다. |
> | 실행력 | Microsoft.ApiManagement/service/diagnostics/delete | 지정 된 진단을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/diagnostics/read | API Management 서비스 인스턴스에 대 한 모든 진단을 나열 합니다. 또는 해당 식별자로 지정 된 진단 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/diagnostics/write | 새 진단을 만들거나 기존 진단을 업데이트 합니다. 또는는 해당 식별자로 지정 된 진단 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/작업 | 게이트웨이 구성을 검색 합니다. 또는 게이트웨이 하트 비트를 업데이트 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/a p i/삭제 | 지정 된 게이트웨이에서 지정 된 API를 삭제 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/a p i/읽기 | 게이트웨이와 연결 된 Api의 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/a p i/쓰기 | 지정 된 게이트웨이에 API를 추가 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/삭제 | 특정 게이트웨이를 삭제 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/hostnameConfigurations/읽기 | 지정 된 게이트웨이의 호스트 이름 구성 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/키/작업 | 게이트웨이 키를 검색 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/읽기 | 서비스 인스턴스에 등록 된 게이트웨이의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 게이트웨이의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/regeneratePrimaryKey/작업 | 기본 게이트웨이 키를 생성 하 여 만든 모든 토큰을 invalidationg 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/regenerateSecondaryKey/작업 | Invalidationg 보조 게이트웨이 키를 사용 하 여 만든 모든 토큰을 다시 생성 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/토큰/작업 | 게이트웨이에 대 한 공유 액세스 권한 부여 토큰을 가져옵니다. |
> | 실행력 | Microsoft.apimanagement/서비스/게이트웨이/쓰기 | Api Management 인스턴스에서 사용할 게이트웨이를 만들거나 업데이트 합니다. 또는는 해당 식별자로 지정 된 게이트웨이의 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/getssotoken/action | API Management 서비스 레거시 포털에 관리자로 로그인하는 데 사용할 수 있는 SSO 토큰을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/groups/delete | API Management 서비스 인스턴스의 특정 그룹을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/groups/read | 서비스 인스턴스 내에 정의 된 그룹의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 그룹의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/groups/users/delete | 기존 그룹에서 기존 사용자를 제거 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/groups/users/read | 그룹에 연결 된 사용자 엔터티의 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/groups/users/write | 기존 그룹에 기존 사용자를 추가합니다. |
> | 실행력 | Microsoft.ApiManagement/service/groups/write | 그룹을 만들거나 업데이트 합니다. 또는 해당 식별자로 지정 된 그룹의 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/identityProviders/delete | 지정 된 id 공급자 구성을 삭제 합니다. |
> | 실행력 | Microsoft.apimanagement/service/identityProviders/listSecrets/작업 | Id 공급자 암호를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/identityProviders/read | 지정 된 서비스 인스턴스에 구성 된 Id 공급자의 컬렉션을 나열 합니다. 또는 암호 없이 id 공급자의 구성 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/identityProviders/write | IdentityProvider 구성을 만들거나 업데이트 합니다. 또는 기존 IdentityProvider 구성을 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/issues/read | 지정 된 서비스 인스턴스의 문제 컬렉션을 나열 합니다. 또는 API Management 문제 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/locations/networkstatus/read | 해당 위치에는 서비스가 종속되는 리소스의 네트워크 액세스 상태를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/loggers/delete | 지정 된로 거를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/loggers/read | 지정 된 서비스 인스턴스에 대 한로 거 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된로 거에 대 한 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/loggers/write | 로 거를 만들거나 업데이트 합니다. 또는는 기존로 거를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/managedeployments/action | SKU/단위를 변경하고 API Management 서비스의 지역별 배포를 추가 또는 제거합니다. |
> | 실행력 | Microsoft.apimanagement/service/namedValues/delete | API Management 서비스 인스턴스에서 명명 된 특정 값을 삭제 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/namedValues/listSecrets/작업 | 해당 식별자로 지정 된 명명 된 값의 암호를 가져옵니다. |
> | 실행력 | Microsoft.apimanagement/service/namedValues/read | 서비스 인스턴스 내에 정의 된 명명 된 값의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 명명 된 값의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.apimanagement/서비스/namedValues/write | 명명 된 값을 만들거나 업데이트 합니다. 또는 특정 명명 된 값을 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/networkstatus/read | 서비스가 종속되는 리소스의 네트워크 액세스 상태를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/notifications/action | 지정된 사용자에게 알림을 보냅니다. |
> | 실행력 | Microsoft.ApiManagement/service/notifications/read | 서비스 인스턴스 내에 정의 된 속성의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 알림의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 알림 목록에서 전자 메일을 제거 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/notifications/recipientEmails/read | 알림을 구독 하는 알림 받는 사람 전자 메일의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 알림에 대 한 받는 사람 목록에 전자 메일 주소를 추가 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 알림 목록에서 API Management 사용자를 제거 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 알림을 구독 하는 알림 받는 사람 목록을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/notifications/recipientUsers/write | 알림에 대 한 받는 사람 목록에 API Management 사용자를 추가 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/notifications/write | API Management 게시자 알림을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/openidConnectProviders/delete | API Management 서비스 인스턴스의 특정 Openid connect Connect 공급자를 삭제 합니다. |
> | 실행력 | Microsoft.apimanagement/service/openidConnectProviders/listSecrets/작업 | 특정 Openid connect Connect 공급자 암호를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/openidConnectProviders/read | 모든 Openid connect Connect 공급자 목록입니다. 또는는 비밀 없이 특정 Openid connect 연결 공급자를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/openidConnectProviders/write | Openid connect Connect 공급자를 만들거나 업데이트 합니다. 또는 특정 Openid connect Connect 공급자를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/operationresults/read | 장기 실행 작업의 현재 상태를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/policies/delete | Api Management 서비스의 전역 정책 구성을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/policies/read | Api Management 서비스의 모든 전역 정책 정의를 나열 합니다. 또는 Api Management 서비스의 전역 정책 정의를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/policies/write | Api Management 서비스의 전역 정책 구성을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/policy/delete | 테넌트 수준에서 정책 구성을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/policy/read | 테넌트 수준에서 정책 구성 가져오기 |
> | 실행력 | Microsoft.ApiManagement/service/policy/write | 테넌트 수준에서 정책 구성 만들기 |
> | 실행력 | Microsoft.apimanagement/서비스/정책 설명/읽기 | 모든 정책 설명을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/policySnippets/read | 모든 정책 코드 조각을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/portalsettings/read | 포털 설정의 컬렉션을 나열 합니다. 또는 포털에 대 한 로그인 설정을 가져오거나 포털에 대 한 등록 설정을 가져오거나 포털에 대 한 위임 설정을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/portalsettings/write | 로그인 설정을 업데이트 합니다. 또는 로그인 설정을 만들거나 업데이트 합니다. 또는 등록 설정을 업데이트 하거나 등록 설정을 업데이트 하거나 위임 설정을 업데이트 합니다. 또는 위임 설정을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/apis/delete | 지정 된 제품에서 지정 된 API를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/apis/read | 제품과 연결 된 Api 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/apis/write | 지정 된 제품에 API를 추가 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/delete | 제품을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/groups/delete | 지정 된 그룹과 제품 간의 연결을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/groups/read | 지정 된 제품과 연결 된 개발자 그룹의 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/groups/write | 지정 된 제품을 사용 하 여 지정 된 개발자 그룹 간의 연결을 추가 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/policies/delete | 제품에서 정책 구성을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/policies/read | 제품 수준에서 정책 구성을 가져옵니다. 또는 제품 수준에서 정책 구성을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/policies/write | 제품에 대 한 정책 구성을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/policy/delete | 제품 수준에서 정책 구성을 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/policy/read | 제품 수준에서 정책 구성 가져오기 |
> | 실행력 | Microsoft.ApiManagement/service/products/policy/write | 제품 수준에서 정책 구성 만들기 |
> | 실행력 | Microsoft.ApiManagement/service/products/read | 지정 된 서비스 인스턴스의 제품 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 제품의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/subscriptions/read | 지정 된 제품에 대 한 구독 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/tags/delete | 제품에서 태그를 분리 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/tags/read | 제품과 연결 된 모든 태그를 나열 합니다. 또는 제품과 연결 된 태그를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/tags/write | 제품에 태그를 할당 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/products/write | 제품을 만들거나 업데이트 합니다. 또는 기존 제품 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/productsByTags/read | 태그와 연결 된 제품의 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/properties/delete | API Management 서비스 인스턴스에서 특정 속성을 삭제 합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/속성/listSecrets/작업 | 해당 식별자로 지정 된 속성의 암호를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/properties/read | 서비스 인스턴스 내에 정의 된 속성의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 속성의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/properties/write | 속성을 만들거나 업데이트 합니다. 또는 특정 속성을 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/quotas/periods/read | 기간에 대한 할당량 카운터 값을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/quotas/periods/write | 할당량 카운터 현재 값을 설정합니다. |
> | 실행력 | Microsoft.ApiManagement/service/quotas/read | 할당량에 대한 값을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/quotas/write | 할당량 카운터 현재 값을 설정합니다. |
> | 실행력 | Microsoft.ApiManagement/service/read | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
> | 실행력 | Microsoft.ApiManagement/service/regions/read | 서비스가 존재 하는 모든 azure 지역을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/reports/read | 기간별로 집계된 보고서를 가져오거나 지역별로 집계된 보고서를 가져오거나 개발자별로 집계된 보고서를 가져옵니다.<br>또는 제품별로 집계된 보고서를 가져옵니다.<br>또는 API별로 집계된 보고서를 가져오거나 작업별로 집계된 보고서를 가져오거나 구독별로 집계된 보고서를 가져옵니다.<br>또는 데이터 보고 요청을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/restore/action | 사용자가 제공한 스토리지 계정의 지정된 컨테이너에서 API Management 서비스 복원 |
> | 실행력 | Microsoft.ApiManagement/service/subscriptions/delete | 지정 된 구독을 삭제 합니다. |
> | 실행력 | Microsoft.apimanagement/service/subscription/listSecrets/작업 | 지정 된 구독 키를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/subscriptions/read | API Management 서비스 인스턴스에 대 한 모든 구독을 나열 합니다. 또는 지정 된 구독 엔터티를 가져옵니다 (키 포함 안 함). |
> | 실행력 | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | API Management 서비스 인스턴스에 대 한 기존 구독의 기본 키를 다시 생성 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | API Management 서비스 인스턴스에 대 한 기존 구독의 보조 키를 다시 생성 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/subscriptions/write | 지정한 제품에 대해 지정한 사용자의 구독을 만들거나 업데이트 합니다. 또는는 해당 식별자로 지정 된 구독의 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/tagResources/read | 태그와 연결 된 리소스의 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/tags/delete | API Management 서비스 인스턴스의 특정 태그를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/tags/read | 서비스 인스턴스 내에 정의 된 태그의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 태그의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/tags/write | 태그를 만듭니다. 또는는 해당 식별자로 지정 된 태그의 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/templates/delete | 기본 API Management 이메일 템플릿을 다시 설정합니다. |
> | 실행력 | Microsoft.ApiManagement/service/templates/read | 모든 이메일 템플릿을 가져오거나 API Management 이메일 템플릿 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/templates/write | API Management 이메일 템플릿을 만들거나/업데이트하거나 API Management 이메일 템플릿을 업데이트합니다. |
> | 실행력 | Microsoft.ApiManagement/service/tenant/delete | 테넌트에 대한 정책 구성을 제거합니다. |
> | 실행력 | Microsoft.ApiManagement/service/tenant/deploy/action | 배포 작업을 실행하여 지정된 git 분기에서 데이터베이스의 구성에 대해 변경된 내용을 적용합니다. |
> | 실행력 | Microsoft.apimanagement/서비스/테넌트/listSecrets/작업 | 테넌트 액세스 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/tenant/operationResults/read | 작업 결과 목록을 가져오거나 특정 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/tenant/read | Api Management 서비스의 전역 정책 정의를 가져옵니다. 또는 테넌트 액세스 정보 정보 가져오기 |
> | 실행력 | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | 기본 액세스 키를 다시 생성합니다. |
> | 실행력 | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | 보조 액세스 키를 다시 생성합니다. |
> | 실행력 | Microsoft.ApiManagement/service/tenant/save/action | 리포지토리의 지정된 분기에 대한 구성 스냅샷을 사용하여 커밋을 만듭니다. |
> | 실행력 | Microsoft.ApiManagement/service/tenant/syncState/read | 마지막 git 동기화의 상태를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/tenant/validate/action | 지정된 git 분기에서 변경된 내용이 유효한지 검사합니다. |
> | 실행력 | Microsoft.ApiManagement/service/tenant/write | 테넌트에 대한 정책 구성을 설정하거나 테넌트 액세스 세부 정보를 업데이트합니다. |
> | 실행력 | Microsoft.ApiManagement/service/updatecertificate/action | API Management 서비스에 대한 SSL 인증서를 업로드합니다. |
> | 실행력 | Microsoft.ApiManagement/service/updatehostname/action | API Management 서비스에 대한 사용자 지정 도메인 이름 설정, 업데이트 또는 제거합니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/action | 새 사용자를 등록합니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/confirmations/send/action | 확인을 보냅니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/delete | 특정 사용자를 삭제 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/generateSsoUrl/action | 지정 된 사용자를 개발자 포털에 서명 하기 위한 인증 토큰이 포함 된 리디렉션 URL을 검색 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/groups/read | 모든 사용자 그룹을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/identities/read | 모든 사용자 id의 목록입니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/keys/read | 사용자와 연결 된 키 가져오기 |
> | 실행력 | Microsoft.ApiManagement/service/users/read | 지정 된 서비스 인스턴스에 등록 된 사용자의 컬렉션을 나열 합니다. 또는 해당 식별자로 지정 된 사용자의 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/subscriptions/read | 지정 된 사용자의 구독 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/token/action | 사용자에 대 한 공유 액세스 권한 부여 토큰을 가져옵니다. |
> | 실행력 | Microsoft.ApiManagement/service/users/write | 사용자를 만들거나 업데이트 합니다. 또는는 해당 식별자로 지정 된 사용자의 세부 정보를 업데이트 합니다. |
> | 실행력 | Microsoft.ApiManagement/service/write | API Management 서비스의 새 인스턴스 만들기 |
> | 실행력 | Microsoft.ApiManagement/unregister/action | Microsoft.ApiManagement 리소스 공급자에 대한 구독을 등록 취소합니다. |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft AppConfiguration/checkNameAvailability/read | 리소스 이름을 사용할 수 있는지 여부를 확인 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/delete | 구성 저장소를 삭제 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/eventGridFilters/delete | 구성 저장소 event grid 필터를 삭제 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/eventGridFilters/read | 지정 된 구성 저장소 event grid 필터의 속성을 가져오거나 지정 된 구성 저장소에 있는 모든 구성 저장소 event grid 필터를 나열 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/eventGridFilters/write | 지정 된 매개 변수를 사용 하 여 구성 저장소 event grid 필터를 만들거나 업데이트 합니다. |
> | DataAction | Microsoft AppConfiguration/configurationStores/keyValues/delete | 구성 저장소에서 기존 키 값을 삭제 합니다. |
> | DataAction | Microsoft AppConfiguration/configurationStores/keyValues/read | 구성 저장소에서 키-값을 읽습니다. |
> | DataAction | Microsoft AppConfiguration/configurationStores/keyValues/write | 구성 저장소에서 키-값을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/ListKeys/action | 지정 된 구성 저장소에 대 한 API 키를 나열 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/ListKeyValue/action | 지정 된 구성 저장소에 대 한 키 값을 나열 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/privateEndpointConnectionProxies/delete | 지정 된 구성 저장소에서 개인 엔드포인트 연결 프록시를 삭제 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/privateEndpointConnectionProxies/read | 지정 된 구성 저장소에서 개인 엔드포인트 연결 프록시를 가져옵니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/privateEndpointConnectionProxies/validate/action | 지정 된 구성 저장소에서 개인 엔드포인트 연결 프록시의 유효성을 검사 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/privateEndpointConnectionProxies/write | 지정 된 구성 저장소에서 개인 엔드포인트 연결 프록시를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/providers/diagnosticSettings/read | 구성 저장소에 대 한 모든 진단 설정 값을 읽습니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/providers/Microsoft. Insights/diagnosticSettings/write | Microsoft 앱 구성에 대 한 진단 설정을 쓰거나 덮어씁니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/providers/metricDefinitions/read | Microsoft 앱 구성에 대 한 모든 메트릭 정의를 검색 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/read | 지정 된 구성 저장소의 속성을 가져오거나 지정 된 리소스 그룹 또는 구독 아래의 모든 구성 저장소를 나열 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/RegenerateKey/action | 지정 된 구성 저장소에 대 한 API 키를 다시 생성 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/syncTasks/delete | 구성 저장소 동기화 작업을 삭제 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/syncTasks/read | 지정 된 구성 저장소 동기화 작업의 속성을 가져오거나 지정 된 구성 저장소에 있는 모든 구성 저장소 동기화 작업을 나열 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/syncTasks/write | 지정 된 매개 변수를 사용 하 여 구성 저장소 동기화 작업을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft AppConfiguration/configurationStores/write | 지정 된 매개 변수를 사용 하 여 구성 저장소를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft AppConfiguration/위치/operationsStatus/읽기 | 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft AppConfiguration/작업/읽기 | Microsoft 앱 구성에서 지원 되는 모든 작업을 나열 합니다. |
> | 실행력 | Microsoft AppConfiguration/register/action | Microsoft 앱 구성을 사용 하는 구독을 등록 합니다. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Authorization/classicAdministrators/delete | 구독에서 관리자를 제거합니다. |
> | 실행력 | Microsoft.Authorization/classicAdministrators/operationstatuses/read | 구독의 관리자 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.Authorization/classicAdministrators/read | 구독에 대한 관리자를 읽습니다. |
> | 실행력 | Microsoft.Authorization/classicAdministrators/write | 구독에 대한 관리자를 추가 또는 수정합니다. |
> | 실행력 | Microsoft.Authorization/denyAssignments/delete | 지정된 범위에서 거부 할당을 삭제합니다. |
> | 실행력 | Microsoft.Authorization/denyAssignments/read | 거부 할당에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.Authorization/denyAssignments/write | 지정된 범위에서 거부 할당을 만듭니다. |
> | 실행력 | Microsoft.Authorization/elevateAccess/Action | 테넌트 범위에서 호출자에게 사용자 액세스 관리자 액세스 권한을 부여합니다. |
> | 실행력 | Microsoft.Authorization/locks/delete | 지정된 범위에서 잠금을 삭제합니다. |
> | 실행력 | Microsoft.Authorization/locks/read | 지정된 범위에서 잠금을 가져옵니다. |
> | 실행력 | Microsoft.Authorization/locks/write | 지정된 범위에서 잠금을 추가합니다. |
> | 실행력 | Microsoft.Authorization/operations/read | 작업 목록을 가져옵니다. |
> | 실행력 | Microsoft.Authorization/permissions/read | 호출자가 지정된 범위에서 갖는 모든 사용 권한을 나열합니다. |
> | 실행력 | Microsoft. 권한 부여/정책/감사/작업 | ' 감사 ' 효과를 사용 하 여 Azure Policy 평가 결과로 수행 된 작업 |
> | 실행력 | Microsoft. 권한 부여/정책/auditIfNotExists/작업 | ' AuditIfNotExists ' 효과를 사용 하 여 Azure Policy 평가 결과로 수행 된 작업 |
> | 실행력 | Microsoft. 권한 부여/정책/거부/작업 | ' 거부 ' 효과가 있는 Azure Policy의 평가 결과로 수행 된 작업 |
> | 실행력 | Microsoft. 권한 부여/정책/deployIfNotExists/작업 | ' DeployIfNotExists ' 효과를 사용 하 여 Azure Policy 평가 결과로 수행 된 작업 |
> | 실행력 | Microsoft.Authorization/policyAssignments/delete | 지정된 범위에서 정책 할당을 삭제합니다. |
> | 실행력 | Microsoft.Authorization/policyAssignments/read | 정책 할당에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.Authorization/policyAssignments/write | 지정된 범위에서 정책 할당을 만듭니다. |
> | 실행력 | Microsoft.Authorization/policyDefinitions/delete | 정책 정의를 삭제합니다. |
> | 실행력 | Microsoft.Authorization/policyDefinitions/read | 정책 정의에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.Authorization/policyDefinitions/write | 사용자 지정 정책 정의를 만듭니다. |
> | 실행력 | Microsoft.Authorization/policySetDefinitions/delete | 정책 집합 정의를 삭제합니다. |
> | 실행력 | Microsoft.Authorization/policySetDefinitions/read | 정책 집합 정의에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.Authorization/policySetDefinitions/write | 사용자 지정 정책 집합 정의를 만듭니다. |
> | 실행력 | Microsoft.Authorization/providerOperations/read | 역할 정의에 사용할 수 있는 모든 리소스 공급자에 대한 작업을 가져옵니다. |
> | 실행력 | Microsoft.Authorization/roleAssignments/delete | 지정된 범위에서 역할 할당을 삭제합니다. |
> | 실행력 | Microsoft.Authorization/roleAssignments/read | 역할 할당에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.Authorization/roleAssignments/write | 지정된 범위에서 역할 할당을 만듭니다. |
> | 실행력 | Microsoft.Authorization/roleDefinitions/delete | 지정된 사용자 지정 역할 정의를 삭제합니다. |
> | 실행력 | Microsoft.Authorization/roleDefinitions/read | 역할 정의에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.Authorization/roleDefinitions/write | 지정된 사용 권한 및 할당 가능 범위를 사용하여 사용자 지정 역할 정의를 만들거나 업데이트합니다. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Azure Automation DSC의 등록 정보를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Azure Automation DSC 키를 다시 생성하는 요청을 작성합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/certificates/delete | Azure Automation 인증서 자산을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/certificates/getCount/action | 인증서 수를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/certificates/read | Azure Automation 인증서 자산을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/certificates/write | Azure Automation 인증서 자산을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/compilationjobs/read | Azure Automation DSC의 컴파일을 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/compilationjobs/read | Azure Automation DSC의 컴파일을 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/compilationjobs/write | Azure Automation DSC의 컴파일을 작성합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/compilationjobs/write | Azure Automation DSC의 컴파일을 작성합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/configurations/content/read | 구성 미디어 콘텐츠를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/configurations/delete | Azure Automation DSC의 콘텐츠를 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/configurations/getCount/action | Azure Automation DSC의 콘텐츠 수를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/configurations/read | Azure Automation DSC의 콘텐츠를 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/configurations/write | Azure Automation DSC의 콘텐츠를 작성합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/connections/delete | Azure Automation 연결 자산을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/connections/getCount/action | 연결 수를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/connections/read | Azure Automation 연결 자산을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/connections/write | Azure Automation 연결 자산을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/connectionTypes/delete | Azure Automation 연결 형식 자산을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/connectionTypes/read | Azure Automation 연결 형식 자산을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/connectionTypes/write | Azure Automation 연결 형식 자산을 만듭니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/credentials/delete | Azure Automation 자격 증명 자산을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/credentials/getCount/action | 자격 증명 수를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/credentials/read | Azure Automation 자격 증명 자산을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/credentials/write | Azure Automation 자격 증명 자산을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/delete | Azure Automation 계정을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hybrid Runbook Worker 리소스를 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker 리소스를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobs/output/read | 작업의 출력을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation 작업을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation 작업을 계속합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | 작업 실행 시 Azure Automation runbook의 콘텐츠를 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation 작업을 중지합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation 작업 스트림을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation 작업 스트림을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation 작업을 일시 중단합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation 작업을 만듭니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobSchedules/delete | Azure Automation 작업 일정을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation 작업 일정을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/jobSchedules/write | Azure Automation 작업 일정을 만듭니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 자동화 계정에 연결된 작업 영역 가져오기 |
> | 실행력 | Microsoft.Automation/automationAccounts/listKeys/action | 자동화 계정에 대한 키를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/modules/activities/read | Azure Automation 작업을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/modules/delete | Azure Automation Powershell 모듈을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/modules/getCount/action | Automation Account 내에서 Powershell 모듈 수를 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/modules/read | Azure Automation Powershell 모듈을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/modules/write | Azure Automation Powershell Powershell 모듈을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Azure Automation DSC의 노드 구성을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Azure Automation DSC의 노드 구성 콘텐츠를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Azure Automation DSC의 노드 구성을 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Azure Automation DSC의 노드 구성을 작성합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodecounts/read | 지정된 유형에 대한 노드 수 요약을 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodes/delete | Azure Automation DSC 노드를 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodes/read | Azure Automation DSC 노드를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Azure Automation DSC 보고서 콘텐츠를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodes/reports/read | Azure Automation DSC 보고서를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/nodes/write | Azure Automation DSC 노드를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Azure Automation TypeFields를 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/python2Packages/delete | Azure Automation Python 2 패키지를 삭제 |
> | 실행력 | Microsoft.Automation/automationAccounts/python2Packages/read | Azure Automation Python 2 패키지를 가져오기 |
> | 실행력 | Microsoft.Automation/automationAccounts/python2Packages/write | Azure Automation Python 2 패키지를 만들거나 업데이트 |
> | 실행력 | Microsoft.Automation/automationAccounts/python3Packages/delete | Azure Automation Python 3 패키지를 삭제 |
> | 실행력 | Microsoft.Automation/automationAccounts/python3Packages/read | Azure Automation Python 3 패키지를 가져오기 |
> | 실행력 | Microsoft.Automation/automationAccounts/python3Packages/write | Azure Automation Python 3 패키지를 만들거나 업데이트 |
> | 실행력 | Microsoft.Automation/automationAccounts/read | Azure Automation 계정을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/content/read | Azure Automation runbook의 콘텐츠를 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/delete | Azure Automation Runbook을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Azure Automation Runbook 초안의 콘텐츠를 만듭니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Azure Automation Runbook 초안 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/draft/read | Azure Automation Runbook 초안을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Azure Automation Runbook 초안 테스트 작업을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Azure Automation Runbook 초안 테스트 작업을 계속합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Azure Automation Runbook 초안 테스트 작업을 중지합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Azure Automation Runbook 초안 테스트 작업을 일시 중단합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Azure Automation Runbook 초안 테스트 작업을 만듭니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Azure Automation Runbook 초안에 대한 편집을 실행 취소합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Azure Automation Runbook 수를 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/publish/action | Azure Automation Runbook 초안을 게시합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/runbooks/write | Azure Automation Runbook을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/schedules/delete | Azure Automation 일정 자산을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/schedules/getCount/action | Azure Automation 일정 수를 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation 일정 자산을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/schedules/write | Azure Automation 일정 자산을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/read | 소프트웨어 업데이트 구성 컴퓨터 실행 Azure Automation를 가져옵니다. |
> | 실행력 | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/read | Azure Automation 소프트웨어 업데이트 구성 실행을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation 소프트웨어 업데이트 구성을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation 소프트웨어 업데이트 구성을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation 소프트웨어 업데이트 구성을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation 소프트웨어 업데이트 구성을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation 소프트웨어 업데이트 구성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation 소프트웨어 업데이트 구성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/statistics/read | Azure Automation 통계를 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Azure Automation 업데이트 배포 머신을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Azure Automation 업데이트 관리 패치 작업을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/usages/read | Azure Automation 사용량을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/variables/delete | Azure Automation 변수 자산을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/variables/read | Azure Automation 변수 자산을 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/variables/write | Azure Automation 변수 자산을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/watchers/delete | Azure Automation Watcher 작업을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/watchers/read | Azure Automation Watcher 작업을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/watchers/start/action | Azure Automation Watcher 작업을 시작합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/watchers/stop/action | Azure Automation Watcher 작업을 중지합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/watchers/streams/read | Azure Automation Watcher 작업 스트림을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Azure Automation Watcher 작업 동작을 삭제합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Azure Automation Watcher 작업 동작을 가져옵니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Azure Automation Watcher 작업 동작을 만듭니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/watchers/write | Azure Automation Watcher 작업을 만듭니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/webhooks/action | Azure Automation 웹후크에 대한 URI를 생성합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/webhooks/delete | Azure Automation 웹후크를 삭제합니다.  |
> | 실행력 | Microsoft.Automation/automationAccounts/webhooks/read | Azure Automation 웹후크를 읽습니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/webhooks/write | Azure Automation 웹후크를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/automationAccounts/write | Azure Automation 계정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Automation/operations/read | Azure Automation 리소스에 사용 가능한 작업을 가져옵니다. |
> | 실행력 | Microsoft.Automation/register/action | 구독을 Azure Automation에 등록합니다. |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C 디렉터리 리소스를 삭제합니다. |
> | 실행력 | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C 디렉터리 리소스를 봅니다. |
> | 실행력 | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C 디렉터리 리소스를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.AzureActiveDirectory/b2ctenants/read | 사용자가 멤버인 모든 B2C 테넌트를 나열 합니다. |
> | 실행력 | Microsoft.AzureActiveDirectory/operations/read | Microsoft.AzureActiveDirectory 리소스 공급자에 사용할 수 있는 모든 API 작업을 읽습니다. |
> | 실행력 | Microsoft.AzureActiveDirectory/register/action | Microsoft.AzureActiveDirectory 리소스 공급자 구독을 등록합니다. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.AzureStack/Operations/read | 리소스 공급자 작업의 속성을 가져옵니다. |
> | 실행력 | Microsoft.AzureStack/register/action | Microsoft.AzureStack 리소스 공급자에 구독을 등록합니다. |
> | 실행력 | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Azure Stack 고객 구독을 삭제합니다. |
> | 실행력 | Microsoft.AzureStack/registrations/customerSubscriptions/read | Azure Stack 고객 구독의 속성을 가져옵니다. |
> | 실행력 | Microsoft.AzureStack/registrations/customerSubscriptions/write | Azure Stack 고객 구독을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.AzureStack/registrations/delete | Azure Stack 등록을 삭제합니다. |
> | 실행력 | Microsoft.AzureStack/registrations/getActivationKey/action | 최신 Azure Stack 활성화 키를 가져옵니다. |
> | 실행력 | Microsoft AzureStack/등록/제품/t s p s/작업 | Azure Stack Marketplace 제품을 검색 합니다. |
> | 실행력 | Microsoft AzureStack/등록/제품/t s p 제품/작업 | Azure Stack Marketplace 제품의 목록을 검색 합니다. |
> | 실행력 | Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace 제품에 대한 세부 정보 검색 |
> | 실행력 | Microsoft.AzureStack/registrations/products/read | Azure Stack Marketplace 제품의 속성 가져오기 |
> | 실행력 | Microsoft AzureStack/등록/제품/uploadProductLog/작업 | Azure Stack Marketplace 제품 작업 상태 및 타임 스탬프를 기록 합니다. |
> | 실행력 | Microsoft.AzureStack/registrations/read | Azure Stack 등록의 속성 가져오기 |
> | 실행력 | Microsoft.AzureStack/registrations/write | Azure Stack 등록을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft AzureStack/verificationKeys/getCurrentKey/action | Azure Stack 서명 공개 키의 현재 버전을 가져옵니다. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Batch/batchAccounts/applications/delete | 애플리케이션을 삭제합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/applications/read | 애플리케이션을 나열하거나 애플리케이션의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/applications/versions/activate/action | 애플리케이션 패키지를 활성화합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/applications/versions/delete | 애플리케이션 패키지를 삭제합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/applications/versions/read | 애플리케이션 패키지의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/applications/versions/write | 새 애플리케이션 패키지를 만들거나 기존 애플리케이션 패키지를 업데이트합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/applications/write | 새 애플리케이션을 만들거나 기존 애플리케이션을 업데이트합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/certificateOperationResults/read | 배치 계정에 대한 장기 실행 인증서 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | 배치 계정에서 실패한 인증서 삭제를 취소합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/certificates/delete | 배치 계정에서 인증서를 삭제합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/certificates/read | 배치 계정의 인증서를 나열하거나 인증서 속성을 가져옵니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/certificates/write | 배치 계정에 새 인증서를 만들거나 기존 인증서를 업데이트합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/delete | Batch 계정을 삭제합니다. |
> | DataAction | Microsoft. Batch/batchAccounts/jobs/delete | Batch 계정에서 작업을 삭제 합니다. |
> | DataAction | Microsoft. Batch/batchAccounts/jobs/read | 일괄 처리 계정에 대 한 작업을 나열 하거나 작업의 속성을 가져옵니다. |
> | DataAction | Microsoft. Batch/batchAccounts/jobs/write | Batch 계정에 새 작업을 만들거나 기존 작업을 업데이트 합니다. |
> | DataAction | Microsoft. Batch/batchAccounts/jobSchedules/삭제 | Batch 계정에서 작업 일정을 삭제 합니다. |
> | DataAction | Microsoft. Batch/batchAccounts/jobSchedules/읽기 | Batch 계정에 대 한 작업 일정을 나열 하거나 작업 일정의 속성을 가져옵니다. |
> | DataAction | Microsoft. Batch/batchAccounts/jobSchedules/write | Batch 계정에 새 작업 일정을 만들거나 기존 작업 일정을 업데이트 합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/listkeys/action | Batch 계정에 대한 액세스 키를 나열합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/operationResults/read | 장기 실행 배치 계정 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/poolOperationResults/read | 배치 계정에 대한 장기 실행 풀 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/pools/delete | 배치 계정에서 풀을 삭제합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | 배치 계정 풀에 대한 자동 크기 조정을 해제합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/pools/read | 배치 계정의 풀을 나열하거나 풀 속성을 가져옵니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/pools/stopResize/action | 배치 계정 풀에서 진행 중인 크기 조정 작업을 중지합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/pools/write | 배치 계정에 새 풀을 만들거나 기존 풀을 업데이트합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/read | Batch 계정을 나열하거나 Batch 계정의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/regeneratekeys/action | Batch 계정에 대한 액세스 키를 다시 생성합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Batch 계정에 대해 구성된 자동 스토리지 계정의 액세스 키를 동기화합니다. |
> | 실행력 | Microsoft.Batch/batchAccounts/write | 새 Batch 계정을 만들거나 기존 Batch 계정을 업데이트합니다. |
> | 실행력 | Microsoft.Batch/locations/accountOperationResults/read | 장기 실행 배치 계정 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Batch/locations/checkNameAvailability/action | 계정 이름이 올바르고 사용되고 있지 않은지 확인합니다. |
> | 실행력 | Microsoft.Batch/locations/quotas/read | 지정된 Azure 지역에서 지정된 구독의 Batch 할당량을 가져옵니다. |
> | 실행력 | Microsoft.Batch/operations/read | Microsoft.Batch 리소스 공급자에서 사용할 수 있는 작업을 나열합니다. |
> | 실행력 | Microsoft.Batch/register/action | Batch 리소스 공급자에 대한 구독을 등록하고 Batch 계정을 만들도록 설정합니다. |
> | 실행력 | Microsoft.Batch/unregister/action | Batch 리소스 공급자에 대한 구독을 등록 취소하여 Batch 계정 만들기를 차단합니다. |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft. 청구/billingAccounts/계약/읽기 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingPermissions/읽기 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/billingPermissions/read |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/고객/읽기 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/청구서/가격표/다운로드/작업 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/invoiceSections/billingPermissions/read |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/이동/동작 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/전송/작업 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/작업 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/invoiceSections/products/move/action |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/invoiceSections/제품/전송/작업 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/invoiceSections/products/validateMoveEligibility/작업 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/invoiceSections/read |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/invoiceSections/write |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/가격표/다운로드/작업 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/읽기 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/쓰기 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingProfiles/쓰기 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/billingSubscriptions/읽기 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/고객/billingPermissions/읽기 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/고객/읽기 |  |
> | 실행력 | Microsoft.Billing/billingAccounts/departments/read |  |
> | 실행력 | Microsoft. 청구/billingAccounts/enrollmentAccounts/billingPermissions/read |  |
> | 실행력 | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | 실행력 | Microsoft. 청구/billingAccounts/enrollmentDepartments/billingPermissions/read |  |
> | 실행력 | Microsoft. 청구/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/작업 |  |
> | 실행력 | Microsoft. 청구/billingAccounts/제품/읽기 |  |
> | 실행력 | Microsoft.Billing/billingAccounts/read |  |
> | 실행력 | Microsoft. 청구/billingAccounts/쓰기 |  |
> | 실행력 | Microsoft.Billing/departments/read |  |
> | 실행력 | Microsoft. 청구/송장/다운로드/작업 | 목록에서 다운로드 링크를 사용 하 여 청구서 다운로드 |
> | 실행력 | Microsoft.Billing/invoices/read |  |
> | 실행력 | Microsoft.Billing/register/action |  |
> | 실행력 | Microsoft. 요금 청구/s i d/작업 |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.BingMaps/mapApis/Delete | 삭제 작업 |
> | 실행력 | Microsoft.BingMaps/mapApis/listSecrets/action | 암호를 나열합니다. |
> | 실행력 | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | 리소스에 대한 Single Sign-On 권한 부여 토큰을 읽습니다. |
> | 실행력 | Microsoft.BingMaps/mapApis/Read | 읽기 작업 |
> | 실행력 | Microsoft.BingMaps/mapApis/regenerateKey/action | 키 다시 생성 |
> | 실행력 | Microsoft.BingMaps/mapApis/Write | 쓰기 작업 |
> | 실행력 | Microsoft.BingMaps/Operations/read | 작업에 대한 설명입니다. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Blockchain/blockchainMembers/delete | 기존 Blockchain 멤버를 삭제 합니다. |
> | 실행력 | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | 기존 Blockchain 구성원 API 키를 가져오거나 나열 합니다. |
> | 실행력 | Microsoft.Blockchain/blockchainMembers/read | 기존 Blockchain 멤버를 가져오거나 나열 합니다. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Blockchain 구성원 트랜잭션 노드에 연결 합니다. |
> | 실행력 | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | 기존 Blockchain 구성원 트랜잭션 노드를 삭제 합니다. |
> | 실행력 | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | 기존 Blockchain 구성원 트랜잭션 노드 API 키를 가져오거나 나열 합니다. |
> | 실행력 | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | 기존 Blockchain 구성원 트랜잭션 노드를 가져오거나 나열 합니다. |
> | 실행력 | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Blockchain 구성원 트랜잭션 노드를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Blockchain/blockchainMembers/write | Blockchain 멤버를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft Blockchain/cordaMembers/delete | 기존 Blockchain Corda 멤버를 삭제 합니다. |
> | 실행력 | Microsoft Blockchain/cordaMembers/read | 기존 Blockchain Corda 멤버를 가져오거나 나열 합니다. |
> | 실행력 | Microsoft Blockchain/cordaMembers/write | Blockchain Corda 멤버를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Blockchain 멤버의 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft.Blockchain/locations/checkNameAvailability/action | 리소스 이름이 유효 하며 사용 되 고 있지 않은지 확인 합니다. |
> | 실행력 | Microsoft.Blockchain/operations/read | Microsoft Blockchain 리소스 공급자의 모든 작업을 나열 합니다. |
> | 실행력 | Microsoft.Blockchain/register/action | Blockchain 리소스 공급자에 대 한 구독을 등록 합니다. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | 모든 청사진 아티팩트 읽기 |
> | 실행력 | Microsoft.Blueprint/blueprintAssignments/delete | 청사진 아티팩트 삭제 |
> | 실행력 | Microsoft.Blueprint/blueprintAssignments/read | 모든 청사진 아티팩트 읽기 |
> | 실행력 | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Azure 청사진 서비스 사용자 개체 Id를 가져옵니다. |
> | 실행력 | Microsoft.Blueprint/blueprintAssignments/write | 청사진 아티팩트 만들기 또는 업데이트 |
> | 실행력 | Microsoft.Blueprint/blueprints/artifacts/delete | 청사진 아티팩트 삭제 |
> | 실행력 | Microsoft.Blueprint/blueprints/artifacts/read | 모든 청사진 아티팩트 읽기 |
> | 실행력 | Microsoft.Blueprint/blueprints/artifacts/write | 청사진 아티팩트 만들기 또는 업데이트 |
> | 실행력 | Microsoft.Blueprint/blueprints/delete | 청사진 삭제 |
> | 실행력 | Microsoft.Blueprint/blueprints/read | 청사진 읽기 |
> | 실행력 | Microsoft.Blueprint/blueprints/versions/artifacts/read | 모든 청사진 아티팩트 읽기 |
> | 실행력 | Microsoft.Blueprint/blueprints/versions/delete | 청사진 삭제 |
> | 실행력 | Microsoft.Blueprint/blueprints/versions/read | 청사진 읽기 |
> | 실행력 | Microsoft.Blueprint/blueprints/versions/write | 청사진 만들기 또는 업데이트 |
> | 실행력 | Microsoft.Blueprint/blueprints/write | 청사진 만들기 또는 업데이트 |
> | 실행력 | Microsoft.Blueprint/register/action | Azure 청사진 리소스 공급자 등록 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.BotService/botServices/channels/delete | Bot Service를 삭제합니다. |
> | 실행력 | Microsoft.BotService/botServices/channels/read | Bot Service를 읽습니다. |
> | 실행력 | Microsoft.BotService/botServices/channels/write | Bot Service를 작성합니다. |
> | 실행력 | Microsoft.BotService/botServices/connections/delete | Bot Service를 삭제합니다. |
> | 실행력 | Microsoft.BotService/botServices/connections/read | Bot Service를 읽습니다. |
> | 실행력 | Microsoft.BotService/botServices/connections/write | Bot Service를 작성합니다. |
> | 실행력 | Microsoft.BotService/botServices/delete | Bot Service를 삭제합니다. |
> | 실행력 | Microsoft.BotService/botServices/read | Bot Service를 읽습니다. |
> | 실행력 | Microsoft.BotService/botServices/write | Bot Service를 작성합니다. |
> | 실행력 | Microsoft.BotService/locations/operationresults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft.BotService/Operations/read | 모든 리소스 종류에 대한 작업을 읽습니다. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Cache/checknameavailability/action | 이름을 새 Redis Cache에서 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.Cache/locations/operationresults/read | 이전에 'Location' 헤더가 클라이언트에 반환된 장기 실행 작업의 결과를 가져옵니다 |
> | 실행력 | Microsoft.Cache/operations/read | 'Microsoft.Cache' 공급자가 지원하는 작업을 나열합니다. |
> | 실행력 | Microsoft.Cache/redis/delete | 전체 Redis Cache를 삭제합니다. |
> | 실행력 | Microsoft.Cache/redis/export/action | Redis 데이터를 지정된 형식의 접두사 지정 스토리지 blob으로 내보냅니다. |
> | 실행력 | Microsoft.Cache/redis/firewallRules/delete | Redis Cache의 IP 방화벽 규칙을 삭제합니다. |
> | 실행력 | Microsoft.Cache/redis/firewallRules/read | Redis Cache의 IP 방화벽 규칙을 가져옵니다. |
> | 실행력 | Microsoft.Cache/redis/firewallRules/write | Redis Cache의 IP 방화벽 규칙을 편집합니다. |
> | 실행력 | Microsoft.Cache/redis/forceReboot/action | 캐시 인스턴스를 강제로 다시 부팅합니다. 데이터가 손실될 수 있습니다. |
> | 실행력 | Microsoft.Cache/redis/import/action | 여러 blob에서 지정된 형식의 데이터를 Redis로 가져옵니다. |
> | 실행력 | Microsoft.Cache/redis/linkedservers/delete | Redis Cache에서 연결된 서버를 삭제합니다. |
> | 실행력 | Microsoft.Cache/redis/linkedservers/read | Redis Cache와 관련된 연결된 서버를 가져옵니다. |
> | 실행력 | Microsoft.Cache/redis/linkedservers/write | Redis Cache에 연결된 서버를 추가합니다. |
> | 실행력 | Microsoft.Cache/redis/listKeys/action | 관리 포털에서 Redis Cache 액세스 키의 값을 봅니다. |
> | 실행력 | Microsoft.Cache/redis/metricDefinitions/read | Redis Cache에 대해 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Cache/redis/patchSchedules/delete | Redis Cache의 패치 일정을 삭제합니다. |
> | 실행력 | Microsoft.Cache/redis/patchSchedules/read | Redis Cache의 패치 일정을 가져옵니다. |
> | 실행력 | Microsoft.Cache/redis/patchSchedules/write | Redis Cache의 패치 일정을 수정합니다. |
> | 실행력 | Microsoft.Cache/redis/read | 관리 콘솔에서 Redis Cache의 설정 및 구성을 봅니다. |
> | 실행력 | Microsoft.Cache/redis/regenerateKey/action | 관리 포털에서 Redis Cache 액세스 키의 값을 변경합니다. |
> | 실행력 | Microsoft.Cache/redis/start/action | 캐시 인스턴스를 시작합니다. |
> | 실행력 | Microsoft.Cache/redis/stop/action | 캐시 인스턴스를 중지합니다. |
> | 실행력 | Microsoft.Cache/redis/write | 관리 콘솔에서 Redis Cache의 설정 및 구성을 수정합니다. |
> | 실행력 | Microsoft.Cache/register/action | ‘Microsoft.Cache’ 리소스 공급자를 구독에 등록합니다. |
> | 실행력 | Microsoft.Cache/unregister/action | ‘Microsoft.Cache’ 리소스 공급자를 구독에서 등록 취소합니다. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Capacity/appliedreservations/read | 모든 예약을 읽습니다. |
> | 실행력 | Microsoft.Capacity/calculateexchange/action | 새 구매의 교환 금액 및 가격을 계산 하 고 정책 오류를 반환 합니다. |
> | 실행력 | Microsoft.Capacity/calculateprice/action | 예약 가격을 계산합니다. |
> | 실행력 | Microsoft.Capacity/catalogs/read | 예약 카탈로그를 읽습니다. |
> | 실행력 | Microsoft.Capacity/checkoffers/action | 구독 제안을 확인합니다. |
> | 실행력 | Microsoft.Capacity/checkscopes/action | 구독을 확인합니다. |
> | 실행력 | Microsoft.Capacity/commercialreservationorders/read | 테넌트에서 작성된 예약 주문을 가져옵니다. |
> | 실행력 | Microsoft.Capacity/exchange/action | 모든 예약 교환 |
> | 실행력 | Microsoft.Capacity/operations/read | 작업을 읽습니다. |
> | 실행력 | Microsoft.Capacity/register/action | 용량 리소스 공급자를 등록하고 용량 리소스 만들기를 사용하도록 설정합니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/action | 예약을 업데이트합니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/availablescopes/action | 사용 가능한 범위를 찾습니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/calculaterefund/action | 새 구매의 환불 금액 및 가격을 계산 하 고 정책 오류를 반환 합니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/delete | 예약을 삭제합니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/merge/action | 예약을 병합합니다. |
> | 실행력 | Microsoft Capacity/reservationorders/mergeoperationresults/read | 모든 병합 작업 폴링 |
> | 실행력 | Microsoft.Capacity/reservationorders/read | 모든 예약을 읽습니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/reservations/action | 예약을 업데이트합니다. |
> | 실행력 | Microsoft Capacity/reservationorders/예약은/availablescopes/action | 사용 가능한 범위를 찾습니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/reservations/delete | 예약을 삭제합니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/reservations/read | 모든 예약을 읽습니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/reservations/revisions/read | 모든 예약을 읽습니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/reservations/write | 예약을 만듭니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/return/action | 예약을 반환합니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/split/action | 예약을 분할합니다. |
> | 실행력 | Microsoft Capacity/reservationorders/splitoperationresults/read | 모든 분할 작업 폴링 |
> | 실행력 | Microsoft.Capacity/reservationorders/swap/action | 예약을 교환합니다. |
> | 실행력 | Microsoft.Capacity/reservationorders/write | 예약을 만듭니다. |
> | 실행력 | Microsoft.Capacity/tenants/register/action | 테넌트를 등록합니다. |
> | 실행력 | Microsoft.Capacity/unregister/action | 테넌트를 등록 취소합니다. |
> | 실행력 | Microsoft.Capacity/validatereservationorder/action | 예약의 유효성을 검사합니다. |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft Cdn/cdnwebapplicationfirewallmanagedrulesets/delete |  |
> | 실행력 | Microsoft Cdn/cdnwebapplicationfirewallmanagedrulesets/read |  |
> | 실행력 | Microsoft Cdn/cdnwebapplicationfirewallmanagedrulesets/write |  |
> | 실행력 | Microsoft Cdn/cdnwebapplicationfirewallpolicies/delete |  |
> | 실행력 | Microsoft Cdn/cdnwebapplicationfirewallpolicies/read |  |
> | 실행력 | Microsoft Cdn/cdnwebapplicationfirewallpolicies/write |  |
> | 실행력 | Microsoft.Cdn/CheckNameAvailability/action |  |
> | 실행력 | Microsoft.Cdn/CheckResourceUsage/action |  |
> | 실행력 | Microsoft.Cdn/edgenodes/delete |  |
> | 실행력 | Microsoft.Cdn/edgenodes/read |  |
> | 실행력 | Microsoft.Cdn/edgenodes/write |  |
> | 실행력 | Microsoft Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/delete |  |
> | 실행력 | Microsoft Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/read |  |
> | 실행력 | Microsoft Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/write |  |
> | 실행력 | Microsoft.Cdn/operationresults/delete |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/read |  |
> | 실행력 | Microsoft.Cdn/operationresults/profileresults/write |  |
> | 실행력 | Microsoft.Cdn/operationresults/read |  |
> | 실행력 | Microsoft.Cdn/operationresults/write |  |
> | 실행력 | Microsoft.Cdn/operations/read |  |
> | 실행력 | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | 실행력 | Microsoft.Cdn/profiles/delete |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/delete |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/read |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | 실행력 | Microsoft.Cdn/profiles/endpoints/write |  |
> | 실행력 | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | 실행력 | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | 실행력 | Microsoft.Cdn/profiles/read |  |
> | 실행력 | Microsoft.Cdn/profiles/write |  |
> | 실행력 | Microsoft.Cdn/register/action | CDN 리소스 공급자에 대한 구독을 등록하고 CDN 프로필 생성을 활성화합니다. |
> | 실행력 | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 기존 인증서를 삭제합니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 인증서 목록을 가져옵니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 새 인증서를 추가하거나 기존 인증서를 업데이트합니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/Delete | 기존 AppServiceCertificate를 삭제합니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/Read | CertificateOrders의 목록을 가져옵니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | 기존 certificateorder를 재발급합니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/renew/Action | 기존 certificateorder를 갱신합니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | 인증서 전자 메일을 다시 보냅니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 요청 전자 메일을 다른 전자 메일 주소로 다시 보냅니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 발급된 App Service Certificate에 대한 사이트 봉인을 검색합니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | 인증서 작업의 목록을 검색합니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | 인증서 전자 메일 기록을 검색합니다. |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | 도메인 소유권 확인 |
> | 실행력 | Microsoft.CertificateRegistration/certificateOrders/Write | 새 certificateOrder를 추가하거나 기존 certificateOrder를 업데이트합니다. |
> | 실행력 | Microsoft.CertificateRegistration/operations/Read | 앱 서비스 인증서 등록의 모든 작업을 나열합니다. |
> | 실행력 | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | 서비스 앱 사용자에 대한 서비스 주체를 프로비전합니다. |
> | 실행력 | Microsoft.CertificateRegistration/register/action | 구독에 대한 Microsoft 인증서 리소스 공급자를 등록합니다. |
> | 실행력 | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 인증서 구매 개체를 제출하지 않고 유효성을 검사합니다. |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ClassicCompute/capabilities/read | 기능을 표시합니다. |
> | 실행력 | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 지정된 도메인 이름의 가용성을 확인합니다. |
> | 실행력 | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 지정된 도메인 이름의 가용성을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/active/write | 활성 도메인 이름을 설정합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/availabilitySets/read | 리소스에 대한 가용성 집합을 표시합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/capabilities/read | 도메인 이름 기능을 표시합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/delete | 리소스에 대한 도메인 이름을 제거합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/deploymentslots/read | 배포 슬롯을 표시합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | 도메인 이름의 배포 슬롯의 역할 가져오기 |
> | 실행력 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | 도메인 이름의 배포 슬롯의 역할에 대한 역할 인스턴스 가져오기 |
> | 실행력 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | 배포 슬롯 상태를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | 배포 슬롯 상태를 추가합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | 도메인 이름의 배포 슬롯에 대한 업그레이드 도메인 가져오기 |
> | 실행력 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | 도메인 이름의 배포 슬롯에 대한 업그레이드 도메인 업데이트 |
> | 실행력 | Microsoft.ClassicCompute/domainNames/deploymentslots/write | 배포를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/extensions/delete | 도메인 이름 확장을 제거합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | 도메인 이름 확장에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/extensions/read | 도메인 이름 확장을 반환합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/extensions/write | 도메인 이름 확장을 추가합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 새 내부 부하 분산 장치를 제거합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | 도메인 이름 내부 부하 분산 장치에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 내부 부하 분산 장치를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 새 내부 부하 분산 장치를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | 도메인 이름 부하 분산 엔드포인트 집합에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 부하가 분산된 엔드포인트 집합을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 부하가 분산된 엔드포인트 집합을 추가합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/operationstatuses/read | 도메인 이름의 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/operationStatuses/read | 도메인 이름 확장에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/read | 리소스에 대한 도메인 이름을 반환합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 사용된 서비스 인증서를 삭제합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | 도메인 이름 서비스 인증서에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 사용된 서비스 인증서를 반환합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 사용된 서비스 인증서를 추가 또는 수정합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | 배포 슬롯의 마이그레이션을 중단합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | 배포 슬롯의 마이그레이션을 커밋합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/delete | 지정된 배포 슬롯을 삭제합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | 도메인 이름 슬롯에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | 배포 슬롯의 마이그레이션을 준비합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/read | 배포 슬롯을 표시합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | 배포 슬롯 역할에 대한 확장 참조를 제거합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | 도메인 이름 슬롯 확장 참조에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | 배포 슬롯 역할에 대한 확장 참조를 반환합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | 배포 슬롯 역할에 대한 확장 참조를 추가하거나 수정합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | 도메인 이름의 역할 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | 도메인 이름의 역할 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | 도메인 이름 슬롯 역할의 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/read | 배포 슬롯에 대한 역할을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | 도메인 이름 슬롯 역할의 역할 인스턴스에 대한 원격 데스크톱 연결 파일을 다운로드합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | 도메인 이름 슬롯 역할의 역할 인스턴스의 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | 역할 인스턴스를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | 역할 인스턴스를 다시 빌드합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | 역할 인스턴스를 이미지로 다시 설치합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | 역할 인스턴스를 다시 시작합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | 배포 슬롯의 역할 SKU를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/roles/write | 배포 슬롯의 역할을 추가합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/start/action | 배포 슬롯을 시작합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/state/start/write | 배포 슬롯 상태를 중지됨으로 변경합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | 배포 슬롯 상태를 시작됨으로 변경합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/stop/action | 배포 슬롯을 일시 중단합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | 도메인 업그레이드를 탐색합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | 배포 슬롯의 마이그레이션 유효성을 검사합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/slots/write | 배포를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/swap/action | 스테이징 슬롯을 프로덕션 슬롯으로 바꿉니다. |
> | 실행력 | Microsoft.ClassicCompute/domainNames/write | 리소스에 대한 도메인 이름을 추가 또는 수정합니다. |
> | 실행력 | Microsoft.ClassicCompute/moveSubscriptionResources/action | 모든 클래식 리소스를 다른 구독으로 이동합니다. |
> | 실행력 | Microsoft.ClassicCompute/operatingSystemFamilies/read | Microsoft Azure에서 사용 가능한 게스트 운영 체제 제품군을 나열하고 각 제품군에서 사용 가능한 운영 체제 버전도 나열합니다. |
> | 실행력 | Microsoft.ClassicCompute/operatingSystems/read | Microsoft Azure에서 현재 사용할 수 있는 게스트 운영 체제의 버전을 나열합니다. |
> | 실행력 | Microsoft.ClassicCompute/operations/read | 작업 목록을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/operationStatuses/read | 리소스의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/quotas/read | 구독에 대한 할당량을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/register/action | 클래식 Compute에 등록 |
> | 실행력 | Microsoft.ClassicCompute/resourceTypes/skus/read | 지원되는 리소스 형식에 대한 SKU 목록을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 클래식 이동 작업에 대한 구독의 가용성이 유효한지 확인합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 가상 머신과 연결된 네트워크 보안 그룹을 삭제합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 네트워크 보안 그룹에 연결된 가상 머신에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 가상 컴퓨터와 연결된 네트워크 보안 그룹을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 가상 머신과 연결된 네트워크 보안 그룹을 추가합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 가능한 비동기 작업을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | 데이터 디스크를 가상 머신에 연결합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/capture/action | 가상 머신을 캡처합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/delete | 가상 머신을 제거합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | 데이터 디스크를 가상 머신에서 분리합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 가상 머신 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/disks/read | 데이터 디스크의 목록을 검색합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | 가상 머신에 대한 RDP 파일을 다운로드합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 가상 머신 확장의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/extensions/read | 가상 머신 확장을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/extensions/write | 가상 머신 확장을 배치합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 가상 머신 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/metrics/read | 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 삭제합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 네트워크 보안 그룹에 연결된 가상 머신에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | 네트워크 인터페이스에 연결된 네트워크 보안 그룹을 추가합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 가상 머신의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | 가상 머신에서 유지 관리를 수행합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/read | 가상 머신 목록을 검색합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/redeploy/action | 가상 머신을 다시 배포합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/restart/action | 가상 머신을 다시 시작합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/shutdown/action | 가상 머신을 종료합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/start/action | 가상 머신을 시작합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/stop/action | 가상 머신을 중지합니다. |
> | 실행력 | Microsoft.ClassicCompute/virtualMachines/write | 가상 머신을 추가 또는 수정합니다. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Express 경로 교차 연결 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Express 경로 교차 연결 피어링을 삭제합니다. |
> | 실행력 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Express 경로 교차 연결 피어링 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Express 경로 교차 연결 피어링을 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Express 경로 교차 연결 피어링을 추가합니다. |
> | 실행력 | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Express 경로 교차 연결을 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Express 경로 교차 연결을 추가합니다. |
> | 실행력 | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | 지원되는 디바이스 목록을 검색합니다. |
> | 실행력 | Microsoft.ClassicNetwork/networkSecurityGroups/delete | 네트워크 보안 그룹을 삭제합니다. |
> | 실행력 | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | 네트워크 보안 그룹의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | 네트워크 보안 그룹 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | 네트워크 보안 그룹 진단 설정을 만들거나 업데이트합니다. 이 작업은 인사이트 리소스 공급자를 통해 보완됩니다. |
> | 실행력 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | 네트워크 보안 그룹에 대한 이벤트를 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/networkSecurityGroups/read | 네트워크 보안 그룹을 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | 보안 규칙을 삭제합니다. |
> | 실행력 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | 네트워크 보안 그룹 보안 규칙의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | 보안 규칙을 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | 보안 규칙을 추가하거나 업데이트합니다. |
> | 실행력 | Microsoft.ClassicNetwork/networkSecurityGroups/write | 새 네트워크 보안 그룹을 추가합니다. |
> | 실행력 | Microsoft.ClassicNetwork/operations/read | 클래식 네트워크 작업을 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/quotas/read | 구독에 대한 할당량을 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/register/action | 클래식 네트워크에 등록합니다. |
> | 실행력 | Microsoft.ClassicNetwork/reservedIps/delete | 예약된 IP를 삭제합니다. |
> | 실행력 | Microsoft.ClassicNetwork/reservedIps/join/action | 예약된 IP를 조인합니다. |
> | 실행력 | Microsoft.ClassicNetwork/reservedIps/link/action | 예약된 IP를 연결합니다. |
> | 실행력 | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | 예약된 IP의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicNetwork/reservedIps/read | 예약된 IP를 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/reservedIps/write | 예약된 IP를 새로 추가합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Virtual Network의 마이그레이션을 중단합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 기능을 표시합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | 가상 네트워크에서 지정된 IP 주소의 가용성을 확인합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Virtual Network의 마이그레이션을 커밋합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/delete | 가상 네트워크를 삭제합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | 클라이언트 인증서의 해지를 취소합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 해지된 클라이언트 인증서를 읽습니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | 클라이언트 인증서를 해지합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 가상 네트워크 게이트웨이 클라이언트 인증서를 삭제합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 지문으로 인증서를 다운로드합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 가상 네트워크 게이트웨이 인증서 패키지를 나열합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | 클라이언트 루트 인증서를 찾습니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 새 클라이언트 루트 인증서를 업로드합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | 사이트 간 게이트웨이 연결을 연결합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | 사이트 간 게이트웨이 연결을 끊습니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 연결 목록을 검색합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | 사이트 간 게이트웨이 연결을 테스트합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 가상 네트워크 게이트웨이를 삭제합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | 디바이스 구성 스크립트를 다운로드합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | 게이트웨이 진단을 다운로드합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 회로 서비스 키를 검색합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 가상 네트워크 게이트웨이 패키지를 나열합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 가상 네트워크 게이트웨이의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 가상 네트워크 게이트웨이 패키지를 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 가상 네트워크 게이트웨이를 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 가상 네트워크 게이트웨이에 대한 진단을 시작합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 가상 네트워크 게이트웨이에 대한 진단을 중지합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 가상 네트워크 게이트웨이를 추가합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/join/action | 가상 네트워크를 조인합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 가상 네트워크의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/peer/action | 가상 네트워크를 다른 가상 네트워크와 피어링합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Virtual Network의 마이그레이션을 준비합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/read | 가상 네트워크를 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 원격 가상 네트워크 피어링 프록시를 삭제합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 원격 가상 네트워크 피어링 프록시를 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 원격 가상 네트워크 피어링 프록시를 추가하거나 업데이트합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | 서브넷과 연결된 네트워크 보안 그룹을 삭제합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 네트워크 보안 그룹과 연결된 가상 네트워크 서브넷에 대한 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | 서브넷과 연결된 네트워크 보안 그룹을 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | 서브넷과 연결된 네트워크 보안 그룹을 추가합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Virtual Network의 마이그레이션 유효성을 검사합니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 가상 네트워크 피어링을 가져옵니다. |
> | 실행력 | Microsoft.ClassicNetwork/virtualNetworks/write | 새 가상 네트워크를 추가합니다. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ClassicStorage/capabilities/read | 기능을 표시합니다. |
> | 실행력 | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | 스토리지 계정의 가용성을 확인합니다. |
> | 실행력 | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | 스토리지 계정의 가용성을 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/disks/read | 스토리지 계정 디스크를 반환합니다. |
> | 실행력 | Microsoft.ClassicStorage/images/operationstatuses/read | 이미지 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/images/read | 이미지를 반환합니다. |
> | 실행력 | Microsoft.ClassicStorage/operations/read | 클래식 스토리지 작업을 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/osImages/read | 운영 체제 이미지를 반환합니다. |
> | 실행력 | Microsoft.ClassicStorage/osPlatformImages/read | 운영 체제 플랫폼 이미지를 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/publicImages/read | 공용 가상 컴퓨터 이미지를 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/quotas/read | 구독에 대한 할당량을 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/register/action | 클래식 스토리지에 등록합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | 스토리지 계정의 마이그레이션을 중단합니다. |
> | 실행력 | ClassicStorage/storageAccounts/blobServices/providers/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 실행력 | ClassicStorage/storageAccounts/blobServices/providers/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> | 실행력 | ClassicStorage/storageAccounts/blobServices/providers/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | 스토리지 계정의 마이그레이션을 커밋합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/delete | 스토리지 계정을 삭제합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/disks/delete | 지정된 스토리지 계정 디스크를 삭제합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | 리소스의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/disks/read | 스토리지 계정 디스크를 반환합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/disks/write | 스토리지 계정 디스크를 추가합니다. |
> | 실행력 | ClassicStorage/storageAccounts/fileServices/providers/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 실행력 | ClassicStorage/storageAccounts/fileServices/providers/Microsoft. Insights/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> | 실행력 | ClassicStorage/storageAccounts/fileServices/providers/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/images/delete | 지정된 장소 계정 이미지를 삭제합니다. (사용되지 않음, 대신 ‘Microsoft.ClassicStorage/storageAccounts/vmImages’ 사용) |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | 스토리지 계정 이미지 작업 상태를 반환합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/images/read | 스토리지 계정 이미지를 반환합니다. (사용되지 않음, 대신 ‘Microsoft.ClassicStorage/storageAccounts/vmImages’ 사용) |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | 리소스의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 지정된 스토리지 계정 운영 체제 이미지를 삭제합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/osImages/read | 스토리지 계정 운영 체제 이미지를 반환합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/osImages/write | 지정된 스토리지 계정 운영 체제 이미지를 추가합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | 스토리지 계정의 마이그레이션을 준비합니다. |
> | 실행력 | ClassicStorage/storageAccounts/providers/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 실행력 | ClassicStorage/storageAccounts/providers/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> | 실행력 | ClassicStorage/storageAccounts/providers/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> | 실행력 | ClassicStorage/storageAccounts/queueServices/providers/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 실행력 | ClassicStorage/storageAccounts/queueServices/providers/Microsoft. Insights/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> | 실행력 | ClassicStorage/storageAccounts/queueServices/providers/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/read | 지정된 계정의 스토리지 계정을 반환합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | 스토리지 계정에 대한 기존 액세스 키를 다시 생성합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/services/read | 사용 가능한 서비스를 가져옵니다. |
> | 실행력 | ClassicStorage/storageAccounts/tableServices/providers/diagnosticSettings/read | 진단 설정을 가져옵니다. |
> | 실행력 | ClassicStorage/storageAccounts/tableServices/providers/Microsoft. Insights/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> | 실행력 | ClassicStorage/storageAccounts/tableServices/providers/metricDefinitions/read | 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | 스토리지 계정의 마이그레이션 유효성을 검사합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 지정된 가상 머신 이미지를 삭제합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 지정된 가상 머신 이미지 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 가상 머신 이미지를 반환합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 지정된 가상 머신 이미지를 추가합니다. |
> | 실행력 | Microsoft.ClassicStorage/storageAccounts/write | 새 스토리지 계정을 추가합니다. |
> | 실행력 | Microsoft.ClassicStorage/vmImages/read | 가상 머신 이미지를 나열합니다. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | DataAction | Cognitiveservices account/accounts/AnomalyDetector/시계열////검색/작업 | 이 작업은 전체 계열을 사용 하 여 모델을 생성 합니다. 각 요소는 동일한 모델을 사용 하 여 검색 됩니다.<br>이 메서드를 사용 하면 특정 지점 전후에 점이 비정상 인지 여부를 확인 하는 데 사용 됩니다.<br>전체 검색을 통해 시계열의 전체 상태를 사용자에 게 제공할 수 있습니다. |
> | DataAction | Cognitiveservices account/accounts/AnomalyDetector/시계열/last/검색/작업 | 이 작업을 수행 하면 최신 요소 앞의 요소를 사용 하 여 모델을 생성 합니다. 이 메서드를 사용 하 여 대상 지점이 비정상 인지 여부를 확인 하는 데 기록 지점만 사용 됩니다. 최신 지점 검색은 비즈니스 메트릭의 실시간 모니터링 시나리오와 일치 합니다. |
> | DataAction | Cognitiveservices account/계정/Autosuggest/검색/작업 | 이 작업은 지정 된 쿼리 또는 부분 쿼리를 위한 제안을 제공 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | 이 작업은 이미지 콘텐츠를 기반으로 다양 한 시각적 기능 집합을 추출 합니다.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | 이 작업은 이미지의 가장 중요 한 영역 주위에 경계 상자를 반환 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | 이 작업을 수행 하면 전체 문장이 있는 사람이 읽을 수 있는 언어로 이미지에 대 한 설명이 생성 됩니다.<br> 설명은 작업에 의해 반환 되는 콘텐츠 태그의 컬렉션을 기반으로 합니다.<br>각 이미지에 대해 둘 이상의 설명을 생성할 수 있습니다.<br> 설명은 신뢰성 점수를 기준으로 정렬 됩니다.<br>모든 설명은 영어로 되어 있습니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | 이 작업은 지정 된 이미지에서 개체 검색을 수행 합니다.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | 이 작업을 수행 하면 사용자 지정 너비와 높이가 포함 된 미리 보기 이미지가 생성 됩니다.<br> 기본적으로 서비스는 이미지를 분석하고 ROI(관심 영역)를 식별하며 ROI를 기반으로 스마트 자르기 좌표를 생성합니다.<br> 스마트 자르기는 입력 이미지의 가로 세로 비율을 지정 하는 경우에 유용 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | 이 작업은 도메인별 모델을 적용 하 여 이미지 내에서 콘텐츠를 인식 합니다.<br> /모델 GET 요청을 사용 하 여 Computer Vision API에서 지 원하는 도메인별 모델 목록을 검색할 수 있습니다.<br> 현재이 API는 다음과 같은 도메인별 모델을 제공 합니다. 유명인, 랜드마크. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | 이 작업은 Computer Vision API에서 지 원하는 도메인별 모델 목록을 반환 합니다.  현재이 API는 다음과 같은 도메인별 모델 (유명인 인식기, 랜드마크 인식기)을 지원 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | OCR (광학 문자 인식)은 이미지의 텍스트를 검색 하 고 인식 된 문자를 컴퓨터에서 사용 가능한 문자 스트림으로 추출 합니다.    |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/읽기/분석/작업 | 이 인터페이스를 사용 하 여 텍스트를 많이 사용 하는 문서에 대해 최적화 된 최신 OCR (광학 문자 인식) 알고리즘을 사용 하 여 읽기 작업을 수행 합니다.<br>문서 작성, 인쇄 또는 혼합 문서를 처리할 수 있습니다.<br>Read 인터페이스를 사용 하는 경우 응답에 ' Operation-Location ' 이라는 헤더가 포함 됩니다.<br>' 작업-위치 ' 헤더에는 읽기 결과 가져오기 작업에서 OCR 결과에 액세스 하는 데 사용 해야 하는 URL이 포함 되어 있습니다. |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/읽기/analyzeresults/읽기 | 이 인터페이스를 사용 하 여 읽기 작업의 상태 및 OCR 결과를 검색 합니다.  ' OperationId '를 포함 하는 URL은 읽기 작업 ' 작업-위치 ' 응답 헤더에 반환 됩니다. |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/읽기/코어/a c a r/작업 | 이 인터페이스를 사용 하 여 최신 광학 문자를 사용 하는 일괄 읽기 파일 작업의 결과를 가져올 수 있습니다. |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/읽기/작업/읽기 | 이 인터페이스는 읽기 작업의 OCR 결과를 가져오는 데 사용 됩니다. 일괄 처리 읽기 파일 인터페이스에서 반환 된 <b>"작업 위치"</b> 필드에서이 인터페이스의 URL을 검색 해야 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | 이 인터페이스를 사용 하 여 텍스트 인식 작업의 결과를 가져옵니다. 텍스트 인식 인터페이스를 사용 하는 경우 응답에는 "작업-위치" 라는 필드가 포함 됩니다. "작업 위치" 필드에는 가져오기 텍스트 인식 작업 결과 작업에 사용 해야 하는 URL이 포함 되어 있습니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | 이 작업을 수행 하면 제공 된 이미지의 내용과 관련 된 단어 또는 태그 목록이 생성 됩니다.<br>Computer Vision API는 이미지에 있는 개체, 거실 생물, 장면 또는 작업을 기반으로 태그를 반환할 수 있습니다.<br>범주와 달리 태그는 계층적 분류 시스템에 따라 구성 되지 않지만 이미지 내용에 해당 합니다.<br>태그는 모호성을 방지 하거나 컨텍스트를 제공 하는 힌트를 포함할 수 있습니다. 예를 들어 "cello" 태그는 "음악 계측" 힌트와 함께 사용 될 수 있습니다.<br>모든 태그는 영어로 되어 있습니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | 이 인터페이스는 인식 텍스트 작업 결과를 가져오는 데 사용 됩니다. 이 인터페이스의 URL은 텍스트 인식 인터페이스에서 반환 된 <b>"작업 위치"</b> 필드에서 검색 해야 합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/imagelists/action | 이미지 목록을 만듭니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/imagelists/delete | 이미지 목록-삭제 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/imagelists/images/delete | 이미지 목록에서 이미지를 삭제 합니다. 이미지 목록은 이미지/일치 API를 사용 하는 경우 다른 이미지에 대 한 유사 일치를 수행 하는 데 사용할 수 있습니다. 목록에서 모든 이미지를 삭제 합니다. 이미지/일치 API를 사용 하는 경우 이미지 목록을 사용 하 여 다른 이미지와의 유사 일치를 수행할 수 있습니다. * |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/imagelists/images/read | 이미지-모든 이미지 Id 가져오기 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/imagelists/images/write | 이미지 목록에 이미지를 추가 합니다. 이미지 목록은 이미지/일치 API를 사용 하는 경우 다른 이미지에 대 한 유사 일치를 수행 하는 데 사용할 수 있습니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/imagelists/read | 이미지 목록-세부 정보 가져오기-이미지 목록-모두 가져오기 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/imagelists/refreshindex/action | 이미지 목록-검색 인덱스 새로 고침 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/imagelists/write | 이미지 목록-업데이트 세부 정보 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/processimage/평가/작업 | 외설 또는 성인 콘텐츠를 포함 하는 이미지의 확률을 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/processimage/findfaces/작업 | 이미지에서 얼굴을 찾습니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/processimage/match/action | Fuzzily 사용자 지정 이미지 목록 중 하나에 대해 이미지를 일치 시킵니다.<br>이 API를 사용 하 여 사용자 지정 이미지 목록을 만들고 관리할 수 있습니다.<br> |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/processimage/ocr/action | 지정 된 언어의 이미지에 있는 모든 텍스트를 반환 합니다. 입력에 언어가 지정 되지 않은 경우 검색의 기본값은 영어입니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/processtext/detectlanguage/action | 이 작업을 수행 하면 지정 된 입력 콘텐츠의 언어가 검색 됩니다.<br>전송 된 텍스트를 구성 하는 언어에 대 한 ISO 639-3 코드를 반환 합니다.<br>110 이상의 언어가 지원 됩니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/processtext/화면/작업 | 이 작업은 100개가 넘는 언어의 비속어를 감지하고 사용자 지정 및 공유 블랙리스트와 대조합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/작업/작업 | 이 엔드포인트에 게시 된 이미지 콘텐츠에 대 한 작업 Id가 반환 됩니다.  |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/작업/읽기 | 작업 Id에 대 한 작업 세부 정보를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/팀/리뷰/accesskey/read | 팀에 대 한 콘텐츠 검토 선택 키를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/리뷰/작업 | 만든 리뷰는 팀의 검토자에 게 표시 됩니다. 검토자가 검토를 완료 하면 지정 된 콜백 엔드포인트에서 검토 결과가 게시 됩니다 (즉, HTTP POST). |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/리뷰/프레임/읽기 | *NotDefined* |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/리뷰/프레임/쓰기 | 비디오 검토를 위한 프레임을 추가 하려면이 메서드를 사용 합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/리뷰/게시/작업 | 비디오 검토는 게시 되지 않은 상태로 처음 생성 됩니다. 즉, 팀의 검토자가 아직 검토할 수 없습니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/리뷰/읽기 | 전달 된 검토 Id에 대 한 검토 세부 정보를 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/팀/리뷰/성적/작업 | 이 API는 비디오 검토에 대 한 기록 파일 (비디오에서 말하는 모든 단어의 텍스트 버전)을 추가 합니다. 파일은 올바른 WebVTT 형식 이어야 합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/리뷰/transcriptmoderationresult/작업 | 이 API는 비디오 검토를 위한 기록 화면 텍스트 결과 파일을 추가 합니다. 기록 화면 텍스트 결과 파일은 화면 텍스트 API의 결과입니다. 녹음/기록 화면 텍스트 결과 파일을 생성 하려면 화면 텍스트 API를 사용 하 여 사용이 금지 된 기록 파일을 차단 해야 합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/설정/템플릿/삭제 | 팀에서 템플릿 삭제 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/설정/템플릿/읽기 | 이 팀에서 프로 비전 된 검토 템플릿의 배열을 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/설정/템플릿/쓰기 | 지정 된 템플릿을 만들거나 업데이트 합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/워크플로/읽기 | 팀의 특정 워크플로에 대 한 세부 정보를 가져옵니다. 팀에 사용할 수 있는 모든 워크플로 가져오기 * |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/팀/워크플로/쓰기 | 새 워크플로를 만들거나 기존 워크플로를 업데이트 합니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/termlists/작업 | 용어 목록을 만듭니다. |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/termlists/대량 업데이트/작업 | 용어 목록-대량 업데이트 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/termlists/delete | 용어 목록-삭제 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator 사람/termlists/읽기 | 용어 목록-모든 용어 목록 가져오기-세부 정보 가져오기 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/termlists/refreshindex/action | 용어 목록-검색 인덱스 새로 고침 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/termlists/사용 약관/삭제 | 용어 삭제-용어 삭제-모든 용어 삭제 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/termlists/약관/읽기 | 용어-모든 용어 가져오기 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/termlists/사용 약관/쓰기 | 용어-용어 추가 |
> | DataAction | Cognitiveservices account/accounts/ContentModerator/termlists/write | 용어 목록-업데이트 세부 정보 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/분류/반복/이미지/작업 | 이미지를 분류 하 고 결과를 저장 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/분류/반복/이미지/nostore/작업 | 결과를 저장 하지 않고 이미지를 분류 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/분류/반복/u r l/작업 | 이미지 url을 분류 하 고 결과를 저장 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/분류/반복/u r l/nostore/작업 | 결과를 저장 하지 않고 이미지 url을 분류 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/검색/반복/이미지/작업 | 이미지에서 개체를 검색 하 고 결과를 저장 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/검색/반복/이미지/nostore/작업 | 결과를 저장 하지 않고 이미지에서 개체를 검색 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/검색/반복/u r l/작업 | 이미지 url에서 개체를 검색 하 고 결과를 저장 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/검색/반복/u r l/nostore/작업 | 결과를 저장 하지 않고 이미지 url에서 개체를 검색 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/도메인/읽기 | 특정 도메인에 대 한 정보를 가져옵니다. 사용 가능한 도메인 목록을 가져옵니다. * |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/레이블 제안/설정/작업 | 레이블 제안의 풀 크기를 설정 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/레이블 제안/설정/읽기 | 이 프로젝트에 대 한 레이블 제안의 풀 크기를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/마이그레이션/작업 | *NotDefined* |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/작업 | 프로젝트를 만듭니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/삭제 | 특정 프로젝트를 삭제 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/작업 | 이 API는 본문 콘텐츠를 다중 파트/양식 데이터 및 응용 프로그램/8 진수 스트림으로 받아들입니다. Multipart를 사용 하는 경우 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/삭제 | 학습 이미지 집합에서 이미지를 삭제 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/파일/작업 | 이 API는 파일의 일괄 처리와 필요에 따라 태그를 적용 하 여 이미지를 만듭니다. 최대 64개의 이미지와 20개의 태그를 만들 수 있습니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/i d/읽기 | 이 API는 지정 된 태그 및 선택적으로 반복에 대 한 이미지 집합을 반환 합니다. 반복이 지정 되지 않은 경우 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/예측/작업 | 이 API는 지정 된 예측 이미지에서 이미지 일괄 처리를 만듭니다. 최대 64개의 이미지와 20개의 태그를 만들 수 있습니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/지역 제안/작업 | 이 API는 지역에 대 한 confidences 함께 이미지에 대 한 지역 제안서를 가져옵니다. 제안을 찾을 수 없는 경우 빈 배열을 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/지역/작업 | 이 API는 영역 정보를 사용 하 여 기존 이미지를 업데이트 하기 위해 이미지 영역의 일괄 처리 및 필요에 따라 태그를 허용 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/지역/삭제 | 이미지 영역 집합을 삭제 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/제안/작업 | 이 API는 제안 된 태그 Id로 필터링 된 태그 없는 이미지를 페치합니다. 이미지를 찾을 수 없는 경우 빈 배열을 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/제안/개수/작업 | 이 API는 태그 Id를 사용 하 여 지정 된 임계값에 대해 제안 된 태그 마다 태그가 지정 된 이미지 수를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/태그/개수/읽기 | 필터링이 and/or 관계에 있습니다. 예를 들어 제공 된 태그 id가 "Dog" 및 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/태그 지정/읽기 | 이 API는 일괄 처리 및 범위 선택을 지원 합니다. 기본적으로 이미지와 일치 하는 첫 번째 50 이미지만 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/태그/작업 | 이미지 집합과 태그 집합을 연결 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/태그/삭제 | 이미지 집합에서 태그 집합을 제거 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/태그 없음/개수/읽기 | 이 API는 지정 된 프로젝트에 대 한 태그가 없는 이미지 및 선택적으로 반복을 반환 합니다. 반복이 지정 되지 않은 경우 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/태그 없음/읽기 | 이 API는 일괄 처리 및 범위 선택을 지원 합니다. 기본적으로 이미지와 일치 하는 첫 번째 50 이미지만 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/이미지/u r i/작업 | 이 API는 url의 일괄 처리와 필요에 따라 태그를 적용 하 여 이미지를 만듭니다. 최대 64개의 이미지와 20개의 태그를 만들 수 있습니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/삭제 | 프로젝트의 특정 반복을 삭제 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/내보내기/작업 | 학습 된 반복을 내보냅니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/내보내기/읽기 | 특정 반복에 대 한 내보내기 목록을 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/성능/이미지/개수/읽기 | 필터링이 and/or 관계에 있습니다. 예를 들어 제공 된 태그 id가 "Dog" 및 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/성능/이미지/읽기 | 이 API는 일괄 처리 및 범위 선택을 지원 합니다. 기본적으로 이미지와 일치 하는 첫 번째 50 이미지만 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/성능/읽기 | 반복에 대 한 자세한 성능 정보를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/게시/작업 | 특정 반복을 게시 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/게시/삭제 | 특정 반복의 게시를 취소 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/읽기 | 특정 반복을 가져옵니다. 프로젝트에 대 한 반복을 가져옵니다. * |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/반복/쓰기 | 특정 반복을 업데이트 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/예측/삭제 | 예측 이미지 및 관련 예측 결과 집합을 삭제 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/예측/쿼리/동작 | 예측 엔드포인트으로 전송 된 이미지를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/quicktest/image/action | 이미지를 빠르게 테스트 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/quicktest/u r l/작업 | 이미지 url을 빠르게 테스트 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/읽기 | 특정 프로젝트를 가져옵니다. 프로젝트를 가져옵니다. * |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/태그/동작 | 프로젝트에 대 한 태그를 만듭니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/태그/삭제 | 프로젝트에서 태그를 삭제 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/태그/읽기 | 특정 태그에 대 한 정보를 가져옵니다. 지정 된 프로젝트 및 반복에 대 한 태그를 가져옵니다. * |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/태그/쓰기 | 태그를 업데이트 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/tagsandregions/제안/작업 | 이 API는 태그에 대 한 confidences와 함께 태그가 없는 이미지의 배열/일괄 처리에 대 한 제안 된 태그 및 영역을 가져옵니다. 태그를 찾을 수 없는 경우 빈 배열을 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/학습/작업 | 학습을 위한 큐 프로젝트입니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/프로젝트/쓰기 | 특정 프로젝트를 업데이트 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/할당량/작업 | *NotDefined* |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/할당량/삭제 | *NotDefined* |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/할당량/새로 고침/쓰기 | *NotDefined* |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/사용량/예측/사용자/읽기 | Oxford 사용자에 대 한 예측 리소스 사용량 가져오기 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/사용량/학습/리소스/계층/읽기 | Azure 사용자에 대 한 학습 리소스 사용량 가져오기 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/사용량/학습/사용자/읽기 | Oxford 사용자에 대 한 학습 리소스 사용량 가져오기 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/사용자/작업 | *NotDefined* |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/사용자/삭제 | *NotDefined* |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/사용자/상태/쓰기 | 사용자 상태 업데이트 |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/사용자/계층/쓰기 | *NotDefined* |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/사용자/읽기 | *NotDefined* |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/허용 목록/삭제 | 특정 기능을 가진 허용 목록 사용자를 삭제 합니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/허용 목록/읽기 | 특정 기능을 가진 허용 목록 사용자 목록을 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/CustomVision. 예측/허용 목록/쓰기 | 특정 기능을 사용 하 여 허용 목록에서 사용자를 업데이트 하거나 만듭니다. |
> | 실행력 | Microsoft.CognitiveServices/accounts/delete | API 계정을 삭제합니다. |
> | DataAction | Cognitiveservices account/accounts/EntitySearch/search/action | 엔터티를 가져오고 지정 된 쿼리에 대 한 결과를 저장 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | 이미지에서 인간 얼굴을 감지 하 고, 얼굴 사각형을 반환 하 고, 선택적으로 faceIds, 랜드마크 및 특성을 사용 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | 지정 된 얼굴 목록을 삭제 합니다. 얼굴 목록에서 관련 된 얼굴 이미지도 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | FaceListId 및 persisitedFaceId를 지정 하 여 얼굴 목록에서 얼굴을 삭제 합니다. 관련 된 얼굴 이미지도 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | 지정 된 얼굴 목록에 얼굴을 최대 1000 면까지 추가 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | 얼굴 목록에서 얼굴 목록 faceListId, 이름, userData 및 얼굴을 검색 합니다. 목록에서 ' faceListId, name, userData '를 나열 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | 사용자 지정 faceListId, 이름 및 userData (선택 사항)를 사용 하 여 빈 얼굴 목록을 만듭니다. 이름 및 userData를 비롯 한 얼굴 목록의 업데이트 정보는 최대 64 얼굴 목록에 허용 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | 쿼리 표면의 faceId를 지정 하 여 faceId 배열, 얼굴 목록 또는 커다란 얼굴 목록에서 비슷한 모양의 얼굴을 검색 합니다. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | 얼굴 유사성을 기준으로 후보 얼굴을 그룹으로 나눕니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | 일대다 그룹 또는 대기업에서 특정 쿼리 사용자에 게 가장 가까운 일치 항목을 찾는 데 대 한 일대다 id입니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | 지정 된 많은 얼굴 목록을 삭제 합니다. 또한 많은 면 목록에 있는 관련 된 얼굴 이미지가 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | LargeFaceListId 및 persisitedFaceId를 지정 하 여 많은 얼굴 목록에서 얼굴을 삭제 합니다. 관련 된 얼굴 이미지도 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | LargeFaceListId 및 persistedFaceId로 많은 얼굴 목록에서 지속형 얼굴을 검색 합니다. 지정 된 많은 얼굴 목록에서 ' persistedFaceId 및 userData '를 나열 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | 지정 된 많은 얼굴 목록에 얼굴을 최대 100만 면까지 추가 합니다. 많은 얼굴 목록에서 지정 된 얼굴의 userData 필드를 persistedFaceId로 업데이트 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | 많은 얼굴 목록의 largeFaceListId, name, userData를 검색 합니다. LargeFaceListId, 이름 및 userData의 정보를 나열 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | 커다란 얼굴 목록 교육 작업을 제출 합니다. 교육은 학습 된 많은 얼굴 목록 에서만 사용할 수 있는 중요 한 단계입니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | 커다란 얼굴 목록 학습 상태를 완료 또는 계속 진행 중으로 확인 합니다. LargeFaceList 교육은 비동기 작업입니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | 사용자 지정 largeFaceListId, 이름 및 userData (선택 사항)를 사용 하 여 빈 긴 얼굴 목록을 만듭니다. 이름 및 userData를 포함 하 여 많은 얼굴 목록의 정보를 업데이트 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | 지정 된 personGroupId를 사용 하 여 기존 large person 그룹을 삭제 합니다. 이 많은 사용자 그룹의 지속형 데이터가 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | 지정 된 large person 그룹에 새 사용자를 만듭니다. 이 사용자에 게 얼굴을 추가 하려면 다음을 호출 하세요. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | 대량 사용자 그룹에서 기존 사용자를 삭제 합니다. 사용자 항목의 저장 된 모든 사용자 데이터와 얼굴 이미지가 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | 대기업의 사용자에서 얼굴을 삭제 합니다. 이 얼굴 항목과 관련 된 얼굴 데이터 및 이미지도 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | 사용자 얼굴 정보를 검색 합니다. 지속형 사용자 face는 해당 largePersonGroupId, personId 및 persistedFaceId에 의해 지정 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | 얼굴 식별 또는 확인을 위해 개인에 게 얼굴 이미지를 추가 합니다. 사용자가 저장 한 면의 userData 필드를 업데이트 하는 이미지를 처리 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | 사용자의 이름과 userData를 검색 하 고, 등록 된 사용자 얼굴 이미지를 나타내는 지속형 faceIds을 검색 합니다. PersonId, name, userData 및 persistedFaceIds를 포함 하 여 지정 된 large person 그룹의 모든 사람 정보를 나열 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | 개인의 이름 또는 userData를 업데이트 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | 이름 및 userData를 포함 하 여 large person 그룹의 정보를 검색 합니다. 이 API는 기존의 많은 개인 그룹의 largePesonGroupId, 이름 및 userData를 모두 반환 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | 대량 사용자 그룹 학습 작업을 제출 합니다. 교육은 학습 된 많은 사용자 그룹에서 사용할 수 있는 중요 한 단계입니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | 대량 사용자 그룹의 학습 상태를 완료 또는 계속 진행 중으로 확인 합니다. LargePersonGroup 교육은 비동기 작업입니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | 사용자가 지정한 largePersonGroupId, 이름 및 선택적 userData를 사용 하 여 새 large person 그룹을 만듭니다. 기존 large person 그룹의 이름 및 userData를 업데이트 합니다. 요청 본문에 없는 속성은 변경 되지 않은 상태로 유지 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | 지정 된 personGroupId를 사용 하 여 기존 사용자 그룹을 삭제 합니다. 이 사용자 그룹의 지속형 데이터가 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | 지정 된 사용자 그룹에 새 사용자를 만듭니다. 이 사용자에 게 얼굴을 추가 하려면 다음을 호출 하세요. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | 사용자 그룹에서 기존 사용자를 삭제 합니다. 사용자 항목의 저장 된 모든 사용자 데이터와 얼굴 이미지가 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | 개인 그룹의 사람에서 얼굴을 삭제 합니다. 이 얼굴 항목과 관련 된 얼굴 데이터 및 이미지도 삭제 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | 사용자 얼굴 정보를 검색 합니다. 지속형 사용자 face는 해당 personGroupId, personId 및 persistedFaceId에 의해 지정 됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | 얼굴 식별 또는 확인을 위해 개인 그룹에 얼굴 이미지를 추가 합니다. 여러 업데이트의 이미지를 처리 하려면 개인 저장 된 면의 userData 필드를 사용 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | 사용자의 이름과 userData를 검색 하 고, 등록 된 사용자 얼굴 이미지를 나타내는 지속형 faceIds을 검색 합니다. PersonId, name, userData 및 persistedFaceIds를 포함 하 여 지정 된 사용자 그룹의 모든 사람 정보를 나열 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | 개인의 이름 또는 userData를 업데이트 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | 사용자 그룹 이름 및 userData를 검색 합니다. 이 personGroup에 대 한 사용자 정보를 가져오려면 사용자 그룹의 pesonGroupId, 이름 및 userData를 사용 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | 개인 그룹 학습 작업을 제출 합니다. 학습은 학습 된 사용자 그룹에만 사용할 수 있는 중요 한 단계입니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | 사용자 그룹 학습 상태를 완료 또는 계속 진행 중으로 확인 합니다. PersonGroup 교육은 트리거된 비동기 작업입니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | 지정 된 personGroupId, 이름 및 사용자가 제공한 userData를 사용 하 여 새 person 그룹을 만듭니다. 기존 사용자 그룹의 이름 및 userData를 업데이트 합니다. 요청 본문에 없는 속성은 변경 되지 않은 상태로 유지 됩니다. |
> | DataAction | Cognitiveservices account/계정/얼굴/스냅숏/적용/작업 | 사용자 지정 개체 id를 제공 하 여 스냅숏을 적용 합니다. |
> | DataAction | Cognitiveservices account/계정/얼굴/스냅숏/삭제 | 스냅숏을 삭제 합니다. |
> | DataAction | Cognitiveservices account/계정/얼굴/스냅숏/읽기 | 스냅숏 작업의 상태를 가져옵니다. |
> | DataAction | Cognitiveservices account/계정/얼굴/스냅숏/사용/작업 | 개체에 대 한 스냅숏을 만듭니다. |
> | DataAction | Cognitiveservices account/계정/얼굴/스냅숏/쓰기 | 스냅숏의 속성을 업데이트 합니다. |
> | DataAction | Cognitiveservices account/accounts/Face/스냅숏/읽기 | 정보를 사용 하 여 모든 사용자의 액세스 가능한 스냅숏을 나열 합니다. * |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | 두 얼굴이 동일한 사람에 게 속하는지 또는 한 얼굴이 사람에 게 속하는지 여부를 확인 합니다. |
> | DataAction | Cognitiveservices account/계정/폼 인식기/사용자 지정/모델/분석/작업 | 지정 된 문서에서 키-값 쌍을 추출 합니다. 입력 문서는 지원 되는 콘텐츠 형식 ' application/pdf ', ' image/jpeg ' 또는 ' image/png ' 중 하나 여야 합니다. 성공 응답이 JSON으로 반환됩니다. |
> | DataAction | Cognitiveservices account/계정/폼 인식기/사용자 지정/모델/삭제 | 모델 아티팩트를 삭제 합니다. |
> | DataAction | Cognitiveservices account/계정/폼 인식기/사용자 지정/모델/키/읽기 | 모델에 대 한 키를 검색 합니다. |
> | DataAction | Cognitiveservices account/계정/폼 인식기/사용자 지정/모델/읽기 | 모델에 대 한 정보를 가져옵니다. 학습 된 모든 사용자 지정 모델에 대 한 정보 가져오기 * |
> | DataAction | Cognitiveservices account/계정/폼 인식기/사용자 지정/학습/작업 | 사용자 지정 모델을 만들고 학습 합니다.<br>학습 요청은 외부에서 액세스할 수 있는 Azure Storage blob 컨테이너 Uri (가급적 공유 액세스 서명 Uri) 이거나 로컬로 탑재 된 드라이브의 데이터 폴더에 대 한 유효한 경로인 원본 매개 변수를 포함 해야 합니다.<br>로컬 경로를 지정 하는 경우 해당 경로는 Linux/Unix 경로 형식을 따라야 하며 입력 탑재 구성의 루트로 지정 된 절대 경로 여야 합니다. |
> | DataAction | Cognitiveservices account/accounts/FormRecognizer/미리 작성/수신/s t a t e r/asyncbatchanalyze/action | 지정 된 수신 문서에서 필드 텍스트 및 의미 체계 값을 추출 합니다. 입력 이미지 문서는 지원 되는 콘텐츠 형식 (JPEG, PNG, BMP, PDF 또는 TIFF) 중 하나 여야 합니다. 성공 응답은 ' Operation-Location ' 이라는 필드를 포함 하는 JSON입니다 .이 필드에는 결과를 비동기적으로 검색 하기 위한 수신 확인 결과 가져오기 작업의 URL이 포함 되어 있습니다. |
> | DataAction | Cognitiveservices account/계정/폼 인식기/미리 작성/수신/작업/작업 | 상태를 쿼리하고 수신 분석 작업의 결과를 검색 합니다. 이 인터페이스의 URL은 분석 수신 응답의 ' 작업-위치 ' 헤더에서 가져올 수 있습니다. |
> | DataAction | Cognitiveservices account/accounts/ImageSearch/details/action | 이미지를 포함 하는 웹 페이지와 같은 이미지에 대 한 정보를 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/ImageSearch/search/action | 지정 된 쿼리에 대 한 관련 이미지를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/ImageSearch/추세/작업 | 현재 추세 이미지를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/ImmersiveReader/getcontentmodelforreader/action | 몰입 형 판독기 세션을 만듭니다. |
> | DataAction | Cognitiveservices account/계정/InkRecognizer/인식/동작 | 스트로크 데이터 집합이 지정 된 경우 콘텐츠를 분석 하 고 인식 된 텍스트를 포함 하 여 인식 된 엔터티 목록을 생성 합니다. |
> | 실행력 | Microsoft.CognitiveServices/accounts/listKeys/action | 키를 나열합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | 지정 된 쿼리에 대 한 게시 된 엔드포인트 예측을 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/NewsSearch/categorysearch/action | 제공 된 범주에 대 한 뉴스를 반환 합니다. |
> | DataAction | Cognitiveservices account/계정/NewsSearch/검색/작업 | 지정 된 쿼리와 관련 된 뉴스 기사를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/NewsSearch/trendingtopics/action | Bing에서 식별 하는 추세 항목을 가져옵니다. 이러한 항목은 Bing 홈 페이지의 아래쪽에 있는 배너에 표시 되는 것과 같습니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/변경/읽기 | 런타임에 변경 사항을 다운로드 합니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/변경/쓰기 | 변경 데이터를 바꿉니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/endpointkeys/read | 엔드포인트에 대 한 엔드포인트 키를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/endpointkeys/refreshkeys/작업 | 엔드포인트 키를 다시 생성 합니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/endpointsettings/read | 엔드포인트에 대 한 엔드포인트 설정을 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/endpointsettings/write | 엔드포인트에 대 한 seettings 엔드포인트을 업데이트 합니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/기술 자료/create/write | 새 기술 자료를 만드는 비동기 작업입니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/기술 자료/delete | 기술 자료 및 모든 해당 데이터를 삭제 합니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/기술 자료/다운로드/읽기 | 기술 자료를 다운로드 하세요. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/기술 자료/generateanswer/action | 기술 자료를 쿼리 하기 위한 GenerateAnswer 호출입니다. |
> | DataAction | Cognitiveservices account/계정/QnAMaker/기술 자료/게시/작업 | 기술 자료의 테스트 인덱스에 있는 모든 변경 내용을 해당 prod 인덱스에 게시 합니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/기술 자료/read | 특정 knowledgebaser의 기술 자료 또는 세부 정보 목록을 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/기술 자료/학습/작업 | 기술 자료에 제안 사항을 추가 하기 위해 호출을 학습 합니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/기술 자료/write | 기술 자료를 수정 하거나 기술 자료 콘텐츠를 교체 하는 비동기 작업입니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/작업/읽기 | 특정 장기 실행 작업에 대한 세부 정보를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/QnAMaker/root/action | QnA Maker |
> | 실행력 | Microsoft.CognitiveServices/accounts/read | API 계정을 읽습니다. |
> | 실행력 | Microsoft.CognitiveServices/accounts/regenerateKey/action | 키를 다시 생성합니다. |
> | 실행력 | Microsoft.CognitiveServices/accounts/skus/read | 기존 리소스에 대한 사용 가능한 SKU를 읽습니다. |
> | DataAction | Cognitiveservices account/accounts/SpellCheck/SpellCheck/action | GET 또는 POST를 통해 맞춤법 검사 쿼리 결과를 가져옵니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | API는 지정 된 문서에서 알려진 엔터티와 일반적인 명명 된 엔터티 (\"Person\", \"Location\", \"조직\" 등) 목록을 반환 합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | 이 API는 입력 텍스트의 핵심 요지를 나타내는 문자열 목록을 추출합니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | 이 API는 검색된 언어 및 0~1 사이의 숫자 점수를 추출합니다. 점수가 1에 가까울수록 식별된 언어가 true라는 100% 확실성을 나타냅니다. 총 120개의 언어가 지원됩니다. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | 이 API는 0~1 사이의 숫자 점수를 반환합니다.<br>1에 가까운 점수는 긍정적인 정서를 나타내고, 0에 가까운 점수는 부정적인 정서를 나타냅니다.<br>점수 0.5은 감정 (예:<br>factoid 문). |
> | 실행력 | Microsoft.CognitiveServices/accounts/usages/read | 기존 리소스에 대한 할당량 사용량을 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/VideoSearch/details/action | 관련 비디오와 같은 비디오에 대 한 정보를 얻습니다. |
> | DataAction | Cognitiveservices account/계정/비디오 검색/검색/작업 | 지정 된 쿼리와 관련 된 비디오를 가져옵니다. |
> | DataAction | Cognitiveservices account/accounts/VideoSearch/추세/작업 | 현재 인기 있는 비디오를 받으세요. |
> | DataAction | Cognitiveservices account/accounts/VisualSearch/search/action | 제공 된 이미지와 관련 된 태그 목록을 반환 합니다. |
> | DataAction | Cognitiveservices account/accounts/WebSearch/search/action | 지정 된 쿼리에 대 한 웹, 이미지, 뉴스 & 비디오 결과를 가져옵니다. |
> | 실행력 | Microsoft.CognitiveServices/accounts/write | API 계정을 씁니다. |
> | 실행력 | Microsoft.CognitiveServices/checkDomainAvailability/action | 구독에 대해 사용 가능한 Sku를 읽습니다. |
> | 실행력 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 구독에 대해 사용 가능한 Sku를 읽습니다. |
> | 실행력 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 구독에 대해 사용 가능한 Sku를 읽습니다. |
> | 실행력 | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | VirtualNetworks 또는 서브넷을 삭제 하는 Microsoft 네트워크의 알림입니다. |
> | 실행력 | Cognitiveservices account/위치/operationresults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft.CognitiveServices/Operations/read | 사용 가능한 모든 작업을 나열합니다. |
> | 실행력 | Microsoft.CognitiveServices/register/action | Cognitive Services에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.CognitiveServices/register/action | Cognitive Services에 대한 구독을 등록합니다. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Commerce/RateCard/read | 지정된 구독에 대한 제안 데이터, 리소스/미터 메타데이터 및 요율을 제공합니다. |
> | 실행력 | Microsoft. 상거래용/register/action | Microsoft Commerce UsageAggregate에 대 한 구독 등록 |
> | 실행력 | Microsoft. 상거래/등록 취소/작업 | Microsoft Commerce UsageAggregate에 대 한 구독 등록 취소 |
> | 실행력 | Microsoft.Commerce/UsageAggregates/read | 구독별 Microsoft Azure 소비를 검색합니다. 결과에는 특정 시간 범위의 집계 사용량 현황 데이터, 구독 및 리소스 관련 정보가 포함됩니다. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Compute/availabilitySets/delete | 가용성 집합을 삭제합니다. |
> | 실행력 | Microsoft.Compute/availabilitySets/read | 가용성 집합의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/availabilitySets/vmSizes/read | 가용성 집합에서 가상 머신을 만들거나 업데이트하기 위한 사용 가능 크기를 나열합니다. |
> | 실행력 | Microsoft.Compute/availabilitySets/write | 새 가용성 집합을 만들거나 기존 가용성 집합을 업데이트합니다. |
> | 실행력 | Microsoft. Compute/Disk를 설정/삭제 | 디스크 암호화 집합 삭제 |
> | 실행력 | Microsoft. Compute/Disk를 설정/읽기 | 디스크 암호화 집합의 속성을 가져옵니다. |
> | 실행력 | Microsoft. Compute/Disk/Set/write | 새 디스크 암호화 집합 만들기 또는 기존 항목 업데이트 |
> | 실행력 | Microsoft.Compute/disks/beginGetAccess/action | Blob 액세스에 대한 디스크의 SAS URI를 가져옵니다. |
> | 실행력 | Microsoft.Compute/disks/delete | 디스크를 삭제합니다. |
> | 실행력 | Microsoft.Compute/disks/endGetAccess/action | 디스크의 SAS URI를 취소합니다. |
> | 실행력 | Microsoft.Compute/disks/read | 디스크의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/disks/write | 새 디스크를 만들거나 기존 디스크를 업데이트합니다. |
> | 실행력 | Microsoft. Compute/갤러리/응용 프로그램/삭제 | 갤러리 응용 프로그램을 삭제 합니다. |
> | 실행력 | Microsoft. Compute/갤러리/응용 프로그램/읽기 | 갤러리 응용 프로그램의 속성을 가져옵니다. |
> | 실행력 | Microsoft. Compute/갤러리/응용 프로그램/버전/삭제 | 갤러리 응용 프로그램 버전을 삭제 합니다. |
> | 실행력 | Microsoft. Compute/갤러리/응용 프로그램/버전/읽기 | 갤러리 응용 프로그램 버전의 속성을 가져옵니다. |
> | 실행력 | Microsoft. Compute/갤러리/응용 프로그램/버전/쓰기 | 새 갤러리 응용 프로그램 버전을 만들거나 기존 갤러리 응용 프로그램 버전을 업데이트 합니다. |
> | 실행력 | Microsoft. Compute/갤러리/응용 프로그램/쓰기 | 새 갤러리 응용 프로그램을 만들거나 기존 갤러리 응용 프로그램을 업데이트 합니다. |
> | 실행력 | Microsoft.Compute/galleries/delete | 갤러리를 삭제합니다. |
> | 실행력 | Microsoft.Compute/galleries/images/delete | 갤러리 이미지를 삭제합니다. |
> | 실행력 | Microsoft.Compute/galleries/images/read | 갤러리 이미지의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/galleries/images/versions/delete | 갤러리 이미지 버전을 삭제합니다. |
> | 실행력 | Microsoft.Compute/galleries/images/versions/read | 갤러리 이미지 버전의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/galleries/images/versions/write | 새 갤러리 이미지 버전을 만들거나 기존 갤러리 이미지 버전을 업데이트합니다. |
> | 실행력 | Microsoft.Compute/galleries/images/write | 새 갤러리 이미지를 만들거나 기존 갤러리 이미지를 업데이트합니다. |
> | 실행력 | Microsoft.Compute/galleries/read | 갤러리의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/galleries/write | 새 갤러리를 만들거나 기존 갤러리를 업데이트합니다. |
> | 실행력 | Microsoft.Compute/hostGroups/delete | 호스트 그룹을 삭제 합니다. |
> | 실행력 | Microsoft. Compute/hostGroups/hosts/delete | 호스트를 삭제 합니다. |
> | 실행력 | Microsoft. Compute/hostGroups/hosts/read | 호스트의 속성을 가져옵니다. |
> | 실행력 | Microsoft. Compute/hostGroups/hosts/write | 새 호스트를 만들거나 기존 호스트를 업데이트 합니다. |
> | 실행력 | Microsoft.Compute/hostGroups/read | 호스트 그룹의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/hostGroups/write | 새 호스트 그룹을 만들거나 기존 호스트 그룹을 업데이트 합니다. |
> | 실행력 | Microsoft.Compute/images/delete | 이미지를 삭제합니다. |
> | 실행력 | Microsoft.Compute/images/read | 이미지의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/images/write | 새 이미지를 만들거나 기존 이미지를 업데이트합니다. |
> | 실행력 | Microsoft.Compute/locations/capsOperations/read | 비동기 Caps 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/diskOperations/read | 비동기 디스크 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | 제한 진단을 지원하기 위해 시간 간격별로 총 요청을 표시할 로그를 만듭니다. |
> | 실행력 | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | ResourceName, OperationName 또는 적용된 제한 정책에서 그룹화한 제한된 요청의 집계를 표시할 로그를 만듭니다. |
> | 실행력 | Microsoft.Compute/locations/operations/read | 비동기 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | 플랫폼 이미지 제품의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | 플랫폼 이미지 Sku의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | 플랫폼 이미지 버전의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/publishers/artifacttypes/types/read | VMExtension 형식의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | VMExtension 버전의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/publishers/read | 게시자의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/runCommands/read | 위치에 사용할 수 있는 실행 명령을 나열합니다. |
> | 실행력 | Microsoft.Compute/locations/usages/read | 한 위치에 있는 구독의 Compute 리소스에 대한 서비스 제한 및 현재 사용 수량을 가져옵니다. |
> | 실행력 | Microsoft.Compute/locations/vmSizes/read | 한 위치에서 사용 가능한 가상 머신 크기를 나열합니다. |
> | 실행력 | Microsoft. Compute/위치/vsmOperations/읽기 | 가상 머신 런타임 서비스 확장을 사용 하 여 가상 머신 확장 집합에 대 한 비동기 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Compute/operations/read | Microsoft.Compute 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | 실행력 | Microsoft.Compute/proximityPlacementGroups/delete | 근접 배치 그룹을 삭제 합니다. |
> | 실행력 | Microsoft.Compute/proximityPlacementGroups/read | 근접 배치 그룹의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/proximityPlacementGroups/write | 새 근접 배치 그룹을 만들거나 기존 항목을 업데이트 합니다. |
> | 실행력 | Microsoft.Compute/register/action | Microsoft.Compute 리소스 공급자에 구독을 등록합니다. |
> | 실행력 | Microsoft.Compute/restorePointCollections/delete | 복원 지점 컬렉션 및 포함된 복원 지점을 삭제합니다. |
> | 실행력 | Microsoft.Compute/restorePointCollections/read | 복원 지점 컬렉션의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/restorePointCollections/restorePoints/delete | 복원 지점을 삭제합니다. |
> | 실행력 | Microsoft.Compute/restorePointCollections/restorePoints/read | 복원 지점의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Blob SAS URI와 함께 복원 지점의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/restorePointCollections/restorePoints/write | 새 복원 지점을 만듭니다. |
> | 실행력 | Microsoft.Compute/restorePointCollections/write | 새 복원 지점 컬렉션을 만들거나 기존 복원 지점 컬렉션을 업데이트합니다. |
> | 실행력 | Microsoft.Compute/sharedVMImages/delete | SharedVMImage를 삭제합니다. |
> | 실행력 | Microsoft.Compute/sharedVMImages/read | SharedVMImage의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/sharedVMImages/versions/delete | SharedVMImageVersion을 삭제합니다. |
> | 실행력 | Microsoft.Compute/sharedVMImages/versions/read | SharedVMImageVersion의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/sharedVMImages/versions/replicate/action | SharedVMImageVersion을 대상 지역에 복제합니다. |
> | 실행력 | Microsoft.Compute/sharedVMImages/versions/write | 새 SharedVMImageVersion을 만들거나 기존 SharedVMImageVersion을 업데이트합니다. |
> | 실행력 | Microsoft.Compute/sharedVMImages/write | 새 SharedVMImage를 만들거나 기존 SharedVMImage를 업데이트합니다. |
> | 실행력 | Microsoft.Compute/skus/read | 구독에 사용할 수 있는 Microsoft.Compute SKU 목록을 가져옵니다. |
> | 실행력 | Microsoft.Compute/snapshots/beginGetAccess/action | Blob 액세스에 대한 스냅샷의 SAS URI를 가져옵니다. |
> | 실행력 | Microsoft.Compute/snapshots/delete | 스냅샷을 삭제합니다. |
> | 실행력 | Microsoft.Compute/snapshots/endGetAccess/action | 스냅샷의 SAS URI를 취소합니다. |
> | 실행력 | Microsoft.Compute/snapshots/read | 스냅샷의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/snapshots/write | 새 스냅샷을 만들거나 기존 스냅샷을 업데이트합니다. |
> | 실행력 | Microsoft.Compute/unregister/action | Microsoft. Compute 리소스 공급자를 사용 하 여 구독 등록을 취소 합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/capture/action | 가상 하드 디스크를 복사하여 가상 머신을 캡처하고 유사한 가상 머신을 만드는 데 사용할 수 있는 템플릿을 생성합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | 가상 머신의 blob 기반 디스크를 관리되는 디스크로 변환합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/deallocate/action | 가상 머신을 끄고 Compute 리소스를 해제합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/delete | 가상 머신을 삭제합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/extensions/delete | 가상 머신 확장을 삭제합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/extensions/read | 가상 머신 확장의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/extensions/write | 새 가상 머신 확장을 만들거나 기존 가상 머신 확장을 업데이트합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/generalize/action | 가상 컴퓨터 상태를 일반화됨으로 설정하고 가상 컴퓨터 캡처를 준비합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/instanceView/read | 가상 머신 및 해당 리소스의 자세한 런타임 상태를 가져옵니다. |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | 가상 머신에 일반 사용자로 로그인합니다. |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 가상 머신에 Windows 관리자 또는 Linux 루트 사용자 권한으로 로그인합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/performMaintenance/action | VM에서 유지 관리 작업을 수행합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/powerOff/action | 가상 머신을 끕니다. 가상 컴퓨터에는 요금이 계속 청구됩니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/read | 가상 머신의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/redeploy/action | 가상 머신을 다싯 배포합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/reimage/action | 차이점 보관용 디스크를 사용하는 가상 머신을 이미지로 다시 설치합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/restart/action | 가상 머신을 다시 시작합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/runCommand/action | 가상 머신에서 미리 정의된 스크립트를 실행합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/start/action | 가상 머신을 시작합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/vmSizes/read | 가상 머신이 업데이트될 수 있는 사용 가능한 크기를 나열합니다. |
> | 실행력 | Microsoft.Compute/virtualMachines/write | 새 가상 머신을 만들거나 기존 가상 머신을 업데이트합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | 가상 머신 확장 집합의 인스턴스에 대한 Compute 리소스를 끄고 해제합니다.  |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/delete | 가상 머신 확장 집합을 삭제합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/delete/action | 가상 머신 확장 집합의 인스턴스를 삭제합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 가상 머신 확장 집합 확장을 삭제합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 가상 머신 확장 집합 확장의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 새 가상 머신 확장 집합 확장을 만들거나 기존 가상 머신 확장 집합 확장을 업데이트합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | 서비스 패브릭 가상 머신 확장 집합의 플랫폼 업데이트 도메인을 수동으로 탐색하여 중단된 보류 중인 업데이트를 마칩니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 가상 머신 확장 집합의 인스턴스 보기를 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | 수동으로 인스턴스를 최신 모델의 가상 머신 확장 집합으로 업데이트합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 가상 머신 확장 집합의 모든 네트워크 인터페이스 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | 모든 가상 머신 확장 집합 인스턴스를 사용 가능한 최신 플랫폼 이미지 OS 버전으로 이동하도록 롤링 업그레이드를 시작합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 가상 머신 확장 집합에 대한 OS 업그레이드의 기록을 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | 가상 머신 확장 집합의 인스턴스에서 계획된 유지 관리를 수행합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | 가상 머신 확장 집합의 인스턴스를 끕니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 가상 머신 확장 집합의 모든 공용 IP 주소 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/Read | 가상 머신 확장 집합의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | 가상 머신 확장 집합의 인스턴스를 다시 배포합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/reimage/action | 가상 머신 확장 집합의 인스턴스를 이미지로 다시 설치합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | 가상 머신 확장 집합 인스턴스의 모든 디스크(OS 디스크 및 데이터 디스크)를 이미지로 다시 설치합니다.  |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/restart/action | 가상 머신 확장 집합의 인스턴스를 다시 시작합니다. |
> | 실행력 | Microsoft. Compute/virtualMachineScaleSets/rollingUpgrades/action | 가상 머신 확장 집합의 롤링 업그레이드를 취소합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 가상 머신 확장 집합의 최신 롤링 업그레이드 상태를 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/scale/action | 기존 가상 머신 확장 집합이 지정된 인스턴스 개수로 규모 감축/ 규모 확장할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/skus/read | 기존 가상 머신 확장 집합에 대한 유효한 SKU를 나열합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/start/action | 가상 머신 확장 집합의 인스턴스를 시작합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | VM 확장 집합의 Virtual Machine에 대한 Compute 리소스를 끄고 해제합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | VM 확장 집합에서 특정 Virtual Machine을 삭제합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | VM 크기 집합에 있는 Virtual Machine의 인스턴스 보기를 검색합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 가상 머신 확장 집합을 사용하여 만든 공용 IP 주소의 속성을 가져옵니다. 가상 머신 확장 집합은 ipconfiguration(프라이빗 IP)당 공용 IP를 하나만 만들 수 있습니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 가상 머신 확장 집합을 사용하여 만든 네트워크 인터페이스의 한 IP 구성 또는 모든 IP 구성의 속성을 가져옵니다. IP 구성은 프라이빗 IP를 나타냅니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 가상 머신 확장 집합을 사용하여 만든 가상 머신의 한 네트워크 인터페이스 또는 모든 네트워크 인터페이스의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | 가상 머신 확장 집합의 가상 머신 인스턴스에서 계획된 유지 관리를 수행합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | VM 확장 집합에서 Virtual Machine 인스턴스를 끕니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | VM 확장 집합에서 Virtual Machine의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | 가상 머신 확장 집합의 가상 머신 인스턴스를 이미지로 다시 배포합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | 가상 머신 확장 집합의 가상 머신 인스턴스를 이미지로 다시 설치합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | 가상 머신 확장 집합의 가상 머신 인스턴스에 대한 모든 디스크(OS 디스크 및 데이터 디스크)를 이미지로 다시 설치합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | VM 확장 집합에서 Virtual Machine 인스턴스를 다시 시작합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | 가상 머신 확장 집합의 가상 머신 인스턴스에서 미리 정의된 스크립트를 실행합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | VM 확장 집합에서 Virtual Machine 인스턴스를 시작합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | VM 확장 집합에 있는 가상 머신의 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | 가상 머신 확장 집합에서 가상 머신을 만들거나 업데이트 하는 데 사용할 수 있는 크기를 나열 합니다. |
> | 실행력 | Microsoft.Compute/virtualMachineScaleSets/Write | 새 가상 머신 확장 집합을 만들거나 기존 가상 머신 확장 집합을 업데이트합니다. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft 사용/aggregatedcost/읽기 | 관리 그룹에 대 한 AggregatedCost를 나열 합니다. |
> | 실행력 | Microsoft.Consumption/balances/read | 관리 그룹의 청구 기간에 대한 사용률 요약 정보를 나열합니다. |
> | 실행력 | Microsoft.Consumption/budgets/delete | 구독 또는 관리 그룹별로 예산을 삭제합니다. |
> | 실행력 | Microsoft.Consumption/budgets/read | 구독 또는 관리 그룹별로 예산을 나열합니다. |
> | 실행력 | Microsoft.Consumption/budgets/write | 구독 또는 관리 그룹별로 예산을 만들고 업데이트합니다. |
> | 실행력 | Microsoft.Consumption/charges/read | 요금을 나열합니다. |
> | 실행력 | Microsoft.Consumption/credits/read | 크레딧을 나열합니다. |
> | 실행력 | Microsoft.Consumption/events/read | 이벤트를 나열합니다. |
> | 실행력 | Microsoft. 사용/externalBillingAccounts/태그/읽기 | EA 및 구독에 대한 태그를 나열합니다. |
> | 실행력 | Microsoft. 사용/s i d 구독/태그/읽기 | EA 및 구독에 대한 태그를 나열합니다. |
> | 실행력 | Microsoft.Consumption/forecasts/read | 예측을 나열합니다. |
> | 실행력 | Microsoft.Consumption/lots/read | lots를 나열합니다. |
> | 실행력 | Microsoft.Consumption/marketplaces/read | EA 및 WebDirect 구독의 범위에 대한 마켓플레이스 리소스 사용 정보를 나열합니다. |
> | 실행력 | Microsoft.Consumption/operations/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.Consumption/operations/read | Microsoft.Consumption 리소스 공급자가 지원하는 모든 작업을 나열합니다. |
> | 실행력 | Microsoft.Consumption/operationstatus/read | 작업 상태를 나열합니다. |
> | 실행력 | Microsoft.Consumption/pricesheets/read | 구독 또는 관리 그룹의 가격표 데이터를 나열합니다. |
> | 실행력 | Microsoft.Consumption/register/action | 소비 RP에 등록합니다. |
> | 실행력 | Microsoft.Consumption/reservationDetails/read | 예약된 인스턴스의 사용률 세부 정보를 예약 순서 또는 관리 그룹별로 나열합니다. 세부 정보 데이터는 일별 인스턴스 수준입니다. |
> | 실행력 | Microsoft.Consumption/reservationRecommendations/read | 구독의 예약 인스턴스에 대한 단일 또는 공유 권장 사항을 나열합니다. |
> | 실행력 | Microsoft.Consumption/reservationSummaries/read | 예약된 인스턴스의 사용률 요약 정보를 예약 순서 또는 관리 그룹별로 나열합니다. 요약 데이터는 월별 또는 일별 수준입니다. |
> | 실행력 | Microsoft.Consumption/reservationTransactions/read | 관리 그룹에서 예약한 인스턴스의 트랜잭션 기록을 나열합니다. |
> | 실행력 | Microsoft.Consumption/tags/read | EA 및 구독에 대한 태그를 나열합니다. |
> | 실행력 | Microsoft.Consumption/tenants/read | 테넌트 나열 |
> | 실행력 | Microsoft.Consumption/tenants/register/action | 테넌트에 의한 Microsoft.Consumption 범위에 대한 작업을 등록합니다. |
> | 실행력 | Microsoft.Consumption/terms/read | 구독 또는 관리 그룹의 사용 약관을 나열합니다. |
> | 실행력 | Microsoft.Consumption/usageDetails/read | EA 및 WebDirect 구독의 범위에 대한 사용량 세부 정보를 나열합니다. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | ContainerInstance/containerGroups/컨테이너/buildlogs/read | 특정 컨테이너에 대 한 빌드 로그를 가져옵니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/containers/exec/action | 특정 컨테이너에 실행합니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 특정 컨테이너에 대한 로그를 가져옵니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/delete | 특정 컨테이너 그룹을 삭제합니다. |
> | 실행력 | ContainerInstance/containerGroups/operationResults/read | 비동기 작업 결과 가져오기 |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | 컨테이너 그룹에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | 컨테이너 그룹에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | 컨테이너 그룹에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/read | 모든 컨테이너 그룹을 가져옵니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/restart/action | 특정 컨테이너 그룹을 다시 시작합니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/start/action | 특정 컨테이너 그룹을 시작합니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/stop/action | 특정 컨테이너 그룹을 중지합니다. 컴퓨팅 리소스 할당이 취소되고 청구가 중지됩니다. |
> | 실행력 | Microsoft.ContainerInstance/containerGroups/write | 특정 컨테이너 그룹을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ContainerInstance/locations/cachedImages/read | Azure 지역의 구독에 대해 캐시된 이미지를 가져옵니다. |
> | 실행력 | Microsoft.ContainerInstance/locations/capabilities/read | Azure 지역에 대한 기능을 가져옵니다. |
> | 실행력 | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | 가상 네트워크 또는 서브넷이 삭제됨을 Microsoft.ContainerInstance에 알립니다. |
> | 실행력 | ContainerInstance/위치/작업/읽기 | Azure Container Instance 서비스에 대 한 작업을 나열 합니다. |
> | 실행력 | Microsoft.ContainerInstance/locations/usages/read | 특정 Azure 지역의 사용량을 가져옵니다. |
> | 실행력 | ContainerInstance/작업/읽기 | Azure Container Instance 서비스에 대 한 작업을 나열 합니다. |
> | 실행력 | Microsoft.ContainerInstance/register/action | 컨테이너 인스턴스 리소스 공급자에 대한 구독을 등록하고 컨테이너 그룹을 만들 수 있도록 합니다. |
> | 실행력 | ContainerInstance/serviceassociationlinks/delete | 서브넷에서 azure container instance 리소스 공급자에 의해 만들어진 서비스 연결 링크를 삭제 합니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ContainerRegistry/checkNameAvailability/read | 컨테이너 레지스트리 이름을 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 가상 네트워크 또는 서브넷을 삭제 중임을 Microsoft.ContainerRegistry에 알립니다. |
> | 실행력 | Microsoft.ContainerRegistry/locations/operationResults/read | 비동기 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/operations/read | 사용 가능한 모든 Azure Container Registry REST API 작업을 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/register/action | 컨테이너 레지스트리 리소스 공급자에 대한 구독을 등록하고 컨테이너 레지스트리를 만들도록 설정합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/artifacts/delete | 컨테이너 레지스트리에서 아티팩트를 삭제 합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/builds/cancel/action | 기존 빌드를 취소합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | 빌드 로그를 다운로드할 링크를 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/builds/read | 지정된 빌드 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 빌드를 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/builds/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 빌드를 업데이트합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/buildTasks/delete | 컨테이너 레지스트리에서 빌드 작업을 삭제합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | 빌드 작업에 대한 원본 제어 속성을 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/buildTasks/read | 지정된 빌드 작업의 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 빌드 작업을 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | 빌드 작업에서 빌드 단계를 삭제합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | 비밀 인수를 포함하여 빌드 단계에 대한 빌드 인수를 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | 지정된 빌드 단계의 속성을 가져오거나 지정된 빌드 작업의 모든 빌드 단계를 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | 지정된 매개 변수를 사용하여 빌드 작업에 대한 빌드 단계를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/buildTasks/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 빌드 작업을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/delete | 컨테이너 레지스트리를 삭제합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | 컨테이너 레지스트리에서 Event Grid 필터를 삭제합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 지정된 Event Grid 필터의 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 Event Grid 필터를 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 Event Grid 필터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.containerregistry/레지스트리/generateCredentials/작업 | 지정 된 컨테이너 레지스트리의 토큰에 대 한 키를 생성 합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | 사용자가 원본을 업로드할 수 있는 업로드 위치를 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/importImage/action | 지정된 매개 변수를 사용하여 이미지를 컨테이너 레지스트리에 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | 컨테이너 레지스트리에 대한 원본 업로드 URL 위치를 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/listCredentials/action | 지정된 컨테이너 레지스트리에 대한 로그인 자격 증명을 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/listPolicies/read | 지정된 컨테이너 레지스트리에 대한 정책을 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/listUsages/read | 지정된 컨테이너 레지스트리에 대한 할당량 사용량을 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/metadata/read | 컨테이너 레지스트리에 대 한 특정 리포지토리의 메타 데이터를 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/metadata/write | 컨테이너 레지스트리에 대 한 리포지토리의 메타 데이터를 업데이트 합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/operationStatuses/read | 레지스트리 비동기 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/pull/read | 컨테이너 레지스트리에서 이미지를 끌어오거나 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/push/write | 컨테이너 레지스트리에 이미지를 푸시하거나 작성합니다. |
> | 실행력 | Microsoft.containerregistry/레지스트리/격리/읽기 | 컨테이너 레지스트리에서 격리된 이미지를 끌어오거나 가져옵니다. |
> | 실행력 | Microsoft.containerregistry/레지스트리/격리/쓰기 | 격리된 이미지의 격리 상태를 작성/수정합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/queueBuild/action | 요청 매개 변수를 기반으로 새 빌드를 만들고 빌드 큐에 추가합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/read | 지정된 컨테이너 레지스트리의 속성을 가져오거나 지정된 리소스 그룹 또는 구독에 속한 모든 컨테이너 레지스트리를 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 지정된 컨테이너 레지스트리에 대한 로그인 자격 증명 중 하나를 다시 생성합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/replications/delete | 컨테이너 레지스트리에서 복제를 삭제합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | 복제 비동기 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/replications/read | 지정된 복제 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 복제를 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/replications/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 복제를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/runs/cancel/action | 기존 실행을 취소합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | 실행을 위한 로그 SAS URL을 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/runs/read | 컨테이너 레지스트리 또는 목록 실행에 대한 실행 속성을 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/runs/write | 실행을 업데이트합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/scheduleRun/action | 컨테이너 레지스트리에 대한 실행을 예약합니다. |
> | 실행력 | Microsoft.containerregistry/registry/scopeMaps/delete | 컨테이너 레지스트리에서 범위 맵을 삭제 합니다. |
> | 실행력 | Microsoft.containerregistry/registry/scopeMaps/operationStatuses/읽기 | 범위 매핑 비동기 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.containerregistry/registry/scopeMaps/read | 지정 된 범위 맵의 속성을 가져오거나 지정 된 컨테이너 레지스트리에 대 한 모든 범위 맵을 나열 합니다. |
> | 실행력 | Microsoft.containerregistry/registry/scopeMaps/write | 지정 된 매개 변수를 사용 하 여 컨테이너 레지스트리에 대 한 범위 맵을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/sign/write | 컨테이너 레지스트리에 대한 콘텐츠 신뢰 메타데이터를 푸시/풀합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/tasks/delete | 컨테이너 레지스트리에 대한 작업을 삭제합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | 컨테이너 레지스트리에 대한 작업의 모든 세부 정보를 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/tasks/read | 컨테이너 레지스트리에 대한 작업을 가져오거나 모든 작업을 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/tasks/write | 컨테이너 레지스트리에 대한 작업을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.containerregistry/레지스트리/토큰/삭제 | 컨테이너 레지스트리에서 토큰을 삭제 합니다. |
> | 실행력 | Microsoft.containerregistry/레지스트리/토큰/operationStatuses/읽기 | 토큰 비동기 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.containerregistry/레지스트리/토큰/읽기 | 지정 된 토큰의 속성을 가져오거나 지정 된 컨테이너 레지스트리에 대 한 모든 토큰을 나열 합니다. |
> | 실행력 | Microsoft.containerregistry/레지스트리/토큰/쓰기 | 지정 된 매개 변수를 사용 하 여 컨테이너 레지스트리에 대 한 토큰을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/updatePolicies/write | 지정된 컨테이너 레지스트리에 대한 정책을 업데이트합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/webhooks/delete | 컨테이너 레지스트리에서 웹후크를 삭제합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | 웹후크에 대한 서비스 URI 및 사용자 지정 헤더의 구성을 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 지정된 웹후크에 대한 최신 이벤트를 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | 웹후크 비동기 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/webhooks/ping/action | 웹후크로 보낼 ping 이벤트를 트리거합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/webhooks/read | 지정된 웹후크 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 웹후크를 나열합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/webhooks/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 웹후크를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ContainerRegistry/registries/write | 지정된 매개 변수를 사용하여 컨테이너 레지스트리를 만들거나 업데이트합니다. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ContainerService/containerServices/delete | 컨테이너 서비스를 삭제합니다. |
> | 실행력 | Microsoft.ContainerService/containerServices/read | 컨테이너 서비스를 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/containerServices/write | 새 컨테이너 서비스를 만들거나 기존 컨테이너 서비스를 업데이트합니다. |
> | 실행력 | Microsoft.ContainerService/locations/operationresults/read | 비동기 작업 결과의 상태를 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/locations/operations/read | 비동기 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/locations/orchestrators/read | 지원되는 오케스트레이터를 나열합니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 자격 증명 나열을 사용하여 역할 이름별로 관리되는 클러스터 액세스 프로필을 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/accessProfiles/read | 역할 이름별로 관리되는 클러스터 액세스 프로필을 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/agentPools/delete | 에이전트 풀을 삭제 합니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/agentPools/read | 에이전트 풀을 가져옵니다. |
> | 실행력 | ContainerService/managedClusters/agentPools/upgradeProfiles/read | 에이전트 풀의 업그레이드 프로필을 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/agentPools/write | 새 에이전트 풀을 만들거나 기존 에이전트 풀을 업데이트 합니다. |
> | 실행력 | ContainerService/managedClusters/해당 하는 Agentpoolversions/읽기 | 클러스터의 사용 가능한 에이전트 풀 버전을 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/delete | 관리되는 클러스터를 삭제합니다. |
> | 실행력 | ContainerService/managedClusters/감지기/read | 관리 되는 클러스터 탐지기 가져오기 |
> | 실행력 | ContainerService/managedClusters/diagnosticsState/read | 클러스터의 진단 상태를 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 관리되는 클러스터의 clusterAdmin 자격 증명을 나열합니다. |
> | 실행력 | ContainerService/managedClusters/listClusterMonitoringUserCredential/작업 | 관리 되는 클러스터의 clusterMonitoringUser 자격 증명 나열 |
> | 실행력 | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | 실행력 | ContainerService/managedClusters/privateEndpointConnectionsApproval/작업 | 사용자가 개인 엔드포인트 연결을 승인할 수 있는지 여부를 결정 합니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/read | 관리되는 클러스터를 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/resetAADProfile/action | 관리형 클러스터의 AAD 프로필을 다시 설정합니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | 관리형 클러스터의 서비스 주체 프로필을 다시 설정합니다. |
> | 실행력 | ContainerService/managedClusters/rotateClusterCertificates/작업 | 관리 되는 클러스터의 인증서 회전 |
> | 실행력 | ContainerService/managedClusters/upgradeProfiles/read | 클러스터의 업그레이드 프로필을 가져옵니다. |
> | 실행력 | Microsoft.ContainerService/managedClusters/write | 새 관리되는 클러스터를 만들거나 기존 관리되는 클러스터를 업데이트합니다. |
> | 실행력 | Microsoft.ContainerService/openShiftClusters/delete | 열린 Shift 클러스터 삭제 |
> | 실행력 | Microsoft.ContainerService/openShiftClusters/read | 열린 Shift 클러스터 가져오기 |
> | 실행력 | Microsoft.ContainerService/openShiftClusters/write | 새 Open Shift 클러스터를 만들거나 기존 Open Shift 클러스터를 업데이트합니다. |
> | 실행력 | Microsoft.ContainerService/openShiftManagedClusters/delete | 열린 Shift 관리 클러스터 삭제 |
> | 실행력 | Microsoft.ContainerService/openShiftManagedClusters/read | Open Shift 관리 되는 클러스터 가져오기 |
> | 실행력 | Microsoft.ContainerService/openShiftManagedClusters/write | 새 Open Shift 관리형 클러스터를 만들거나 기존 Open Shift 관리 클러스터를 업데이트합니다. |
> | 실행력 | Microsoft.ContainerService/operations/read | Microsoft.ContainerService 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | 실행력 | Microsoft.ContainerService/register/action | Microsoft.ContainerService 리소스 공급자에 구독을 등록합니다. |
> | 실행력 | Microsoft.ContainerService/unregister/action | Microsoft.ContainerService 리소스 공급자에 구독을 등록 취소합니다. |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | CostManagement/경고/쓰기 | 경고를 업데이트합니다. |
> | 실행력 | Microsoft.CostManagement/cloudConnectors/delete | 지정 된 cloudConnector를 삭제 합니다. |
> | 실행력 | Microsoft.CostManagement/cloudConnectors/read | 인증 된 사용자에 대 한 cloudConnectors를 나열 합니다. |
> | 실행력 | Microsoft.CostManagement/cloudConnectors/write | 지정 된 cloudConnector를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.CostManagement/dimensions/read | 범위별 지원되는 모든 차원을 나열합니다. |
> | 실행력 | Microsoft.CostManagement/exports/action | 지정된 내보내기를 실행합니다. |
> | 실행력 | Microsoft.CostManagement/exports/delete | 지정된 내보내기를 삭제합니다. |
> | 실행력 | Microsoft.CostManagement/exports/read | 범위에 따라 내보내기를 나열합니다. |
> | 실행력 | Microsoft.CostManagement/exports/run/action | 내보내기를 실행 합니다. |
> | 실행력 | Microsoft.CostManagement/exports/write | 지정된 내보내기를 만들거나 업데이트합니다. |
> | 실행력 | CostManagement/externalBillingAccounts/차원/읽기 | 외부 BillingAccounts 지원 되는 모든 차원을 나열 합니다. |
> | 실행력 | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | 인증 된 사용자에 대 한 externalBillingAccount 내에 externalSubscriptions을 나열 합니다. |
> | 실행력 | CostManagement/externalBillingAccounts/예측/작업 | 외부 BillingAccounts에 대 한 사용 현황 데이터를 예측 합니다. |
> | 실행력 | CostManagement/externalBillingAccounts/예측/읽기 | 외부 BillingAccounts에 대 한 사용 현황 데이터를 예측 합니다. |
> | 실행력 | CostManagement/externalBillingAccounts/쿼리/동작 | 외부 BillingAccounts에 대 한 사용 현황 데이터를 쿼리 합니다. |
> | 실행력 | CostManagement/externalBillingAccounts/쿼리/읽기 | 외부 BillingAccounts에 대 한 사용 현황 데이터를 쿼리 합니다. |
> | 실행력 | Microsoft.CostManagement/externalBillingAccounts/read | 인증 된 사용자에 대 한 externalBillingAccounts를 나열 합니다. |
> | 실행력 | CostManagement/externalSubscriptions/차원/읽기 | 외부 구독에 대해 지원 되는 모든 차원을 나열 합니다. |
> | 실행력 | CostManagement/externalSubscriptions/예측/작업 | 외부 BillingAccounts에 대 한 사용 현황 데이터를 예측 합니다. |
> | 실행력 | CostManagement/externalSubscriptions/예측/읽기 | 외부 BillingAccounts에 대 한 사용 현황 데이터를 예측 합니다. |
> | 실행력 | CostManagement/externalSubscriptions/query/action | 외부 구독에 대 한 사용 현황 데이터를 쿼리 합니다. |
> | 실행력 | CostManagement/externalSubscriptions/query/read | 외부 구독에 대 한 사용 현황 데이터를 쿼리 합니다. |
> | 실행력 | Microsoft.CostManagement/externalSubscriptions/read | 인증 된 사용자에 대 한 externalSubscriptions을 나열 합니다. |
> | 실행력 | Microsoft.CostManagement/externalSubscriptions/write | ExternalSubscription의 연결 된 관리 그룹 업데이트 |
> | 실행력 | CostManagement/예측/작업 | 범위에서 사용 데이터를 예측 합니다. |
> | 실행력 | CostManagement/예측/읽기 | 범위에서 사용 데이터를 예측 합니다. |
> | 실행력 | CostManagement/작업/읽기 | CostManagement 리소스 공급자에 의해 지원 되는 모든 작업을 나열 합니다. |
> | 실행력 | Microsoft.CostManagement/query/action | 범위별 사용량 현황 데이터를 쿼리합니다. |
> | 실행력 | Microsoft.CostManagement/query/read | 범위별 사용량 현황 데이터를 쿼리합니다. |
> | 실행력 | Microsoft.CostManagement/register/action | 구독에의 한 CostManagement 범위에 대 한 작업을 등록 합니다. |
> | 실행력 | Microsoft.CostManagement/reports/action | 범위별 사용량 현황 데이터에 관한 보고서를 예약합니다. |
> | 실행력 | Microsoft.CostManagement/reports/read | 범위별 사용량 현황 데이터에 관한 보고서를 예약합니다. |
> | 실행력 | Microsoft.CostManagement/tenants/register/action | CostManagement의 범위에 대 한 작업을 테넌트에 등록 합니다. |
> | 실행력 | CostManagement/뷰/작업 | 뷰를 만듭니다. |
> | 실행력 | CostManagement/views/delete | 저장 된 보기를 삭제 합니다. |
> | 실행력 | CostManagement/뷰/읽기 | 저장 된 모든 뷰를 나열 합니다. |
> | 실행력 | CostManagement/views/write | 업데이트 뷰입니다. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 반송 배송을 위한 픽업을 예약할 수 있습니다. |
> | 실행력 | Microsoft.DataBox/jobs/cancel/action | 진행 중인 주문을 취소합니다. |
> | 실행력 | Microsoft.DataBox/jobs/delete | 주문을 삭제합니다. |
> | 실행력 | Microsoft.DataBox/jobs/listCredentials/action | 주문과 관련된 암호화되지 않은 자격 증명을 나열합니다. |
> | 실행력 | Microsoft.DataBox/jobs/read | 주문을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBox/jobs/write | 주문을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataBox/locations/availableSkus/action | 이 메서드는 사용할 수 있는 SKU 목록을 반환합니다. |
> | 실행력 | DataBox/위치/availableSkus/읽기 | 사용 가능한 Sku 나열 또는 가져오기 |
> | 실행력 | Microsoft.DataBox/locations/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | DataBox/위치/지역 구성/작업 | 이 메서드는 영역에 대 한 구성을 반환 합니다. |
> | 실행력 | Microsoft.DataBox/locations/validateAddress/action | 배송 주소의 유효성을 검사하고, 있는 경우, 대체 주소를 제공합니다. |
> | 실행력 | DataBox/위치/validateInputs/작업 | 이 메서드는 모든 유형의 유효성 검사를 수행 합니다. |
> | 실행력 | DataBox/작업/읽기 | 작업 나열 또는 가져오기 |
> | 실행력 | Microsoft.DataBox/register/action | 공급자 Microsoft.Databox를 등록합니다. |
> | 실행력 | DataBox/subscription/resourceGroups/moveResources/action | 이 메서드는 리소스 이동을 수행 합니다. |
> | 실행력 | DataBox/subscription/resourceGroups/validateMoveResources/action | 이 메서드는 리소스 이동이 허용 되는지 여부를 확인 합니다. |
> | 실행력 | DataBox/등록 취소/작업 | 공급자 등록 취소 Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 경고를 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | 대역폭 일정을 삭제 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 대역폭 일정을 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 대역폭 일정을 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | 대역폭 일정을 만들거나 업데이트 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Data Box Edge 디바이스를 삭제 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | 디바이스에서 업데이트를 다운로드 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | 리소스 확장 정보를 검색 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | 디바이스에 업데이트를 설치 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | 작업을 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | 디바이스 네트워크 설정을 나열하거나 가져오기 |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/노드/읽기 | 노드를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | 작업 상태를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | 주문을 삭제 합니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | 주문을 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | 주문을 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | 주문을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Data Box Edge 디바이스를 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Data Box Edge 디바이스를 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Data Box Edge 디바이스를 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | 역할을 삭제 합니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 역할을 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 역할을 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | 역할을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | 업데이트를 검색 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | 보안 설정을 업데이트 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | 공유를 삭제 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 공유를 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 공유를 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | 클라우드의 데이터로 공유 메타 데이터를 새로 고칩니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | 공유를 만들거나 업데이트 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | 스토리지 계정 자격 증명을 삭제 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 스토리지 계정 자격 증명을 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 스토리지 계정 자격 증명을 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | 스토리지 계정 자격 증명을 만들거나 업데이트 |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/컨테이너/삭제 | 컨테이너를 삭제 합니다. |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/컨테이너/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/컨테이너/읽기 | 컨테이너를 나열 하거나 가져옵니다. |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/컨테이너/새로 고침/작업 | 클라우드의 데이터로 컨테이너 메타 데이터를 새로 고칩니다. |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/컨테이너/쓰기 | 컨테이너를 만들거나 업데이트 합니다. |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/delete | 저장소 계정을 삭제 합니다. |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/read | 저장소 계정을 나열 하거나 가져옵니다. |
> | 실행력 | DataBoxEdge/dataBoxEdgeDevices/storageAccounts/write | 저장소 계정을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | 트리거를 삭제 합니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | 트리거를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | 트리거를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | 트리거를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | 업데이트 요약을 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | 디바이스 등록에 대한 인증서를 업로드 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | 공유 사용자를 삭제 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | 작업 결과를 나열 하거나 가져옵니다. |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 공유 사용자를 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 공유 사용자를 나열하거나 가져오기 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | 공유 사용자를 만들거나 업데이트 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Data Box Edge 디바이스를 만들거나 업데이트 |
> | 실행력 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Data Box Edge 디바이스를 만들거나 업데이트 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Databricks/locations/getNetworkPolicies/action | NRP에서 사용 하는 위치에 따라 서브넷에 대 한 네트워크 의도 정책 가져오기 |
> | 실행력 | Databricks/위치/operationstatuses/읽기 | 리소스의 작업 상태를 읽습니다. |
> | 실행력 | Databricks/작업/읽기 | 작업 목록을 가져옵니다. |
> | 실행력 | Microsoft.Databricks/register/action | Databricks에 등록합니다. |
> | 실행력 | Microsoft.Databricks/workspaces/delete | Databricks 작업 영역을 제거합니다. |
> | 실행력 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Databricks 작업 영역에 사용할 수 있는 진단 설정을 구성합니다. |
> | 실행력 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 진단 설정을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Databricks 작업 영역에 사용할 수 있는 로그 정의를 가져옵니다. |
> | 실행력 | Microsoft.Databricks/workspaces/read | Databricks 작업 영역의 목록을 검색합니다. |
> | 실행력 | Databricks/작업 영역/refreshPermissions/작업 | 작업 영역에 대 한 새로 고침 권한 |
> | 실행력 | Databricks/작업 영역/storageEncryption/delete | Databricks 작업 영역의 관리 되는 저장소 계정에서 고객 관리 키 암호화를 사용 하지 않도록 설정 합니다. |
> | 실행력 | Databricks/작업 영역/storageEncryption/write | Databricks 작업 영역의 관리 되는 저장소 계정에서 고객이 관리 하는 키 암호화를 사용 하도록 설정 합니다. |
> | 실행력 | Databricks/작업 영역/updateDenyAssignment/작업 | 작업 영역의 관리 되는 리소스 그룹에 대 한 작업 거부 할당 업데이트 |
> | 실행력 | Databricks/작업 영역/virtualNetworkPeerings/삭제 | 가상 네트워크 피어링을 삭제합니다. |
> | 실행력 | Databricks/작업 영역/virtualNetworkPeerings/읽기 | 가상 네트워크 피어링을 가져옵니다. |
> | 실행력 | Databricks/작업 영역/virtualNetworkPeerings/쓰기 | 가상 네트워크 피어 링 추가 또는 수정 |
> | 실행력 | Microsoft.Databricks/workspaces/write | Databricks 작업 영역을 만듭니다. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DataCatalog/catalogs/delete | Data Catalog 리소스 공급자에 대 한 카탈로그 리소스를 삭제 합니다. |
> | 실행력 | Microsoft.DataCatalog/catalogs/read | Data Catalog 리소스 공급자에 대 한 카탈로그 리소스를 읽습니다. |
> | 실행력 | Microsoft.DataCatalog/catalogs/write | Data Catalog 리소스 공급자에 대 한 카탈로그 리소스를 작성 합니다. |
> | 실행력 | Microsoft.DataCatalog/checkNameAvailability/read | Data Catalog 리소스 공급자에 대 한 카탈로그 이름 가용성을 확인 합니다. |
> | 실행력 | Microsoft.DataCatalog/datacatalogs/delete | Data Catalog 리소스 공급자에 대 한 Microsoft.datacatalog 리소스를 삭제 합니다. |
> | 실행력 | Microsoft.DataCatalog/datacatalogs/read | Data Catalog 리소스 공급자에 대 한 Microsoft.datacatalog 리소스를 읽습니다. |
> | 실행력 | Microsoft.DataCatalog/datacatalogs/write | Data Catalog 리소스 공급자에 대 한 Microsoft.datacatalog 리소스를 씁니다. |
> | 실행력 | Microsoft.DataCatalog/operations/read | Data Catalog 리소스 공급자에서 사용 가능한 모든 작업을 읽습니다. |
> | 실행력 | Microsoft.DataCatalog/register/action | Data Catalog 리소스 공급자에 대 한 구독 등록 |
> | 실행력 | Microsoft.DataCatalog/unregister/action | Data Catalog 리소스 공급자에 대 한 구독 등록 취소 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Data Factory 이름을 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/activitywindows/read | 지정된 매개 변수를 사용하여 Data Factory의 활동 창을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 지정된 매개 변수를 사용하여 파이프라인 활동에 대한 활동 창을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 지정된 매개 변수를 사용하여 파이프라인에 대한 활동 창을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datapipelines/delete | 모든 파이프라인을 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datapipelines/pause/action | 모든 파이프라인을 일시 중지합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datapipelines/read | 모든 파이프라인을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datapipelines/resume/action | 모든 파이프라인을 다시 시작합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datapipelines/update/action | 모든 파이프라인을 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datapipelines/write | 모든 파이프라인을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 지정된 매개 변수를 사용하여 데이터 세트에 대한 활동 창을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datasets/delete | 모든 데이터 세트를 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datasets/read | 모든 데이터 세트를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 지정된 시작 시간을 사용하여 지정된 데이터 세트에 대한 데이터 조각 실행을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datasets/slices/read | 지정된 기간의 데이터 조각을 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datasets/slices/write | 데이터 조각의 상태를 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/datasets/write | 모든 데이터 세트를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/delete | Data Factory를 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | 모든 게이트웨이에 대한 연결 정보를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/gateways/delete | 모든 게이트웨이를 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | 모든 게이트웨이에 대한 인증 키를 나열합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/gateways/read | 모든 게이트웨이를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | 모든 게이트웨이에 대한 인증 키를 다시 생성합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/gateways/write | 모든 게이트웨이를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/linkedServices/delete | 모든 연결된 서비스를 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/linkedServices/read | 모든 연결된 서비스를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/linkedServices/write | 모든 연결된 서비스를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/read | Data Factory를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/runs/loginfo/read | 로그가 포함된 Blob 컨테이너에 대한 SAS URI를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/tables/delete | 모든 데이터 세트를 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/tables/read | 모든 데이터 세트를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/tables/write | 모든 데이터 세트를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/datafactories/write | Data Factory를 만들거나 업데이트합니다. |
> | 실행력 | DataFactory/공장/addDataFlowToDebugSession/작업 | 미리 보기에 대 한 디버그 세션에 데이터 흐름을 추가 합니다. |
> | 실행력 | Microsoft.DataFactory/factories/cancelpipelinerun/action | 실행 ID로 지정된 파이프라인 실행을 취소합니다. |
> | 실행력 | DataFactory/공장/cancelSandboxPipelineRun/작업 | 파이프라인에 대 한 디버그 실행을 취소 합니다. |
> | 실행력 | Microsoft.DataFactory/factories/createdataflowdebugsession/action | 데이터 흐름 디버그 세션을 만듭니다. |
> | 실행력 | Microsoft.DataFactory/factories/dataflows/delete | 데이터 흐름을 삭제 합니다. |
> | 실행력 | Microsoft.DataFactory/factories/dataflows/read | 데이터 흐름을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/dataflows/write | 데이터 흐름 만들기 또는 업데이트 |
> | 실행력 | Microsoft.DataFactory/factories/datasets/delete | 모든 데이터 세트를 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/factories/datasets/read | 모든 데이터 세트를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/datasets/write | 모든 데이터 세트를 만들거나 업데이트합니다. |
> | 실행력 | DataFactory/공장/debugpipelineruns/취소/작업 | 파이프라인에 대 한 디버그 실행을 취소 합니다. |
> | 실행력 | Microsoft.DataFactory/factories/delete | 데이터 팩터리를 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | 데이터 흐름 디버그 세션을 삭제 합니다. |
> | 실행력 | DataFactory/공장/executeDataFlowDebugCommand/작업 | 데이터 흐름 실행 디버그 명령입니다. |
> | 실행력 | Microsoft.DataFactory/factories/getDataPlaneAccess/action | ADF 데이터 평면 서비스에 대한 액세스를 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/factories/getDataPlaneAccess/read | ADF 데이터 평면 서비스에 대한 액세스를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/getFeatureValue/action | 특정 위치에 대한 노출 제어 기능 값을 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/factories/getFeatureValue/read | 특정 위치에 대한 노출 제어 기능 값을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/getGitHubAccessToken/action | GitHub 액세스를 토큰을 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/delete | 모든 Integration Runtime을 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Integration Runtime 연결 정보를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | 지정된 Integration Runtime에 대한 SSIS Integration Runtime 메타데이터를 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Integration Runtime 상태를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | 지정된 공유 Integration Runtime에 연결된 Integration Runtime 참조를 만듭니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | 모든 Integration Runtime에 대한 인증 키를 나열합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 모든 Integration Runtime에 대한 모니터링 데이터를 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 지정된 Integration Runtime에 대한 노드를 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Integration Runtime의 지정된 노드에 대한 IP 주소를 반환합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | 지정된 Integration Runtime에 대한 노드를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 자체 호스팅 Integration Runtime 노드를 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/read | 모든 Integration Runtime을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | 지정된 Integration Runtime에 대한 SSIS Integration Runtime 메타데이터를 새로 고칩니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | 지정된 Integration Runtime에 대한 인증 키를 다시 생성합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | 지정된 Integration Runtime에서 연결된 Integration Runtime 참조를 제거합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/start/action | 모든 Integration Runtime을 시작합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/stop/action | 모든 Integration Runtime을 중지합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | 지정된 Integration Runtime에 대한 자격 증명을 동기화합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | 지정된 Integration Runtime을 업그레이드합니다. |
> | 실행력 | Microsoft.DataFactory/factories/integrationruntimes/write | Integration Runtime을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/factories/linkedServices/delete | 연결된 서비스를 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/factories/linkedServices/read | 연결된 서비스를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/linkedServices/write | 연결된 서비스를 만들거나 업데이트합니다. |
> | 실행력 | DataFactory/factory/operationResults/read | 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 지정된 파이프라인 실행 ID에 대한 활동 실행을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 실행 ID로 지정된 파이프라인 실행을 취소합니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 지정된 파이프라인 실행 ID에 대한 활동 실행을 쿼리합니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | 지정된 파이프라인 실행 ID에 대한 쿼리 활동 실행의 결과를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelineruns/read | 파이프라인 실행을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelines/createrun/action | 파이프라인에 대한 실행을 만듭니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelines/delete | 파이프라인을 삭제합니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | 활동 실행의 진행률을 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | 파이프라인 실행을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelines/read | 파이프라인을 읽습니다. |
> | 실행력 | DataFactory/공장/파이프라인/sandbox/작업 | 파이프라인에 대 한 디버그 실행 환경을 만듭니다. |
> | 실행력 | DataFactory/공장/파이프라인/sandbox/만들기/작업 | 파이프라인에 대 한 디버그 실행 환경을 만듭니다. |
> | 실행력 | DataFactory/공장/파이프라인/sandbox/실행/작업 | 파이프라인에 대 한 디버그 실행을 만듭니다. |
> | 실행력 | Microsoft.DataFactory/factories/pipelines/write | 파이프라인을 만들거나 업데이트합니다. |
> | 실행력 | DataFactory/공장/querydataflowdebugsessions/작업 | 데이터 흐름 디버그 세션을 쿼리 합니다. |
> | 실행력 | Microsoft.DataFactory/factories/querydebugpipelineruns/action | 디버그 파이프라인 실행을 쿼리합니다. |
> | 실행력 | Microsoft.DataFactory/factories/querypipelineruns/action | 파이프라인 실행을 쿼리합니다. |
> | 실행력 | Microsoft.DataFactory/factories/querypipelineruns/read | 쿼리 파이프라인 실행의 결과를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/querytriggerruns/action | 트리거 실행을 쿼리합니다. |
> | 실행력 | Microsoft.DataFactory/factories/querytriggerruns/read | 트리거 실행의 결과를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/read | 데이터 팩터리를 읽습니다. |
> | 실행력 | DataFactory/공장/sandboxpipelineruns/작업 | 디버그 파이프라인 실행을 쿼리합니다. |
> | 실행력 | DataFactory/factory/sandboxpipelineruns/read | 파이프라인에 대 한 디버그 실행 정보를 가져옵니다. |
> | 실행력 | DataFactory/factory/sandboxpipelineruns/sandboxActivityRuns/read | 활동에 대 한 디버그 실행 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/factories/startdataflowdebugsession/action | 데이터 흐름 디버그 세션을 시작 합니다. |
> | 실행력 | Microsoft.DataFactory/factories/triggerruns/read | 트리거 실행을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/triggers/delete | 모든 트리거를 삭제합니다. |
> | 실행력 | DataFactory/factory/triggers/geteventsubscriptionstatus/action | 이벤트 구독 상태입니다. |
> | 실행력 | Microsoft.DataFactory/factories/triggers/read | 모든 트리거를 읽습니다. |
> | 실행력 | Microsoft.DataFactory/factories/triggers/start/action | 모든 트리거를 시작합니다. |
> | 실행력 | Microsoft.DataFactory/factories/triggers/stop/action | 모든 트리거를 중지합니다. |
> | 실행력 | DataFactory/factory/triggers/subscribetoevents/action | 이벤트를 구독 합니다. |
> | 실행력 | Microsoft.DataFactory/factories/triggers/triggerruns/read | 트리거 실행을 읽습니다. |
> | 실행력 | DataFactory/factory/triggers/unsubscribefromevents/action | 이벤트 구독을 취소 합니다. |
> | 실행력 | Microsoft.DataFactory/factories/triggers/write | 모든 트리거를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/factories/write | 데이터 팩터리를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataFactory/locations/configureFactoryRepo/action | 팩터리에 대해 리포지토리를 구성합니다. |
> | 실행력 | Microsoft.DataFactory/locations/getFeatureValue/action | 특정 위치에 대한 노출 제어 기능 값을 가져옵니다. |
> | 실행력 | Microsoft.DataFactory/locations/getFeatureValue/read | 특정 위치에 대한 노출 제어 기능 값을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/operations/read | Microsoft Data Factory 공급자의 모든 작업을 읽습니다. |
> | 실행력 | Microsoft.DataFactory/register/action | Data Factory 리소스 공급자에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.DataFactory/unregister/action | Data Factory 리소스 공급자에 대한 구독을 등록 취소합니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | 컴퓨팅 정책을 삭제합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | 컴퓨팅 정책에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | 컴퓨팅 정책을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics 계정에서 DataLakeStore 계정을 연결 해제합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | DataLakeAnalytics 계정과 연결된 DataLakeStore 계정에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics 계정과 연결된 DataLakeStore 계정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics 계정을 삭제합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | 방화벽 규칙을 삭제합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | 방화벽 규칙에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | 방화벽 규칙을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/operationResults/read | DataLakeAnalytics 계정 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/read | 기존 DataLakeAnalytics 계정에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | DataLakeAnalytics 계정과 연결된 Storage 계정의 스토리지 컨테이너 SAS 토큰을 나열합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | DataLakeAnalytics 계정과 연결된 Storage 계정의 컨테이너를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | DataLakeAnalytics 계정에서 Storage 계정을 연결 해제합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | DataLakeAnalytics 계정과 연결된 Storage 계정에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | DataLakeAnalytics 계정과 연결된 Storage 계정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 다른 사용자가 제출한 작업을 취소하는 권한을 부여합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | DataLakeAnalytics 계정 간에 SystemMaxAnalyticsUnits를 전송합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | 가상 네트워크 규칙을 삭제 합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | 가상 네트워크 규칙에 대 한 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | 가상 네트워크 규칙을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/accounts/Write | DataLakeAnalytics 계정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/locations/capability/read | DataLakeAnalytics 사용과 관련된 구독의 기능 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | DataLakeAnalytics 계정 이름의 가용성을 확인합니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/locations/operationResults/read | DataLakeAnalytics 계정 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/locations/usages/read | DataLakeAnalytics 사용과 관련된 구독의 할당량 사용량 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/operations/read | DataLakeAnalytics에서 사용 가능한 작업을 가져옵니다. |
> | 실행력 | Microsoft.DataLakeAnalytics/register/action | DataLakeAnalytics에 구독을 등록합니다. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DataLakeStore/accounts/delete | DataLakeStore 계정을 삭제합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/enableKeyVault/action | DataLakeStore 계정에 Keyvault를 사용하도록 설정합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | EventGrid 필터를 삭제합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/eventGridFilters/read | EventGrid 필터를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/eventGridFilters/write | EventGrid 필터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/firewallRules/delete | 방화벽 규칙을 삭제합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/firewallRules/read | 방화벽 규칙에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/firewallRules/write | 방화벽 규칙을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/operationResults/read | DataLakeStore 계정 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/read | 기존 DataLakeStore 계정에 대한 정보를 가져옵니다. |
> | 실행력 | DataLakeStore/계정/공유/삭제 | 공유를 삭제 합니다. |
> | 실행력 | DataLakeStore/계정/공유/읽기 | 공유에 대 한 정보를 가져옵니다. |
> | 실행력 | DataLakeStore/계정/공유/쓰기 | 공유를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/Superuser/action | Microsoft.Authorization/roleAssignments/write가 부여되면 Data Lake Store에 대한 슈퍼 사용자 권한을 부여합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 신뢰할 수 있는 ID 공급자를 삭제합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 신뢰할 수 있는 ID 공급자에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 신뢰할 수 있는 ID 공급자를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | 가상 네트워크 규칙을 삭제 합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | 가상 네트워크 규칙에 대 한 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | 가상 네트워크 규칙을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DataLakeStore/accounts/write | DataLakeStore 계정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataLakeStore/locations/capability/read | DataLakeStore 사용과 관련된 구독의 기능 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeStore/locations/checkNameAvailability/action | DataLakeStore 계정 이름의 가용성을 확인합니다. |
> | 실행력 | Microsoft.DataLakeStore/locations/operationResults/read | DataLakeStore 계정 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeStore/locations/usages/read | DataLakeStore 사용과 관련된 구독의 할당량 사용량 정보를 가져옵니다. |
> | 실행력 | Microsoft.DataLakeStore/operations/read | DataLakeStore에서 사용 가능한 작업을 가져옵니다. |
> | 실행력 | Microsoft.DataLakeStore/register/action | DataLakeStore에 구독을 등록합니다. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DataMigration/locations/operationResults/read | 202 수락됨 응답과 관련된 장기 실행 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.DataMigration/locations/operationStatuses/read | 202 수락됨 응답과 관련된 장기 실행 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.DataMigration/register/action | Azure Database Migration Service 공급자를 사용하여 구독을 등록합니다. |
> | 실행력 | Microsoft.DataMigration/services/addWorker/action | 서비스의 availiable 작업자에 DMS 작업자를 추가 합니다. |
> | 실행력 | Microsoft.DataMigration/services/checkStatus/action | 서비스가 배포되어 실행되고 있는지 확인합니다. |
> | 실행력 | Microsoft.DataMigration/services/configureWorker/action | 서비스의 availiable 작업자에 대해 DMS 작업자를 구성 합니다. |
> | 실행력 | Microsoft.DataMigration/services/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 실행력 | Microsoft.datamigration/서비스/getHybridDownloadLink/작업 | RP Blob Storage에서 DMS 작업자 패키지 다운로드 링크를 가져옵니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/accessArtifacts/action | 프로젝트 아티팩트 GET 또는 PUT에 사용할 수 있는 URL을 생성합니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/files/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/files/read | 리소스에 대한 정보를 읽습니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/files/read/action | 파일의 콘텐츠를 읽는 데 사용할 수 있는 URL을 가져옵니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/files/readWrite/action | 파일의 콘텐츠를 읽거나 쓰는 데 사용할 수 있는 URL을 가져옵니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/files/write | 리소스 및 해당 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/read | 리소스에 대한 정보를 읽습니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/tasks/cancel/action | 작업이 현재 실행 중인 경우 취소합니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/tasks/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/tasks/read | 리소스에 대한 정보를 읽습니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/tasks/write | Azure Database Migration Service 작업을 실행합니다. |
> | 실행력 | Microsoft.DataMigration/services/projects/write | Azure Database Migration Service 작업을 실행합니다. |
> | 실행력 | Microsoft.DataMigration/services/read | 리소스에 대한 정보를 읽습니다. |
> | 실행력 | Microsoft.DataMigration/services/removeWorker/action | 서비스의 availiable 작업자에 대해 DMS 작업자를 제거 합니다. |
> | 실행력 | Microsoft.DataMigration/services/serviceTasks/cancel/action | 작업이 현재 실행 중인 경우 취소합니다. |
> | 실행력 | Microsoft.DataMigration/services/serviceTasks/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 실행력 | Microsoft.DataMigration/services/serviceTasks/read | 리소스에 대한 정보를 읽습니다. |
> | 실행력 | Microsoft.DataMigration/services/serviceTasks/write | Azure Database Migration Service 작업을 실행합니다. |
> | 실행력 | Microsoft.DataMigration/services/slots/delete | 리소스와 해당 하위 항목을 모두 삭제합니다. |
> | 실행력 | Microsoft.DataMigration/services/slots/read | 리소스에 대한 정보를 읽습니다. |
> | 실행력 | Microsoft.DataMigration/services/slots/write | 리소스 및 해당 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataMigration/services/start/action | 마이그레이션을 다시 처리할 수 있도록 DMS 서비스를 시작합니다. |
> | 실행력 | Microsoft.DataMigration/services/stop/action | 비용을 최소화하기 위해 DMS 서비스를 중지합니다. |
> | 실행력 | Microsoft.DataMigration/services/updateAgentConfig/action | 제공 된 값을 사용 하 여 DMS 에이전트 구성을 업데이트 합니다. |
> | 실행력 | Microsoft.DataMigration/services/write | 리소스 및 해당 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DataMigration/skus/read | DMS 리소스에서 지원하는 SKU 목록을 가져옵니다. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DBforMariaDB/checkNameAvailability/action | 지정된 서버 이름을 지정된 구독에 대해 전 세계적으로 프로비전에 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | 서버 작업 결과 반환 (& u) |
> | 실행력 | Microsoft.DBforMariaDB/locations/operationResults/read | ResourceGroup 기반 MariaDB 서버 작업 결과 반환 |
> | 실행력 | Microsoft.DBforMariaDB/locations/operationResults/read | 서버 작업 결과 반환 (& u) |
> | 실행력 | Microsoft.DBforMariaDB/locations/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> | 실행력 | DBforMariaDB/위치/privateEndpointConnectionAzureAsyncOperation/읽기 | 개인 엔드포인트 연결 작업에 대 한 결과를 가져옵니다. |
> | 실행력 | DBforMariaDB/위치/privateEndpointConnectionOperationResults/읽기 | 개인 엔드포인트 연결 작업에 대 한 결과를 가져옵니다. |
> | 실행력 | DBforMariaDB/위치/privateEndpointConnectionProxyAzureAsyncOperation/읽기 | 개인 엔드포인트 연결 프록시 작업의 결과를 가져옵니다. |
> | 실행력 | DBforMariaDB/위치/privateEndpointConnectionProxyOperationResults/읽기 | 개인 엔드포인트 연결 프록시 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | 서버 위협 검색 작업 결과의 목록을 반환 합니다. |
> | 실행력 | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | 서버 위협 검색 작업 결과의 목록을 반환 합니다. |
> | 실행력 | DBforMariaDB/위치/serverKeyAzureAsyncOperation/읽기 | 투명 한 데이터 암호화 서버 키에 대 한 진행 중인 작업을 가져옵니다. |
> | 실행력 | DBforMariaDB/위치/serverKeyOperationResults/read | 투명 한 데이터 암호화 서버 키에 대 한 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/operations/read | 작업 목록 반환. |
> | 실행력 | Microsoft.DBforMariaDB/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> | 실행력 | Microsoft.DBforMariaDB/register/action | MariaDB 리소스 공급자 등록 |
> | 실행력 | Microsoft.DBforMariaDB/servers/administrators/delete | 기존 관리자의 기존 관리자를 삭제 합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/administrators/read | MariaDB 서버 관리자의 목록을 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/administrators/write | 지정 된 매개 변수를 사용 하 여 MariaDB 서버 관리자를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | 새 권장 구성 작업 세션을 만듭니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/advisors/read | 관리자 목록을 반환합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/advisors/read | Advisor 반환 |
> | 실행력 | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 권장된 작업 목록을 반환 |
> | 실행력 | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 권장된 작업 목록을 반환 |
> | 실행력 | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 권장 작업 반환 |
> | 실행력 | Microsoft.DBforMariaDB/servers/configurations/read | 서버에 대한 구성 목록을 반환하거나 지정된 구성에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/configurations/write | 지정된 구성의 값을 업데이트합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/databases/delete | 기존에 있는 데이터베이스를 삭제 합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/databases/read | 반환 된 데이터베이스에 대 한 속성을 가져오거나 반환 합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/databases/write | 지정 된 매개 변수를 사용 하 여, 지정 된 데이터베이스에 대 한 속성을 업데이트 하는에 대 한 속성을 업데이트 합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/delete | 기존 서버를 삭제합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/firewallRules/delete | 기존 방화벽 규칙을 삭제합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/firewallRules/read | 서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/firewallRules/write | 지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다. |
> | 실행력 | DBforMariaDB/servers/keys/delete | 기존 서버 키를 삭제합니다. |
> | 실행력 | DBforMariaDB/서버/키/읽기 | 서버 키 목록을 가져오거나 지정된 서버 키에 대한 속성을 가져옵니다. |
> | 실행력 | DBforMariaDB/servers/keys/write | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 서버 키에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/logFiles/read | Aadb LogFiles의 목록을 반환 합니다. |
> | 실행력 | DBforMariaDB/servers/privateEndpointConnectionProxies/delete | 기존 개인 엔드포인트 연결 프록시를 삭제 합니다. |
> | 실행력 | DBforMariaDB/servers/privateEndpointConnectionProxies/read | 개인 엔드포인트 연결 프록시의 목록을 반환 하거나, 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성을 가져옵니다. |
> | 실행력 | DBforMariaDB/servers/privateEndpointConnectionProxies/validate/action | NRP 쪽에서 개인 엔드포인트 연결 만들기 호출의 유효성을 검사 합니다. |
> | 실행력 | DBforMariaDB/servers/privateEndpointConnectionProxies/write | 지정 된 매개 변수를 사용 하 여 개인 엔드포인트 연결 프록시를 만들거나 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 실행력 | DBforMariaDB/servers/privateEndpointConnections/delete | 기존 개인 엔드포인트 연결을 삭제 합니다. |
> | 실행력 | DBforMariaDB/servers/privateEndpointConnections/read | 개인 엔드포인트 연결의 목록을 반환 하거나, 지정 된 개인 엔드포인트 연결에 대 한 속성을 가져옵니다. |
> | 실행력 | DBforMariaDB/servers/privateEndpointConnections/write | 기존 개인 엔드포인트 연결을 승인 하거나 거부 합니다. |
> | 실행력 | DBforMariaDB/servers/privateLinkResources/read | 해당 하는 Aadb 서버에 대 한 개인 링크 리소스를 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | MariaDB 서버에 사용 가능한 로그를 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/queryTexts/action | 쿼리 목록의 텍스트 반환 |
> | 실행력 | Microsoft.DBforMariaDB/servers/queryTexts/action | 쿼리 텍스트를 반환합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/recoverableServers/read | 복구 가능한 MariaDB 서버 정보를 반환 |
> | 실행력 | Microsoft.DBforMariaDB/servers/replicas/read | MariaDB 서버의 읽기 복제본 가져오기 |
> | 실행력 | Microsoft.DBforMariaDB/servers/restart/action | 특정 서버를 다시 시작 합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 지정된 서버에 구성된 서버 위협 검색 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | 지정된 서버에 대한 서버 위협 검색 정책을 변경합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | 최상위 쿼리에 대한 쿼리 통계 목록을 반환합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | 쿼리 통계 반환 |
> | 실행력 | Microsoft.DBforMariaDB/servers/updateConfigurations/action | 지정된 서버의 구성을 업데이트합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | 기존 가상 네트워크 규칙을 삭제합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | 가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | 지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/waitStatistics/read | 인스턴스에 대한 대기 통계를 반환합니다. |
> | 실행력 | Microsoft.DBforMariaDB/servers/waitStatistics/read | 대기 통계 반환 |
> | 실행력 | Microsoft.DBforMariaDB/servers/write | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DBforMySQL/checkNameAvailability/action | 지정된 서버 이름을 지정된 구독에 대해 전 세계적으로 프로비전에 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | MySQL 서버 작업 결과 반환 |
> | 실행력 | Microsoft.DBforMySQL/locations/operationResults/read | ResourceGroup 기반 MySQL 서버 작업 결과 반환 |
> | 실행력 | Microsoft.DBforMySQL/locations/operationResults/read | MySQL 서버 작업 결과 반환 |
> | 실행력 | Microsoft.DBforMySQL/locations/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> | 실행력 | Microsoft DBforMySQL/위치/privateEndpointConnectionAzureAsyncOperation/읽기 | 개인 엔드포인트 연결 작업에 대 한 결과를 가져옵니다. |
> | 실행력 | Microsoft DBforMySQL/위치/privateEndpointConnectionOperationResults/읽기 | 개인 엔드포인트 연결 작업에 대 한 결과를 가져옵니다. |
> | 실행력 | Microsoft DBforMySQL/위치/privateEndpointConnectionProxyAzureAsyncOperation/읽기 | 개인 엔드포인트 연결 프록시 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft DBforMySQL/위치/privateEndpointConnectionProxyOperationResults/읽기 | 개인 엔드포인트 연결 프록시 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | 서버 위협 검색 작업 결과의 목록을 반환 합니다. |
> | 실행력 | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | 서버 위협 검색 작업 결과의 목록을 반환 합니다. |
> | 실행력 | Microsoft DBforMySQL/위치/serverKeyAzureAsyncOperation/읽기 | 투명 한 데이터 암호화 서버 키에 대 한 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft DBforMySQL/위치/serverKeyOperationResults/read | 투명 한 데이터 암호화 서버 키에 대 한 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.DBforMySQL/operations/read | MySQL 작업 목록을 반환 합니다. |
> | 실행력 | Microsoft.DBforMySQL/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> | 실행력 | Microsoft.DBforMySQL/register/action | MySQL 리소스 공급자 등록 |
> | 실행력 | Microsoft.DBforMySQL/servers/administrators/delete | MySQL 서버의 기존 관리자를 삭제 합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/administrators/read | MySQL server 관리자 목록을 가져옵니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/administrators/write | 지정 된 매개 변수를 사용 하 여 MySQL 서버 관리자를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | 새 권장 구성 작업 세션을 만듭니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/advisors/read | 관리자 목록을 반환합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/advisors/read | Advisor 반환 |
> | 실행력 | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 권장된 작업 목록을 반환 |
> | 실행력 | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 권장된 작업 목록을 반환 |
> | 실행력 | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 권장 작업 반환 |
> | 실행력 | Microsoft.DBforMySQL/servers/configurations/read | 서버에 대한 구성 목록을 반환하거나 지정된 구성에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/configurations/write | 지정된 구성의 값을 업데이트합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/databases/delete | 기존 MySQL 데이터베이스를 삭제 합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/databases/read | MySQL 데이터베이스 목록을 반환 하거나 지정 된 데이터베이스에 대 한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/databases/write | 지정 된 매개 변수를 사용 하 여 MySQL 데이터베이스를 만들거나 지정 된 데이터베이스에 대 한 속성을 업데이트 합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/delete | 기존 서버를 삭제합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/firewallRules/delete | 기존 방화벽 규칙을 삭제합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/firewallRules/read | 서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/firewallRules/write | 지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다. |
> | 실행력 | Microsoft DBforMySQL/servers/keys/delete | 기존 서버 키를 삭제합니다. |
> | 실행력 | Microsoft DBforMySQL/servers/keys/read | 서버 키 목록을 가져오거나 지정된 서버 키에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft DBforMySQL/servers/keys/write | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 서버 키에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/logFiles/read | MySQL 로그의 목록을 반환 합니다. |
> | 실행력 | Microsoft DBforMySQL/servers/privateEndpointConnectionProxies/delete | 기존 개인 엔드포인트 연결 프록시를 삭제 합니다. |
> | 실행력 | Microsoft DBforMySQL/servers/privateEndpointConnectionProxies/read | 개인 엔드포인트 연결 프록시의 목록을 반환 하거나, 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성을 가져옵니다. |
> | 실행력 | Microsoft DBforMySQL/servers/privateEndpointConnectionProxies/validate/action | NRP 쪽에서 개인 엔드포인트 연결 만들기 호출의 유효성을 검사 합니다. |
> | 실행력 | Microsoft DBforMySQL/servers/privateEndpointConnectionProxies/write | 지정 된 매개 변수를 사용 하 여 개인 엔드포인트 연결 프록시를 만들거나 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 실행력 | Microsoft DBforMySQL/servers/privateEndpointConnections/delete | 기존 개인 엔드포인트 연결을 삭제 합니다. |
> | 실행력 | Microsoft DBforMySQL/servers/privateEndpointConnections/read | 개인 엔드포인트 연결의 목록을 반환 하거나, 지정 된 개인 엔드포인트 연결에 대 한 속성을 가져옵니다. |
> | 실행력 | Microsoft DBforMySQL/servers/privateEndpointConnections/write | 기존 개인 엔드포인트 연결을 승인 하거나 거부 합니다. |
> | 실행력 | Microsoft DBforMySQL/servers/privateLinkResources/read | 해당 MySQL Server에 대 한 개인 링크 리소스 가져오기 |
> | 실행력 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | MySQL 서버에 사용 가능한 로그를 가져오기 |
> | 실행력 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/queryTexts/action | 쿼리 목록의 텍스트 반환 |
> | 실행력 | Microsoft.DBforMySQL/servers/queryTexts/action | 쿼리 텍스트를 반환합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/recoverableServers/read | 복구 가능한 MySQL 서버 정보를 반환합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/replicas/read | MySQL 서버의 읽기 복제본 가져오기 |
> | 실행력 | Microsoft.DBforMySQL/servers/restart/action | 특정 서버를 다시 시작 합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 지정된 서버에 구성된 서버 위협 검색 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | 지정된 서버에 대한 서버 위협 검색 정책을 변경합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/topQueryStatistics/read | 최상위 쿼리에 대한 쿼리 통계 목록을 반환합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/topQueryStatistics/read | 쿼리 통계 반환 |
> | 실행력 | Microsoft.DBforMySQL/servers/updateConfigurations/action | 지정된 서버의 구성을 업데이트합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 기존 가상 네트워크 규칙을 삭제합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/waitStatistics/read | 인스턴스에 대한 대기 통계를 반환합니다. |
> | 실행력 | Microsoft.DBforMySQL/servers/waitStatistics/read | 대기 통계 반환 |
> | 실행력 | Microsoft.DBforMySQL/servers/write | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DBforPostgreSQL/checkNameAvailability/action | 지정된 서버 이름을 지정된 구독에 대해 전 세계적으로 프로비전에 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | PostgreSQL 서버 작업 결과 반환 |
> | 실행력 | Microsoft.DBforPostgreSQL/locations/operationResults/read | ResourceGroup 기반 PostgreSQL 서버 작업 결과 반환 |
> | 실행력 | Microsoft.DBforPostgreSQL/locations/operationResults/read | PostgreSQL 서버 작업 결과 반환 |
> | 실행력 | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> | 실행력 | DBforPostgreSQL/위치/privateEndpointConnectionAzureAsyncOperation/읽기 | 개인 엔드포인트 연결 작업에 대 한 결과를 가져옵니다. |
> | 실행력 | DBforPostgreSQL/위치/privateEndpointConnectionOperationResults/읽기 | 개인 엔드포인트 연결 작업에 대 한 결과를 가져옵니다. |
> | 실행력 | DBforPostgreSQL/위치/privateEndpointConnectionProxyAzureAsyncOperation/읽기 | 개인 엔드포인트 연결 프록시 작업의 결과를 가져옵니다. |
> | 실행력 | DBforPostgreSQL/위치/privateEndpointConnectionProxyOperationResults/읽기 | 개인 엔드포인트 연결 프록시 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | 서버 위협 검색 작업 결과의 목록을 반환 합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | 서버 위협 검색 작업 결과의 목록을 반환 합니다. |
> | 실행력 | DBforPostgreSQL/위치/serverKeyAzureAsyncOperation/읽기 | 투명 한 데이터 암호화 서버 키에 대 한 진행 중인 작업을 가져옵니다. |
> | 실행력 | DBforPostgreSQL/위치/serverKeyOperationResults/read | 투명 한 데이터 암호화 서버 키에 대 한 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/operations/read | PostgreSQL 작업의 목록을 반환 합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/performanceTiers/read | 사용할 수 있는 성능 계층 목록을 반환합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/register/action | PostgreSQL 리소스 공급자 등록 |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/administrators/delete | PostgreSQL 서버의 기존 관리자를 삭제 합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/administrators/read | PostgreSQL 서버 관리자의 목록을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/administrators/write | 지정 된 매개 변수를 사용 하 여 PostgreSQL 서버 관리자를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/advisors/read | 관리자 목록을 반환합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | 권장된 작업 목록을 반환 |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | 권장 사항 수행 |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/configurations/read | 서버에 대한 구성 목록을 반환하거나 지정된 구성에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/configurations/write | 지정된 구성의 값을 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/databases/delete | 기존 PostgreSQL 데이터베이스를 삭제 합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/databases/read | PostgreSQL 데이터베이스의 목록을 반환 하거나 지정 된 데이터베이스에 대 한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/databases/write | 지정 된 매개 변수를 사용 하 여 PostgreSQL 데이터베이스를 만들거나 지정 된 데이터베이스에 대 한 속성을 업데이트 합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/delete | 기존 서버를 삭제합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 기존 방화벽 규칙을 삭제합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/firewallRules/read | 서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다. |
> | 실행력 | DBforPostgreSQL/servers/keys/delete | 기존 서버 키를 삭제합니다. |
> | 실행력 | DBforPostgreSQL/서버/키/읽기 | 서버 키 목록을 가져오거나 지정된 서버 키에 대한 속성을 가져옵니다. |
> | 실행력 | DBforPostgreSQL/servers/keys/write | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 서버 키에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/logFiles/read | PostgreSQL LogFiles의 목록을 반환 합니다. |
> | 실행력 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/delete | 기존 개인 엔드포인트 연결 프록시를 삭제 합니다. |
> | 실행력 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/read | 개인 엔드포인트 연결 프록시의 목록을 반환 하거나, 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성을 가져옵니다. |
> | 실행력 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | NRP 쪽에서 개인 엔드포인트 연결 만들기 호출의 유효성을 검사 합니다. |
> | 실행력 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | 지정 된 매개 변수를 사용 하 여 개인 엔드포인트 연결 프록시를 만들거나 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 실행력 | DBforPostgreSQL/servers/privateEndpointConnections/delete | 기존 개인 엔드포인트 연결을 삭제 합니다. |
> | 실행력 | DBforPostgreSQL/servers/privateEndpointConnections/read | 개인 엔드포인트 연결의 목록을 반환 하거나, 지정 된 개인 엔드포인트 연결에 대 한 속성을 가져옵니다. |
> | 실행력 | DBforPostgreSQL/servers/privateEndpointConnections/write | 기존 개인 엔드포인트 연결을 승인 하거나 거부 합니다. |
> | 실행력 | DBforPostgreSQL/servers/privateLinkResources/read | 해당 PostgreSQL 서버에 대 한 개인 링크 리소스를 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Postgres 서버에 사용 가능한 로그를 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/queryTexts/action | 쿼리 텍스트를 반환합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/queryTexts/read | 쿼리 목록의 텍스트 반환 |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 복구 가능한 PostgreSQL 서버 정보를 반환합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/replicas/read | PostgreSQL 서버의 읽기 복제본 가져오기 |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/restart/action | 특정 서버를 다시 시작 합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 지정된 서버에 구성된 서버 위협 검색 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 지정된 서버에 대한 서버 위협 검색 정책을 변경합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 최상위 쿼리에 대한 쿼리 통계 목록을 반환합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 지정된 서버의 구성을 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 기존 가상 네트워크 규칙을 삭제합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | 인스턴스에 대한 대기 통계를 반환합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/servers/write | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/configurations/read | 서버에 대한 구성 목록을 반환하거나 지정된 구성에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/configurations/write | 지정된 구성의 값을 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/delete | 기존 서버를 삭제합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | 기존 방화벽 규칙을 삭제합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | 서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | 지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Postgres 서버에 사용 가능한 로그를 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | 지정된 서버의 구성을 업데이트합니다. |
> | 실행력 | Microsoft.DBforPostgreSQL/serversv2/write | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft. 장치/계정/diagnosticSettings/읽기 | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft. 장치/계정/diagnosticSettings/쓰기 | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft. 장치/계정/s i d 정의/읽기 | IotHub 서비스에 사용 가능한 로그 정의를 가져옵니다. |
> | 실행력 | Microsoft. 장치/계정/metricDefinitions/읽기 | IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Devices/checkNameAvailability/Action | IotHub 이름이 사용 가능한지 확인합니다. |
> | 실행력 | Microsoft.Devices/checkNameAvailability/Action | IotHub 이름이 사용 가능한지 확인합니다. |
> | 실행력 | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 프로비전 서비스 이름을 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 프로비전 서비스 이름을 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft. Devices/digitalTwins/Delete | 기존 디지털 쌍 계정과 모든 자식을 삭제 합니다. |
> | 실행력 | Microsoft. Devices/digitalTwins/operationresults/Read | 디지털 쌍 계정에 대해 작업 상태 가져오기 |
> | 실행력 | Microsoft. 장치/digitalTwins/읽기 | 구독과 연결 된 디지털 Twins 계정의 목록을 가져옵니다. |
> | 실행력 | Microsoft. Devices/digitalTwins/sku/읽기 | 디지털 쌍 계정에 대 한 유효한 Sku 목록 가져오기 |
> | 실행력 | Microsoft. Devices/digitalTwins/Write | 새 Digitial Twins 계정 만들기 |
> | 실행력 | Microsoft.Devices/ElasticPools/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.Devices/ElasticPools/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft. Devices/elasticPools/eventGridFilters/Delete | Elastic Pool Event Grid 필터를 삭제 합니다. |
> | 실행력 | Microsoft. Devices/elasticPools/eventGridFilters/Read | Elastic Pool Event Grid 필터를 가져옵니다. |
> | 실행력 | Microsoft. Devices/elasticPools/eventGridFilters/Write | 새로 만들거나 기존 Elastic Pool Event Grid 필터를 업데이트 합니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 인증서를 삭제합니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 확인 코드를 생성합니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 인증서를 가져옵니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 인증서 리소스를 확인합니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 인증서를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/Delete | IotHub 테넌트 리소스를 삭제합니다. |
> | 실행력 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | EventHub 소비자 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | EventHub 소비자 그룹을 가져옵니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | EventHub 소비자 그룹을 만듭니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | 디바이스를 내보냅니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | IotHub 테넌트 통계 리소스를 가져옵니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | 디바이스 가져오기 |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | IotHub 테넌트 키를 가져옵니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 지정된 IotHub에 대해 제출된 작업 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | IotHub 테넌트 키를 가져옵니다. |
> | 실행력 | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | IotHub 서비스에 사용 가능한 로그 정의를 가져옵니다. |
> | 실행력 | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | 할당량 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/Read | IotHub 테넌트 리소스를 가져옵니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 모든 기존 경로에 대해 메시지를 테스트합니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 제공된 테스트 경로에 대해 메시지를 테스트합니다. |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | IotHub에 대한 모든 라우팅 엔드포인트의 상태를 가져옵니다. |
> | 실행력 | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | Iot 테넌트 허브 보안 설정에 대 한 비동기 Put 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Read | Iot 테넌트 허브에서 Azure Security Center 설정 가져오기 |
> | 실행력 | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Write | Iot 테넌트 허브에서 Azure Security Center 설정 업데이트 |
> | 실행력 | Microsoft.Devices/elasticPools/iotHubTenants/Write | IotHub 테넌트 리소스를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Devices/ElasticPools/metricDefinitions/read | IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/certificates/Delete | 인증서를 삭제합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 확인 코드를 생성합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/certificates/Read | 인증서를 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/certificates/verify/Action | 인증서 리소스를 확인합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/certificates/Write | 인증서를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/Delete | IotHub 리소스를 삭제합니다. |
> | 실행력 | Microsoft.Devices/IotHubs/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.Devices/IotHubs/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Event Grid 필터를 삭제합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/eventGridFilters/Read | Event Grid 필터를 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/eventGridFilters/Write | 새 Event Grid 필터를 만들거나 기존 Event Grid 필터를 업데이트합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | EventHub 소비자 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | EventHub 소비자 그룹을 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | EventHub 소비자 그룹을 만듭니다. |
> | 실행력 | Microsoft.Devices/iotHubs/exportDevices/Action | 디바이스를 내보냅니다. |
> | 실행력 | Microsoft.Devices/iotHubs/importDevices/Action | 디바이스 가져오기 |
> | 실행력 | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 지정된 이름의 IotHub 키를 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/iotHubStats/Read | IotHub 통계를 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/jobs/Read | 지정된 IotHub에 대해 제출된 작업 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/listkeys/Action | 모든 IotHub 키를 가져옵니다. |
> | 실행력 | Microsoft.Devices/IotHubs/logDefinitions/read | IotHub 서비스에 사용 가능한 로그 정의를 가져옵니다. |
> | 실행력 | Microsoft.Devices/IotHubs/metricDefinitions/read | IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/operationresults/Read | 작업 결과를 가져옵니다(사용되지 않는 API). |
> | 실행력 | Microsoft.Devices/iotHubs/quotaMetrics/Read | 할당량 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/Read | IotHub 리소스를 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/routing/$testall/Action | 모든 기존 경로에 대해 메시지를 테스트합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/routing/$testnew/Action | 제공된 테스트 경로에 대해 메시지를 테스트합니다. |
> | 실행력 | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | IotHub에 대한 모든 라우팅 엔드포인트의 상태를 가져옵니다. |
> | 실행력 | Microsoft. Devices/iotHubs/securitySettings/operationResults/Read | Iot Hub SecuritySettings에 대 한 비동기 Put 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft. Devices/iotHubs/securitySettings/Read | Iot Hub에서 Azure Security Center 설정 가져오기 |
> | 실행력 | Microsoft. Devices/iotHubs/securitySettings/Write | Iot Hub에서 Azure Security Center 설정 업데이트 |
> | 실행력 | Microsoft.Devices/iotHubs/skus/Read | 유효한 IotHub SKU를 가져옵니다. |
> | 실행력 | Microsoft.Devices/iotHubs/Write | IotHub 리소스를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Devices/locations/operationresults/Read | 작업 결과를 기반으로 위치를 가져옵니다. |
> | 실행력 | Microsoft.Devices/operationresults/Read | 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft.Devices/operations/Read | 모든 ResourceProvider 작업을 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/certificates/Delete | 인증서를 삭제합니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 확인 코드를 생성합니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/certificates/Read | 인증서를 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/certificates/verify/Action | 인증서 리소스를 확인합니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/certificates/Write | 인증서를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/Delete | IotDps 리소스를 삭제합니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/keys/listkeys/Action | 키 이름에 대 한 IotDps 키를 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/listkeys/Action | 모든 IotDps 키를 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/logDefinitions/read | 서비스를 프로비전하는 데 사용할 수 있는 로그 정의를 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/metricDefinitions/read | 서비스를 프로비전하는 데 사용할 수 있는 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/operationresults/Read | DPS 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/Read | IotDps 리소스를 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/skus/Read | 유효한 IotDps Sku를 가져옵니다. |
> | 실행력 | Microsoft.Devices/provisioningServices/Write | IotDps 리소스를 만듭니다. |
> | 실행력 | Microsoft.Devices/register/action | IotHub 리소스 공급자에 대한 구독을 등록하고 IotHub 리소스를 만들도록 설정합니다. |
> | 실행력 | Microsoft.Devices/usages/Read | 이 공급자에 대한 구독 사용 정보를 가져옵니다. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DevSpaces/controllers/delete | Azure Dev Spaces 컨트롤러 및 데이터 평면 서비스 삭제 |
> | 실행력 | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Azure Dev Spaces 컨트롤러의 인프라에 대한 연결 세부 정보 나열 |
> | 실행력 | Microsoft.DevSpaces/controllers/read | Azure Dev Spaces 컨트롤러 속성 읽기 |
> | 실행력 | Microsoft.DevSpaces/controllers/write | Azure Dev Spaces 컨트롤러 속성 만들기 또는 업데이트 |
> | 실행력 | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | 컨테이너 호스트에 대 한 기존 컨트롤러 매핑 확인 |
> | 실행력 | Microsoft DevSpaces/위치/checkContainerHostMapping/읽기 | 컨테이너 호스트에 대 한 기존 컨트롤러 매핑 확인 |
> | 실행력 | Microsoft.DevSpaces/locations/operationresults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft.DevSpaces/register/action | 구독으로 Microsoft Dev Spaces 리소스 공급자 등록 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DevTestLab/labCenters/delete | 랩 센터를 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labCenters/read | 랩 센터를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labCenters/write | 랩 센터를 추가 또는 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Azure Resource Manager 템플릿을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 지정 된 아티팩트에 대 한 Azure Resource Manager 템플릿을 생성 하 고, 필요한 파일을 저장소 계정에 업로드 하 고, 생성 된 아티팩트의 유효성을 검사 합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | 아티팩트를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/artifactSources/delete | 아티팩트 원본을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/artifactSources/read | 아티팩트 원본을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/artifactSources/write | 아티팩트 원본을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/ClaimAnyVm/action | 랩에서 임의 클레임 가능 가상 머신을 클레임합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/costs/read | 비용을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/costs/write | 비용을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/CreateEnvironment/action | 랩에 가상 머신을 만듭니다. |
> | 실행력 | Microsoft.DevTestLab/labs/customImages/delete | 사용자 지정 이미지를 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/customImages/read | 사용자 지정 이미지를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/customImages/write | 사용자 지정 이미지를 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/delete | 랩을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | 현재 사용자가 랩에서 유효한 프로필을 갖고 있는지 확인 합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/ExportResourceUsage/action | 랩 리소스 사용량을 스토리지 계정으로 내보냅니다. |
> | 실행력 | Microsoft.DevTestLab/labs/formulas/delete | 수식을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/formulas/read | 수식을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/formulas/write | 수식을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/galleryImages/read | 갤러리 이미지를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/GenerateUploadUri/action | 랩에 사용자 지정 디스크 이미지를 업로드하기 위한 URI를 생성합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 가상 머신을 다른 랩으로 가져옵니다. |
> | 실행력 | Microsoft.DevTestLab/labs/ListVhds/action | 사용자 지정 이미지를 만드는 데 사용할 수 있는 디스크 이미지를 나열합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/notificationChannels/delete | 알림 채널을 삭제 합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 제공된 채널에 알림을 보냅니다. |
> | 실행력 | Microsoft.DevTestLab/labs/notificationChannels/read | 알림 채널을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/notificationChannels/write | 알림 채널을 추가 하거나 수정 합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | 랩 정책을 평가합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/policySets/policies/delete | 정책을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/policySets/policies/read | 정책을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/policySets/policies/write | 정책을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/policySets/read | 정책 집합을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/read | 랩을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/schedules/delete | 일정을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/schedules/Execute/action | 일정을 실행합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 적용 가능한 모든 일정을 나열합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/schedules/read | 일정을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/schedules/write | 일정을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/serviceRunners/delete | 서비스 실행기를 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/serviceRunners/read | 서비스 실행기를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/serviceRunners/write | 서비스 실행기를 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/sharedGalleries/delete | 공유 갤러리를 삭제 합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/sharedGalleries/read | 공유 갤러리를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | 공유 이미지를 삭제 합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | 공유 이미지를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | 공유 이미지를 추가 하거나 수정 합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/sharedGalleries/write | 공유 갤러리를 추가 하거나 수정 합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/delete | 사용자 프로필을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/disks/Attach/action | 디스크의 임대를 만들고 가상 머신에 연결합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/disks/delete | 디스크를 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/disks/Detach/action | 가상 머신에 연결된 디스크의 임대를 분리하고 해제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/disks/read | 디스크를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/disks/write | 디스크를 추가 또는 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/environments/delete | 환경을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/environments/read | 환경을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/environments/write | 환경을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/read | 사용자 프로필을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/secrets/delete | 암호를 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/secrets/read | 암호를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/secrets/write | 암호를 추가 또는 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | 서비스 패브릭을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 해당 시작/중지 일정이 있는 경우 이를 나열합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/read | 서비스 패브릭을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | 일정을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | 일정을 실행합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | 일정을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | 일정을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | 서비스 패브릭을 시작합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | 서비스 패브릭을 중지합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/serviceFabrics/write | 서비스 패브릭을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/users/write | 사용자 프로필을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 가상 머신에 새 또는 기존 데이터 디스크를 연결합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 가상 머신에 아티팩트를 적용합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/claim/action | 기존 가상 머신의 소유권을 가져옵니다. |
> | 실행력 | Microsoft DevTestLab/labs/virtualMachines/ClearArtifactResults/작업 | 가상 컴퓨터의 아티팩트 결과를 지웁니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/delete | 가상 머신을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | 가상 머신에서 지정된 디스크를 분리합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | 가상 머신에 대한 RDP 파일의 콘텐츠를 나타내는 문자열을 가져옵니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | 해당 시작/중지 일정이 있는 경우 이를 나열합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/read | 가상 머신을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | 가상 머신을 다시 배포합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | 가상 머신 크기를 조정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | 가상 머신을 다시 시작합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | 일정을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | 일정을 실행합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | 일정을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | 일정을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/Start/action | 가상 머신을 시작합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | 가상 머신 중지 |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | 현재 사용자가 소유할 가상 머신에 연결된 모든 데이터 디스크를 전송합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | 기존 가상 머신의 소유권을 해제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualMachines/write | 가상 머신을 추가 또는 수정합니다. |
> | 실행력 | Microsoft. DevTestLab/labs/virtualNetworks/bastionHosts/delete | Bastionhosts를 삭제 합니다. |
> | 실행력 | Microsoft. DevTestLab/labs/virtualNetworks/bastionHosts/read | Bastionhosts을 읽습니다. |
> | 실행력 | Microsoft. DevTestLab/labs/virtualNetworks/bastionHosts/write | Bastionhosts를 추가 하거나 수정 합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualNetworks/delete | 가상 네트워크를 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualNetworks/read | 가상 네트워크를 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/virtualNetworks/write | 가상 네트워크를 추가 또는 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/vmPools/delete | 가상 머신 풀을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/vmPools/read | 가상 머신 풀을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/labs/vmPools/write | 가상 머신 풀을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/labs/write | 랩을 추가 또는 수정합니다. |
> | 실행력 | Microsoft.DevTestLab/locations/operations/read | 읽기 작업 |
> | 실행력 | Microsoft.DevTestLab/register/action | 구독을 등록합니다. |
> | 실행력 | Microsoft.DevTestLab/schedules/delete | 일정을 삭제합니다. |
> | 실행력 | Microsoft.DevTestLab/schedules/Execute/action | 일정을 실행합니다. |
> | 실행력 | Microsoft.DevTestLab/schedules/read | 일정을 읽습니다. |
> | 실행력 | Microsoft.DevTestLab/schedules/Retarget/action | 일정의 대상 리소스 ID를 업데이트합니다. |
> | 실행력 | Microsoft.DevTestLab/schedules/write | 일정을 추가하거나 수정합니다. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DocumentDB/databaseAccountNames/read | 이름 가용성을 확인합니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/backup/action | 백업 구성하는 요청 제출 |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/delete | Cassandra keyspace를 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/read | Cassandra keyspace을 읽거나 모든 Cassandra keyspaces를 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/delete | Cassandra 테이블을 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/read | Cassandra 테이블을 읽거나 모든 Cassandra 테이블을 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/read | Cassandra 테이블 처리량을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/write | Cassandra 테이블 처리량을 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/tables/write | Cassandra 테이블을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/read | Cassandra keyspace 처리량을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/write | Cassandra keyspace 처리량을 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/cassandraKeyspaces/write | Cassandra keyspace를 만듭니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | 데이터베이스 계정의 리소스 그룹을 변경합니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | 컬렉션 메트릭 정의를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | 컬렉션 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | 데이터베이스 계정 파티션 키 수준 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | 데이터베이스 계정 파티션 수준 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | 컬렉션의 데이터베이스 계정 파티션을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | 데이터베이스 계정 파티션 수준 사용량을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | 컬렉션 사용 현황을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | 데이터베이스 메트릭 정의를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | 데이터베이스 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | 데이터베이스 사용 현황을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/delete | 데이터베이스 계정을 삭제합니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | 데이터베이스 계정 영역의 장애 조치 우선 순위를 변경합니다. 수동 장애 조치 작업을 수행하는 데 사용됩니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/getBackupPolicy/action | 데이터베이스 계정의 백업 정책 가져오기 |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/delete | Gremlin 데이터베이스를 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/graph/delete | Gremlin 그래프를 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/graph/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/graph/read | Gremlin 그래프를 읽거나 모든 Gremlin 그래프를 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/graph/throughputSettings/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/graph/throughputSettings/read | Gremlin graph 처리량을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/graph/throughputSettings/write | Gremlin graph 처리량을 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/graph/write | Gremlin graph를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/read | Gremlin 데이터베이스를 읽거나 모든 Gremlin 데이터베이스를 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/read | Gremlin 데이터베이스 처리량을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/write | Gremlin 데이터베이스 처리량을 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/gremlinDatabases/write | Gremlin 데이터베이스를 만듭니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | 데이터베이스 계정에 대한 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/listKeys/action | 데이터베이스 계정의 키를 나열합니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | 데이터베이스 계정 메트릭 정의를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/metrics/read | 데이터베이스 계정 메트릭을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collections/delete | MongoDB 컬렉션을 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collections/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collections/read | MongoDB 컬렉션을 읽거나 모든 MongoDB 컬렉션을 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/read | MongoDB 컬렉션 처리량을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/write | MongoDB 컬렉션 처리량을 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/collections/write | MongoDB 컬렉션을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/delete | MongoDB 데이터베이스를 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/read | MongoDB 데이터베이스를 읽거나 모든 MongoDB 데이터베이스를 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/read | MongoDB 데이터베이스 처리량을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/write | MongoDB 데이터베이스 처리량을 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/mongodbDatabases/write | MongoDB 데이터베이스를 만듭니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | 데이터베이스 계정의 영역을 오프라인으로 설정합니다.  |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | 데이터베이스 계정의 영역을 온라인으로 설정합니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 비동기 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 대기 시간 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/percentile/read | 복제 대기 시간의 백분위 수를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 특정 소스 및 대상 지역에 대한 대기 시간 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 특정 대상 지역에 대한 대기 시간 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | 데이터베이스 계정의 개인 엔드포인트 연결 프록시를 삭제 합니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | 개인 엔드포인트 연결 프록시 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | 데이터베이스 계정의 개인 엔드포인트 연결 프록시 읽기 |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | 데이터베이스 계정의 개인 엔드포인트 연결 프록시 유효성 검사 |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | 데이터베이스 계정의 개인 엔드포인트 연결 프록시를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnections/delete | 데이터베이스 계정의 개인 엔드포인트 연결 삭제 |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/read | PrivateEndpointConnenctions 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnections/read | 개인 엔드포인트 연결을 읽거나 데이터베이스 계정의 모든 개인 엔드포인트 연결을 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/privateEndpointConnections/write | 데이터베이스 계정의 개인 엔드포인트 연결을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/privateLinkResources/read | 개인 링크 리소스를 읽거나 데이터베이스 계정의 모든 개인 링크 리소스를 나열 합니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/read | 데이터베이스 계정을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 데이터베이스 계정 읽기 전용 키를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | 데이터베이스 계정 읽기 전용 키를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | 데이터베이스 계정의 키를 순환합니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | 지역 컬렉션 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | 지역 데이터베이스 계정 파티션 키 수준 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | 지역 데이터베이스 계정 파티션 수준 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | 컬렉션의 지역 데이터베이스 계정 파티션을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | 지역 및 데이터베이스 계정 메트릭을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/restore/action | 복원 요청 제출 |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/삭제 | SQL 컨테이너를 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/읽기 | SQL 컨테이너를 읽거나 모든 SQL 컨테이너를 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/storedProcedures/delete | SQL 저장 프로시저를 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/storedProcedures/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/storedProcedures/read | SQL 저장 프로시저를 읽거나 모든 SQL 저장 프로시저를 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/storedProcedures/write | SQL 저장 프로시저를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/throughputSettings/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/throughputSettings/read | SQL 컨테이너 처리량을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/throughputSettings/write | SQL 컨테이너 처리량을 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/트리거/삭제 | SQL 트리거를 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/트리거/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/트리거/읽기 | SQL 트리거를 읽거나 모든 SQL 트리거를 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/트리거/쓰기 | SQL 트리거를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/userDefinedFunctions/delete | SQL 사용자 정의 함수를 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/userDefinedFunctions/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/userDefinedFunctions/read | SQL 사용자 정의 함수를 읽거나 모든 SQL 사용자 정의 함수를 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/userDefinedFunctions/write | SQL 사용자 정의 함수를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/쓰기 | SQL 컨테이너를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/delete | SQL 데이터베이스를 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/read | SQL 데이터베이스를 읽거나 모든 SQL 데이터베이스를 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/read | SQL database 처리량을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/write | SQL database 처리량을 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/sqlDatabases/write | SQL 데이터베이스를 만듭니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/tables/delete | 테이블을 삭제 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/tables/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/tables/read | 테이블을 읽거나 모든 테이블을 나열 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/tables/throughputSettings/operationResults/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/tables/throughputSettings/read | 테이블 처리량을 읽습니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/tables/throughputSettings/write | 테이블 처리량을 업데이트 합니다. |
> | 실행력 | Microsoft DocumentDB/databaseAccounts/tables/write | 테이블을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/usages/read | 데이터베이스 계정 사용 현황을 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/databaseAccounts/write | 데이터베이스 계정을 업데이트합니다. |
> | 실행력 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Microsoft.DocumentDB에 가상 네트워크 또는 서브넷이 삭제됨을 알립니다. |
> | 실행력 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | deleteVirtualNetworkOrSubnets 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/locations/operationsStatus/read | 비동기 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/operationResults/read | 비동기 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.DocumentDB/operations/read | Microsoft DocumentDB에 사용 가능한 작업을 읽습니다.  |
> | 실행력 | Microsoft.DocumentDB/register/action |  구독에 대한 Microsoft DocumentDB 리소스 공급자를 등록합니다. |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.DomainRegistration/checkDomainAvailability/Action | 도메인을 구매할 수 있는지를 확인합니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/Delete | 기존 도메인을 삭제합니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 소유권 식별자를 삭제합니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 소유권 식별자를 나열합니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 소유권 식별자를 가져옵니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 업데이트 식별자를 만듭니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/operationresults/Read | 도메인 작업을 가져옵니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/Read | 도메인 목록을 가져옵니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/Read | 도메인을 가져옵니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/renew/Action | 기존 도메인을 갱신합니다. |
> | 실행력 | Microsoft.DomainRegistration/domains/Write | 새 도메인을 추가하거나 기존 도메인을 업데이트합니다. |
> | 실행력 | Microsoft.DomainRegistration/generateSsoRequest/Action | 도메인 제어 센터에 대한 로그인 요청을 생성합니다. |
> | 실행력 | Microsoft.DomainRegistration/listDomainRecommendations/Action | 키워드를 기준으로 목록 도메인 권장 사항을 검색합니다. |
> | 실행력 | Microsoft.DomainRegistration/operations/Read | 앱 서비스 도메인 등록의 모든 작업을 나열합니다. |
> | 실행력 | Microsoft.DomainRegistration/register/action | 구독에 대한 Microsoft 도메인 리소스 공급자를 등록합니다. |
> | 실행력 | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 규약 작업을 나열합니다. |
> | 실행력 | Microsoft.DomainRegistration/topLevelDomains/Read | 최상위 도메인을 가져옵니다. |
> | 실행력 | Microsoft.DomainRegistration/topLevelDomains/Read | 최상위 도메인을 가져옵니다. |
> | 실행력 | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | 도메인 구매 개체를 제출하지 않고 유효성을 검사합니다. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.EventGrid/domains/delete | 도메인을 삭제합니다. |
> | 실행력 | Microsoft.EventGrid/domains/listKeys/action | 도메인에 대한 키를 나열합니다. |
> | 실행력 | Microsoft EventGrid/domains/providers/Microsoft. Insights/logDefinitions/읽기 | 진단 로그에 대 한 액세스 허용 |
> | 실행력 | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | 도메인에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.EventGrid/domains/read | 도메인을 읽습니다. |
> | 실행력 | Microsoft.EventGrid/domains/regenerateKey/action | 도메인에 대한 키를 다시 생성합니다. |
> | 실행력 | Microsoft EventGrid/도메인/항목/삭제 | 도메인 항목 삭제 |
> | 실행력 | Microsoft.EventGrid/domains/topics/read | 도메인 토픽을 읽습니다. |
> | 실행력 | Microsoft EventGrid/도메인/토픽/쓰기 | 도메인 항목 만들기 또는 업데이트 |
> | 실행력 | Microsoft.EventGrid/domains/write | 도메인을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.EventGrid/eventSubscriptions/delete | EventSubscription 삭제 |
> | 실행력 | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | 이벤트 구독의 전체 url을 가져옵니다. |
> | 실행력 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | 이벤트 구독에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | 이벤트 구독에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | eventSubscription에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.EventGrid/eventSubscriptions/read | EventSubscription 읽기 |
> | 실행력 | Microsoft.EventGrid/eventSubscriptions/write | EventSubscription 만들기 또는 업데이트 |
> | 실행력 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | 토픽에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | 토픽에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | 토픽에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.EventGrid/extensionTopics/read | extensionTopic을 읽습니다. |
> | 실행력 | Microsoft.EventGrid/locations/eventSubscriptions/read | 지역 이벤트 구독을 나열합니다. |
> | 실행력 | Microsoft.EventGrid/locations/operationResults/read | 국가별 작업의 결과를 읽습니다. |
> | 실행력 | Microsoft.EventGrid/locations/operationsStatus/read | 국가별 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | 항목 유형별로 지역 이벤트 구독을 나열합니다. |
> | 실행력 | Microsoft.EventGrid/operationResults/read | 작업의 결과를 읽습니다. |
> | 실행력 | Microsoft.EventGrid/operations/read | EventGrid 작업을 나열합니다. |
> | 실행력 | Microsoft.EventGrid/operationsStatus/read | 작업의 상태를 읽습니다. |
> | 실행력 | Microsoft.EventGrid/register/action | EventGrid 리소스 공급자에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.EventGrid/topics/delete | 항목 삭제 |
> | 실행력 | Microsoft.EventGrid/topics/listKeys/action | 토픽에 대한 키를 나열합니다. |
> | 실행력 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | 토픽에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | 토픽에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft EventGrid/토픽/공급자/Microsoft Insights/logDefinitions/읽기 | 진단 로그에 대 한 액세스 허용 |
> | 실행력 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | 토픽에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.EventGrid/topics/read | 토픽을 읽습니다. |
> | 실행력 | Microsoft.EventGrid/topics/regenerateKey/action | 토픽에 대한 키를 다시 생성합니다. |
> | 실행력 | Microsoft.EventGrid/topics/write | 토픽을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.EventGrid/topictypes/eventSubscriptions/read | 항목 유형별로 글로벌 이벤트 구독을 나열합니다. |
> | 실행력 | Microsoft.EventGrid/topictypes/eventtypes/read | topictype에서 지원하는 eventtype을 읽습니다. |
> | 실행력 | Microsoft.EventGrid/topictypes/read | 토픽을 읽습니다. |
> | 실행력 | Microsoft.EventGrid/unregister/action | EventGrid 리소스 공급자에 대한 구독을 등록 취소합니다. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.EventHub/availableClusterRegions/read | Azure 지역에서 사용 가능한 미리 프로 비전 된 클러스터를 나열 하는 읽기 작업입니다. |
> | 실행력 | Microsoft.EventHub/checkNameAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. |
> | 실행력 | Microsoft.EventHub/checkNamespaceAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. 이 API는 사용되지 않으므로 CheckNameAvailability를 대신 사용하세요. |
> | 실행력 | Microsoft.EventHub/clusters/delete | 기존 클러스터 리소스를 삭제 합니다. |
> | 실행력 | Microsoft.EventHub/clusters/namespaces/read | 클러스터 내의 네임 스페이스에 대 한 네임 스페이스 Azure Resource Manager Id를 나열 합니다. |
> | 실행력 | Microsoft.EventHub/clusters/operationresults/read | 비동기 클러스터 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | 클러스터 메트릭 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/clusters/read | 클러스터 리소스 설명을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/clusters/write | 기존 클러스터 리소스를 만들거나 수정 합니다. |
> | 실행력 | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | 지정된 VNet에 대한 EventHub 리소스 공급자의 VNet 규칙을 삭제합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/authorizationRules/action | 네임스페이스 권한 부여 규칙을 업데이트합니다. 이 API는 더 이상 사용되지 않습니다. 그 대신 PUT 호출을 사용하여 네임스페이스 권한 부여 규칙을 업데이트하세요. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 실행력 | Microsoft.EventHub/namespaces/authorizationRules/delete | 네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다.  |
> | 실행력 | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 네임스페이스 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/authorizationRules/read | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/authorizationRules/write | 네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.EventHub/namespaces/Delete | 네임스페이스 리소스를 삭제합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 재해 복구 기본 네임스페이스의 권한 부여 규칙 키를 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | 재해 복구 기본 네임스페이스의 권한 부여 규칙을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | 재해 복구를 사용하지 않도록 설정하고 기본 네임스페이스에서 보조 네임스페이스로의 변경 복제를 중지합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 지정된 구독에서 네임스페이스 별칭의 가용성을 확인합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 네임스페이스와 연결된 재해 복구 구성을 삭제합니다. 이 작업은 기본 네임스페이스를 통해서만 호출할 수 있습니다. |
> | 실행력 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | GEO DR 장애 조치(failover)를 호출하고, 네임스페이스 별칭이 보조 네임스페이스를 가리키도록 다시 구성합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 네임스페이스와 연결된 재해 복구 구성을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 네임스페이스와 연결된 재해 복구 구성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | EventHub를 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> | 실행력 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | EventHub 권한 부여 규칙 삭제 작업 |
> | 실행력 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | EventHub의 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  EventHub 권한 부여 규칙 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | EventHub 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | ConsumerGroup 리소스 삭제 작업 |
> | 실행력 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | ConsumerGroup 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | ConsumerGroup 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/eventhubs/Delete | EventHub 리소스 삭제 작업 |
> | 실행력 | Microsoft.EventHub/namespaces/eventhubs/read | EventHub 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/eventhubs/write | EventHub 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/ipFilterRules/delete | IP 필터 리소스 삭제 |
> | 실행력 | Microsoft.EventHub/namespaces/ipFilterRules/read | IP 필터 리소스 가져오기 |
> | 실행력 | Microsoft.EventHub/namespaces/ipFilterRules/write | IP 필터 리소스 만들기 |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | 메시지 받기 |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | 메시지 보내기 |
> | 실행력 | Microsoft.EventHub/namespaces/messagingPlan/read | 네임스페이스에 대한 메시지 계획을 가져옵니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 실행력 | Microsoft.EventHub/namespaces/messagingPlan/write | 네임스페이스에 대한 메시지 계획을 업데이트합니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 실행력 | Microsoft. EventHub/네임 스페이스/networkruleset 집합/삭제 | VNET 규칙 리소스 삭제 |
> | 실행력 | Microsoft. EventHub/네임 스페이스/networkruleset 집합/읽기 | NetworkRuleSet 집합 리소스를 가져옵니다. |
> | 실행력 | Microsoft. EventHub/네임 스페이스/networkruleset 집합/쓰기 | VNET 규칙 리소스 만들기 |
> | 실행력 | Microsoft.EventHub/namespaces/networkrulesets/delete | VNET 규칙 리소스 삭제 |
> | 실행력 | Microsoft.EventHub/namespaces/networkrulesets/read | NetworkRuleSet 집합 리소스를 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/networkrulesets/write | VNET 규칙 리소스 만들기 |
> | 실행력 | Microsoft.EventHub/namespaces/operationresults/read | 네임스페이스 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 네임스페이스 로그 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/read | 네임스페이스 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS 네임스페이스를 제거합니다. |
> | 실행력 | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | VNET 규칙 리소스 삭제 |
> | 실행력 | Microsoft.EventHub/namespaces/virtualNetworkRules/read | VNET 규칙 리소스 가져오기 |
> | 실행력 | Microsoft.EventHub/namespaces/virtualNetworkRules/write | VNET 규칙 리소스 만들기 |
> | 실행력 | Microsoft.EventHub/namespaces/write | 네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다. |
> | 실행력 | Microsoft.EventHub/operations/read | 작업을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/register/action | EventHub 리소스 공급자에 대한 구독을 등록하고 EventHub 리소스를 만들도록 설정합니다. |
> | 실행력 | Microsoft.EventHub/sku/read | Sku 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/sku/regions/read | SkuRegions 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.EventHub/unregister/action | EventHub 리소스 공급자를 등록합니다. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Features/features/read | 구독 기능을 가져옵니다. |
> | 실행력 | Microsoft.Features/operations/read | 작업 목록을 가져옵니다. |
> | 실행력 | Microsoft.Features/providers/features/read | 지정된 리소스 공급자에서 구독의 기능을 가져옵니다. |
> | 실행력 | Microsoft.Features/providers/features/register/action | 지정된 리소스 공급자에서 구독의 기능을 등록합니다. |
> | 실행력 | Microsoft.Features/providers/features/unregister/action | 지정된 리소스 공급자에서 구독의 기능을 등록 취소합니다. |
> | 실행력 | Microsoft.Features/register/action | 구독 기능을 등록합니다. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | 게스트 구성 할당을 삭제 합니다. |
> | 실행력 | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | 게스트 구성 할당을 가져옵니다. |
> | 실행력 | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | 게스트 구성 할당 보고서를 가져옵니다. |
> | 실행력 | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | 새 게스트 구성 할당을 만듭니다. |
> | 실행력 | GuestConfiguration/작업/읽기 | GuestConfiguration 리소스 공급자에 대 한 작업을 가져옵니다. |
> | 실행력 | Microsoft.GuestConfiguration/register/action | GuestConfiguration 리소스 공급자에 대 한 구독을 등록 합니다. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.HDInsight/clusters/applications/delete | HDInsight 클러스터용 애플리케이션을 삭제합니다. |
> | 실행력 | Microsoft.HDInsight/clusters/applications/read | HDInsight 클러스터용 애플리케이션을 가져옵니다. |
> | 실행력 | Microsoft.HDInsight/clusters/applications/write | HDInsight 클러스터용 애플리케이션을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.HDInsight/clusters/changerdpsetting/action | HDInsight 클러스터의 RDP 설정을 변경합니다. |
> | 실행력 | Microsoft.HDInsight/clusters/configurations/action | HDInsight 클러스터 구성을 가져옵니다. |
> | 실행력 | Microsoft.HDInsight/clusters/configurations/read | HDInsight 클러스터 구성을 가져옵니다. |
> | 실행력 | Microsoft.HDInsight/clusters/delete | HDInsight 클러스터를 삭제합니다. |
> | 실행력 | Microsoft HDInsight/클러스터/확장/삭제 | HDInsight 클러스터에 대 한 클러스터 확장 삭제 |
> | 실행력 | Microsoft HDInsight/클러스터/확장/읽기 | HDInsight 클러스터에 대 한 클러스터 확장 가져오기 |
> | 실행력 | Microsoft HDInsight/클러스터/확장/쓰기 | HDInsight 클러스터에 대 한 클러스터 확장 만들기 |
> | 실행력 | Microsoft.HDInsight/clusters/getGatewaySettings/action | HDInsight 클러스터에 대 한 게이트웨이 설정 가져오기 |
> | 실행력 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | 리소스 HDInsight 클러스터에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | 리소스 HDInsight 클러스터에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | HDInsight 클러스터에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.HDInsight/clusters/read | HDInsight 클러스터에 대한 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.HDInsight/clusters/roles/resize/action | HDInsight 클러스터 크기를 조정합니다. |
> | 실행력 | Microsoft.HDInsight/clusters/updateGatewaySettings/action | HDInsight 클러스터에 대 한 게이트웨이 설정 업데이트 |
> | 실행력 | Microsoft.HDInsight/clusters/write | HDInsight 클러스터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.HDInsight/locations/capabilities/read | 구독 기능을 가져옵니다. |
> | 실행력 | Microsoft.HDInsight/locations/checkNameAvailability/read | 이름 가용성을 확인합니다. |
> | 실행력 | Microsoft HDInsight/register/action | 구독에 대 한 HDInsight 리소스 공급자 등록 |
> | 실행력 | Microsoft HDInsight/등록 취소/작업 | 구독에 대 한 HDInsight 리소스 공급자 등록 취소 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | HybridCompute/컴퓨터/삭제 | Azure Arc 컴퓨터 삭제 |
> | 실행력 | HybridCompute/컴퓨터/확장/삭제 | Azure Arc 확장 삭제 |
> | 실행력 | HybridCompute/컴퓨터/확장/읽기 | Azure Arc 확장을 읽습니다. |
> | 실행력 | HybridCompute/컴퓨터/확장/쓰기 | Azure Arc 확장 설치 또는 업데이트 |
> | 실행력 | HybridCompute/컴퓨터/읽기 | 모든 Azure Arc 컴퓨터를 읽습니다. |
> | 실행력 | HybridCompute/컴퓨터/다시 연결/작업 | Azure Arc 컴퓨터 다시 연결 |
> | 실행력 | HybridCompute/컴퓨터/쓰기 | Azure Arc 컴퓨터 작성 |
> | 실행력 | HybridCompute/register/action | HybridCompute 리소스 공급자를 등록 합니다. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ImportExport/jobs/delete | 기존 작업을 삭제합니다. |
> | 실행력 | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 지정된 작업에 대한 BitLocker 키를 가져옵니다. |
> | 실행력 | Microsoft.ImportExport/jobs/read | 지정된 작업에 대한 속성을 가져오거나 작업 목록을 반환합니다. |
> | 실행력 | Microsoft.ImportExport/jobs/write | 지정된 매개 변수를 사용하여 작업을 만들거나 지정된 작업에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.ImportExport/locations/read | 지정된 위치에 대한 속성을 가져오거나 위치 목록을 반환합니다. |
> | 실행력 | Microsoft. ImportExport/작업/읽기 | 리소스 공급자가 지 원하는 작업을 가져옵니다. |
> | 실행력 | Microsoft.ImportExport/register/action | Import/Export 리소스 공급자에 대한 구독을 등록하고 Import/Export 작업을 만들도록 설정합니다. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Insights/ActionGroups/Delete | 작업 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Insights/ActionGroups/Read | 작업 그룹을 읽습니다. |
> | 실행력 | Microsoft.Insights/ActionGroups/Write | 작업 그룹을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Insights/ActivityLogAlerts/Activated/Action | 활동 로그 경고가 활성화됩니다. |
> | 실행력 | Microsoft.Insights/ActivityLogAlerts/Delete | 활동 로그 경고를 삭제합니다. |
> | 실행력 | Microsoft.Insights/ActivityLogAlerts/Read | 활동 로그 경고를 읽습니다. |
> | 실행력 | Microsoft.Insights/ActivityLogAlerts/Write | 활동 로그 경고를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Insights/AlertRules/Activated/Action | 클래식 메트릭 경고가 활성화되었습니다. |
> | 실행력 | Microsoft.Insights/AlertRules/Delete | 클래식 메트릭 경고를 삭제합니다. |
> | 실행력 | Microsoft.Insights/AlertRules/Incidents/Read | 클래식 메트릭 경고 인시던트를 읽습니다. |
> | 실행력 | Microsoft.Insights/AlertRules/Read | 클래식 메트릭 경고를 읽습니다. |
> | 실행력 | Microsoft.Insights/AlertRules/Resolved/Action | 클래식 메트릭 경고가 해결되었습니다. |
> | 실행력 | Microsoft.Insights/AlertRules/Throttled/Action | 클래식 메트릭 경고 규칙이 제한되었습니다. |
> | 실행력 | Microsoft.Insights/AlertRules/Write | 클래식 메트릭 경고를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/Delete | 자동 크기 조정 설정을 삭제합니다. |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | 리소스 진단 설정을 읽습니다. |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | 리소스 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | 로그 정의 읽기 |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | 메트릭 정의 읽기 |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/Read | 자동 크기 조정 설정을 읽습니다. |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 자동 크기 조정 규모 축소가 시작되었습니다. |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 자동 크기 조정 규모 축소가 완료되었습니다. |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 자동 크기 조정 규모 확대가 시작되었습니다. |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 자동 크기 조정 규모 확대가 완료되었습니다. |
> | 실행력 | Microsoft.Insights/AutoscaleSettings/Write | 자동 크기 조정 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Insights/Baseline/Read | 메트릭 기준선 읽기 (미리 보기) |
> | 실행력 | Microsoft.Insights/CalculateBaseline/Read | 메트릭 값의 기준선 계산 (미리 보기) |
> | 실행력 | Microsoft.Insights/Components/AnalyticsItems/Delete | Application Insights 분석 항목을 삭제합니다. |
> | 실행력 | Microsoft.Insights/Components/AnalyticsItems/Read | Application Insights 분석 항목을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/AnalyticsItems/Write | Application Insights 분석 항목을 작성합니다. |
> | 실행력 | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights 분석 테이블 작업입니다. |
> | 실행력 | Microsoft.Insights/Components/AnalyticsTables/Delete | Application Insights 분석 테이블 스키마를 삭제합니다. |
> | 실행력 | Microsoft.Insights/Components/AnalyticsTables/Read | Application Insights 분석 테이블 스키마를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/AnalyticsTables/Write | Application Insights 분석 테이블 스키마를 작성합니다. |
> | 실행력 | Microsoft.Insights/Components/Annotations/Delete | Application Insights 주석을 삭제합니다. |
> | 실행력 | Microsoft.Insights/Components/Annotations/Read | Application Insights 주석을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/Annotations/Write | Application Insights 주석을 작성합니다. |
> | 실행력 | Microsoft.Insights/Components/Api/Read | Application Insights 구성 요소 데이터 API를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/ApiKeys/Action | Application Insights API 키를 생성합니다. |
> | 실행력 | Microsoft.Insights/Components/ApiKeys/Delete | Application Insights API 키를 삭제합니다. |
> | 실행력 | Microsoft.Insights/Components/ApiKeys/Read | Application Insights API 키를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/BillingPlanForComponent/Read | Application Insights 구성 요소에 대한 청구 계획을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Application Insights 구성 요소에 대한 현재 청구 기능을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Application Insights 구성 요소에 대한 현재 청구 기능을 작성합니다. |
> | 실행력 | Microsoft Insights/Components/DailyCapReached/Action | Application Insights 구성 요소의 일일 상한에 도달 했습니다. |
> | 실행력 | Microsoft Insights/Components/DailyCapWarningThresholdReached/Action | Application Insights 구성 요소에 대 한 일일 상한 경고 임계값에 도달 했습니다. |
> | 실행력 | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Application Insights 기본 ALM 통합 구성을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/Delete | Application Insights 구성 요소 구성을 삭제합니다. |
> | 실행력 | Microsoft.Insights/Components/Events/Read | OData 쿼리 형식을 사용하여 Application Insights에서 로그를 가져옵니다. |
> | 실행력 | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights 내보내기 설정 작업입니다. |
> | 실행력 | Microsoft.Insights/Components/ExportConfiguration/Delete | Application Insights 내보내기 설정을 삭제합니다. |
> | 실행력 | Microsoft.Insights/Components/ExportConfiguration/Read | Application Insights 내보내기 설정을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/ExportConfiguration/Write | Application Insights 내보내기 설정을 작성합니다. |
> | 실행력 | Microsoft.Insights/Components/ExtendQueries/Read | Application Insights 구성 요소 확장 쿼리 결과를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/Favorites/Delete | Application Insights 즐겨찾기를 삭제합니다. |
> | 실행력 | Microsoft.Insights/Components/Favorites/Read | Application Insights 즐겨찾기를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/Favorites/Write | Application Insights 즐겨찾기를 작성합니다. |
> | 실행력 | Microsoft.Insights/Components/FeatureCapabilities/Read | Application Insights 구성 요소 기능을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | 청구 기능에 사용 가능한 Application Insights 구성 요소를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/GetToken/Read | Application Insights 구성 요소 토큰을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/MetricDefinitions/Read | Application Insights 구성 요소 메트릭 정의를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/Metrics/Read | Application Insights 구성 요소 메트릭을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/Move/Action | Application Insights 구성 요소를 다른 리소스 그룹 또는 구독으로 이동합니다. |
> | 실행력 | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Application Insights 개인 분석 항목을 삭제합니다. |
> | 실행력 | Microsoft.Insights/Components/MyAnalyticsItems/Read | Application Insights 개인 분석 항목을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/MyAnalyticsItems/Write | Application Insights 개인 분석 항목을 작성합니다. |
> | 실행력 | Microsoft.Insights/Components/MyFavorites/Read | Application Insights 개인 즐겨찾기를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/Operations/Read | Application Insights에서 장기 실행 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Insights/Components/PricingPlans/Read | Application Insights 구성 요소 가격 책정 계획을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/PricingPlans/Write | Application Insights 구성 요소 가격 책정 계획을 작성합니다. |
> | 실행력 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Application Insights 자동 관리 검색 구성을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Application Insights 자동 관리 검색 구성을 작성합니다. |
> | 실행력 | Microsoft Insights/Components/providers/diagnosticSettings/Read | 리소스 진단 설정을 읽습니다. |
> | 실행력 | Microsoft 인 사이트/구성 요소/공급자/Microsoft의 통찰력/diagnosticSettings/Write | 리소스 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft Insights/Components/providers/Microsoft. Insights/logDefinitions/읽기 | 로그 정의 읽기 |
> | 실행력 | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | 메트릭 정의 읽기 |
> | 실행력 | Microsoft.Insights/components/purge/action | Application Insights에서 데이터 삭제 |
> | 실행력 | Microsoft.Insights/Components/Query/Read | Application Insights 로그에 대해 쿼리를 실행합니다. |
> | 실행력 | Microsoft.Insights/Components/QuotaStatus/Read | Application Insights 구성 요소 할당량 상태를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/Read | Application Insights 구성 요소 구성을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights 웹 테스트 위치를 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/Webtests/Read | 웹 테스트 구성을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/WorkItemConfigs/Delete | Application Insights ALM 통합 구성을 삭제합니다. |
> | 실행력 | Microsoft.Insights/Components/WorkItemConfigs/Read | Application Insights ALM 통합 구성을 읽습니다. |
> | 실행력 | Microsoft.Insights/Components/WorkItemConfigs/Write | Application Insights ALM 통합 구성을 작성합니다. |
> | 실행력 | Microsoft.Insights/Components/Write | Application Insights 구성 요소 구성에 씁니다. |
> | 실행력 | DataCollectionRuleAssociations/삭제 | 데이터 수집 규칙과 리소스의 연결을 삭제 합니다. |
> | 실행력 | Microsoft Insights/DataCollectionRuleAssociations/Read | 데이터 수집 규칙과 리소스의 연결 읽기 |
> | 실행력 | Microsoft DataCollectionRuleAssociations/Write/Write | 데이터 수집 규칙을 사용 하 여 리소스의 연결 만들기 또는 업데이트 |
> | DataAction | DataCollectionRules//데이터/쓰기 | 데이터 수집 규칙에 데이터 보내기 |
> | 실행력 | DataCollectionRules/삭제 | 데이터 수집 규칙 삭제 |
> | 실행력 | Microsoft Insights/DataCollectionRules/Read | 데이터 수집 규칙 읽기 |
> | 실행력 | Microsoft DataCollectionRules/Write/Write | 데이터 수집 규칙 만들기 또는 업데이트 |
> | 실행력 | Microsoft.Insights/DiagnosticSettings/Delete | 리소스 진단 설정을 삭제합니다. |
> | 실행력 | Microsoft.Insights/DiagnosticSettings/Read | 리소스 진단 설정을 읽습니다. |
> | 실행력 | Microsoft.Insights/DiagnosticSettings/Write | 리소스 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Insights/EventCategories/Read | 사용 가능한 활동 로그 이벤트 범주를 읽습니다. |
> | 실행력 | Microsoft.Insights/eventtypes/digestevents/Read | 관리 이벤트 유형 다이제스트 읽기 |
> | 실행력 | Microsoft.Insights/eventtypes/values/Read | 활동 로그 이벤트를 읽습니다. |
> | 실행력 | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | 네트워크 흐름 로그 진단 설정을 삭제합니다. |
> | 실행력 | Microsoft.Insights/ExtendedDiagnosticSettings/Read | 네트워크 흐름 로그 진단 설정을 읽습니다. |
> | 실행력 | Microsoft.Insights/ExtendedDiagnosticSettings/Write | 네트워크 흐름 로그 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Insights/ListMigrationDate/Action | 구독 마이그레이션 날짜를 뒤로 돌립니다. |
> | 실행력 | Microsoft.Insights/ListMigrationDate/Read | 구독 마이그레이션 날짜를 뒤로 돌립니다. |
> | 실행력 | Microsoft.Insights/LogDefinitions/Read | 로그 정의 읽기 |
> | 실행력 | Microsoft.Insights/LogProfiles/Delete | 활동 로그의 로그 프로필을 삭제합니다. |
> | 실행력 | Microsoft.Insights/LogProfiles/Read | 활동 로그의 로그 프로필을 읽습니다. |
> | 실행력 | Microsoft.Insights/LogProfiles/Write | 활동 로그의 로그 프로필을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | ADAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ADReplicationResult/Read | ADReplicationResult 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | ADSecurityAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/Alert/Read | Alert 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/AlertHistory/Read | AlertHistory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ApplicationInsights/Read | ApplicationInsights 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/AzureActivity/Read | AzureActivity 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/AzureMetrics/Read | AzureMetrics 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/BoundPort/Read | BoundPort 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/CommonSecurityLog/Read | CommonSecurityLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ComputerGroup/Read | ComputerGroup 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ConfigurationChange/Read | ConfigurationChange 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ConfigurationData/Read | ConfigurationData 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ContainerImageInventory/Read | ContainerImageInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ContainerInventory/Read | ContainerInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ContainerLog/Read | ContainerLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ContainerServiceLog/Read | ContainerServiceLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceAppCrash/Read | DeviceAppCrash 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceAppLaunch/Read | DeviceAppLaunch 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceCalendar/Read | DeviceCalendar 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceCleanup/Read | DeviceCleanup 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceConnectSession/Read | DeviceConnectSession 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceEtw/Read | DeviceEtw 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | DeviceHardwareHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceHealth/Read | DeviceHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceHeartbeat/Read | DeviceHeartbeat 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | DeviceSkypeHeartbeat 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | DeviceSkypeSignIn 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DeviceSleepState/Read | DeviceSleepState 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DHAppFailure/Read | DHAppFailure 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DHAppReliability/Read | DHAppReliability 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DHDriverReliability/Read | DHDriverReliability 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DHLogonFailures/Read | DHLogonFailures 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DHLogonMetrics/Read | DHLogonMetrics 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DHOSCrashData/Read | DHOSCrashData 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DHOSReliability/Read | DHOSReliability 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DHWipAppLearning/Read | DHWipAppLearning 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DnsEvents/Read | DnsEvents 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/DnsInventory/Read | DnsInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ETWEvent/Read | ETWEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/Event/Read | Event 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | ExchangeAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | ExchangeOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/Heartbeat/Read | Heartbeat 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | IISAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/InboundConnection/Read | InboundConnection 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/KubeNodeInventory/Read | KubeNodeInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/KubePodInventory/Read | KubePodInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/LinuxAuditLog/Read | LinuxAuditLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAApplication/Read | MAApplication 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAApplicationHealth/Read | MAApplicationHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | MAApplicationHealthAlternativeVersions 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | MAApplicationHealthIssues 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAApplicationInstance/Read | MAApplicationInstance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | MAApplicationInstanceReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAApplicationReadiness/Read | MAApplicationReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MADeploymentPlan/Read | MADeploymentPlan 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MADevice/Read | MADevice 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MADevicePnPHealth/Read | MADevicePnPHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | MADevicePnPHealthAlternativeVersions 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | MADevicePnPHealthIssues 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.Insights/Logs/MADeviceReadiness/Read | MADeviceReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | MADriverInstanceReadiness 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.Insights/Logs/MADriverReadiness/Read | MADriverReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeAddin/Read | MAOfficeAddin 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | MAOfficeAddinHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | MAOfficeAddinHealthIssues 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | MAOfficeAddinInstance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | MAOfficeAddinInstanceReadiness 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | MAOfficeAddinReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeApp/Read | MAOfficeApp 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | MAOfficeAppHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | MAOfficeAppInstance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | MAOfficeAppReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | MAOfficeBuildInfo 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | MAOfficeCurrencyAssessment 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | MAOfficeCurrencyAssessmentDailyCounts 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | MAOfficeDeploymentStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | MAOfficeMacroHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | MAOfficeMacroHealthIssues 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | MAOfficeMacroIssueInstanceReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | MAOfficeMacroIssueReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | MAOfficeMacroSummary 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeSuite/Read | MAOfficeSuite 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | MAOfficeSuiteInstance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | MAProposedPilotDevices 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | MAWindowsBuildInfo 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | MAWindowsCurrencyAssessment 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | MAWindowsCurrencyAssessmentDailyCounts 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | MAWindowsDeploymentStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | MAWindowsSysReqInstanceReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/NetworkMonitoring/Read | NetworkMonitoring 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/OfficeActivity/Read | OfficeActivity 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/Operation/Read | Operation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/OutboundConnection/Read | OutboundConnection 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/Perf/Read | Perf 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ProtectionStatus/Read | ProtectionStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/Read | 모든 로그에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | ReservedAzureCommonFields 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ReservedCommonFields/Read | ReservedCommonFields 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | SCCMAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | SCOMAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SecurityAlert/Read | SecurityAlert 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SecurityBaseline/Read | SecurityBaseline 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | SecurityBaselineSummary 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SecurityDetection/Read | SecurityDetection 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SecurityEvent/Read | SecurityEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | ServiceFabricOperationalEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | ServiceFabricReliableActorEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | ServiceFabricReliableServiceEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | SfBAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | SfBOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | SharePointOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | SPAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | SQLAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SQLQueryPerformance/Read | SQLQueryPerformance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/Syslog/Read | Syslog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/SysmonEvent/Read | SysmonEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft Insights/Logs/Tables. 사용자 지정/읽기 | 사용자 지정 로그에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UAApp/Read | UAApp 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UAComputer/Read | UAComputer 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UAComputerRank/Read | UAComputerRank 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UADriver/Read | UADriver 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UADriverProblemCodes/Read | UADriverProblemCodes 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UAFeedback/Read | UAFeedback 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UAHardwareSecurity/Read | UAHardwareSecurity 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | UAIESiteDiscovery 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UAOfficeAddIn/Read | UAOfficeAddIn 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UAProposedActionPlan/Read | UAProposedActionPlan 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UASysReqIssue/Read | UASysReqIssue 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UAUpgradedComputer/Read | UAUpgradedComputer 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/Update/Read | Update 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UpdateRunProgress/Read | UpdateRunProgress 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/UpdateSummary/Read | UpdateSummary 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/Usage/Read | Usage 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/W3CIISLog/Read | W3CIISLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | WaaSDeploymentStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | WaaSInsiderStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | WaaSUpdateStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WDAVStatus/Read | WDAVStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WDAVThreat/Read | WDAVThreat 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | WindowsClientAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WindowsFirewall/Read | WindowsFirewall 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | WindowsServerAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WireData/Read | WireData 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | WUDOAggregatedStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/Logs/WUDOStatus/Read | WUDOStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.Insights/MetricAlerts/Delete | 메트릭 경고를 삭제합니다. |
> | 실행력 | Microsoft.Insights/MetricAlerts/Read | 메트릭 경고를 읽습니다. |
> | 실행력 | Microsoft.Insights/MetricAlerts/Status/Read | 메트릭 경고 상태를 읽습니다. |
> | 실행력 | Microsoft.Insights/MetricAlerts/Write | 메트릭 경고를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Insights/MetricBaselines/Read | 메트릭 기준 읽기 |
> | 실행력 | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | 메트릭 정의 읽기 |
> | 실행력 | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | 메트릭 정의 읽기 |
> | 실행력 | Microsoft.Insights/MetricDefinitions/Read | 메트릭 정의 읽기 |
> | 실행력 | Microsoft.Insights/Metricnamespaces/Read | 메트릭 네임 스페이스 읽기 |
> | 실행력 | Microsoft.Insights/Metrics/Action | 메트릭 작업 |
> | 실행력 | Microsoft.Insights/Metrics/Microsoft.Insights/Read | 메트릭 읽기 |
> | 실행력 | Microsoft.Insights/Metrics/providers/Metrics/Read | 메트릭 읽기 |
> | 실행력 | Microsoft.Insights/Metrics/Read | 메트릭 읽기 |
> | DataAction | Microsoft.Insights/Metrics/Write | 메트릭을 작성합니다. |
> | 실행력 | Microsoft.Insights/MigrateToNewpricingModel/Action | 구독을 새로운 가격 책정 모델로 마이그레이션합니다. |
> | 실행력 | Microsoft.Insights/Operations/Read | 읽기 작업 |
> | 실행력 | Microsoft.Insights/Register/Action | Microsoft Insights 공급자를 등록합니다. |
> | 실행력 | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 구독을 레거시 가격 책정 모델로 롤백합니다. |
> | 실행력 | Microsoft.Insights/ScheduledQueryRules/Delete | 예약된 쿼리 규칙을 삭제합니다. |
> | 실행력 | Microsoft.Insights/ScheduledQueryRules/Read | 예약된 쿼리 규칙을 읽습니다. |
> | 실행력 | Microsoft.Insights/ScheduledQueryRules/Write | 예약된 쿼리 규칙을 씁니다. |
> | 실행력 | Microsoft.Insights/Tenants/Register/Action | Microsoft Insights 공급자를 초기화합니다. |
> | 실행력 | Microsoft.Insights/Unregister/Action | Microsoft Insights 공급자를 등록합니다. |
> | 실행력 | Microsoft.Insights/Webtests/Delete | 웹 테스트 구성을 삭제합니다. |
> | 실행력 | Microsoft.Insights/Webtests/GetToken/Read | 웹 테스트 토큰을 읽습니다. |
> | 실행력 | Microsoft.Insights/Webtests/MetricDefinitions/Read | 웹 테스트 메트릭 정의를 읽습니다. |
> | 실행력 | Microsoft.Insights/Webtests/Metrics/Read | 웹 테스트 메트릭을 읽습니다. |
> | 실행력 | Microsoft.Insights/Webtests/Read | 웹 테스트 구성을 읽습니다. |
> | 실행력 | Microsoft.Insights/Webtests/Write | 웹 테스트 구성에 씁니다. |
> | 실행력 | Microsoft Insights/통합 문서/삭제 | 통합 문서 삭제 |
> | 실행력 | Microsoft Insights/통합 문서/읽기 | 통합 문서 읽기 |
> | 실행력 | Microsoft Insights/통합 문서/쓰기 | 통합 문서 만들기 또는 업데이트 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Intune/diagnosticsettings/delete | 진단 설정을 삭제합니다. |
> | 실행력 | Microsoft.Intune/diagnosticsettings/read | 진단 설정을 읽습니다. |
> | 실행력 | Microsoft.Intune/diagnosticsettings/write | 진단 설정을 작성합니다. |
> | 실행력 | Microsoft.Intune/diagnosticsettingscategories/read | 진단 설정 범주를 읽습니다. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.IoTCentral/appTemplates/action | Azure IoT Central에서 사용 가능한 모든 응용 프로그램 템플릿을 가져옵니다. |
> | 실행력 | Microsoft.IoTCentral/checkNameAvailability/action | IoT Central 애플리케이션 이름이 사용 가능한지 확인합니다. |
> | 실행력 | Microsoft.IoTCentral/checkSubdomainAvailability/action | IoT Central 애플리케이션 하위 도메인이 사용 가능한지 확인합니다. |
> | 실행력 | Microsoft.IoTCentral/IoTApps/delete | IoT Central 애플리케이션 삭제 |
> | 실행력 | Microsoft.IoTCentral/IoTApps/read | 단일 IoT Central 애플리케이션 가져오기 |
> | 실행력 | Microsoft.IoTCentral/IoTApps/write | IoT Central 애플리케이션을 만들거나 업데이트 |
> | 실행력 | Microsoft.IoTCentral/operations/read | IoT Central 애플리케이션에 사용 가능한 모든 작업 가져오기 |
> | 실행력 | Microsoft.IoTCentral/register/action | Azure IoT Central 리소스 공급자에 대한 구독 등록 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.IoTSpaces/Graph/delete | Microsoft.IoTSpaces Graph 리소스를 삭제 |
> | 실행력 | Microsoft.IoTSpaces/Graph/read | Microsoft.IoTSpaces Graph 리소스를 가져오기 |
> | 실행력 | Microsoft.IoTSpaces/Graph/write | Microsoft.IoTSpaces Graph 리소스를 만들기 |
> | 실행력 | Microsoft.IoTSpaces/register/action | 리소스를 만들 수 있게 설정하려면 Microsoft.IoTSpaces Graph 리소스 공급자에 대한 구독을 등록하세요. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.KeyVault/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | 실행력 | Microsoft.KeyVault/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | 실행력 | Microsoft.KeyVault/hsmPools/delete | HSM 풀을 삭제합니다. |
> | 실행력 | Microsoft.KeyVault/hsmPools/joinVault/action | 키 자격 증명 모음을 HSM 풀에 조인합니다. |
> | 실행력 | Microsoft.KeyVault/hsmPools/read | HSM 풀의 속성을 봅니다. |
> | 실행력 | Microsoft.KeyVault/hsmPools/write | 기존 HSM 풀의 속성 업데이트에 대해 새 HSM 풀을 만듭니다. |
> | 실행력 | Microsoft.KeyVault/locations/deletedVaults/purge/action | 일시 삭제된 Key Vault를 제거합니다. |
> | 실행력 | Microsoft.KeyVault/locations/deletedVaults/read | 일시 삭제된 한 Key Vault의 속성을 봅니다. |
> | 실행력 | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 가상 네트워크 또는 서브넷이 삭제됨을 Microsoft.KeyVault에 알립니다. |
> | 실행력 | Microsoft.KeyVault/locations/operationResults/read | 장기 실행 작업의 결과를 확인합니다. |
> | 실행력 | Microsoft.KeyVault/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | 실행력 | Microsoft.KeyVault/register/action | 구독을 등록합니다. |
> | 실행력 | Microsoft.KeyVault/unregister/action | 구독을 등록 취소합니다. |
> | 실행력 | Microsoft.KeyVault/vaults/accessPolicies/write | 병합 또는 바꾸기를 통해 기존 액세스 정책을 업데이트하거나 새 액세스 정책을 자격 증명 모음에 추가합니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/certificatecas/delete | 인증서 Issuser 삭제 |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/certificatecas/읽기 | 인증서 Issuser 읽기 |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/certificatecas/write | 인증서 Issuser 쓰기 |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/certificatecontacts/write | 인증서 연락처 관리 |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/인증서/백업/작업 | 인증서의 백업 파일을 만듭니다. 이 파일은 동일한 구독의 Key Vault에서 인증서를 복원 하는 데 사용할 수 있습니다. 제한이 적용 될 수 있습니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/인증서/만들기/작업 | 새 인증서를 만듭니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/인증서/삭제 | 인증서를 삭제 합니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/인증서/가져오기/작업 | 개인 키가 포함 된 기존의 유효한 인증서를 Azure Key Vault 가져옵니다. 가져올 인증서는 PFX 또는 PEM 형식일 수 있습니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/인증서/제거/작업 | 인증서를 제거 하 여 복구할 수 없게 만듭니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/인증서/읽기 | 지정 된 키 자격 증명 모음에 인증서를 나열 하거나 인증서에 대 한 정보를 가져옵니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/인증서/복구/작업 | 삭제 된 인증서를 복구 합니다. 작업은 삭제 작업의 반전을 수행 합니다. 작업은 일시 삭제에 대해 설정 된 자격 증명 모음에 적용할 수 있으며, 보존 간격 중에 발급 되어야 합니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/인증서/복원/작업 | 백업 된 인증서와 모든 해당 버전을 자격 증명 모음에 복원 합니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/인증서/업데이트/작업 | 지정 된 인증서와 연결 된 지정 된 특성을 업데이트 합니다. |
> | 실행력 | Microsoft.KeyVault/vaults/delete | Key Vault를 삭제합니다. |
> | 실행력 | Microsoft.KeyVault/vaults/deploy/action | Azure Resource를 배포하는 동안 Key Vault의 비밀에 액세스할 수 있습니다. |
> | 실행력 | Microsoft.KeyVault/vaults/eventGridFilters/delete | Key Vault에 대 한 EventGrid 구독이 삭제 됨을 Microsoft KeyVault에 알립니다. |
> | 실행력 | Microsoft.KeyVault/vaults/eventGridFilters/read | Key Vault에 대 한 EventGrid 구독을 표시 하 고 있음을 Microsoft. KeyVault에 알립니다. |
> | 실행력 | Microsoft.KeyVault/vaults/eventGridFilters/write | Key Vault에 대 한 새 EventGrid 구독을 만들고 있음을 Microsoft. KeyVault에 알립니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/백업/작업 | 키의 백업 파일을 만듭니다. 이 파일은 동일한 구독의 Key Vault에서 키를 복원 하는 데 사용할 수 있습니다. 제한이 적용 될 수 있습니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/만들기/작업 | 새 키를 만듭니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/암호 해독/작업 | 키를 사용 하 여 암호 해독 암호를 해독 합니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/삭제 | 키를 삭제 합니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/암호화/동작 | 키를 사용 하 여 일반 텍스트를 암호화 합니다. 키가 비대칭 이면 읽기 권한이 있는 보안 주체가이 작업을 수행할 수 있습니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/키/가져오기/작업 | 외부에서 만든 키를 가져와 저장한 다음 키 매개 변수와 특성을 클라이언트로 반환합니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/키/제거/작업 | 키를 제거 하 여 복구할 수 없게 만듭니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/키/읽기 | 지정 된 자격 증명 모음에 키를 나열 하거나 키의 속성 및 공개 자료를 읽습니다.<br>비대칭 키의 경우이 작업은 공개 키를 노출 하 고 서명 암호화 및 확인 같은 공개 키 알고리즘을 수행 하는 기능을 포함 합니다.<br>개인 키 및 대칭 키는 노출 되지 않습니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/복구/작업 | 삭제 된 키를 복구 합니다. 작업은 삭제 작업의 반전을 수행 합니다. 작업은 일시 삭제에 대해 설정 된 자격 증명 모음에 적용할 수 있으며, 보존 간격 중에 발급 되어야 합니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/키/복원/작업 | 백업 된 키 및 모든 해당 버전을 자격 증명 모음에 복원 합니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/서명/작업 | 키를 사용 하 여 해시에 서명 합니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/래핑 해제/작업 | Key Vault 키를 사용 하 여 대칭 키를 래핑 해제 합니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/업데이트/작업 | 지정 된 키와 연결 된 지정 된 특성을 업데이트 합니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/확인/작업 | 해시를 확인 합니다. 키가 비대칭 이면 읽기 권한이 있는 보안 주체가이 작업을 수행할 수 있습니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/키/줄 바꿈/작업 | Key Vault 키를 사용 하 여 대칭 키를 래핑합니다. Key Vault 키가 비대칭 인 경우 읽기 액세스를 사용 하 여이 작업을 수행할 수 있습니다. |
> | 실행력 | Microsoft.KeyVault/vaults/read | Key Vault의 속성을 봅니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/비밀/백업/작업 | 비밀의 백업 파일을 만듭니다. 이 파일은 동일한 구독의 Key Vault에서 암호를 복원 하는 데 사용할 수 있습니다. 제한이 적용 될 수 있습니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/비밀/삭제 | 비밀을 삭제 합니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/비밀/getSecret/작업 | 비밀 값을 가져옵니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/비밀/제거/작업 | 비밀을 제거 하 여 복구할 수 없게 만듭니다. |
> | 실행력 | Microsoft.KeyVault/vaults/secrets/read | 암호의 속성은 볼 수 있지만 해당 값은 볼 수 없습니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/비밀/readMetadata/action | 암호의 속성을 나열 하거나 표시 하지만 해당 값은 표시 하지 않습니다. |
> | DataAction | Microsoft. KeyVault/자격 증명 모음/비밀/복구/작업 | 삭제 된 암호를 복구 합니다. 작업은 삭제 작업의 반전을 수행 합니다. 작업은 일시 삭제에 대해 설정 된 자격 증명 모음에 적용할 수 있으며, 보존 간격 중에 발급 되어야 합니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/비밀/복원/작업 | 백업 된 비밀 및 모든 해당 버전을 자격 증명 모음에 복원 합니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/비밀/t a b/암호/작업 | 새 비밀을 만듭니다. |
> | DataAction | Microsoft. 키 자격 증명 모음/자격 증명 모음/비밀/업데이트/작업 | 지정 된 암호와 연결 된 지정 된 특성을 업데이트 합니다. |
> | 실행력 | Microsoft.KeyVault/vaults/secrets/write | 새 비밀을 만들거나 기존 비밀의 값을 업데이트 합니다. |
> | 실행력 | Microsoft.KeyVault/vaults/write | 새 Key Vault를 만들거나 기존 Key Vault의 속성을 업데이트합니다. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Kusto/Clusters/Activate/action | 클러스터를 시작 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | 연결 된 데이터베이스 구성 리소스를 삭제 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | 연결 된 데이터베이스 구성 리소스를 읽습니다. |
> | 실행력 | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | 연결 된 데이터베이스 구성 리소스를 작성 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/CheckNameAvailability/action | 클러스터 이름 가용성을 확인 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | 데이터베이스 보안 주체를 추가 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | 지정 된 형식에 대 한 이름 가용성을 확인 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | 데이터 연결 리소스를 삭제합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/DataConnections/read | 데이터 연결 리소스를 읽습니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/DataConnections/write | 데이터 연결 리소스를 씁니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | 데이터베이스 데이터 연결의 유효성을 검사 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/delete | 데이터베이스 리소스를 삭제합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | 이벤트 허브 연결 리소스를 삭제 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | 이벤트 허브 연결 리소스를 읽습니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | 이벤트 허브 연결 리소스를 씁니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | 데이터베이스 이벤트 허브 연결의 유효성을 검사 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | 데이터베이스 보안 주체를 나열 합니다. |
> | 실행력 | Microsoft. Kusto/클러스터/데이터베이스/PrincipalAssignments/delete | 데이터베이스 보안 주체 할당 리소스를 삭제 합니다. |
> | 실행력 | Microsoft .Kusto/클러스터/데이터베이스/PrincipalAssignments/읽기 | 데이터베이스 보안 주체 할당 리소스를 읽습니다. |
> | 실행력 | Microsoft .Kusto/클러스터/데이터베이스/PrincipalAssignments/write | 데이터베이스 보안 주체 할당 리소스를 씁니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/read | 데이터베이스 리소스를 읽습니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | 데이터베이스 보안 주체를 제거 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Databases/write | 데이터베이스 리소스를 씁니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Deactivate/action | 클러스터를 중지 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/delete | 클러스터 리소스를 삭제합니다. |
> | 실행력 | Microsoft. Kusto/클러스터/DetachFollowerDatabases/작업 | 종동체 데이터베이스를 분리 합니다. |
> | 실행력 | Microsoft. Kusto/클러스터/DiagnoseVirtualNetwork/작업 | 서비스가 종속 된 외부 리소스에 대 한 네트워크 연결 상태를 진단 합니다. |
> | 실행력 | Microsoft .Kusto/클러스터/ListFollowerDatabases/action | 종동체의 데이터베이스를 나열 합니다. |
> | 실행력 | Microsoft. Kusto/클러스터/PrincipalAssignments/delete | 클러스터 보안 주체 할당 리소스를 삭제 합니다. |
> | 실행력 | Microsoft. Kusto/클러스터/PrincipalAssignments/읽기 | 클러스터 보안 주체 할당 리소스를 읽습니다. |
> | 실행력 | Microsoft. Kusto/클러스터/PrincipalAssignments/write | 클러스터 보안 주체 할당 리소스를 씁니다. |
> | 실행력 | Microsoft.Kusto/Clusters/read | 클러스터 리소스를 읽습니다. |
> | 실행력 | Microsoft.Kusto/Clusters/SKUs/read | 클러스터 SKU 리소스를 읽습니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Start/action | 클러스터를 시작 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/Stop/action | 클러스터를 중지 합니다. |
> | 실행력 | Microsoft.Kusto/Clusters/write | 클러스터 리소스를 씁니다. |
> | 실행력 | Microsoft.Kusto/Locations/CheckNameAvailability/action | 리소스 이름 가용성을 확인 합니다. |
> | 실행력 | Microsoft .Kusto/위치/s s v e r/작업 | 네트워크 의도 정책을 가져옵니다. |
> | 실행력 | Microsoft.Kusto/locations/operationresults/read | 작업 리소스를 읽습니다. |
> | 실행력 | Microsoft.Kusto/Operations/read | 작업 리소스를 읽습니다. |
> | 실행력 | Microsoft. Kusto/등록/동작 | 구독 등록 작업 |
> | 실행력 | Microsoft.Kusto/Register/action | 구독을 Kusto 리소스 공급자에 등록 합니다. |
> | 실행력 | Microsoft.Kusto/SKUs/read | SKU 리소스를 읽습니다. |
> | 실행력 | Microsoft.Kusto/Unregister/action | Kusto 리소스 공급자에 대 한 구독을 등록 취소 합니다. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.LabServices/labAccounts/CreateLab/action | 랩 계정에서 랩을 만듭니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/delete | 랩 계정을 삭제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/galleryImages/delete | 갤러리 이미지를 삭제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/galleryImages/read | 갤러리 이미지를 읽습니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/galleryImages/write | 갤러리 이미지를 추가하거나 수정합니다. |
> | 실행력 | Microsoft. 서비스/랩 계정/GetPricingAndAvailability/작업 | 랩 계정의 크기, 지역 및 운영 체제 조합에 대 한 가격 책정 및 가용성을 가져옵니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | 랩 계정에서 구성된 각 크기 범주에 대한 지역별 가용성 정보를 가져옵니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/AddUsers/action | 랩에 사용자 추가 |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/delete | 랩을 삭제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 환경 설정을 삭제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 환경을 삭제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 환경을 읽습니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | 환경의 사용자 암호 다시 설정 |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 환경 내의 모든 리소스를 시작하여 환경을 시작합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 환경 내의 모든 리소스를 중지하여 환경을 중지합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | 랩/환경 설정의 현재 상태에 따라 환경 설정에 필요한 리소스를 프로비전/프로비전 해제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 환경 설정을 읽습니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | 템플릿 가상 컴퓨터에서 암호를 다시 설정 합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | 랩 계정의 공유 갤러리에 현재 템플릿 이미지를 저장 합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | 일정을 삭제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | 일정을 읽습니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | 일정을 추가하거나 수정합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | 템플릿 내의 모든 리소스를 시작하여 템플릿을 시작합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | 템플릿 내의 모든 리소스를 중지하여 템플릿을 중지합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 환경 설정을 추가하거나 수정합니다. |
> | 실행력 | Microsoft. 서비스/랩 계정/실습/GetLabPricingAndAvailability/작업 | 이 랩의 랩 단위당 가격 책정 및이 랩을 확장할 수 있는지 여부를 나타내는 가용성을 가져옵니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/read | 랩을 읽습니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/SendEmail/action | 등록 링크가 포함된 이메일을 랩으로 보냅니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/users/delete | 사용자를 삭제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/users/read | 사용자를 읽습니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/users/write | 사용자를 추가하거나 수정합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/labs/write | 랩을 추가 또는 수정합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/read | 랩 계정을 읽습니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/sharedGalleries/delete | sharedgalleries를 삭제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/sharedGalleries/read | sharedgalleries를 읽습니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/sharedGalleries/write | sharedgalleries를 추가 또는 수정합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/sharedImages/delete | sharedimages를 삭제합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/sharedImages/read | sharedimages를 읽습니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/sharedImages/write | sharedimages를 추가 또는 수정합니다. |
> | 실행력 | Microsoft.LabServices/labAccounts/write | 랩 계정을 추가 또는 수정합니다. |
> | 실행력 | Microsoft.LabServices/locations/operations/read | 읽기 작업 |
> | 실행력 | Microsoft.LabServices/register/action | 구독을 등록합니다. |
> | 실행력 | Microsoft.LabServices/users/ListAllEnvironments/action | 사용자에 대 한 모든 환경 나열 |
> | 실행력 | Microsoft.LabServices/users/Register/action | 사용자를 관리되는 랩에 등록합니다. |
> | 실행력 | Microsoft.LabServices/users/ResetPassword/action | 환경의 사용자 암호 다시 설정 |
> | 실행력 | Microsoft.LabServices/users/StartEnvironment/action | 환경 내의 모든 리소스를 시작하여 환경을 시작합니다. |
> | 실행력 | Microsoft.LabServices/users/StopEnvironment/action | 환경 내의 모든 리소스를 중지하여 환경을 중지합니다. |
> | 실행력 | Microsoft. 서비스/사용자/UserSettings/작업 | 개인 사용자 설정을 업데이트 하 고 반환 합니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Logic/integrationAccounts/agreements/delete | 통합 계정에서 규약을 삭제합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 통합 계정의 규약 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/agreements/read | 통합 계정에서 규약을 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/agreements/write | 통합 계정에서 규약을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/assemblies/delete | 통합 계정에서 어셈블리를 삭제합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 통합 계정의 어셈블리 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/assemblies/read | 통합 계정에서 어셈블리를 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/assemblies/write | 통합 계정에서 어셈블리를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 통합 계정에서 일괄 처리 구성을 삭제합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 통합 계정에서 일괄 처리 구성을 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 통합 계정에서 일괄 처리 구성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/certificates/delete | 통합 계정에서 인증서를 삭제합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/certificates/read | 통합 계정에서 인증서를 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/certificates/write | 통합 계정에서 인증서를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/delete | 통합 계정을 삭제합니다. |
> | 실행력 | IntegrationAccounts/그룹/삭제 | 통합 계정에서 그룹을 삭제 합니다. |
> | 실행력 | IntegrationAccounts/그룹/읽기 | 통합 계정에서 그룹을 읽습니다. |
> | 실행력 | Microsoft 논리/integrationAccounts/그룹/쓰기 | 통합 계정에서 그룹을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/join/action | 통합 계정을 조인합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 통합 계정의 콜백 URL을 가져옵니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | 키 자격 증명 모음의 키를 가져옵니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 통합 계정의 추적 이벤트를 기록합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/maps/delete | 통합 계정의 맵을 삭제합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 통합 계정의 맵 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/maps/read | 통합 계정에서 맵을 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/maps/write | 통합 계정에서 맵을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/partners/delete | 통합 계정에서 파트너를 삭제합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 통합 계정의 파트너 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/partners/read | 통합 계정에서 파트너를 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/partners/write | 통합 계정에서 파트너를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 통합 계정 로그 정의를 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/read | 통합 계정을 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | 선택키 암호를 다시 생성합니다. |
> | 실행력 | Microsoft. 논리/integrationAccounts/rosettaNetProcessConfigurations/delete | 통합 계정에서 RosettaNet 프로세스 구성을 삭제 합니다. |
> | 실행력 | Microsoft. 논리/integrationAccounts/rosettaNetProcessConfigurations/read | 통합 계정에서 RosettaNet 프로세스 구성을 읽습니다. |
> | 실행력 | Microsoft. 논리/integrationAccounts/rosettaNetProcessConfigurations/write | 통합 계정에서 RosettaNet 프로세스 구성을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/schemas/delete | 통합 계정에서 스키마를 삭제합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 통합 계정의 스키마 콘텐츠에 대한 콜백 URL을 가져옵니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/schemas/read | 통합 계정에서 스키마를 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/schemas/write | 통합 계정에서 스키마를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/sessions/delete | 통합 계정에서 세션을 삭제합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/sessions/read | 통합 계정에서 세션을 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/sessions/write | 통합 계정에서 세션을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/integrationAccounts/write | 통합 계정을 만들거나 업데이트합니다. |
> | 실행력 | IntegrationServiceEnvironments/availableManagedApis/read | 사용 가능한 관리 되는 Api 통합 서비스 환경을 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationServiceEnvironments/delete | 통합 서비스 환경을 삭제합니다. |
> | 실행력 | Microsoft.Logic/integrationServiceEnvironments/join/action | 통합 서비스 환경에 조인합니다. |
> | 실행력 | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | 통합 서비스 환경 관리되는 API 작업을 읽습니다. |
> | 실행력 | Microsoft 논리/integrationServiceEnvironments/managedApis/join/action | 통합 서비스 환경 관리 되는 API에 조인 합니다. |
> | 실행력 | IntegrationServiceEnvironments/managedApis/operationStatuses/읽기 | Integration service environment 관리 되는 API 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | 통합 서비스 환경 관리되는 API를 읽습니다. |
> | 실행력 | IntegrationServiceEnvironments/managedApis/write | Integration service environment 관리 되는 API를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | 통합 서비스 환경 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | 통합 서비스 환경 메트릭 정의를 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationServiceEnvironments/read | 통합 서비스 환경을 읽습니다. |
> | 실행력 | Microsoft.Logic/integrationServiceEnvironments/write | 통합 서비스 환경을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | 워크플로 권장 작업 그룹을 가져옵니다. |
> | 실행력 | Microsoft.Logic/locations/workflows/validate/action | 워크플로의 유효성을 검사합니다. |
> | 실행력 | Microsoft.Logic/operations/read | 작업을 가져옵니다. |
> | 실행력 | Microsoft.Logic/register/action | 지정된 구독에 대한 Microsoft.Logic 리소스 공급자를 등록합니다. |
> | 실행력 | Microsoft.Logic/workflows/accessKeys/delete | 선택키를 삭제합니다. |
> | 실행력 | Microsoft.Logic/workflows/accessKeys/list/action | 선택키 암호를 나열합니다. |
> | 실행력 | Microsoft.Logic/workflows/accessKeys/read | 선택키를 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/accessKeys/regenerate/action | 선택키 암호를 다시 생성합니다. |
> | 실행력 | Microsoft.Logic/workflows/accessKeys/write | 선택키를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/workflows/delete | 워크플로를 삭제합니다. |
> | 실행력 | Microsoft. 논리/워크플로/감지기/읽기 | 워크플로 탐지기를 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/disable/action | 워크플로를 사용하지 않도록 설정합니다. |
> | 실행력 | Microsoft.Logic/workflows/enable/action | 워크플로를 사용하도록 설정합니다. |
> | 실행력 | Microsoft.Logic/workflows/listCallbackUrl/action | 워크플로의 콜백 URL을 가져옵니다. |
> | 실행력 | Microsoft.Logic/workflows/listSwagger/action | 워크플로 Swagger 정의를 가져옵니다. |
> | 실행력 | Microsoft.Logic/workflows/move/action | 워크플로를 기존의 구독 ID, 리소스 그룹 및/또는 이름에서 다른 구독 ID, 리소스 그룹 및/또는 이름으로 이동합니다. |
> | 실행력 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | 워크플로 진단 설정을 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | 워크플로 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | 워크플로 로그 정의를 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | 워크플로 메트릭 정의를 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/read | 워크플로를 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/regenerateAccessKey/action | 선택키 암호를 다시 생성합니다. |
> | 실행력 | Microsoft.Logic/workflows/run/action | 워크플로의 실행을 시작합니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | 워크플로 실행 작업 식 추적을 가져옵니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/actions/read | 워크플로 실행 작업을 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | 워크플로 실행 작업 반복 식 추적을 가져옵니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/actions/repetitions/read | 워크플로 실행 작업 반복을 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | 워크플로 실행 반복 작업 요청 기록을 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/actions/requestHistories/read | 워크플로 실행 작업 요청 기록을 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | 워크플로 실행 작업 범위 반복을 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/cancel/action | 워크플로 실행을 취소합니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/delete | 워크플로 실행을 삭제 합니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/operations/read | 워크플로 실행 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/runs/read | 워크플로 실행을 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/suspend/action | 워크플로를 일시 중단합니다. |
> | 실행력 | Microsoft.Logic/workflows/triggers/histories/read | 트리거 기록을 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/triggers/histories/resubmit/action | 워크플로 트리거를 다시 전송합니다. |
> | 실행력 | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | 워크플로의 콜백 URL을 가져옵니다. |
> | 실행력 | Microsoft.Logic/workflows/triggers/read | 트리거를 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/triggers/reset/action | 트리거를 다시 설정합니다. |
> | 실행력 | Microsoft.Logic/workflows/triggers/run/action | 트리거를 실행합니다. |
> | 실행력 | Microsoft.Logic/workflows/triggers/setState/action | 트리거 상태를 설정합니다. |
> | 실행력 | Microsoft.Logic/workflows/validate/action | 워크플로의 유효성을 검사합니다. |
> | 실행력 | Microsoft.Logic/workflows/versions/read | 워크플로 버전을 읽습니다. |
> | 실행력 | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | 워크플로의 콜백 URL을 가져옵니다. |
> | 실행력 | Microsoft.Logic/workflows/write | 워크플로를 만들거나 업데이트합니다. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Machine Learning 약정 요금 관계를 이동합니다. |
> | 실행력 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Machine Learning 약정 요금 관계를 읽습니다. |
> | 실행력 | Microsoft.MachineLearning/commitmentPlans/delete | Machine Learning 약정 요금을 삭제합니다. |
> | 실행력 | Microsoft.MachineLearning/commitmentPlans/join/action | Machine Learning 약정 요금제에 가입합니다. |
> | 실행력 | Microsoft.MachineLearning/commitmentPlans/read | Machine Learning 약정 요금을 읽습니다. |
> | 실행력 | Microsoft.MachineLearning/commitmentPlans/write | Machine Learning 약정 요금을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.MachineLearning/locations/operationresults/read | Machine Learning 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.MachineLearning/locations/operationsstatus/read | 진행 중인 Machine Learning 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.MachineLearning/operations/read | Machine Learning 작업을 가져옵니다. |
> | 실행력 | Microsoft.MachineLearning/register/action | Machine Learning 웹 서비스 리소스 공급자에 대한 구독을 등록하고 웹 서비스를 만들도록 설정합니다. |
> | 실행력 | Microsoft.MachineLearning/skus/read | Machine Learning 약정 요금제 SKU를 가져옵니다. |
> | 실행력 | Microsoft.MachineLearning/webServices/action | 지원되는 지역에 대한 국가별 웹 서비스 속성을 만듭니다. |
> | 실행력 | Microsoft.MachineLearning/webServices/delete | Machine Learning 웹 서비스를 삭제합니다. |
> | 실행력 | Microsoft.MachineLearning/webServices/listkeys/read | Machine Learning 웹 서비스에 대한 키를 가져옵니다. |
> | 실행력 | Microsoft.MachineLearning/webServices/read | Machine Learning 웹 서비스를 읽습니다. |
> | 실행력 | Microsoft.MachineLearning/webServices/write | Machine Learning 웹 서비스를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.MachineLearning/Workspaces/delete | Machine Learning 작업 영역을 삭제합니다. |
> | 실행력 | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Machine Learning 작업 영역에 대한 키를 나열합니다. |
> | 실행력 | Microsoft.MachineLearning/Workspaces/read | Machine Learning 작업 영역을 읽습니다. |
> | 실행력 | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Machine Learning 작업 영역에 대해 구성된 스토리지 계정의 키를 다시 동기화합니다. |
> | 실행력 | Microsoft.MachineLearning/Workspaces/write | Machine Learning 작업 영역을 만들거나 업데이트합니다. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | 특정 컴퓨팅 작업의 상태를 가져옵니다. |
> | 실행력 | MachineLearningServices/위치/할당량/읽기 | VMFamily를 기준으로 현재 할당 된 작업 영역 할당량을 가져옵니다. |
> | 실행력 | MachineLearningServices/위치/Updatas/작업 | 작업 영역에서 각 VM 제품군에 대 한 할당량을 업데이트 합니다. |
> | 실행력 | Microsoft.MachineLearningServices/locations/usages/read | 구독의 aml 컴퓨팅 리소스에 대한 사용량 보고서입니다. |
> | 실행력 | Microsoft.MachineLearningServices/locations/vmsizes/read | 지원되는 vm 크기를 가져옵니다. |
> | 실행력 | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | 특정 작업 영역 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.MachineLearningServices/register/action | Machine Learning Services 리소스 공급자에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.MachineLearningServices/workspaces/computes/delete | Machine Learning Services 작업 영역에서 컴퓨팅 리소스를 삭제합니다. |
> | 실행력 | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Machine Learning Services 작업 영역에서 컴퓨팅 리소스에 대한 비밀을 나열합니다. |
> | 실행력 | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Machine Learning Services 작업 영역의 컴퓨팅 리소스에 대한 노드를 나열합니다. |
> | 실행력 | Microsoft.MachineLearningServices/workspaces/computes/read | Machine Learning Services 작업 영역에서 컴퓨팅 리소스를 가져옵니다. |
> | 실행력 | Microsoft.MachineLearningServices/workspaces/computes/write | Machine Learning Services 작업 영역에서 컴퓨팅 리소스를 만들거나 업데이트합니다. |
> | DataAction | MachineLearningServices/작업 영역/datadriftdetectors/읽기 | Machine Learning Services 작업 영역에서 데이터 드리프트 감지기를 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/datadriftdetectors/쓰기 | Machine Learning Services 작업 영역에서 데이터 드리프트 감지기를 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록/삭제 | Machine Learning Services 작업 영역에서 등록 된 데이터 집합을 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록/미리 보기/읽기 | Machine Learning Services 작업 영역에서 등록 된 데이터 집합에 대 한 데이터 집합 미리 보기를 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록/프로 파일링/읽기 | Machine Learning Services 작업 영역에서 등록 된 데이터 집합에 대 한 데이터 집합 프로필을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록/프로 파일링/쓰기 | Machine Learning Services 작업 영역에서 등록 된 데이터 집합에 대 한 데이터 집합 프로필을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록/읽기 | Machine Learning Services 작업 영역에서 등록 된 데이터 집합을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록/스키마/읽기 | Machine Learning Services 작업 영역에서 등록 된 데이터 집합에 대 한 데이터 집합 스키마를 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록/쓰기 | Machine Learning Services 작업 영역에서 등록 된 데이터 집합을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록 취소/삭제 | Machine Learning Services 작업 영역에서 등록 되지 않은 데이터 집합을 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록 취소/미리 보기/읽기 | Machine Learning Services 작업 영역에서 등록 되지 않은 데이터 집합에 대 한 데이터 집합 미리 보기를 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록 취소/프로필/읽기 | Machine Learning Services 작업 영역에서 등록 되지 않은 데이터 집합에 대 한 데이터 집합 프로필을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록 취소/프로필/쓰기 | Machine Learning Services 작업 영역에서 등록 되지 않은 데이터 집합에 대 한 데이터 집합 프로필을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록 취소/읽기 | Machine Learning Services 작업 영역에서 등록 되지 않은 데이터 집합을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록 취소/스키마/읽기 | Machine Learning Services 작업 영역에서 등록 되지 않은 데이터 집합에 대 한 데이터 집합 스키마를 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 집합/등록 취소/쓰기 | Machine Learning Services 작업 영역에서 등록 되지 않은 데이터 집합을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 저장소/삭제 | Machine Learning Services 작업 영역에서 데이터 저장소를 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 저장소/읽기 | Machine Learning Services 작업 영역에서 데이터 저장소를 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/데이터 저장소/쓰기 | Machine Learning Services 작업 영역에서 데이터 저장소를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.MachineLearningServices/workspaces/delete | Machine Learning Services 작업 영역을 삭제합니다. |
> | DataAction | MachineLearningServices/작업 영역/엔드포인트/파이프라인/읽기 | Machine Learning Services 작업 영역에서 게시 된 파이프라인 및 파이프라인 엔드포인트을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/엔드포인트/파이프라인/쓰기 | Machine Learning Services 작업 영역에서 게시 된 파이프라인 및 파이프라인 엔드포인트을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/환경/빌드/작업 | Machine Learning Services 작업 영역에서 환경 빌드 |
> | DataAction | MachineLearningServices/작업 영역/환경/읽기 | Machine Learning Services 작업 영역에서 환경을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/환경/readSecrets/작업 | Machine Learning Services 작업 영역에서 비밀이 포함 된 환경을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/환경/쓰기 | Machine Learning Services 작업 영역에서 환경을 만들거나 업데이트 합니다. |
> | 실행력 | MachineLearningServices/workspaces/eventGridFilters/read | 특정 작업 영역에 대 한 Event Grid 필터 가져오기 |
> | DataAction | MachineLearningServices/작업 영역/실험/삭제 | Machine Learning Services 작업 영역에서 실험을 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/실험/읽기 | Machine Learning Services 작업 영역에서 실험을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/실험/실행/읽기 | Machine Learning Services 작업 영역에서 실행을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/실험/실행/작업 취소/제출/작업 | Machine Learning Services 작업 영역에서 스크립트 실행을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/실험/실행/쓰기 | Machine Learning Services 작업 영역에서 만들기 또는 업데이트 실행 |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Machine Learning Services 작업 영역에서 실험을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.MachineLearningServices/workspaces/listKeys/action | Machine Learning Services 작업 영역에 대한 비밀을 나열합니다. |
> | DataAction | MachineLearningServices/작업 영역/메타 데이터/아티팩트/삭제 | Machine Learning Services 작업 영역에서 아티팩트를 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/메타 데이터/아티팩트/읽기 | Machine Learning Services 작업 영역의 아티팩트를 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/메타 데이터/아티팩트/쓰기 | Machine Learning Services 작업 영역에서 아티팩트를 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/메타 데이터/비밀/삭제 | Machine Learning Services 작업 영역에서 비밀을 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/메타 데이터/비밀/읽기 | Machine Learning Services 작업 영역에서 비밀을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/메타 데이터/비밀/쓰기 | Machine Learning Services 작업 영역에서 비밀을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/메타 데이터/스냅숏/삭제 | Machine Learning Services 작업 영역에서 스냅숏을 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/메타 데이터/스냅숏/읽기 | Machine Learning Services 작업 영역의 스냅숏을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/메타 데이터/스냅숏/쓰기 | Machine Learning Services 작업 영역에서 스냅숏을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/모델/삭제 | Machine Learning Services 작업 영역에서 모델을 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/모델/다운로드/작업 | Machine Learning Services 작업 영역에서 모델 다운로드 |
> | DataAction | MachineLearningServices/작업 영역/모델/패키지/작업 | Machine Learning Services 작업 영역의 패키지 모델 |
> | DataAction | MachineLearningServices/작업 영역/모델/읽기 | Machine Learning Services 작업 영역에서 모델을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/모델/쓰기 | Machine Learning Services 작업 영역에서 모델을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/모듈/읽기 | 작업 영역 Machine Learning Services 모듈을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/모듈/쓰기 | Machine Learning Services 작업 영역에서 모듈을 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/pipelinedrafts/삭제 | Machine Learning Services 작업 영역에서 파이프라인 초안을 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/pipelinedrafts/읽기 | Machine Learning Services 작업 영역에서 파이프라인 초안을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/pipelinedrafts/쓰기 | Machine Learning Services 작업 영역에서 파이프라인 초안을 만들거나 업데이트 합니다. |
> | 실행력 | MachineLearningServices/작업 영역/privateEndpointConnectionProxies/삭제 | Microsoft. 네트워크 공급자의 개인 엔드포인트 리소스에 대 한 연결 프록시를 삭제 합니다. |
> | 실행력 | MachineLearningServices/작업 영역/privateEndpointConnectionProxies/읽기 | Microsoft. 네트워크 공급자의 개인 엔드포인트 리소스에 대 한 연결 프록시의 상태를 확인 합니다. |
> | 실행력 | MachineLearningServices/작업 영역/privateEndpointConnectionProxies/유효성 검사/작업 | Microsoft. 네트워크 공급자의 개인 엔드포인트 리소스에 대 한 연결 프록시의 유효성을 검사 합니다. |
> | 실행력 | MachineLearningServices/작업 영역/privateEndpointConnectionProxies/쓰기 | 연결 프록시의 상태를 Microsoft 네트워크 공급자의 개인 엔드포인트 리소스로 변경 합니다. |
> | 실행력 | MachineLearningServices/작업 영역/privateEndpointConnections/삭제 | Microsoft. 네트워크 공급자의 개인 엔드포인트 리소스에 대 한 연결을 삭제 합니다. |
> | 실행력 | MachineLearningServices/작업 영역/privateEndpointConnections/읽기 | Microsoft. 네트워크 공급자의 개인 엔드포인트 리소스에 대 한 연결 상태를 확인 합니다. |
> | 실행력 | MachineLearningServices/작업 영역/privateEndpointConnections/쓰기 | Microsoft. 네트워크 공급자의 개인 엔드포인트 리소스에 대 한 연결의 상태를 변경 합니다. |
> | 실행력 | MachineLearningServices/작업 영역/PrivateEndpointConnectionsApproval/작업 | Microsoft. 네트워크 공급자의 개인 엔드포인트 리소스에 대 한 연결을 승인 하거나 거부 합니다. |
> | 실행력 | MachineLearningServices/작업 영역/privateLinkResources/읽기 | Machine Learning Services 작업 영역의 지정 된 인스턴스에 대해 사용 가능한 개인 링크 리소스를 가져옵니다. |
> | 실행력 | Microsoft.MachineLearningServices/workspaces/read | Machine Learning Services 작업 영역을 가져옵니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/aci/삭제 | Machine Learning Services 작업 영역에서 ACI 서비스를 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/aci/listkeys/작업 | Machine Learning Services 작업 영역의 ACI 서비스에 대 한 키를 나열 합니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/aci/쓰기 | Machine Learning Services 작업 영역에서 ACI 서비스를 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/aks/devtest/delete | Machine Learning Services 작업 영역에서 devtest AKS services를 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/aks/devtest/listkeys/작업 | Machine Learning Services 작업 영역에 있는 devtest AKS services의 키를 나열 합니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/aks/devtest/점수/동작 | Machine Learning Services 작업 영역에서 점수 devtest AKS services |
> | DataAction | MachineLearningServices/작업 영역/서비스/aks/devtest/write | Machine Learning Services 작업 영역에서 devtest AKS services를 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/aks/prod/delete | Machine Learning Services 작업 영역에서 prod AKS services를 삭제 합니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/aks/prod/listkeys/작업 | Machine Learning Services 작업 영역에서 prod AKS services에 대 한 키를 나열 합니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/aks/prod/작업 | Machine Learning Services 작업 영역에서 prod AKS services 점수 |
> | DataAction | MachineLearningServices/작업 영역/서비스/aks/prod/쓰기 | Machine Learning Services 작업 영역에서 prod AKS services를 만들거나 업데이트 합니다. |
> | DataAction | MachineLearningServices/작업 영역/서비스/읽기 | Machine Learning Services 작업 영역에서 서비스를 가져옵니다. |
> | 실행력 | Microsoft.MachineLearningServices/workspaces/write | Machine Learning Services 작업 영역을 만들거나 업데이트합니다. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.managedidentity/id/읽기 | 기존 시스템 할당 id를 가져옵니다. |
> | 실행력 | Microsoft.managedidentity/작업/읽기 | Microsoft.managedidentity 리소스 공급자에서 사용할 수 있는 작업을 나열 합니다. |
> | 실행력 | Microsoft.ManagedIdentity/register/action | 관리 ID 리소스 공급자에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | 기존 사용자 할당 ID를 리소스에 할당하기 위한 RBAC 작업입니다. |
> | 실행력 | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 기존 사용자 할당 ID를 삭제합니다. |
> | 실행력 | Microsoft.ManagedIdentity/userAssignedIdentities/read | 기존 사용자 할당 ID를 가져옵니다. |
> | 실행력 | Microsoft.ManagedIdentity/userAssignedIdentities/write | 새로운 사용자 할당 ID를 만들거나 기존 사용자 할당 ID와 연결된 태그를 업데이트합니다. |

## <a name="microsoftmanagedservices"></a>Microsoft ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft ManagedServices/marketplaceRegistrationDefinitions/read | 관리 서비스 등록 정의의 목록을 검색 합니다. |
> | 실행력 | Microsoft ManagedServices/작업/읽기 | 관리 서비스 작업 목록을 검색 합니다. |
> | 실행력 | Microsoft ManagedServices/operationStatuses/읽기 | 리소스의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft ManagedServices/register/action | 관리 서비스에 등록 합니다. |
> | 실행력 | Microsoft ManagedServices/registrationAssignments/delete | 관리 되는 서비스 등록 할당을 제거 합니다. |
> | 실행력 | Microsoft ManagedServices/registrationAssignments/read | 관리 되는 서비스 등록 할당 목록을 검색 합니다. |
> | 실행력 | Microsoft ManagedServices/registrationAssignments/write | 관리 서비스 등록 할당을 추가 하거나 수정 합니다. |
> | 실행력 | Microsoft ManagedServices/registrationDefinitions/삭제 | 관리 서비스 등록 정의를 제거 합니다. |
> | 실행력 | Microsoft ManagedServices/registrationDefinitions/읽기 | 관리 서비스 등록 정의의 목록을 검색 합니다. |
> | 실행력 | Microsoft ManagedServices/registrationDefinitions/쓰기 | 관리 서비스 등록 정의를 추가 하거나 수정 합니다. |
> | 실행력 | Microsoft ManagedServices/등록 취소/작업 | 관리 서비스에서 등록을 취소 합니다. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Management/checkNameAvailability/action | 지정된 관리 그룹 이름이 유효하고 고유한지 확인합니다. |
> | 실행력 | Microsoft.Management/getEntities/action | 인증된 사용자에 대한 모든 엔터티(관리 그룹, 구독 등)를 나열합니다. |
> | 실행력 | Microsoft.Management/managementGroups/delete | 관리 그룹을 삭제합니다. |
> | 실행력 | Microsoft. 관리/관리 그룹/하위 항목/읽기 | 관리 그룹의 모든 하위 항목 (관리 그룹, 구독)을 가져옵니다. |
> | 실행력 | Microsoft.Management/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | 실행력 | Microsoft.Management/managementGroups/subscriptions/delete | 관리 그룹에서 구독의 연결을 해제합니다. |
> | 실행력 | Microsoft.Management/managementGroups/subscriptions/write | 기존 구독을 관리 그룹과 연결합니다. |
> | 실행력 | Microsoft.Management/managementGroups/write | 관리 그룹을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Management/register/action | 지정된 구독을 Microsoft.Management에 등록합니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Maps 계정에 데이터 읽기 액세스 권한을 부여합니다. |
> | 실행력 | Microsoft.Maps/accounts/delete | Maps 계정을 삭제합니다. |
> | 실행력 | Microsoft.Maps/accounts/eventGridFilters/delete | Event Grid 필터를 삭제합니다. |
> | 실행력 | Microsoft.Maps/accounts/eventGridFilters/read | Event Grid 필터를 가져옵니다. |
> | 실행력 | Microsoft.Maps/accounts/eventGridFilters/write | Event Grid 필터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Maps/accounts/listKeys/action | Maps 계정 키를 나열합니다. |
> | 실행력 | Microsoft.Maps/accounts/read | Maps 계정을 가져옵니다. |
> | 실행력 | Microsoft.Maps/accounts/regenerateKey/action | 새 Maps 계정 기본 키 또는 보조 키를 생성합니다. |
> | 실행력 | Microsoft.Maps/accounts/write | Maps 계정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Maps/operations/read | 공급자 작업 읽기 |
> | 실행력 | Microsoft.Maps/register/action | 공급자를 등록합니다. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 규약을 반환합니다. |
> | 실행력 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 서명된 규약을 수락합니다. |
> | 실행력 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | 이미지를 최종 사용자의 ACR로 가져옵니다. |
> | 실행력 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 구성을 반환합니다. |
> | 실행력 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 구성을 저장합니다. |
> | 실행력 | Microsoft.Marketplace/register/action | 구독에서 Microsoft.Marketplace 리소스 공급자를 등록합니다. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.MarketplaceApps/ClassicDevServices/delete | 클래식 개발 서비스 리소스에서 DELETE 작업을 수행합니다. |
> | 실행력 | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | 클래식 개발 서비스 리소스 관리 키를 가져옵니다. |
> | 실행력 | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | 클래식 개발자 서비스에 대한 Single Sign-On URL을 가져옵니다. |
> | 실행력 | Microsoft.MarketplaceApps/ClassicDevServices/read | 클래식 개발 서비스에서 GET 작업을 수행합니다. |
> | 실행력 | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | 클래식 개발 서비스 리소스 관리 키를 생성합니다. |
> | 실행력 | Microsoft.MarketplaceApps/Operations/read | 모든 리소스 종류에 대한 작업을 읽습니다. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 지정된 Marketplace 항목에 대한 규약을 취소합니다. |
> | 실행력 | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 지정된 Marketplace 항목에 대한 규약을 반환합니다. |
> | 실행력 | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 지정된 Marketplace 항목에 대한 규약에 서명합니다. |
> | 실행력 | Microsoft.MarketplaceOrdering/agreements/read | 지정된 구독의 모든 규약을 반환합니다. |
> | 실행력 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 지정된 마켓플레이스 가상 머신 항목에 대한 규약을 가져옵니다. |
> | 실행력 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 지정된 마켓플레이스 가상 머신 항목에 대한 규약에 서명하거나 규약을 취소합니다. |
> | 실행력 | Microsoft.MarketplaceOrdering/operations/read | API에서 가능한 모든 작업 나열 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Media/checknameavailability/action | Media Services 계정 이름을 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.Media/mediaservices/accountfilters/delete | 계정 필터를 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/accountfilters/read | 계정 필터를 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/accountfilters/write | 계정 필터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/assets/assetfilters/delete | 자산 필터를 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/assets/assetfilters/read | 자산 필터를 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/assets/assetfilters/write | 자산 필터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/assets/delete | 모든 자산을 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 자산 암호화 키를 가져옵니다. |
> | 실행력 | Microsoft.Media/mediaservices/assets/listContainerSas/action | 자산 컨테이너 SAS URL을 나열합니다. |
> | 실행력 | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | 자산에 대한 스트리밍 로케이터를 나열합니다. |
> | 실행력 | Microsoft.Media/mediaservices/assets/read | 모든 자산을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/assets/write | 모든 자산을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/contentKeyPolicies/delete | 모든 콘텐츠 키 정책을 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | 비밀을 사용하여 정책 속성을 가져옵니다. |
> | 실행력 | Microsoft.Media/mediaservices/contentKeyPolicies/read | 모든 콘텐츠 키 정책을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/contentKeyPolicies/write | 모든 콘텐츠 키 정책을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/delete | Media Services 계정을 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/eventGridFilters/delete | 모든 Event Grid 필터를 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/eventGridFilters/read | 모든 Event Grid 필터를 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/eventGridFilters/write | 모든 Event Grid 필터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEventOperations/read | 모든 라이브 이벤트 작업을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEvents/delete | 모든 라이브 이벤트를 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | 모든 라이브 출력을 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | 모든 라이브 출력을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | 모든 라이브 출력을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEvents/read | 모든 라이브 이벤트를 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEvents/reset/action | 모든 라이브 이벤트 작업을 다시 설정합니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEvents/start/action | 모든 라이브 이벤트 작업을 시작합니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEvents/stop/action | 모든 라이브 이벤트 작업을 중지합니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveEvents/write | 모든 라이브 이벤트를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/liveOutputOperations/read | 모든 라이브 출력 작업을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/read | Media Services 계정을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingEndpointOperations/read | 모든 스트리밍 엔드포인트 작업을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingEndpoints/delete | 모든 스트리밍 엔드포인트를 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingEndpoints/read | 모든 스트리밍 엔드포인트를 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | 모든 스트리밍 엔드포인트 작업의 크기를 조정합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingEndpoints/start/action | 모든 스트리밍 엔드포인트 작업을 시작합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | 모든 스트리밍 엔드포인트 작업을 중지합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingEndpoints/write | 모든 스트리밍 엔드포인트를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingLocators/delete | 모든 스트리밍 로케이터를 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | 콘텐츠 키를 나열합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | 경로를 나열합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingLocators/read | 모든 스트리밍 로케이터를 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingLocators/write | 모든 스트리밍 로케이터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingPolicies/delete | 모든 스트리밍 정책을 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingPolicies/read | 모든 스트리밍 정책을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/streamingPolicies/write | 모든 스트리밍 정책을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/syncStorageKeys/action | 연결된 Azure Storage 계정에 대한 스토리지 키를 동기화합니다. |
> | 실행력 | Microsoft.Media/mediaservices/transforms/delete | 모든 변환을 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | 작업을 취소합니다. |
> | 실행력 | Microsoft.Media/mediaservices/transforms/jobs/delete | 모든 작업을 삭제합니다. |
> | 실행력 | Microsoft.Media/mediaservices/transforms/jobs/read | 모든 작업을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/transforms/jobs/write | 모든 작업을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/transforms/read | 모든 변환을 읽습니다. |
> | 실행력 | Microsoft.Media/mediaservices/transforms/write | 모든 변환을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/mediaservices/write | Media Services 계정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Media/operations/read | 사용 가능한 작업을 가져옵니다. |
> | 실행력 | Microsoft.Media/register/action | Media Services 리소스 공급자에 대한 구독을 등록하고 Media Services 계정을 만들도록 설정합니다. |
> | 실행력 | Microsoft.Media/unregister/action | Media Services 리소스 공급자에 대한 구독을 등록 취소합니다. |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft. 마이그레이션/assessmentprojects/assessmentOptions/읽기 | 지정된 위치에서 사용할 수 있는 평가 옵션을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/assessments/read | 프로젝트 내의 평가를 나열합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/delete | 평가 프로젝트를 삭제 합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | 평가된 머신의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | 평가를 삭제합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | 평가 보고서의 URL을 다운로드합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/groups/assessments/read | 평가의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/groups/assessments/write | 새 평가를 만들거나 기존 평가를 업데이트합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/groups/delete | 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/groups/read | 그룹의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | 컴퓨터를 추가 또는 제거 하 여 그룹 업데이트 |
> | 실행력 | Microsoft.Migrate/assessmentprojects/groups/write | 새 그룹을 만들거나 기존 그룹을 업데이트합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | HyperV 수집기를 삭제 합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | HyperV 수집기의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | 새 HyperV 수집기를 만들거나 기존 HyperV 수집기를 업데이트 합니다. |
> | 실행력 | Microsoft. 마이그레이션/assessmentprojects/importcollectors/delete | 가져오기 수집기를 삭제 합니다. |
> | 실행력 | Microsoft. 마이그레이션/assessmentprojects/importcollectors/읽기 | 가져오기 수집기의 속성을 가져옵니다. |
> | 실행력 | Microsoft. 마이그레이션/assessmentprojects/importcollectors/write | 새 가져오기 수집기를 만들거나 기존 가져오기 수집기를 업데이트 합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/machines/read | 머신의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/read | 평가 프로젝트의 속성을 가져옵니다. |
> | 실행력 | Microsoft. 마이그레이션/assessmentprojects/servercollectors/읽기 | 서버 수집기의 속성을 가져옵니다. |
> | 실행력 | Microsoft. 마이그레이션/assessmentprojects/servercollectors/write | 새 서버 수집기를 만들거나 기존 서버 수집기를 업데이트 합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | VMware 수집기를 삭제 합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | VMware 수집기의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | 새 VMware 수집기를 만들거나 기존 VMware 수집기를 업데이트 합니다. |
> | 실행력 | Microsoft.Migrate/assessmentprojects/write | 새 평가 프로젝트를 만들거나 기존 평가 프로젝트를 업데이트 합니다. |
> | 실행력 | Microsoft.Migrate/locations/assessmentOptions/read | 지정된 위치에서 사용할 수 있는 평가 옵션을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/locations/checknameavailability/action | 지정된 위치에서 지정된 구독의 리소스 이름의 가용성을 확인합니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | 데이터베이스 인스턴스의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/Databases/read | 데이터베이스의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/delete | 마이그레이션 프로젝트를 삭제 합니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/machines/read | 머신의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | 마이그레이션 이벤트를 삭제 합니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/MigrateEvents/read | 마이그레이션 이벤트의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/read | 마이그레이션 프로젝트의 속성을 가져옵니다. |
> | 실행력 | Microsoft. 마이그레이션/migrateprojects/RefreshSummary/작업 | 프로젝트 마이그레이션 요약을 새로 고칩니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/registerTool/action | 마이그레이션 프로젝트에 도구 등록 |
> | 실행력 | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | 프로젝트 솔루션 데이터 마이그레이션 정리 |
> | 실행력 | Microsoft.Migrate/migrateprojects/solutions/Delete | 프로젝트 마이그레이션 솔루션을 삭제 합니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | 마이그레이션 프로젝트 솔루션 구성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/solutions/read | 프로젝트 솔루션 마이그레이션 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/solutions/write | 새 프로젝트 마이그레이션 솔루션을 만들거나 기존 마이그레이션 프로젝트 솔루션을 업데이트 합니다. |
> | 실행력 | Microsoft.Migrate/migrateprojects/write | 새 마이그레이션 프로젝트를 만들거나 기존 마이그레이션 프로젝트를 업데이트 합니다. |
> | 실행력 | Microsoft.Migrate/Operations/read | Microsoft.KeyVault 리소스 공급자에서 사용할 수 있는 작업을 나열합니다. |
> | 실행력 | Microsoft.Migrate/projects/assessments/read | 프로젝트 내의 평가를 나열합니다. |
> | 실행력 | Microsoft.Migrate/projects/delete | 프로젝트를 삭제합니다. |
> | 실행력 | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 평가된 머신의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/projects/groups/assessments/delete | 평가를 삭제합니다. |
> | 실행력 | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 평가 보고서의 URL을 다운로드합니다. |
> | 실행력 | Microsoft.Migrate/projects/groups/assessments/read | 평가의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/projects/groups/assessments/write | 새 평가를 만들거나 기존 평가를 업데이트합니다. |
> | 실행력 | Microsoft.Migrate/projects/groups/delete | 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Migrate/projects/groups/read | 그룹의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/projects/groups/write | 새 그룹을 만들거나 기존 그룹을 업데이트합니다. |
> | 실행력 | Microsoft.Migrate/projects/keys/action | 프로젝트의 공유 키를 가져옵니다. |
> | 실행력 | Microsoft.Migrate/projects/machines/read | 머신의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/projects/read | 프로젝트의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Migrate/projects/write | 새 프로젝트를 만들거나 기존 프로젝트를 업데이트합니다. |
> | 실행력 | Microsoft.Migrate/register/action | Microsoft.Compute 리소스 공급자로 구독을 등록합니다. |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.MixedReality/register/action | Mixed Reality 리소스 공급자에 대 한 구독을 등록 합니다. |
> | DataAction | MixedReality/RemoteRenderingAccounts/convert/작업 | 자산 변환 시작 |
> | DataAction | MixedReality/RemoteRenderingAccounts/convert/delete | 자산 변환 중지 |
> | DataAction | MixedReality/RemoteRenderingAccounts/convert/read | 자산 변환 속성 가져오기 |
> | DataAction | MixedReality/RemoteRenderingAccounts/진단/읽기 | 원격 렌더링 검사자에 연결 |
> | DataAction | MixedReality/RemoteRenderingAccounts/managesessions/action | 세션 시작 |
> | DataAction | MixedReality/RemoteRenderingAccounts/managesessions/delete | 세션 중지 |
> | DataAction | MixedReality/RemoteRenderingAccounts/managesessions/read | 세션 속성 가져오기 |
> | DataAction | MixedReality/RemoteRenderingAccounts/렌더링/읽기 | 세션에 연결 |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | 공간 앵커 만들기 |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | 공간 앵커 삭제 |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 주변 공간 앵커 검색 |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 공간 앵커의 속성을 가져옵니다. |
> | 실행력 | MixedReality/spatialAnchorsAccounts/providers//diagnosticSettings/read | MixedReality/spatialAnchorsAccounts에 대 한 진단 설정을 가져옵니다. |
> | 실행력 | MixedReality/spatialAnchorsAccounts/providers//diagnosticSettings/write | MixedReality/spatialAnchorsAccounts에 대 한 진단 설정을 만들거나 업데이트 합니다. |
> | 실행력 | MixedReality/spatialAnchorsAccounts/providers//metricDefinitions/read | MixedReality/spatialAnchorsAccounts에 대해 사용 가능한 메트릭을 가져옵니다. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 공간 앵커 찾기 |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | 진단 데이터를 제출 하 여 Azure 공간 앵커 서비스의 품질 향상 |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | 공간 앵커 속성 업데이트 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft NetApp/위치/checkfilepathavailability/작업 | 파일 경로를 사용할 수 있는지 확인 |
> | 실행력 | Microsoft NetApp/위치/checknameavailability/action | 리소스 이름을 사용할 수 있는지 확인 |
> | 실행력 | Microsoft.NetApp/locations/operationresults/read | 작업 결과 리소스를 읽습니다. |
> | 실행력 | Microsoft.NetApp/locations/read | 가용성 확인 리소스를 읽습니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/accountBackups/delete |  |
> | 실행력 | Microsoft NetApp/netAppAccounts/accountBackups/읽기 |  |
> | 실행력 | Microsoft NetApp/netAppAccounts/accountBackups/write |  |
> | 실행력 | Microsoft NetApp/netAppAccounts/backupPolicies/삭제 | 백업 정책 리소스를 삭제 합니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/backupPolicies/읽기 | 백업 정책 리소스를 읽습니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/backupPolicies/쓰기 | 백업 정책 리소스를 씁니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/delete | 풀 리소스를 삭제합니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/read | 풀 리소스를 읽습니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨/AuthorizeReplication/작업 | 원본 볼륨 복제 권한 부여 |
> | 실행력 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨/백업/삭제 | 백업 리소스를 삭제 합니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨/백업/읽기 | 백업 리소스를 읽습니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨/백업/쓰기 | 백업 리소스를 씁니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨/중단/작업 | 볼륨 복제 관계 중단 |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | 볼륨 리소스를 삭제합니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | 마운트 대상 리소스를 읽습니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | 볼륨 리소스를 읽습니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨/Connectingtosource/작업 | 볼륨 복제 상태를 읽습니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨/ResyncReplication/작업 | 대상 볼륨의 복제를 다시 동기화 합니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨/되돌리기/작업 | 볼륨을 특정 스냅숏으로 되돌리기 |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | 스냅샷 리소스를 삭제합니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | 스냅샷 리소스를 읽습니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | 스냅샷 리소스를 씁니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | 볼륨 리소스를 씁니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/capacityPools/write | 풀 리소스를 씁니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/delete | 계정 리소스를 삭제 합니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/read | 계정 리소스를 읽습니다. |
> | 실행력 | Microsoft NetApp/netAppAccounts/자격 증명 모음/읽기 | 자격 증명 모음 리소스를 읽습니다. |
> | 실행력 | Microsoft.NetApp/netAppAccounts/write | 계정 리소스를 씁니다. |
> | 실행력 | Microsoft.NetApp/Operations/read | 작업 리소스를 읽습니다. |
> | 실행력 | Microsoft NetApp/register/action | Microsoft NetApp 리소스 공급자에 구독을 등록 합니다. |
> | 실행력 | Microsoft NetApp/등록 취소/작업 | Microsoft .Net 앱 리소스 공급자를 사용 하 여 구독 등록 취소 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Application Gateway 사용 가능한 요청 헤더를 가져옵니다. |
> | 실행력 | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Application Gateway 사용 가능한 응답 헤더를 가져옵니다. |
> | 실행력 | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Application Gateway 사용 가능한 서버 변수를 가져옵니다. |
> | 실행력 | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl 미리 정의된 정책 |
> | 실행력 | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway에서 사용 가능한 Ssl 옵션 |
> | 실행력 | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Application Gateway 사용 가능 Waf 규칙 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 응용 프로그램 게이트웨이 백 엔드 주소 풀을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/applicationGateways/backendhealth/action | 애플리케이션 게이트웨이 백 엔드 상태를 가져옵니다. |
> | 실행력 | Microsoft.Network/applicationGateways/delete | 애플리케이션 게이트웨이를 삭제합니다. |
> | 실행력 | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/action | 지정 된 http 설정 및 백 엔드 풀에 대 한 요청 시 응용 프로그램 게이트웨이 백 엔드 상태를 가져옵니다. |
> | 실행력 | Microsoft.Network/applicationGateways/read | 애플리케이션 게이트웨이를 가져옵니다. |
> | 실행력 | Microsoft.Network/applicationGateways/start/action | Application Gateway를 시작합니다. |
> | 실행력 | Microsoft.Network/applicationGateways/stop/action | 애플리케이션 게이트웨이를 중지합니다. |
> | 실행력 | Microsoft.Network/applicationGateways/write | 애플리케이션 게이트웨이를 만들거나 애플리케이션 게이트웨이를 업데이트합니다. |
> | 실행력 | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Application Gateway WAF 정책을 삭제 합니다. |
> | 실행력 | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Application Gateway WAF 정책을 가져옵니다. |
> | 실행력 | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Application Gateway WAF 정책을 만들거나 Application Gateway WAF 정책을 업데이트 합니다. |
> | 실행력 | Microsoft.Network/applicationSecurityGroups/delete | 애플리케이션 보안 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | IP 구성을 애플리케이션 보안 그룹에 조인합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 보안 규칙을 애플리케이션 보안 그룹에 조인합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | ApplicationSecurityGroup의 IP 구성을 나열합니다. |
> | 실행력 | Microsoft.Network/applicationSecurityGroups/read | 애플리케이션 보안 그룹 ID를 가져옵니다. |
> | 실행력 | Microsoft.Network/applicationSecurityGroups/write | 애플리케이션 보안 그룹을 만들거나 기존 애플리케이션 보안 그룹을 업데이트합니다. |
> | 실행력 | Microsoft.Network/azureFirewallFqdnTags/read | Azure Firewall FQDN 태그를 가져옵니다. |
> | 실행력 | Microsoft.Network/azurefirewalls/delete | Azure Firewall을 삭제합니다. |
> | 실행력 | Microsoft.Network/azurefirewalls/read | Azure Firewall을 가져옵니다. |
> | 실행력 | Microsoft.Network/azurefirewalls/write | Azure Firewall을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft. Network/bastionHosts/createbsl/action | 방호에서 Vm에 대 한 공유 가능 url을 만들고 url을 반환 합니다. |
> | 실행력 | Microsoft.Network/bastionHosts/delete | 요새 호스트 삭제 |
> | 실행력 | Microsoft. Network/bastionHosts/deletebsl/action | 방호에서 제공 된 Vm에 대 한 공유 가능 url을 삭제 합니다. |
> | 실행력 | Microsoft. Network/bastionHosts/disconnectactivesessions/action | 요새 호스트에서 지정 된 활성 세션의 연결을 끊습니다. |
> | 실행력 | Microsoft. Network/bastionHosts/getactivesessions/action | 요새 호스트에서 활성 세션 가져오기 |
> | 실행력 | Microsoft. Network/bastionHosts/getbsl/action | Url을 만든 경우에는 지정 된 Vm의 지정 된 Vm에 대 한 공유 가능 url을 반환 합니다. |
> | 실행력 | Microsoft.Network/bastionHosts/read | 요새 호스트 가져오기 |
> | 실행력 | Microsoft.Network/bastionHosts/write | 요새 호스트 만들기 또는 업데이트 |
> | 실행력 | Microsoft.Network/bgpServiceCommunities/read | BGP 서비스 커뮤니티를 가져옵니다. |
> | 실행력 | Microsoft.Network/checkFrontDoorNameAvailability/action | Front Door 이름이 사용 가능한지 여부 확인 |
> | 실행력 | Microsoft.Network/checkTrafficManagerNameAvailability/action | Traffic Manager 상대 DNS 이름의 가용성을 확인합니다. |
> | 실행력 | Microsoft.Network/connections/delete | VirtualNetworkGatewayConnection을 삭제합니다. |
> | 실행력 | Microsoft.Network/connections/read | VirtualNetworkGatewayConnection을 가져옵니다. |
> | 실행력 | Microsoft.Network/connections/revoke/action | 기본 경로 연결 상태를 해지됨으로 표시합니다. |
> | 실행력 | Microsoft.Network/connections/sharedkey/action | VirtualNetworkGatewayConnection SharedKey를 가져옵니다. |
> | 실행력 | Microsoft.Network/connections/sharedKey/read | VirtualNetworkGatewayConnection SharedKey를 가져옵니다. |
> | 실행력 | Microsoft.Network/connections/sharedKey/write | VirtualNetworkGatewayConnection SharedKey를 만들거나 기존 VirtualNetworkGatewayConnection SharedKey를 업데이트합니다. |
> | 실행력 | Microsoft. Network/connections/startpacketcapture/action | Virtual Network 게이트웨이 연결 패킷 캡처를 시작 합니다. |
> | 실행력 | Microsoft. Network/connections/stoppacketcapture/action | Virtual Network 게이트웨이 연결 패킷 캡처를 중지 합니다. |
> | 실행력 | Microsoft.Network/connections/vpndeviceconfigurationscript/action | VirtualNetworkGatewayConnection의 Vpn 디바이스 구성을 가져옵니다. |
> | 실행력 | Microsoft.Network/connections/write | VirtualNetworkGatewayConnection을 만들거나 기존 VirtualNetworkGatewayConnection을 업데이트합니다. |
> | 실행력 | Microsoft.Network/ddosCustomPolicies/delete | DDoS 사용자 지정 정책 삭제 |
> | 실행력 | Microsoft.Network/ddosCustomPolicies/read | DDoS 사용자 지정 정책 정의 가져오기 |
> | 실행력 | Microsoft.Network/ddosCustomPolicies/write | DDoS 사용자 지정 정책을 만들거나 기존 DDoS 사용자 지정 정책 업데이트 |
> | 실행력 | Microsoft.Network/ddosProtectionPlans/delete | DDoS 보호 계획을 삭제합니다. |
> | 실행력 | Microsoft.Network/ddosProtectionPlans/join/action | DDoS Protection 계획에 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/ddosProtectionPlans/read | DDoS 보호 계획을 가져옵니다. |
> | 실행력 | Microsoft.Network/ddosProtectionPlans/write | DDoS 보호 계획을 만들거나 DDoS 보호 계획을 업데이트합니다.  |
> | 실행력 | Microsoft.Network/dnsoperationresults/read | DNS 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Network/dnsoperationstatuses/read | DNS 작업의 상태를 가져옵니다.  |
> | 실행력 | Microsoft.Network/dnszones/A/delete | DNS 영역에서 지정된 이름의 레코드 집합 및 'A' 형식을 제거합니다. |
> | 실행력 | Microsoft.Network/dnszones/A/read | JSON 형식의 'A' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/dnszones/A/write | DNS 영역 내에서 'A' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/dnszones/AAAA/delete | DNS 영역에서 지정된 이름의 레코드 집합 및 'AAAA' 형식을 제거합니다. |
> | 실행력 | Microsoft.Network/dnszones/AAAA/read | JSON 형식의 'AAAA' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/dnszones/AAAA/write | DNS 영역 내에서 'AAAA' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/dnszones/all/read | 여러 형식의 DNS 레코드 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/dnszones/CAA/delete | DNS 영역에서 이름이 지정된 ‘CAA’ 유형의 레코드 집합을 제거합니다. |
> | 실행력 | Microsoft.Network/dnszones/CAA/read | 'CAA' 유형의 레코드 집합을 JSON 형식으로 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag가 포함됩니다. |
> | 실행력 | Microsoft.Network/dnszones/CAA/write | DNS 영역 내에서 'CAA' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/dnszones/CNAME/delete | DNS 영역에서 지정된 이름의 레코드 집합 및 'CNAME' 형식을 제거합니다. |
> | 실행력 | Microsoft.Network/dnszones/CNAME/read | JSON 형식의 'CNAME' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag가 포함됩니다. |
> | 실행력 | Microsoft.Network/dnszones/CNAME/write | DNS 영역 내에서 'CNAME' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/dnszones/delete | Json 형식의 DNS 영역을 삭제합니다. 영역 속성에는 tags, etag, numberOfRecordSets 및 maxNumberOfRecordSets가 포함됩니다. |
> | 실행력 | Microsoft.Network/dnszones/MX/delete | DNS 영역에서 지정된 이름의 레코드 집합 및 'MX' 형식을 제거합니다. |
> | 실행력 | Microsoft.Network/dnszones/MX/read | JSON 형식의 'MX' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/dnszones/MX/write | DNS 영역 내에서 'MX' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/dnszones/NS/delete | NS 형식의 DNS 레코드 집합을 삭제합니다. |
> | 실행력 | Microsoft.Network/dnszones/NS/read | NS 형식의 DNS 레코드 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/dnszones/NS/write | NS 형식의 DNS 레코드 집합을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Network/dnszones/PTR/delete | DNS 영역에서 지정된 이름의 레코드 집합 및 'PTR' 형식을 제거합니다. |
> | 실행력 | Microsoft.Network/dnszones/PTR/read | JSON 형식의 'PTR' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/dnszones/PTR/write | DNS 영역 내에서 'PTR' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/dnszones/read | Json 형식의 DNS 영역을 가져옵니다. 영역 속성에는 tags, etag, numberOfRecordSets 및 maxNumberOfRecordSets가 포함됩니다. 이 명령은 영역 내에 포함된 레코드 집합을 검색하지 않습니다. |
> | 실행력 | Microsoft.Network/dnszones/recordsets/read | 여러 형식의 DNS 레코드 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/dnszones/SOA/read | SOA 형식의 DNS 레코드 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/dnszones/SOA/write | SOA 형식의 DNS 레코드 집합을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Network/dnszones/SRV/delete | DNS 영역에서 지정된 이름의 레코드 집합 및 'SRV' 형식을 제거합니다. |
> | 실행력 | Microsoft.Network/dnszones/SRV/read | JSON 형식의 'SRV' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/dnszones/SRV/write | SRV 형식의 레코드 집합을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Network/dnszones/TXT/delete | DNS 영역에서 지정된 이름의 레코드 집합 및 'TXT' 형식을 제거합니다. |
> | 실행력 | Microsoft.Network/dnszones/TXT/read | JSON 형식의 'TXT' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/dnszones/TXT/write | DNS 영역 내에서 'TXT' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/dnszones/write | 리소스 그룹 내에 DNS 영역을 만들거나 업데이트합니다.  DNS 영역 리소스에서 태그를 업데이트하는 데 사용됩니다. 이 명령은 영역 내에서 레코드 집합을 만들거나 업데이트하는 데 사용할 수 없습니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/authorizations/delete | ExpressRouteCircuit Authorization을 삭제합니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/authorizations/read | ExpressRouteCircuit Authorization을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/authorizations/write | ExpressRouteCircuit Authorization을 만들거나 기존 ExpressRouteCircuit Authorization을 업데이트합니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/delete | ExpressRouteCircuit을 삭제합니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/join/action | Express 경로 회로를 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft. Network/expressRouteCircuits/피어 링/arpTables/읽기 | ExpressRouteCircuit Peering ArpTable을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | ExpressRouteCircuit 연결을 삭제합니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/peerings/connections/read | ExpressRouteCircuit 연결을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/peerings/connections/write | ExpressRouteCircuit 연결 리소스를 만들거나 기존 ExpressRouteCircuit 연결 리소스를 업데이트합니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/peerings/delete | ExpressRouteCircuit Peering을 삭제합니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | 피어링 기본 경로 회로 연결을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/peerings/read | ExpressRouteCircuit Peering을 가져옵니다. |
> | 실행력 | Microsoft. Network/expressRouteCircuits/피어 링/routeTables/읽기 | ExpressRouteCircuit Peering RouteTable을 가져옵니다. |
> | 실행력 | Microsoft. Network/expressRouteCircuits/피어 링/routeTablesSummary/읽기 | ExpressRouteCircuit Peering RouteTable Summary를 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/peerings/stats/read | ExpressRouteCircuit Peering Stat을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/peerings/write | ExpressRouteCircuit Peering을 만들거나 기존 ExpressRouteCircuit Peering을 업데이트합니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/read | ExpressRouteCircuit을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/stats/read | ExpressRouteCircuit Stat을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCircuits/write | ExpressRouteCircuit을 만들거나 기존 ExpressRouteCircuit을 업데이트합니다. |
> | 실행력 | Microsoft.Network/expressRouteCrossConnections/join/action | Express 경로 교차 연결을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft. Network/expressRouteCrossConnections/피어 링/arpTables/읽기 | Express Route 교차 연결 피어링 Arp 테이블을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Express Route 교차 연결 피어링을 삭제합니다. |
> | 실행력 | Microsoft.Network/expressRouteCrossConnections/peerings/read | Express Route 교차 연결 피어링을 가져옵니다. |
> | 실행력 | Microsoft. Network/expressRouteCrossConnections/피어 링/routeTables/읽기 | Express Route 교차 연결 피어링 경로 테이블을 가져옵니다. |
> | 실행력 | Microsoft. Network/expressRouteCrossConnections/피어 링/routeTableSummary/읽기 | Express Route 교차 연결 피어링 경로 테이블 요약 정보를 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteCrossConnections/peerings/write | Express Route 교차 연결 피어링을 만들거나 기존 Express Route 교차 연결 피어링을 업데이트 합니다. |
> | 실행력 | Microsoft.Network/expressRouteCrossConnections/read | Express Route 교차 연결을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Express Route 연결을 삭제합니다. |
> | 실행력 | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Express Route 연결을 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Express Route 연결을 만들거나 기존 Express Route 연결을 업데이트 합니다. |
> | 실행력 | Microsoft.Network/expressRouteGateways/join/action | Express 경로 게이트웨이를 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/expressRouteGateways/read | Express Route 게이트웨이를 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRoutePorts/delete | ExpressRoutePorts를 삭제합니다. |
> | 실행력 | Microsoft.Network/expressRoutePorts/join/action | Express 경로 포트를 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/expressRoutePorts/links/read | ExpressRouteLink를 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRoutePorts/read | ExpressRoutePorts를 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRoutePorts/write | ExpressRoutePorts를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Network/expressRoutePortsLocations/read | Express 경로 포트 위치를 가져옵니다. |
> | 실행력 | Microsoft.Network/expressRouteServiceProviders/read | Express Route 서비스 공급자를 가져옵니다. |
> | 실행력 | Microsoft. Network/firewallPolicies/delete | 방화벽 정책을 삭제 합니다. |
> | 실행력 | Microsoft. Network/firewallPolicies/join/action | 방화벽 정책을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft. Network/firewallPolicies/read | 방화벽 정책을 가져옵니다. |
> | 실행력 | Microsoft. Network/firewallPolicies/ruleGroups/delete | 방화벽 정책 규칙 그룹을 삭제 합니다. |
> | 실행력 | Microsoft. Network/firewallPolicies/ruleGroups/read | 방화벽 정책 규칙 그룹을 가져옵니다. |
> | 실행력 | Microsoft. Network/firewallPolicies/ruleGroups/write | 방화벽 정책 규칙 그룹을 만들거나 기존 방화벽 정책 규칙 그룹을 업데이트 합니다. |
> | 실행력 | Microsoft. Network/firewallPolicies/write | 방화벽 정책을 만들거나 기존 방화벽 정책을 업데이트 합니다. |
> | 실행력 | Microsoft.Network/frontDoors/backendPools/delete | 백 엔드 풀 삭제 |
> | 실행력 | Microsoft.Network/frontDoors/backendPools/read | 백 엔드 풀 가져오기 |
> | 실행력 | Microsoft.Network/frontDoors/backendPools/write | 백 엔드 풀 만들기 또는 업데이트 |
> | 실행력 | Microsoft.Network/frontDoors/delete | Front Door 삭제 |
> | 실행력 | Microsoft.Network/frontDoors/frontendEndpoints/delete | 프런트 엔드 엔드포인트 삭제 |
> | 실행력 | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | 프런트 엔드 엔드포인트에서 HTTPS를 사용하지 않도록 설정 |
> | 실행력 | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | 프런트 엔드 엔드포인트에서 HTTPS를 사용하도록 설정 |
> | 실행력 | Microsoft.Network/frontDoors/frontendEndpoints/read | 프런트 엔드 엔드포인트 가져오기 |
> | 실행력 | Microsoft.Network/frontDoors/frontendEndpoints/write | 프런트 엔드 엔드포인트를 만들거나 업데이트 |
> | 실행력 | Microsoft.Network/frontDoors/healthProbeSettings/delete | 상태 프로브 설정 삭제 |
> | 실행력 | Microsoft.Network/frontDoors/healthProbeSettings/read | 상태 프로브 설정 가져오기 |
> | 실행력 | Microsoft.Network/frontDoors/healthProbeSettings/write | 상태 프로브 설정을 만들거나 업데이트 |
> | 실행력 | Microsoft.Network/frontDoors/loadBalancingSettings/delete | 부하 분산 설정을 만들거나 업데이트 |
> | 실행력 | Microsoft.Network/frontDoors/loadBalancingSettings/read | 부하 분산 설정 가져오기 |
> | 실행력 | Microsoft.Network/frontDoors/loadBalancingSettings/write | 부하 분산 설정을 만들거나 업데이트 |
> | 실행력 | Microsoft.Network/frontDoors/purge/action | Front Door에서 캐시된 콘텐츠 제거 |
> | 실행력 | Microsoft.Network/frontDoors/read | Front Door 가져오기 |
> | 실행력 | Microsoft.Network/frontDoors/routingRules/delete | 라우팅 규칙 삭제 |
> | 실행력 | Microsoft.Network/frontDoors/routingRules/read | 라우팅 규칙 가져오기 |
> | 실행력 | Microsoft.Network/frontDoors/routingRules/write | 라우팅 규칙을 만들거나 업데이트 |
> | 실행력 | Microsoft.Network/frontDoors/validateCustomDomain/action | Front Door에 대한 프런트 엔드 엔드포인트 유효성 검사 |
> | 실행력 | Microsoft.Network/frontDoors/write | Front Door를 만들거나 업데이트 |
> | 실행력 | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | 웹 응용 프로그램 방화벽 관리 되는 규칙 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | 웹 애플리케이션 방화벽 정책 삭제 |
> | 실행력 | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/join/action | 웹 응용 프로그램 방화벽 정책을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | 웹 애플리케이션 방화벽 정책 가져오기 |
> | 실행력 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | 웹 애플리케이션 방화벽 정책을 만들거나 업데이트 |
> | 실행력 | Microsoft. Network/ipGroups/delete | IpGroup을 삭제 합니다. |
> | 실행력 | Microsoft. Network/ipGroups/join/action | IpGroup에 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft. Network/ipGroups/read | IpGroup을 가져옵니다. |
> | 실행력 | Microsoft. Network/ipGroups/updateReferences/action | IpGroup의 참조 업데이트 |
> | 실행력 | Microsoft. 네트워크/i p 그룹/유효성 검사/동작 | IpGroup의 유효성을 검사 합니다. |
> | 실행력 | Microsoft. Network/ipGroups/write | IpGroup을 만들거나 기존 Ipgroup를 업데이트 합니다. |
> | 실행력 | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/loadBalancers/backendAddressPools/read | 부하 분산 장치 백 엔드 주소 풀 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/delete | 부하 분산 장치를 삭제합니다. |
> | 실행력 | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 부하 분산 장치 프런트 엔드 IP 구성을 조인합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 부하 분산 장치 프런트 엔드 IP 구성 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 부하 분산 장치 인바운드 NAT 풀을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/loadBalancers/inboundNatPools/read | 부하 분산 장치 인바운드 NAT 풀 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/inboundNatRules/delete | 부하 분산 장치 인바운드 NAT 규칙을 삭제합니다. |
> | 실행력 | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 nat 규칙을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/loadBalancers/inboundNatRules/read | 부하 분산 장치 인바운드 NAT 규칙 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/inboundNatRules/write | 부하 분산 장치 인바운드 NAT 규칙을 만들거나 기존 부하 분산 장치의 인바운드 NAT 규칙을 업데이트합니다. |
> | 실행력 | Microsoft.Network/loadBalancers/loadBalancingRules/read | 부하 분산 장치 부하 분산 규칙 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/networkInterfaces/read | 부하 분산 장치의 모든 네트워크 인터페이스에 대한 참조를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/outboundRules/read | 부하 분산 장치 아웃바운드 규칙 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/probes/join/action | 부하 분산 장치 프로브 사용을 허용합니다. 예를 들어 이 권한이 있으면 VM 확장 집합의 healthProbe 속성이 프로브를 참조할 수 있습니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/loadBalancers/probes/read | 부하 분산 장치 프로브를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/virtualMachines/read | 부하 분산 장치의 모든 가상 머신에 대한 참조를 가져옵니다. |
> | 실행력 | Microsoft.Network/loadBalancers/write | 부하 분산 장치를 만들거나 기존 부하 분산 장치를 업데이트합니다. |
> | 실행력 | Microsoft.Network/localnetworkgateways/delete | LocalNetworkGateway를 삭제합니다. |
> | 실행력 | Microsoft.Network/localnetworkgateways/read | LocalNetworkGateway를 가져옵니다. |
> | 실행력 | Microsoft.Network/localnetworkgateways/write | LocalNetworkGateway를 만들거나 기존 LocalNetworkGateway를 업데이트합니다. |
> | 실행력 | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | 자동 승인 된 개인 링크 서비스를 가져옵니다. |
> | 실행력 | Microsoft.Network/locations/availableDelegations/read | 사용 가능한 위임을 가져옵니다. |
> | 실행력 | Microsoft.Network/locations/availablePrivateEndpointTypes/read | 사용 가능한 개인 엔드포인트 리소스를 가져옵니다. |
> | 실행력 | Microsoft. 네트워크/위치/availableServiceAliases/읽기 | 사용 가능한 서비스 별칭을 가져옵니다. |
> | 실행력 | Microsoft.Network/locations/bareMetalTenants/action | 운영 체제 미 설치 컴퓨터 테넌트를 할당하거나 유효성을 검사합니다. |
> | 실행력 | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 가속 네트워킹 지원을 확인합니다. |
> | 실행력 | Microsoft.Network/locations/checkDnsNameAvailability/read | DNS 레이블을 지정된 위치에서 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | 개인 링크 서비스 표시 여부를 확인 합니다. |
> | 실행력 | Microsoft.Network/locations/operationResults/read | 비동기 POST 또는 DELETE 작업의 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft.Network/locations/operations/read | 비동기 작업의 상태를 나타내는 작업 리소스를 가져옵니다. |
> | 실행력 | Microsoft.Network/locations/serviceTags/read | 서비스 태그 가져오기 |
> | 실행력 | Microsoft.Network/locations/supportedVirtualMachineSizes/read | 지원되는 가상 머신 크기를 가져옵니다. |
> | 실행력 | Microsoft.Network/locations/usages/read | 리소스 사용 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 사용 가능한 가상 네트워크 엔드포인트 서비스 목록을 가져옵니다. |
> | 실행력 | Microsoft.Network/networkIntentPolicies/delete | 네트워크 의도 정책을 삭제합니다. |
> | 실행력 | Microsoft.Network/networkIntentPolicies/read | 네트워크 의도 정책 설명을 가져옵니다. |
> | 실행력 | Microsoft.Network/networkIntentPolicies/write | 네트워크 의도 정책을 만들거나 기존 네트워크 의도 정책을 업데이트합니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/delete | 네트워크 인터페이스를 삭제합니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | VM의 네트워크 인터페이스에 구성된 네트워크 보안 그룹을 가져옵니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | VM의 네트워크 인터페이스에 구성된 경로 테이블을 가져옵니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | 네트워크 인터페이스 IP 구성을 조인합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/ipconfigurations/read | 네트워크 인터페이스의 IP 구성 정의를 가져옵니다.  |
> | 실행력 | Microsoft.Network/networkInterfaces/join/action | 가상 컴퓨터를 네트워크 인터페이스에 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/loadBalancers/read | 네트워크 인터페이스가 속하는 모든 부하 분산 장치를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | 실행력 | Microsoft.Network/networkInterfaces/tapConfigurations/delete | 네트워크 인터페이스 탭 구성을 삭제합니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/tapConfigurations/read | 네트워크 인터페이스 탭 구성을 가져옵니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/tapConfigurations/write | 네트워크 인터페이스 탭 구성을 만들거나 기존 네트워크 인터페이스 탭 구성을 업데이트합니다. |
> | 실행력 | Microsoft.Network/networkInterfaces/write | 네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다.  |
> | 실행력 | Microsoft.Network/networkProfiles/delete | 네트워크 프로필을 삭제합니다. |
> | 실행력 | Microsoft.Network/networkProfiles/read | 네트워크 프로필을 가져옵니다. |
> | 실행력 | Microsoft.Network/networkProfiles/removeContainers/action | 컨테이너를 제거합니다. |
> | 실행력 | Microsoft.Network/networkProfiles/setContainers/action | 컨테이너를 설정합니다. |
> | 실행력 | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | 컨테이너 네트워크 인터페이스를 설정합니다. |
> | 실행력 | Microsoft.Network/networkProfiles/write | 네트워크 프로필을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 기본 보안 규칙 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkSecurityGroups/delete | 네트워크 보안 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Network/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/networkSecurityGroups/read | 네트워크 보안 그룹 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkSecurityGroups/securityRules/delete | 보안 규칙을 삭제합니다. |
> | 실행력 | Microsoft.Network/networkSecurityGroups/securityRules/read | 보안 규칙 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkSecurityGroups/securityRules/write | 보안 규칙을 만들거나 기존 보안 규칙을 업데이트합니다. |
> | 실행력 | Microsoft.Network/networkSecurityGroups/write | 네트워크 보안 그룹을 만들거나 기존 네트워크 보안 그룹을 업데이트합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/availableProvidersList/action | 지정된 Azure 지역에 사용 가능한 모든 인터넷 서비스 공급자를 반환합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/azureReachabilityReport/action | 지정된 위치에서 Azure 지역까지 인터넷 서비스 공급자의 상대 대기 시간을 반환합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/configureFlowLog/action | 대상 리소스에 대한 흐름 로깅을 구성합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/connectionMonitors/delete | 연결 모니터를 삭제합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 지정된 엔드포인트 간의 연결 모니터링을 쿼리합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/connectionMonitors/read | 연결 모니터 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 지정된 엔드포인트 간의 연결 모니터링을 시작합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 지정된 엔드포인트 간의 연결 모니터링을 중지/일시 중지합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/connectionMonitors/write | 연결 모니터를 만듭니다. |
> | 실행력 | Microsoft.Network/networkWatchers/connectivityCheck/action | 가상 머신에서 다른 VM 또는 임의의 원격 서버를 포함한 특정 엔드포인트로 직접 TCP 연결을 설정할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/delete | Network Watcher를 삭제합니다. |
> | 실행력 | Microsoft. Network/networkWatchers/flowLogs/delete | 흐름 로그를 삭제 합니다. |
> | 실행력 | Microsoft. Network/networkWatchers/flowLogs/read | 흐름 로그 정보 가져오기 |
> | 실행력 | Microsoft. Network/networkWatchers/flowLogs/write | 흐름 로그를 만듭니다. |
> | 실행력 | Microsoft.Network/networkWatchers/ipFlowVerify/action | 패킷이 특정 대상에서 허용될지 또는 거부될지를 반환합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/lenses/delete | 렌즈를 삭제합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/lenses/query/action | 지정된 엔드포인트에 대한 네트워크 트래픽 모니터링을 쿼리합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/lenses/read | 렌즈 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkWatchers/lenses/start/action | 지정된 엔드포인트에 대한 네트워크 트래픽 모니터링을 시작합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/lenses/stop/action | 지정된 엔드포인트에 대한 네트워크 트래픽 모니터링을 중지/일시 중지합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/lenses/write | 렌즈를 만듭니다. |
> | 실행력 | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | 네트워크 구성을 진단합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/nextHop/action | 지정된 목표 및 대상 IP 주소에 대해 다음 홉 형식 및 다음 홉 IP 주소를 반환합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/packetCaptures/delete | 패킷 캡처를 삭제합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 패킷 캡처 리소스의 속성 및 상태에 대한 정보를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkWatchers/packetCaptures/read | 패킷 캡처 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkWatchers/packetCaptures/stop/action | 실행 중인 패킷 캡처 세션을 중지합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/packetCaptures/write | 패킷 캡처를 만듭니다. |
> | 실행력 | Microsoft.Network/networkWatchers/pingMeshes/delete | PingMesh를 삭제합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/pingMeshes/read | PingMesh 세부 정보를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkWatchers/pingMeshes/start/action | 지정된 VM 간에 PingMesh를 시작합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/pingMeshes/stop/action | 지정된 VM 간에 PingMesh를 중지합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/pingMeshes/write | PingMesh를 만듭니다. |
> | 실행력 | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | 지정된 엔드포인트 간의 연결 모니터링 쿼리를 일괄 처리합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | 리소스에 대한 흐름 로깅의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | 이전에 실행되었거나 현재 실행 중인 문제 해결 작업의 문제 해결 결과를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkWatchers/read | Network Watcher 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkWatchers/securityGroupView/action | VM에 적용된 유효한 구성된 네트워크 보안 그룹 규칙을 봅니다. |
> | 실행력 | Microsoft.Network/networkWatchers/topology/action | 리소스 그룹에 포함된 리소스의 네트워크 수준 보기 및 해당 관계를 가져옵니다. |
> | 실행력 | Microsoft.Network/networkWatchers/troubleshoot/action | Azure의 네트워킹 리소스에 대한 문제 해결을 시작합니다. |
> | 실행력 | Microsoft.Network/networkWatchers/write | Network Watcher를 만들거나 기존 Network Watcher를 업데이트합니다. |
> | 실행력 | Microsoft.Network/operations/read | 사용 가능한 작업을 가져옵니다. |
> | 실행력 | Microsoft.Network/p2sVpnGateways/delete | P2SVpnGateway를 삭제합니다. |
> | 실행력 | Microsoft. Network/p2sVpnGateways/disconnectp2svpnconnections/action | P2s vpn 연결 끊기 |
> | 실행력 | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | P2SVpnGateway에 대한 Vpn 프로필을 생성합니다. |
> | 실행력 | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | P2SVpnGateway에 대한 P2S Vpn 연결 상태를 가져옵니다. |
> | 실행력 | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/action | P2SVpnGateway에 대 한 자세한 P2S Vpn 연결 상태를 가져옵니다. |
> | 실행력 | Microsoft.Network/p2sVpnGateways/read | P2SVpnGateway를 가져옵니다. |
> | 실행력 | Microsoft.Network/p2sVpnGateways/write | P2SVpnGateway를 씁니다. |
> | 실행력 | Microsoft.Network/privateDnsOperationResults/read | 사설 DNS 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Network/privateDnsOperationStatuses/read | 사설 DNS 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/A/delete | 사설 DNS 영역에서 지정 된 이름의 레코드 집합 및 ' A ' 형식을 제거 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/A/read | JSON 형식의 사설 DNS 영역 내에서 ' A ' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/A/write | 사설 DNS 영역 내에서 ' A ' 형식의 레코드 집합을 만들거나 업데이트 합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/AAAA/delete | 사설 DNS 영역에서 지정 된 이름의 레코드 집합 및 ' AAAA ' 형식을 제거 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/AAAA/read | JSON 형식의 사설 DNS 영역 내에서 ' AAAA ' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/AAAA/write | 사설 DNS 영역 내에서 ' AAAA ' 형식의 레코드 집합을 만들거나 업데이트 합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/ALL/read | 형식 간에 사설 DNS 레코드 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/CNAME/delete | 사설 DNS 영역에서 지정 된 이름의 레코드 집합 및 ' CNAME ' 형식을 제거 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/CNAME/read | JSON 형식의 사설 DNS 영역 내에서 ' CNAME ' 형식의 레코드 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/CNAME/write | 사설 DNS 영역 내에서 ' CNAME ' 형식의 레코드 집합을 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/delete | 사설 DNS 영역을 삭제 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/MX/delete | 사설 DNS 영역에서 지정 된 이름의 레코드 집합 및 ' MX ' 유형을 제거 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/MX/read | JSON 형식의 사설 DNS 영역 내에서 ' MX ' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/MX/write | 사설 DNS 영역 내에서 ' MX ' 형식의 레코드 집합을 만들거나 업데이트 합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/PTR/delete | 사설 DNS 영역에서 지정 된 이름 및 ' PTR ' 형식의 레코드 집합을 제거 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/PTR/read | JSON 형식의 사설 DNS 영역 내에서 ' PTR ' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/PTR/write | 사설 DNS 영역 내에서 ' PTR ' 형식의 레코드 집합을 만들거나 업데이트 합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/read | JSON 형식의 사설 DNS 영역 속성을 가져옵니다. 이 명령은 사설 DNS 영역이 연결 된 가상 네트워크 또는 영역 내에 포함 된 레코드 집합을 검색 하지 않습니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/recordsets/read | 형식 간에 사설 DNS 레코드 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/SOA/read | JSON 형식의 사설 DNS 영역 내에서 ' SOA ' 형식의 레코드 집합을 가져옵니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/SOA/write | 사설 DNS 영역 내에서 ' SOA ' 형식의 레코드 집합을 업데이트 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/SRV/delete | 사설 DNS 영역에서 지정 된 이름의 레코드 집합과 ' SRV ' 형식을 제거 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/SRV/read | JSON 형식의 사설 DNS 영역 내에서 ' SRV ' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/SRV/write | 사설 DNS 영역 내에서 ' SRV ' 형식의 레코드 집합을 만들거나 업데이트 합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/TXT/delete | 사설 DNS 영역에서 지정 된 이름 및 ' TXT ' 형식의 레코드 집합을 제거 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/TXT/read | JSON 형식의 사설 DNS 영역 내에서 ' TXT ' 형식의 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/TXT/write | 사설 DNS 영역 내에서 ' TXT ' 형식의 레코드 집합을 만들거나 업데이트 합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | 가상 네트워크에 대 한 사설 DNS 영역 링크를 삭제 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | JSON 형식의 가상 네트워크 속성에 대 한 사설 DNS 영역 링크를 가져옵니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | 가상 네트워크에 대 한 사설 DNS 영역 링크를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Network/privateDnsZones/write | 리소스 그룹 내의 사설 DNS 영역을 만들거나 업데이트 합니다. 이 명령은 영역 내에서 가상 네트워크 링크 또는 레코드 집합을 만들거나 업데이트 하는 데 사용할 수 없습니다. |
> | 실행력 | Microsoft. Network/privateEndpointRedirectMaps/read | 개인 엔드포인트 RedirectMap을 가져옵니다. |
> | 실행력 | Microsoft. Network/privateEndpointRedirectMaps/write | 개인 엔드포인트 RedirectMap을 만들거나 기존 개인 엔드포인트 RedirectMap을 업데이트 합니다. |
> | 실행력 | Microsoft.Network/privateEndpoints/delete | 개인 엔드포인트 리소스를 삭제 합니다. |
> | 실행력 | Microsoft.Network/privateEndpoints/read | 개인 엔드포인트 리소스를 가져옵니다. |
> | 실행력 | Microsoft.Network/privateEndpoints/write | 새 개인 엔드포인트을 만들거나 기존 개인 엔드포인트을 업데이트 합니다. |
> | 실행력 | Microsoft.Network/privateLinkServices/delete | 프라이빗 링크 서비스 자원을 삭제합니다. |
> | 실행력 | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | 개인 엔드포인트 연결을 삭제 합니다. |
> | 실행력 | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | 개인 엔드포인트 연결 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | 새 개인 엔드포인트 연결을 만들거나 기존 개인 엔드포인트 연결을 업데이트 합니다. |
> | 실행력 | Microsoft.Network/privateLinkServices/read | 프라이빗 링크 서비스 리소스를 가져옵니다. |
> | 실행력 | Microsoft.Network/privateLinkServices/write | 새로운 프라이빗 링크 서비스를 만들거나 기존 프라이빗 링크 서비스를 업데이트합니다. |
> | 실행력 | Microsoft.Network/publicIPAddresses/delete | 공용 IP 주소를 삭제합니다. |
> | 실행력 | Microsoft.Network/publicIPAddresses/join/action | 공용 ip 주소를 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/publicIPAddresses/write | 공용 IP 주소를 만들거나 기존 공용 IP 주소를 업데이트합니다.  |
> | 실행력 | Microsoft.Network/publicIPPrefixes/delete | 공용 IP 접두사를 삭제합니다. |
> | 실행력 | Microsoft.Network/publicIPPrefixes/join/action | PublicIPPrefix을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/publicIPPrefixes/read | 공용 IP 접두사 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/publicIPPrefixes/write | 공용 IP 접두사를 만들거나 기존 공용 IP 접두사를 업데이트합니다. |
> | 실행력 | Microsoft.Network/register/action | 구독을 등록합니다. |
> | 실행력 | Microsoft.Network/routeFilters/delete | 경로 필터 정의를 삭제합니다. |
> | 실행력 | Microsoft.Network/routeFilters/join/action | 경로 필터를 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/routeFilters/read | 경로 필터 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/routeFilters/routeFilterRules/delete | 경로 필터 규칙 정의를 삭제합니다. |
> | 실행력 | Microsoft.Network/routeFilters/routeFilterRules/read | 경로 필터 규칙 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/routeFilters/routeFilterRules/write | 경로 필터 규칙을 만들거나 기존 경로 필터를 규칙을 업데이트합니다. |
> | 실행력 | Microsoft.Network/routeFilters/write | 경로 필터를 만들거나 기존 경로 필터를 업데이트합니다. |
> | 실행력 | Microsoft.Network/routeTables/delete | 경로 테이블 정의를 삭제합니다. |
> | 실행력 | Microsoft.Network/routeTables/join/action | 경로 테이블을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/routeTables/read | 경로 테이블 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/routeTables/routes/delete | 경로 정의를 삭제합니다. |
> | 실행력 | Microsoft.Network/routeTables/routes/read | 경로 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/routeTables/routes/write | 경로를 만들거나 기존 경로를 업데이트합니다. |
> | 실행력 | Microsoft.Network/routeTables/write | 경로 테이블을 만들거나 기존 경로 테이블을 업데이트합니다. |
> | 실행력 | Microsoft.Network/serviceEndpointPolicies/delete | 서비스 엔드포인트 정책을 삭제합니다. |
> | 실행력 | Microsoft.Network/serviceEndpointPolicies/join/action | 서비스 엔드포인트 정책을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | 서브넷을 서비스 엔드포인트 정책에 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/serviceEndpointPolicies/read | 서비스 엔드포인트 정책 설명을 가져옵니다. |
> | 실행력 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | 서비스 엔드포인트 정책 정의를 삭제합니다. |
> | 실행력 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | 서비스 엔드포인트 정책 정의 설명을 가져옵니다. |
> | 실행력 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | 서비스 엔드포인트 정책 정의를 만들거나 기존 서비스 엔드포인트 정책 정의를 업데이트합니다. |
> | 실행력 | Microsoft.Network/serviceEndpointPolicies/write | 서비스 엔드포인트 정책을 만들거나 기존 서비스 엔드포인트 정책을 업데이트합니다. |
> | 실행력 | Microsoft.Network/trafficManagerGeographicHierarchies/read | 지리적 트래픽 라우팅 메서드에서 사용할 수 있는 영역을 포함하는 Traffic Manager 지리적 계층 구조를 가져옵니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 기존 Traffic Manager 프로필에서 Azure 엔드포인트를 삭제합니다. Traffic Manager는 삭제된 Azure 엔드포인트로의 트래픽 라우팅을 중지합니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Azure 엔드포인트의 속성을 포함하여 Traffic Manager 프로필에 속한 Azure 엔드포인트를 가져옵니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 기존 Traffic Manager 프로필에서 새 Azure 엔드포인트를 추가하거나 해당 Traffic Manager 프로필에서 기존 Azure 엔드포인트의 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/delete | Traffic Manager 프로필을 삭제합니다. Traffic Manager 프로필과 연결된 모든 설정이 손실되고 프로필은 더 이상 트래픽을 라우팅하는 데 사용할 수 없습니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 기존 Traffic Manager 프로필에서 외부 엔드포인트를 삭제합니다. Traffic Manager는 삭제된 외부 엔드포인트로의 트래픽 라우팅을 중지합니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | 해당 외부 엔드포인트의 속성을 포함하여 Traffic Manager 프로필에 속한 외부 엔드포인트를 가져옵니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 기존 Traffic Manager 프로필에서 새 외부 엔드포인트를 추가하거나 해당 Traffic Manager 프로필에서 기존 외부 엔드포인트의 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 위치 및 소스 IP별 쿼리 수와 대기 시간 데이터를 포함하고 있는 특정 Traffic Manager의 Traffic Manager 열 지도를 가져옵니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 기존 Traffic Manager 프로필에서 중첩 엔드포인트를 삭제합니다. Traffic Manager는 삭제된 중첩 엔드포인트로의 트래픽 라우팅을 중지합니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | 중첩 엔드포인트의 속성을 포함하여 Traffic Manager 프로필에 속한 중첩 엔드포인트를 가져옵니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 기존 Traffic Manager 프로필에서 새 중첩 엔드포인트를 추가하거나 해당 Traffic Manager 프로필에서 기존 중첩 엔드포인트의 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/read | Traffic Manager 프로필 구성을 가져옵니다. 여기에는 DNS 설정, 트래픽 라우팅 설정, 엔드포인트 모니터링 설정 및 이 Traffic Manager 프로필이 라우팅하는 엔드포인트 목록이 포함됩니다. |
> | 실행력 | Microsoft.Network/trafficManagerProfiles/write | Traffic Manager 프로필을 만들거나 기존 Traffic Manager 프로필의 구성을 수정합니다.<br>여기에는 프로필을 사용하거나 사용하지 않도록 설정, DNS 설정, 트래픽 라우팅 설정 또는 엔드포인트 모니터링 설정의 수정이 포함됩니다.<br>Traffic Manager 프로필이 라우팅하는 엔드포인트를 추가 및 제거하고, 사용하거나 사용하지 않도록 설정할 수 있습니다. |
> | 실행력 | Microsoft.Network/trafficManagerUserMetricsKeys/delete | 실시간 사용자 메트릭 수집에 사용되는 구독 수준 키를 삭제합니다. |
> | 실행력 | Microsoft.Network/trafficManagerUserMetricsKeys/read | 실시간 사용자 메트릭 수집에 사용되는 구독 수준 키를 가져옵니다. |
> | 실행력 | Microsoft.Network/trafficManagerUserMetricsKeys/write | 실시간 사용자 메트릭 수집에 사용할 새로운 구독 수준 키를 만듭니다. |
> | 실행력 | Microsoft.Network/unregister/action | 구독을 등록 취소합니다. |
> | 실행력 | Microsoft.Network/virtualHubs/delete | 가상 허브를 삭제합니다. |
> | 실행력 | Microsoft. Network/virtualHubs/effectiveRoutes/action | 가상 허브에 구성 된 유효 경로를 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | HubVirtualNetworkConnection을 삭제합니다. |
> | 실행력 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | HubVirtualNetworkConnection을 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | HubVirtualNetworkConnection을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Network/virtualHubs/read | 가상 허브를 가져옵니다. |
> | 실행력 | Microsoft. Network/virtualHubs/routeTables/delete | VirtualHubRouteTableV2 삭제 |
> | 실행력 | Microsoft. Network/virtualHubs/routeTables/read | VirtualHubRouteTableV2 가져오기 |
> | 실행력 | Microsoft. Network/virtualHubs/routeTables/write | VirtualHubRouteTableV2 만들기 또는 업데이트 |
> | 실행력 | Microsoft.Network/virtualHubs/write | 가상 허브를 만들거나 업데이트합니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/connections/read | VirtualNetworkGatewayConnection을 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualNetworkGateways/delete | virtualNetworkGateway를 삭제합니다. |
> | 실행력 | microsoft. network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/action | 가상 네트워크 게이트웨이 vpn 연결 끊기 |
> | 실행력 | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | VirtualNetworkGateway용 VpnClient 패키지를 생성합니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | VirtualNetworkGateway용 VpnProfile 패키지를 생성합니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | virtualNetworkGateway 보급된 경로를 가져옵니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | virtualNetworkGateway bgp 피어 상태를 가져옵니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | virtualnetworkgateway 학습한 경로를 가져옵니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | VirtualNetworkGateway에 대한 VPN 클라이언트 연결당 상태를 가져옵니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | VirtualNetworkGateway P2S 클라이언트에 대한 Vpnclient Ipsec 매개 변수를 가져옵니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 미리 생성된 vpn 클라이언트 프로필 패키지의 URL을 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualNetworkGateways/read | VirtualNetworkGateway를 가져옵니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/reset/action | virtualNetworkGateway를 다시 설정합니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | VirtualNetworkGateway P2S 클라이언트에 대한 Vpnclient 공유 키를 다시 설정합니다. |
> | 실행력 | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | VirtualNetworkGateway P2S 클라이언트에 대한 Vpnclient Ipsec 매개 변수를 설정합니다. |
> | 실행력 | microsoft. network/virtualnetworkgateways/startpacketcapture/action | Virtual Network 게이트웨이 패킷 캡처를 시작 합니다. |
> | 실행력 | microsoft. network/virtualnetworkgateways/stoppacketcapture/action | Virtual Network 게이트웨이 패킷 캡처를 중지 합니다. |
> | 실행력 | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | 지원되는 Vpn 디바이스를 나열합니다. |
> | 실행력 | Microsoft.Network/virtualNetworkGateways/write | VirtualNetworkGateway를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/BastionHosts/action | Virtual Network의 요새 호스트 참조를 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Virtual Network의 요새 호스트 참조를 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | IP 주소를 지정된 가상 네트워크에서 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/delete | 가상 네트워크를 삭제합니다. |
> | 실행력 | Microsoft. Network/virtualNetworks/join/action | 가상 네트워크를 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/peer/action | 가상 네트워크를 다른 가상 네트워크와 피어링합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | 실행력 | Microsoft. Network/virtualNetworks/서브넷/contextualServiceEndpointPolicies/delete | 컨텍스트 서비스 엔드포인트 정책을 삭제 합니다. |
> | 실행력 | Microsoft. Network/virtualNetworks/서브넷/contextualServiceEndpointPolicies/read | 상황별 서비스 엔드포인트 정책을 가져옵니다. |
> | 실행력 | Microsoft. Network/virtualNetworks/서브넷/contextualServiceEndpointPolicies/write | 컨텍스트 서비스 엔드포인트 정책을 만들거나 기존 컨텍스트 서비스 엔드포인트 정책을 업데이트 합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/subnets/delete | 가상 네트워크 서브넷을 삭제합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/subnets/join/action | 가상 네트워크를 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | 필요한 네트워크 정책을 적용하여 서브넷을 준비합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/subnets/read | 가상 네트워크 서브넷 정의를 가져옵니다. |
> | 실행력 | Microsoft. Network/virtualNetworks/서브넷/unprepareNetworkPolicies/action | 적용 된 네트워크 정책을 제거 하 여 서브넷을 Unprepare 합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 가상 네트워크 서브넷의 모든 가상 머신에 대한 참조를 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/subnets/write | 가상 네트워크 서브넷을 만들거나 기존 가상 네트워크 서브넷을 업데이트합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/usages/read | 가상 네트워크의 각 서브넷에 대한 IP 사용량을 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/virtualMachines/read | 가상 네트워크의 모든 가상 머신에 대한 참조를 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 가상 네트워크 피어링을 삭제합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 가상 네트워크 피어링 정의를 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 가상 네트워크 피어링을 만들거나 기존 가상 네트워크 피어링을 업데이트합니다. |
> | 실행력 | Microsoft.Network/virtualNetworks/write | 가상 네트워크를 만들거나 기존 가상 네트워크를 업데이트합니다. |
> | 실행력 | Microsoft.Network/virtualNetworkTaps/delete | 가상 네트워크 탭을 삭제합니다. |
> | 실행력 | Microsoft.Network/virtualNetworkTaps/join/action | 가상 네트워크 탭을 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft.Network/virtualNetworkTaps/read | 가상 네트워크 탭을 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualNetworkTaps/write | 가상 네트워크 탭을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft. Network/virtualRouters/delete | VirtualRouter를 삭제 합니다. |
> | 실행력 | Microsoft. Network/virtualRouters/조인/작업 | VirtualRouter를 조인 합니다. 했어야 하지 않습니다. |
> | 실행력 | Microsoft. Network/virtualRouters/피어 링/삭제 | VirtualRouterPeering 링을 삭제 합니다. |
> | 실행력 | Microsoft. Network/virtualRouters/피어 링/읽기 | VirtualRouterPeering 링을 가져옵니다. |
> | 실행력 | Microsoft. Network/virtualRouters/피어 링/쓰기 | VirtualRouterPeering 링을 만들거나 기존 VirtualRouterPeering 링을 업데이트 합니다. |
> | 실행력 | Microsoft. Network/virtualRouters/읽기 | VirtualRouter를 가져옵니다. |
> | 실행력 | Microsoft. Network/virtualRouters/write | VirtualRouter를 만들거나 기존 VirtualRouter를 업데이트 합니다. |
> | 실행력 | Microsoft.Network/virtualWans/delete | 가상 Wan을 삭제합니다. |
> | 실행력 | Microsoft. Network/virtualwans/generateVpnProfile/action | VirtualWanVpnServerConfiguration VpnProfile 생성 |
> | 실행력 | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | 가상 Wan P2SVpnServerConfiguration을 삭제합니다. |
> | 실행력 | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | 가상 Wan P2SVpnServerConfiguration을 가져옵니다. |
> | 실행력 | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | 가장 Wan P2SVpnServerConfiguration을 만들거나 기존 가상 Wan P2SVpnServerConfiguration을 업데이트합니다. |
> | 실행력 | Microsoft.Network/virtualWans/read | 가상 Wan을 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualwans/supportedSecurityProviders/read | 지원되는 VirtualWan 보안 공급자를 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualWans/virtualHubs/read | 가상 WAN을 참조하는 모든 가상 허브를 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualwans/vpnconfiguration/action | VPN 구성을 가져옵니다. |
> | 실행력 | Microsoft. Network/virtualwans/vpnServerConfigurations/action | VirtualWanVpnServerConfigurations 가져오기 |
> | 실행력 | Microsoft.Network/virtualWans/vpnSites/read | 가상 WAN을 참조하는 모든 VPN 사이트를 가져옵니다. |
> | 실행력 | Microsoft.Network/virtualWans/write | 가상 Wan을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Network/vpnGateways/delete | VpnGateway를 삭제합니다. |
> | 실행력 | microsoft.network/vpngateways/listvpnconnectionshealth/action | VpnGateway의 모든 연결 또는 하위 연결에 대한 연결 상태를 가져옵니다. |
> | 실행력 | Microsoft.Network/vpnGateways/read | VpnGateway를 가져옵니다. |
> | 실행력 | microsoft.network/vpngateways/reset/action | VpnGateway를 다시 설정합니다. |
> | 실행력 | microsoft.network/vpnGateways/vpnConnections/delete | VpnConnection을 삭제합니다. |
> | 실행력 | microsoft.network/vpnGateways/vpnConnections/read | VpnConnection을 가져옵니다. |
> | 실행력 | microsoft. network/vpnGateways/vpnConnections/vpnLinkConnections/read | Vpn 링크 연결을 가져옵니다. |
> | 실행력 | microsoft.network/vpnGateways/vpnConnections/write | VpnConnection을 씁니다. |
> | 실행력 | Microsoft.Network/vpnGateways/write | VpnGateway를 씁니다. |
> | 실행력 | Microsoft. Network/vpnServerConfigurations/delete | VpnServerConfiguration 삭제 |
> | 실행력 | Microsoft. Network/vpnServerConfigurations/read | VpnServerConfiguration 가져오기 |
> | 실행력 | Microsoft. Network/vpnServerConfigurations/write | VpnServerConfiguration 만들기 또는 업데이트 |
> | 실행력 | Microsoft.Network/vpnsites/delete | Vpn 사이트 리소스를 삭제합니다. |
> | 실행력 | Microsoft.Network/vpnsites/read | Vpn 사이트 리소스를 가져옵니다. |
> | 실행력 | microsoft. network/vpnSites/vpnSiteLinks/read | Vpn 사이트 링크를 가져옵니다. |
> | 실행력 | Microsoft.Network/vpnsites/write | Vpn 사이트 리소스를 만들거나 업데이트합니다. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | 주어진 네임스페이스 리소스 이름을 NotificationHub 서비스 내에서 사용할 수 있는지 여부를 확인합니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다.  |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 네임스페이스 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 네임스페이스 권한 부여 규칙 다시 생성 Primary/SecondaryKey, 다시 생성할 키를 지정하세요. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | 주어진 NotificationHub 이름을 네임스페이스 내에서 사용할 수 있는지 여부를 확인합니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/Delete | 네임스페이스 리소스를 삭제합니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 알림 허브 권한 부여 규칙 목록을 가져옵니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 알림 허브 권한 부여 규칙을 삭제합니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 알림 허브 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 알림 허브 권한 부여 규칙 목록을 가져옵니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 알림 허브 권한 부여 규칙 다시 생성 Primary/SecondaryKey, 다시 생성할 키를 지정하세요. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 알림 허브 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | 테스트 푸시 알림을 보냅니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | 알림 허브 리소스를 삭제합니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | 모든 알림 허브 PNS 자격 증명을 가져옵니다. 여기에는 WNS, MPNS, APNS, GCM 및 Baidu 자격 증명이 포함됩니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 알림 허브 리소스 설명 목록을 가져옵니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 알림 허브를 만들고 해당 속성을 업데이트합니다. 속성에는 주로 PNS 자격 증명, 권한 부여 규칙 및 TTL이 포함됩니다. 권한 부여 규칙 및 TTL |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/read | 네임스페이스 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.NotificationHubs/Namespaces/write | 네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다. |
> | 실행력 | Microsoft.NotificationHubs/operationResults/read | Notification Hubs 공급자에 대한 작업 결과를 반환합니다. |
> | 실행력 | Microsoft.NotificationHubs/operations/read | Notification Hubs 공급자에 대해 지원되는 작업 목록을 반환합니다. |
> | 실행력 | Microsoft.NotificationHubs/register/action | NotificationHubs 리소스 공급자에 대한 구독을 등록하고 네임스페이스와 NotificationHubs 만들기를 사용하도록 설정합니다. |
> | 실행력 | Microsoft.NotificationHubs/unregister/action | NotificationHubs 리소스 공급자에 대한 구독을 등록 취소하고 네임스페이스와 NotificationHubs 만들기를 사용하도록 설정합니다. |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V 클러스터의 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V 클러스터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/delete | Hyper-V 사이트를 삭제합니다. |
> | 실행력 | Microsoft. OffAzure/HyperVSites/healthsummary/read | Hyper-v 리소스에 대 한 상태 요약을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V 호스트 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V 호스트를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V 작업 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V 머신 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 작업 상태 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/read | Hyper-V 사이트 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V 사이트 내의 개체를 새로 고칩니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 계정으로 실행된 Hyper-V 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V 사이트 사용량을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/HyperVSites/write | Hyper-V 사이트를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft. OffAzure/ImportSites/delete | 가져오기 사이트를 삭제 합니다. |
> | 실행력 | Microsoft. OffAzure/ImportSites/importuri/action | 컴퓨터 CSV 파일을 가져오기 위한 SAS uri를 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ImportSites/job/read | 가져오기 작업의 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ImportSites/machines/delete | 가져오기 컴퓨터를 삭제 합니다. |
> | 실행력 | Microsoft. OffAzure/ImportSites/machines/read | 가져오기 컴퓨터의 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ImportSites/read | 가져오기 사이트의 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ImportSites/write | 가져오기 사이트를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.OffAzure/Operations/read | 노출된 작업을 읽습니다. |
> | 실행력 | Microsoft.OffAzure/register/action | Microsoft.OffAzure 리소스 공급자에 구독을 등록합니다. |
> | 실행력 | Microsoft. OffAzure/ServerSites/delete | 서버 사이트를 삭제 합니다. |
> | 실행력 | Microsoft. OffAzure/ServerSites/job/read | 서버 작업의 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ServerSites/machines/read | 서버 컴퓨터의 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ServerSites/machines/write | 서버 컴퓨터의 속성 쓰기 |
> | 실행력 | Microsoft. OffAzure/ServerSites/operationsstatus/read | 서버 작업 상태의 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ServerSites/read | 서버 사이트의 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ServerSites/refresh/action | 서버 사이트 내의 개체를 새로 고칩니다. |
> | 실행력 | Microsoft. OffAzure/ServerSites/runasaccounts/read | 서버 실행 계정의 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ServerSites/usage/read | 서버 사이트의 용도를 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/ServerSites/write | 서버 사이트를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft. OffAzure/VMwareSites clientGroupMembers/action | 선택한 클라이언트 그룹의 클라이언트 그룹 구성원을 나열 합니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/delete | VMware 사이트를 삭제합니다. |
> | 실행력 | Microsoft. OffAzure/VMwareSites/내보내기 응용 프로그램/작업 | VMware 응용 프로그램 및 역할 데이터를 xls로 내보냅니다. |
> | 실행력 | Microsoft. OffAzure/VMwareSites/작업 | 컴퓨터 목록에 대 한 거칠게 맵을 생성 합니다. |
> | 실행력 | Microsoft. OffAzure/VMwareSites/작업 | 자세한 VMware 거칠게 지도를 생성 합니다. |
> | 실행력 | Microsoft. OffAzure/VMwareSites getApplications/action | 선택한 컴퓨터에 대 한 응용 프로그램 정보를 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/VMwareSites/healthsummary/읽기 | VMware 리소스에 대 한 상태 요약을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/jobs/read | VMware 작업 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/VMwareSites/컴퓨터/응용 프로그램/읽기 | VMware 컴퓨터 응용 프로그램의 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/machines/read | VMware 머신 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware 머신을 시작합니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware 머신을 중지합니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 작업 상태 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/read | VMware 사이트 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/refresh/action | VMware 사이트 내의 개체를 새로 고칩니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 계정으로 실행된 VMware 속성을 가져옵니다. |
> | 실행력 | Microsoft. OffAzure/VMwareSites serverGroupMembers/action | 선택한 서버 그룹에 대 한 서버 그룹 구성원을 나열 합니다. |
> | 실행력 | Microsoft. OffAzure/VMwareSites updateProperties/action | 사이트의 컴퓨터에 대 한 속성을 업데이트 합니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/usage/read | VMware 사이트 사용량을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter 속성을 가져옵니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.OffAzure/VMwareSites/write | VMware 사이트를 만들거나 업데이트합니다. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.OperationalInsights/linkTargets/read | Azure 구독과 연결되지 않은 기존 계정 목록을 표시합니다. Azure 구독을 작업 영역에 연결하려면 이 작업에서 반환된 고객 ID를 작업 영역 만들기 작업의 고객 ID 속성에 사용하세요. |
> | 실행력 | microsoft.operationalinsights/operations/read | 사용 가능한 모든 OperationalInsights Rest API 작업을 나열합니다. |
> | 실행력 | microsoft.operationalinsights/register/action | 구독을 Rergisters 합니다. |
> | 실행력 | Microsoft.OperationalInsights/register/action | 리소스 공급자에 구독을 등록합니다. |
> | 실행력 | microsoft.operationalinsights/unregister/action | 구독의 등록을 취소 합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 검색 스키마 V2를 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/api/query/action | 새 엔진을 사용하여 검색합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 검색 스키마 V2를 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 구성 범위를 삭제합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 구성 범위를 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 구성 범위를 설정합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/datasources/delete | 작업 영역의 데이터 원본을 삭제합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/datasources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/datasources/write | 작업 영역의 데이터 원본을 생성/업데이트합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/delete | 작업 영역을 삭제합니다. 생성 시 작업 영역이 기존 작업 영역에 연결된 경우 연결된 작업 영역은 삭제되지 않습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/gateways/delete | 작업 영역에 대해 구성된 게이트웨이를 제거합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | 작업 영역에 대한 등록 인증서를 생성합니다. 이 인증서는 Microsoft System Center Operation Manager를 작업 영역에 연결하는 데 사용됩니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 지정된 작업 영역에 인텔리전스 팩을 사용하지 않도록 설정합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 지정된 작업 영역에 인텔리전스 팩을 사용하도록 설정합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 지정된 작업 영역에 대해 표시되는 모든 인텔리전스 팩 목록과 해당 작업 영역에 팩을 사용할 수 있는지 여부를 표시합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 지정된 작업 영역에서 연결된 서비스를 삭제합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/linkedServices/read | 지정된 작업 영역에서 연결된 서비스를 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/linkedServices/write | 지정된 작업 영역에서 연결된 서비스를 만듭니다/업데이트합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/listKeys/action | 작업 영역에 대한 목록 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/listKeys/read | 작업 영역에 대한 목록 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/managementGroups/read | 이 작업 영역에 연결된 System Center Operations Manager 관리 그룹의 이름 및 메타데이터를 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | 작업 영역의 메트릭 정의를 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | 작업 영역에 대한 사용자 알림 설정을 삭제합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/notificationSettings/read | 작업 영역에 대한 사용자 알림 설정을 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/notificationSettings/write | 작업 영역에 대한 사용자 알림 설정을 지정합니다. |
> | 실행력 | microsoft.operationalinsights/workspaces/operations/read | OperationalInsights 작업 영역 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/purge/action | 작업 영역에서 지정된 데이터를 삭제합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | AADDomainServicesAccountLogon 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | AADDomainServicesAccountManagement 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | AADDomainServicesDirectoryServiceAccess 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | AADDomainServicesLogonLogoff 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | AADDomainServicesPolicyChange 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | AADDomainServicesPrivilegeUse 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | AADDomainServicesSystemSecurity 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | ADAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AddonAzureBackupAlerts/읽기 | AddonAzureBackupAlerts 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AddonAzureBackupJobs/읽기 | AddonAzureBackupJobs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AddonAzureBackupPolicy/읽기 | AddonAzureBackupPolicy 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AddonAzureBackupProtectedInstance/읽기 | AddonAzureBackupProtectedInstance 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AddonAzureBackupStorage/읽기 | AddonAzureBackupStorage 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | ADFActivityRun 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | ADFPipelineRun 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | ADFTriggerRun 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | ADReplicationResult 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | ADSecurityAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AegDeliveryFailureLogs/읽기 | AegDeliveryFailureLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AegPublishFailureLogs/읽기 | AegPublishFailureLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Alert/read | Alert 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | AlertHistory 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AmlComputeClusterEvent/읽기 | AmlComputeClusterEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AmlComputeClusterNodeEvent/읽기 | AmlComputeClusterNodeEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/workspaces/query/AmlComputeJobEvent/read | AmlComputeJobEvent 테이블에서 데이터 읽기 |
> | 실행력 | OperationalInsights/작업 영역/쿼리/ApiManagementGatewayLogs/읽기 | ApiManagementGatewayLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | AppCenterError 테이블의 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | ApplicationInsights 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AppPlatformLogsforSpring/읽기 | AppPlatformLogsforSpring 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AppPlatformSystemLogs/read | AppPlatformSystemLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AppServiceAppLogs/읽기 | AppServiceAppLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AppServiceAuditLogs/읽기 | AppServiceAuditLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AppServiceConsoleLogs/읽기 | AppServiceConsoleLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/Appservice환경 Platformlogs/read | Appservice환경 Platformlogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AppServiceFileAuditLogs/읽기 | AppServiceFileAuditLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/AppServiceHTTPLogs/읽기 | AppServiceHTTPLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | AuditLogs 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | AutoscaleEvaluationsLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | AutoscaleScaleActionsLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | AWSCloudTrail 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | AzureActivity 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | AzureAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | AzureMetrics 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/BaiClusterEvent/읽기 | BaiClusterEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/BaiClusterNodeEvent/읽기 | BaiClusterNodeEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/BaiJobEvent/읽기 | BaiJobEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | BlockchainApplicationLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | BlockchainProxyLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | BoundPort 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | CommonSecurityLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | ComputerGroup 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | ConfigurationChange 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | ConfigurationData 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | ContainerImageInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | ContainerInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | ContainerLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | ContainerNodeInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/ContainerRegistryLoginEvents/읽기 | ContainerRegistryLoginEvents 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/ContainerRegistryRepositoryEvents/읽기 | ContainerRegistryRepositoryEvents 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | ContainerServiceLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/CoreAzureBackup/읽기 | CoreAzureBackup 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | DatabricksAccounts 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | DatabricksClusters 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | DatabricksDBFS 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/DatabricksInstancePools/읽기 | DatabricksInstancePools 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | DatabricksJobs 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | DatabricksNotebook 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | DatabricksSecrets 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | DatabricksSQLPermissions 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | DatabricksSSH 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | DatabricksTables 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | DatabricksWorkspace 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/종속성/읽기 | 종속성 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | DeviceAppCrash 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | DeviceAppLaunch 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | DeviceCalendar 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | DeviceCleanup 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | DeviceConnectSession 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | DeviceEtw 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | DeviceHardwareHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | DeviceHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | DeviceHeartbeat 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | DeviceSkypeHeartbeat 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | DeviceSkypeSignIn 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | DeviceSleepState 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | DHAppFailure 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | DHAppReliability 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/DHCPActivity/read | DHCPActivity 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | DHDriverReliability 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | DHLogonFailures 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | DHLogonMetrics 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | DHOSCrashData 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | DHOSReliability 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | DHWipAppLearning 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | DnsEvents 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | DnsInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | ETWEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Event/read | Event 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | ExchangeAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | ExchangeOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/FailedIngestion/읽기 | FailedIngestion 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/FunctionAppLogs/읽기 | FunctionAppLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Heartbeat 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | HuntingBookmark 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | IISAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | InboundConnection 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | InsightsMetrics 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | IntuneAuditLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/workspaces/query/IntuneDeviceComplianceOrg/read | IntuneDeviceComplianceOrg 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | IntuneOperationalLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/IoTHubDistributedTracing/읽기 | IoTHubDistributedTracing 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | KubeEvents 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/KubeHealth/읽기 | KubeHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/KubeMonAgentEvents/읽기 | KubeMonAgentEvents 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | KubeNodeInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | KubePodInventory 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | KubeServices 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | LinuxAuditLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | MAApplication 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | MAApplicationHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | MAApplicationHealthAlternativeVersions 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | MAApplicationHealthIssues 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | MAApplicationInstance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | MAApplicationInstanceReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | MAApplicationReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | MADeploymentPlan 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADevice/read | MADevice 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | MADeviceNotEnrolled 테이블의 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | MADeviceNRT 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | MADevicePnPHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | MADevicePnPHealthAlternativeVersions 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | MADevicePnPHealthIssues 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | MADeviceReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | MADriverInstanceReadiness 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | MADriverReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | MAOfficeAddin 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Ma의 Addinentityhealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | MAOfficeAddinHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | MAOfficeAddinHealthEventNRT 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | MAOfficeAddinHealthIssues 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | MAOfficeAddinInstance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | MAOfficeAddinInstanceReadiness 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | MAOfficeAddinReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | MAOfficeApp 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | MAOfficeAppCrashesNRT 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | MAOfficeAppHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | MAOfficeAppInstance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | MAOfficeAppInstanceHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | MAOfficeAppReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | MAOfficeAppSessionsNRT 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | MAOfficeBuildInfo 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | MAOfficeCurrencyAssessment 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | MAOfficeCurrencyAssessmentDailyCounts 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | MAOfficeDeploymentStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | MAOfficeDeploymentStatusNRT 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | MAOfficeMacroErrorNRT 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | MAOfficeMacroGlobalHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | MAOfficeMacroHealth 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | MAOfficeMacroHealthIssues 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | MAOfficeMacroIssueInstanceReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | MAOfficeMacroIssueReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | MAOfficeMacroSummary 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | MAOfficeSuite 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | MAOfficeSuiteInstance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | MAProposedPilotDevices 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | MAWindowsBuildInfo 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | MAWindowsCurrencyAssessment 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | MAWindowsCurrencyAssessmentDailyCounts 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | MAWindowsDeploymentStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | MAWindowsDeploymentStatusNRT 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | MAWindowsSysReqInstanceReadiness 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/McasShadowItReporting/읽기 | McasShadowItReporting 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/MicrosoftAzureBastionAuditLogs/읽기 | MicrosoftAzureBastionAuditLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/MicrosoftDataShareReceivedSnapshotLog/읽기 | MicrosoftDataShareReceivedSnapshotLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/MicrosoftDataShareSentSnapshotLog/읽기 | MicrosoftDataShareSentSnapshotLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/MicrosoftDataShareShareLog/읽기 | MicrosoftDataShareShareLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/MicrosoftDynamicsTelemetryPerformanceLogs/읽기 | MicrosoftDynamicsTelemetryPerformanceLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/MicrosoftDynamicsTelemetrySystemMetricsLogs/읽기 | MicrosoftDynamicsTelemetrySystemMetricsLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/MicrosoftHealthcareApisAuditLogs/읽기 | MicrosoftHealthcareApisAuditLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/MicrosoftInsightsAzureActivityLog/읽기 | MicrosoftInsightsAzureActivityLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | NetworkMonitoring 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | OfficeActivity 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Operation/read | Operation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | OutboundConnection 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Perf/read | Perf 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | ProtectionStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/read | 작업 영역의 데이터에서 쿼리를 실행 |
> | 실행력 | OperationalInsights/작업 영역/쿼리/요청/읽기 | 요청 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | ReservedCommonFields 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | SCCMAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | SCOMAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | SecurityAlert 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | SecurityBaseline 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | SecurityBaselineSummary 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | SecurityDetection 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | SecurityEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | SecurityIoTRawEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | SecurityRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | ServiceFabricOperationalEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | ServiceFabricReliableActorEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | ServiceFabricReliableServiceEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | SfBAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | SfBOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | SharePointOnlineAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/SignalRServiceDiagnosticLogs/읽기 | SignalRServiceDiagnosticLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | SigninLogs 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | SPAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | SQLAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | SQLQueryPerformance 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | SqlThreatProtectionLoginAudits 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | SqlVulnerabilityAssessmentResult 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/StorageBlobLogs/읽기 | StorageBlobLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/StorageFileLogs/read | StorageFileLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/StorageQueueLogs/읽기 | StorageQueueLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/StorageTableLogs/read | StorageTableLogs 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/SucceededIngestion/읽기 | SucceededIngestion 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Syslog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | SysmonEvent 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | 사용자 지정 로그에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | ThreatIntelligenceIndicator 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/추적/읽기 | 추적 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UAApp/read | UAApp 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | UAComputer 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | UAComputerRank 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UADriver/read | UADriver 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | UADriverProblemCodes 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | UAFeedback 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | UAHardwareSecurity 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | UAIESiteDiscovery 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | UAOfficeAddIn 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | UAProposedActionPlan 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | UASysReqIssue 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | UAUpgradedComputer 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Update/read | Update 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | UpdateRunProgress 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Update/read | UpdateSummary 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/Usage/read | Usage 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | VMBoundPort 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/VMComputer/읽기 | VMComputer 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | VMConnection 테이블에서 데이터를 읽습니다. |
> | 실행력 | OperationalInsights/작업 영역/쿼리/VMProcess/read | VMProcess 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | W3CIISLog 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | WaaSDeploymentStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | WaaSInsiderStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | WaaSUpdateStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | WDAVStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | WDAVThreat 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | WindowsClientAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | WindowsEvent 테이블에서 데이터 읽기 |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | WindowsFirewall 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | WindowsServerAssessmentRecommendation 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WireData/read | WireData 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | WorkloadMonitoringPerf 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | WUDOAggregatedStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | WUDOStatus 테이블에서 데이터를 읽습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/read | 기존 작업 영역을 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | 지정된 작업 영역 공유 키를 다시 생성합니다. |
> | 실행력 | microsoft.operationalinsights/workspaces/rules/read | 모든 경고 규칙을 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 저장된 검색 쿼리를 삭제합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/savedSearches/read | 저장된 검색 쿼리를 가져옵니다. |
> | 실행력 | microsoft.operationalinsights/workspaces/savedsearches/results/read | 저장된 검색 결과를 가져옵니다. 사용되지 않음 |
> | 실행력 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | 예약된 검색 작업을 삭제합니다. |
> | 실행력 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | 예약된 검색 작업을 가져옵니다. |
> | 실행력 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | 예약된 검색 작업을 만들거나 업데이트합니다. |
> | 실행력 | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | 예약된 검색을 삭제합니다. |
> | 실행력 | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | 예약된 검색을 가져옵니다. |
> | 실행력 | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | 예약된 검색을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/savedSearches/write | 저장된 검색 쿼리를 만듭니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/schema/read | 작업 영역에 대한 검색 스키마를 가져옵니다.  검색 스키마에는 노출된 필드와 필드 형식이 포함되어 있습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | 실행력 | microsoft.operationalinsights/workspaces/search/read | 검색 결과를 가져옵니다. 사용되지 않습니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | 스토리지 구성을 삭제합니다. 이렇게 하면 Microsoft Operational Insights에서 스토리지 계정의 데이터 읽기를 중지합니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | 스토리지 구성을 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 새 스토리지 구성을 만듭니다. 이러한 구성은 기존 스토리지 계정의 위치에서 데이터를 가져오는 데 사용됩니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | 작업 영역에 대한 검색 업그레이드 변환 오류 로그를 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/usages/read | 작업 영역에서 읽는 데이터 양을 포함하여 작업 영역에 대한 사용량 현황 데이터를 가져옵니다. |
> | 실행력 | Microsoft.OperationalInsights/workspaces/write | 새 작업 영역을 만들거나 기존 작업 영역의 고객 ID를 제공하여 기존 작업 영역에 연결합니다. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.OperationsManagement/managementAssociations/delete | 기존 관리 연결을 삭제합니다. |
> | 실행력 | Microsoft.OperationsManagement/managementAssociations/read | 기존 관리 연결을 가져옵니다. |
> | 실행력 | Microsoft.OperationsManagement/managementAssociations/write | 새 관리 연결을 만듭니다. |
> | 실행력 | Microsoft.OperationsManagement/managementConfigurations/delete | 기존 관리 구성을 삭제합니다. |
> | 실행력 | Microsoft.OperationsManagement/managementConfigurations/read | 기존 관리 구성을 가져옵니다. |
> | 실행력 | Microsoft.OperationsManagement/managementConfigurations/write | 새 기존 관리 구성을 만듭니다. |
> | 실행력 | Microsoft.OperationsManagement/register/action | 리소스 공급자에 구독을 등록합니다. |
> | 실행력 | Microsoft.OperationsManagement/solutions/delete | 기존 OMS 솔루션을 삭제합니다. |
> | 실행력 | Microsoft.OperationsManagement/solutions/read | 기존 OMS 솔루션을 가져옵니다. |
> | 실행력 | Microsoft.OperationsManagement/solutions/write | 새 OMS 솔루션을 만듭니다. |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.PolicyInsights/asyncOperationResults/read | 비동기 작업 결과를 가져옵니다. |
> | DataAction | Microsoft. PolicyInsights/checkDataPolicyCompliance/action | 데이터 정책에 대해 지정 된 구성 요소의 준수 상태를 확인 합니다. |
> | 실행력 | Microsoft.PolicyInsights/operations/read | Microsoft PolicyInsights 네임 스페이스에 대해 지원 되는 작업을 가져옵니다. |
> | DataAction | Microsoft. PolicyInsights/Policyinsights/logDataEvents/action | 리소스 구성 요소 정책 이벤트를 로깅합니다. |
> | 실행력 | Microsoft.PolicyInsights/policyEvents/queryResults/action | 정책 이벤트에 대한 정보를 쿼리합니다. |
> | 실행력 | Microsoft.PolicyInsights/policyEvents/queryResults/read | 정책 이벤트에 대한 정보를 쿼리합니다. |
> | 실행력 | Microsoft. PolicyInsights/Policyinsights/read | 정책 메타 데이터 리소스를 가져옵니다. |
> | 실행력 | Microsoft.PolicyInsights/policyStates/queryResults/action | 정책 상태에 대한 정보를 쿼리합니다. |
> | 실행력 | Microsoft.PolicyInsights/policyStates/queryResults/read | 정책 상태에 대한 정보를 쿼리합니다. |
> | 실행력 | Microsoft.PolicyInsights/policyStates/summarize/action | 최신 정책 상태에 대한 요약 정보를 쿼리합니다. |
> | 실행력 | Microsoft.PolicyInsights/policyStates/summarize/read | 최신 정책 상태에 대한 요약 정보를 쿼리합니다. |
> | 실행력 | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 선택한 범위에 대한 새 준수 평가를 트리거합니다. |
> | 실행력 | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | DeployIfNotExists 정책에 필요한 리소스에 대한 정보를 쿼리합니다. |
> | 실행력 | Microsoft.PolicyInsights/register/action | Microsoft Policy Insights 리소스 공급자를 등록 하 고 작업을 사용 하도록 설정 합니다. |
> | 실행력 | Microsoft.PolicyInsights/remediations/cancel/action | 진행 중인 Microsoft Policy 재구성를 취소 합니다. |
> | 실행력 | Microsoft.PolicyInsights/remediations/delete | 정책 수정을 삭제합니다. |
> | 실행력 | Microsoft.PolicyInsights/remediations/listDeployments/read | 정책 수정에 필요한 배포를 나열합니다. |
> | 실행력 | Microsoft.PolicyInsights/remediations/read | 정책 수정을 가져옵니다. |
> | 실행력 | Microsoft.PolicyInsights/remediations/write | Microsoft Policy 재구성를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.PolicyInsights/unregister/action | Microsoft Policy Insights 리소스 공급자를 등록 취소 합니다. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Portal/consoles/delete | Cloud Shell 인스턴스를 제거 합니다. |
> | 실행력 | Microsoft.Portal/consoles/read | Cloud Shell 인스턴스를 읽습니다. |
> | 실행력 | Microsoft.Portal/consoles/write | Cloud Shell 인스턴스를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Portal/dashboards/delete | 구독에서 대시보드를 제거합니다. |
> | 실행력 | Microsoft.Portal/dashboards/read | 구독에 대한 대시보드를 읽습니다. |
> | 실행력 | Microsoft.Portal/dashboards/write | 구독에 대해 대시보드를 추가하거나 수정합니다. |
> | 실행력 | Microsoft.Portal/register/action | 포털에 등록합니다. |
> | 실행력 | Microsoft.Portal/usersettings/delete | Cloud Shell 사용자 설정을 제거 합니다. |
> | 실행력 | Microsoft.Portal/usersettings/read | Cloud Shell 사용자 설정을 읽습니다. |
> | 실행력 | Microsoft.Portal/usersettings/write | 사용자 설정 Cloud Shell 만들거나 업데이트 합니다. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.PowerBIDedicated/capacities/delete | Power BI 전용 용량을 삭제합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/capacities/read | 지정된 Power BI 전용 용량에 대한 정보를 검색합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/capacities/resume/action | 용량을 다시 시작합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/capacities/skus/read | 용량에 사용 가능한 SKU 정보를 검색합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/capacities/suspend/action | 용량을 일시 중단합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/capacities/write | 지정된 Power BI 전용 용량을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | 지정된 Power BI 전용 용량 이름이 올바르고 사용 중이 아닌지 확인합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/locations/operationresults/read | 지정된 작업 결과에 대한 정보를 검색합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 지정된 작업 상태에 대한 정보를 검색합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/operations/read | 작업 정보를 검색합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/register/action | Power BI 전용 리소스 공급자를 등록합니다. |
> | 실행력 | Microsoft.PowerBIDedicated/skus/read | SKU 정보를 검색합니다. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | 실행력 | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
> | 실행력 | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | 실행력 | microsoft.recoveryservices/Locations/backupProtectedItem/write | 백업 보호 항목을 만듭니다. |
> | 실행력 | microsoft.recoveryservices/Locations/backupProtectedItems/read | 모든 보호 항목 목록을 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | 실행력 | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Check Resource Name Availability는 리소스 이름을 사용할 수 있는지 확인하는 API입니다. |
> | 실행력 | Microsoft.RecoveryServices/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | 실행력 | Microsoft.RecoveryServices/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | 실행력 | Microsoft.RecoveryServices/register/action | 지정된 리소스 공급자에 대한 구독을 등록합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupconfig/read | Recovery Services 자격 증명 모음에 구성을 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupconfig/write | Recovery Services 자격 증명 모음에 대한 구성을 업데이트합니다. |
> | 실행력 | microsoft recoveryservices/자격 증명 모음/backupEncryptionConfigs/읽기 | 백업 리소스 암호화 구성을 가져옵니다. |
> | 실행력 | microsoft recoveryservices/자격 증명 모음/backupEncryptionConfigs/쓰기 | 백업 리소스 암호화 구성을 업데이트 합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/delete | 백업 보호 의도를 삭제합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/read | 백업 보호 의도를 가져옵니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | 백업 보호 의도 만들기 |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | 작업의 상태를 반환합니다. |
> | 실행력 | microsoft recoveryservices/자격 증명 모음/backupFabrics/operationsStatus/읽기 | 작업의 상태를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectableContainers/read | 보호 가능한 컨테이너를 모두 가져옵니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | 등록된 컨테이너를 삭제합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | 컨테이너 내의 워크로드를 조회합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | 컨테이너의 모든 항목을 가져옵니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | 보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다. |
> | 실행력 | microsoft recoveryservices/자격 증명 모음/backupFabrics/protectionContainers/operationsStatus/read | 보호 컨테이너에 대해 수행 된 작업의 상태를 가져옵니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 보호 항목 Backup을 수행합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | 보호된 항목을 삭제합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 보호 항목에 대해 수행된 작업의 결과를 가져옵니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 프로비전합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 보호 항목의 복구 지점을 가져옵니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 보호 항목의 복구 지점을 복원합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 취소합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 백업 보호 항목을 만듭니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | 등록된 모든 컨테이너를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | 등록된 컨테이너를 만듭니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupFabrics/refreshContainers/action | 컨테이너 목록을 새로 고칩니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupJobs/cancel/action | 작업을 취소합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupJobs/operationResults/read | 작업의 작업 결과를 반환합니다. |
> | 실행력 | microsoft recoveryservices/자격 증명 모음/backupJobs/operationsStatus/read | 작업의 상태를 반환 합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupJobs/read | 모든 작업 개체를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupOperationResults/read | Recovery Services 자격 증명 모음의 Backup 작업 결과를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupPolicies/delete | 보호 정책을 삭제합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupPolicies/operationResults/read | 정책 작업의 결과를 가져옵니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupPolicies/operations/read | 정책 작업의 상태를 가져옵니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupPolicies/write | 보호 정책을 만듭니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupProtectableItems/read | 모든 보호 가능한 항목 목록을 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupProtectedItems/read | 모든 보호 항목 목록을 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupProtectionContainers/read | 구독에 속하는 컨테이너를 모두 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupSecurityPIN/action | Recovery Services 자격 증명 모음에 대한 보안 PIN 정보를 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupstorageconfig/read | Recovery Services 자격 증명 모음에 대한 스토리지 구성을 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupstorageconfig/write | Recovery Services 자격 증명 모음에 대한 스토리지 구성을 업데이트합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/backupValidateOperation/action | 보호된 항목에 대한 작업의 유효성을 검사합니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/delete | 자격 증명 모음 삭제 작업에서는 '자격 증명 모음' 형식의 지정된 Azure 리소스를 삭제합니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/extendedInformation/write | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Recovery Services 자격 증명 모음 알림 구성을 가져옵니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Recovery Services 자격 증명 모음에 이메일 알림을 구성합니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | 컨테이너 등록 취소 작업을 사용하여 컨테이너 등록을 취소할 수 있습니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | 서비스 컨테이너 등록 작업을 사용하여 복구 서비스와 함께 컨테이너를 등록할 수 있습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 경고 설정을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | 경고 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 패브릭의 일관성을 검사합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | 패브릭을 삭제합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | 프로세스 서버 이미지를 배포합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Fabric에서 AAD로 마이그레이션합니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationFabrics/operationresults/read | 리소스 패브릭에 대 한 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 패브릭을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 게이트웨이를 다시 연결합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | 패브릭을 제거합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 패브릭용 인증서 갱신 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 논리 네트워크를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 네트워크를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | 네트워크 매핑을 삭제합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | 네트워크 매핑을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 보호 가능한 항목을 검색합니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationFabrics/replicationProtectionContainers/operationresults/read | 리소스 보호 컨테이너에서 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 보호 컨테이너를 제거합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | 마이그레이션 항목을 삭제합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | 마이그레이션 항목 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | 마이그레이션 복제 복구 지점을 읽습니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | 리소스 마이그레이션 항목에 대 한 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | 마이그레이션 항목을 읽습니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/다시 동기화/작업 | 다시 동기화 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | 테스트 마이그레이션 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | 테스트 마이그레이션 정리 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | 마이그레이션 항목을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 항목을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | 디스크 추가 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 복구 지점을 적용합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | 보호된 항목을 삭제합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 장애 조치(Failover) 커밋 |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | 리소스 보호 항목에 대 한 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 계획된 장애 조치(Failover) |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 복제 복구 지점을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | 보호된 항목을 제거합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | 디스크 제거 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 복제를 복구합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 보호된 항목을 다시 보호합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | 피드백을 제출합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | 모든 대상 컴퓨팅 크기 읽기 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 테스트 장애 조치(Failover) |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 테스트 장애 조치(Failover) 정리 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 장애 조치(Failover) |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 모바일 서비스를 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | 보호된 항목을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 보호 컨테이너 매핑을 삭제합니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/read | 리소스 보호 컨테이너 매핑에서 비동기 작업의 결과를 추적 합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 보호 컨테이너 매핑을 제거합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 보호 컨테이너 매핑을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 보호 컨테이너를 전환합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 보호 컨테이너를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Recovery Services 공급자를 삭제합니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationFabrics/replicationRecoveryServicesProviders/operationresults/read | 리소스 Recovery Services 공급자에 대 한 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 공급자를 새로 고칩니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services 공급자를 제거합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Recovery Services 공급자를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 스토리지 분류를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | 스토리지 분류 매핑을 삭제합니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/read | 리소스 저장소 분류 매핑에서 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 스토리지 분류 매핑을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | 스토리지 분류 매핑을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | vCenter를 삭제합니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationFabrics/replicationvCenters/operationresults/read | 리소스 \ 리소스에 대 한 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | vCenter를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationFabrics/write | 패브릭을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | 작업을 취소합니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationJobs/operationresults/read | 리소스 작업에 대 한 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationJobs/read | 작업을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | 작업을 다시 시작합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | 작업을 계속합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | 마이그레이션 항목을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationNetworks/read | 네트워크를 읽습니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationOperationStatus/읽기 | 자격 증명 모음 복제 작업 상태를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | 정책을 삭제합니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationPolicies/operationresults/read | 리소스 정책에 대 한 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 정책을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationPolicies/write | 정책을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 복구 계획을 삭제합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 장애 조치(Failover) 커밋 복구 계획 |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationRecoveryPlans/operationresults/read | 리소스 복구 계획에 대 한 비동기 작업의 결과 추적 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 계획된 장애 조치(Failover) 복구 계획 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 복구 계획을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 복구 계획을 다시 보호합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 테스트 장애 조치(failover) 복구 계획 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 테스트 장애 조치(failover) 정리 복구 계획 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 장애 조치(Failover) 복구 계획 |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 복구 계획을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | 스토리지 분류 매핑을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | 스토리지 분류를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | 모든 항목을 읽습니다.  |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationUsages/read | 자격 증명 모음 복제 사용 현황을 읽습니다. |
> | 실행력 | Microsoft RecoveryServices/자격 증명 모음/replicationVaultHealth/operationresults/read | 리소스 자격 증명 모음 복제 상태에 대 한 비동기 작업의 결과를 추적 합니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | 자격 증명 모음 복제 상태를 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | 자격 증명 모음 상태를 새로 고칩니다. |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | 모든 항목을 읽습니다.  |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | 만들기 또는 업데이트  |
> | 실행력 | Microsoft.RecoveryServices/vaults/replicationvCenters/read | vCenter를 읽습니다. |
> | 실행력 | microsoft.recoveryservices/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/usages/read | 자격 증명 모음 사용 현황을 읽습니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | 실행력 | Microsoft.RecoveryServices/Vaults/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Relay/checkNameAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. |
> | 실행력 | Microsoft.Relay/checkNamespaceAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. 이 API는 사용되지 않으므로 CheckNameAvailability를 대신 사용하세요. |
> | 실행력 | Microsoft.Relay/namespaces/authorizationRules/action | 네임스페이스 권한 부여 규칙을 업데이트합니다. 이 API는 더 이상 사용되지 않습니다. 그 대신 PUT 호출을 사용하여 네임스페이스 권한 부여 규칙을 업데이트하세요. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 실행력 | Microsoft.Relay/namespaces/authorizationRules/delete | 네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다.  |
> | 실행력 | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 네임스페이스 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/authorizationRules/read | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> | 실행력 | Microsoft.Relay/namespaces/authorizationRules/write | 네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.Relay/namespaces/Delete | 네임스페이스 리소스를 삭제합니다. |
> | 실행력 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 재해 복구 기본 네임스페이스의 권한 부여 규칙 키를 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | 재해 복구 기본 네임스페이스의 권한 부여 규칙을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | 재해 복구를 사용하지 않도록 설정하고 기본 네임스페이스에서 보조 네임스페이스로의 변경 복제를 중지합니다. |
> | 실행력 | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 지정된 구독에서 네임스페이스 별칭의 가용성을 확인합니다. |
> | 실행력 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 네임스페이스와 연결된 재해 복구 구성을 삭제합니다. 이 작업은 기본 네임스페이스를 통해서만 호출할 수 있습니다. |
> | 실행력 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | GEO DR 장애 조치(failover)를 호출하고, 네임스페이스 별칭이 보조 네임스페이스를 가리키도록 다시 구성합니다. |
> | 실행력 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 네임스페이스와 연결된 재해 복구 구성을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 네임스페이스와 연결된 재해 복구 구성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | HybridConnection을 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> | 실행력 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | HybridConnection 권한 부여 규칙 삭제 작업 |
> | 실행력 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | HybridConnection 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  HybridConnection 권한 부여 규칙 목록을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> | 실행력 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | HybridConnection 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.Relay/namespaces/HybridConnections/Delete | HybridConnection 리소스 삭제 작업 |
> | 실행력 | Microsoft.Relay/namespaces/HybridConnections/read | HybridConnection 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/HybridConnections/write | HybridConnection 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Relay/namespaces/messagingPlan/read | 네임스페이스에 대한 메시지 계획을 가져옵니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 실행력 | Microsoft.Relay/namespaces/messagingPlan/write | 네임스페이스에 대한 메시지 계획을 업데이트합니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 실행력 | Microsoft. Relay/네임 스페이스/networkrulesets 집합/삭제 | VNET 규칙 리소스 삭제 |
> | 실행력 | Microsoft. Relay/네임 스페이스/networkrulesets 집합/읽기 | NetworkRuleSet 집합 리소스를 가져옵니다. |
> | 실행력 | Microsoft. Relay/네임 스페이스/네트워크 규칙 집합/쓰기 | VNET 규칙 리소스 만들기 |
> | 실행력 | Microsoft.Relay/namespaces/operationresults/read | 네임스페이스 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft. Relay/네임 스페이스/공급자/Microsoft/diagnosticSettings/read | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft. Relay/네임 스페이스/공급자/Microsoft/diagnosticSettings/write | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft. Relay/네임 스페이스/공급자/Microsoft의 Insights/logDefinitions/읽기 | 네임스페이스 로그 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/read | 네임스페이스 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS 네임스페이스를 제거합니다. |
> | 실행력 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | WcfRelay를 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> | 실행력 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | WcfRelay 권한 부여 규칙 삭제 작업 |
> | 실행력 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | WcfRelay의 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  WcfRelay 권한 부여 규칙 목록을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> | 실행력 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | WcfRelay 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.Relay/namespaces/WcfRelays/Delete | WcfRelay 리소스 삭제 작업 |
> | 실행력 | Microsoft.Relay/namespaces/WcfRelays/read | WcfRelay 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.Relay/namespaces/WcfRelays/write | WcfRelay 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Relay/namespaces/write | 네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다. |
> | 실행력 | Microsoft.Relay/operations/read | 작업을 가져옵니다. |
> | 실행력 | Microsoft.Relay/register/action | 릴레이 리소스 공급자에 대한 구독을 등록하고 릴레이 리소스를 만들도록 설정합니다. |
> | 실행력 | Microsoft.Relay/unregister/action | 릴레이 리소스 공급자에 대한 구독을 등록하고 릴레이 리소스를 만들도록 설정합니다. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 지정된 리소스에 대한 가용성 상태를 가져옵니다. |
> | 실행력 | Microsoft.ResourceHealth/AvailabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | 실행력 | Microsoft.ResourceHealth/events/read | 지정된 구독에 대한 Service Health 이벤트를 가져옵니다. |
> | 실행력 | Microsoft.Resourcehealth/healthevent/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 실행력 | Microsoft.Resourcehealth/healthevent/Activated/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 실행력 | Microsoft.Resourcehealth/healthevent/InProgress/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 실행력 | Microsoft.Resourcehealth/healthevent/Pending/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 실행력 | Microsoft.Resourcehealth/healthevent/Resolved/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 실행력 | Microsoft.Resourcehealth/healthevent/Updated/action | 지정된 리소스의 상태 변경 사항을 나타냅니다. |
> | 실행력 | Microsoft.ResourceHealth/impactedResources/read | 지정된 리소스로 인해 영향을 받는 리소스를 가져옵니다. |
> | 실행력 | Microsoft.ResourceHealth/metadata/read | 메타 데이터를 가져옵니다. |
> | 실행력 | Microsoft ResourceHealth/알림/읽기 | Azure Resource Manager 알림을 받습니다. |
> | 실행력 | Microsoft.ResourceHealth/Operations/read | Microsoft ResourceHealth에 사용 가능한 작업을 가져옵니다. |
> | 실행력 | Microsoft.ResourceHealth/register/action | Microsoft ResourceHealth에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.ResourceHealth/unregister/action | Microsoft ResourceHealth에 대한 구독을 등록 취소합니다. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | CalculateTemplateHash/작업 | 제공 된 템플릿의 해시를 계산 합니다. |
> | 실행력 | Microsoft .Resources/checkPolicyCompliance/읽기 | 지정된 리소스의 준수 상태를 리소스 정책과 비교하여 확인합니다. |
> | 실행력 | Microsoft.Resources/checkResourceName/action | 리소스 이름의 유효성을 확인합니다. |
> | 실행력 | Microsoft.Resources/deployments/cancel/action | 배포를 취소합니다. |
> | 실행력 | Microsoft.Resources/deployments/delete | 배포를 삭제합니다. |
> | 실행력 | Microsoft .Resources/배포/내보내기 템플릿/작업 | 배포용 템플릿 내보내기 |
> | 실행력 | Microsoft.Resources/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | 실행력 | Microsoft .Resources/배포/operationstatuses/읽기 | 배포 작업 상태를 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/deployments/read | 배포를 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/deployments/validate/action | 배포의 유효성을 검사합니다. |
> | 실행력 | Microsoft.Resources/deployments/whatIf/action | 템플릿 배포 변경 사항을 예측 합니다. |
> | 실행력 | Microsoft.Resources/deployments/write | 배포를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft .Resources/deploymentScripts/delete | 배포 스크립트를 삭제 합니다. |
> | 실행력 | Microsoft .Resources/deploymentScripts/logs/read | 배포 스크립트 로그를 가져오거나 나열 합니다. |
> | 실행력 | Microsoft .Resources/deploymentScripts/read | 배포 스크립트를 가져오거나 나열 합니다. |
> | 실행력 | Microsoft .Resources/deploymentScripts/write | 배포 스크립트를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Resources/links/delete | 리소스 링크를 삭제합니다. |
> | 실행력 | Microsoft.Resources/links/read | 리소스 링크를 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/links/write | 리소스 링크를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Resources/marketplace/purchase/action | 마켓플레이스에서 리소스를 구매합니다. |
> | 실행력 | Microsoft.Resources/providers/read | 공급자 목록을 가져옵니다. |
> | 실행력 | Microsoft.Resources/resources/read | 필터를 기준으로 리소스 목록을 가져옵니다. |
> | 실행력 | Microsoft.Resources/subscriptions/locations/read | 지원되는 위치를 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft.Resources/subscriptions/providers/read | 리소스 공급자를 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/read | 구독 목록을 가져옵니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourceGroups/delete | 리소스 그룹과 해당하는 모든 리소스를 삭제합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | 배포 작업 상태를 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | 배포를 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | 배포를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | 한 리소스 그룹에서 다른 리소스 그룹으로 리소스를 이동합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourcegroups/resources/read | 리소스 그룹에 대한 리소스를 가져옵니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | 한 리소스 그룹에서 다른 리소스 그룹으로 리소스 이동의 유효성을 검사합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resourceGroups/write | 리소스 그룹을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/resources/read | 구독 리소스를 가져옵니다. |
> | 실행력 | Microsoft.Resources/subscriptions/tagNames/delete | 구독 태그를 삭제합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/tagNames/read | 구독 태그를 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | 구독 태그 값을 삭제합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/tagNames/tagValues/read | 구독 태그 값을 가져오거나 나열합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/tagNames/tagValues/write | 구독 태그 값을 추가합니다. |
> | 실행력 | Microsoft.Resources/subscriptions/tagNames/write | 구독 태그를 추가합니다. |
> | 실행력 | Microsoft.Resources/tags/delete | 리소스의 모든 태그를 제거 합니다. |
> | 실행력 | Microsoft.Resources/tags/read | 리소스의 모든 태그를 가져옵니다. |
> | 실행력 | Microsoft.Resources/tags/write | 기존 태그를 새 태그 집합으로 대체 하거나 병합 하거나 기존 태그를 제거 하 여 리소스에 대 한 태그를 업데이트 합니다. |
> | 실행력 | Microsoft.Resources/tenants/read | 테넌트 목록을 가져옵니다. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Scheduler/jobcollections/delete | 작업 컬렉션을 삭제합니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/disable/action | 작업 컬렉션을 사용하지 않도록 설정합니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/enable/action | 작업 컬렉션을 사용하도록 설정합니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/jobs/delete | 작업을 삭제합니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Scheduler 작업을 기반으로 하여 논리 앱 정의를 생성합니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | 작업 내역을 가져옵니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/jobs/read | 작업을 가져옵니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/jobs/run/action | 작업을 실행합니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/jobs/write | 작업을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/read | 작업 컬렉션을 가져옵니다. |
> | 실행력 | Microsoft.Scheduler/jobcollections/write | 작업 컬렉션을 만들거나 업데이트합니다. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Search/checkNameAvailability/action | 서비스 이름의 가용성을 확인합니다. |
> | 실행력 | Microsoft.Search/operations/read | Microsoft.Search 공급자의 사용 가능한 작업을 모두 나열합니다. |
> | 실행력 | Microsoft.Search/register/action | Search 리소스 공급자에 대한 구독을 등록하고 Search 서비스를 만들도록 설정합니다. |
> | 실행력 | Microsoft.Search/searchServices/createQueryKey/action | 쿼리 키를 만듭니다. |
> | 실행력 | Microsoft.Search/searchServices/delete | Search 서비스를 삭제합니다. |
> | 실행력 | Microsoft.Search/searchServices/deleteQueryKey/delete | 쿼리 키를 삭제합니다. |
> | 실행력 | Microsoft.Search/searchServices/listAdminKeys/action | 관리 키를 읽습니다. |
> | 실행력 | Microsoft. 검색/searchServices/listQueryKeys/action | 지정된 Azure Search 서비스에 대한 쿼리 API 키 목록을 반환합니다. |
> | 실행력 | Microsoft.Search/searchServices/listQueryKeys/read | 지정된 Azure Search 서비스에 대한 쿼리 API 키 목록을 반환합니다. |
> | 실행력 | Microsoft. 검색/searchServices/privateEndpointConnectionProxies/delete | 기존 개인 엔드포인트 연결 프록시를 삭제 합니다. |
> | 실행력 | Microsoft. 검색/searchServices/privateEndpointConnectionProxies/읽기 | 개인 엔드포인트 연결 프록시의 목록을 반환 하거나, 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성을 가져옵니다. |
> | 실행력 | Microsoft. 검색/searchServices/privateEndpointConnectionProxies/유효성 검사/작업 | NRP 쪽에서 개인 엔드포인트 연결 만들기 호출의 유효성을 검사 합니다. |
> | 실행력 | Microsoft. 검색/searchServices/privateEndpointConnectionProxies/write | 지정 된 매개 변수를 사용 하 여 개인 엔드포인트 연결 프록시를 만들거나 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 실행력 | Microsoft.Search/searchServices/read | Search 서비스를 읽습니다. |
> | 실행력 | Microsoft.Search/searchServices/regenerateAdminKey/action | 관리 키를 다시 생성합니다. |
> | 실행력 | Microsoft.Search/searchServices/start/action | Search 서비스를 시작합니다. |
> | 실행력 | Microsoft.Search/searchServices/stop/action | Search 서비스를 중지합니다. |
> | 실행력 | Microsoft.Search/searchServices/write | Search 서비스를 만들거나 업데이트합니다. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | 지정 된 네트워크 보안 그룹에서 일치 하는 보안 규칙을 만들어 지정 된 트래픽 강화 규칙을 적용 합니다. |
> | 실행력 | Microsoft.Security/adaptiveNetworkHardenings/read | Azure로 보호 되는 리소스의 적응 네트워크 강화 권장 사항을 가져옵니다. |
> | 실행력 | Microsoft.Security/advancedThreatProtectionSettings/read | 리소스에 대한 Advanced Threat Protection 설정을 가져옵니다. |
> | 실행력 | Microsoft.Security/advancedThreatProtectionSettings/write | 리소스에 대한 Advanced Threat Protection 설정을 업데이트합니다. |
> | 실행력 | Microsoft.Security/alerts/read | 모든 사용 가능한 보안 경고를 가져옵니다. |
> | 실행력 | Microsoft.Security/applicationWhitelistings/read | 애플리케이션 허용 목록을 가져옵니다. |
> | 실행력 | Microsoft.Security/applicationWhitelistings/write | 새 애플리케이션 허용 목록을 만들거나 기존 애플리케이션 허용 목록을 업데이트합니다. |
> | 실행력 | Microsoft. Security/assessmentMetadata/read | 구독에서 사용 가능한 보안 평가 메타 데이터 가져오기 |
> | 실행력 | Microsoft. Security/assessmentMetadata/write | 보안 평가 메타 데이터를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft. 보안/평가/읽기 | 구독에 대 한 보안 평가 가져오기 |
> | 실행력 | Microsoft. 보안/평가/작성 | 구독에서 보안 평가를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.Security/complianceResults/read | 리소스에 대한 준수 결과를 가져옵니다. |
> | 실행력 | Microsoft.Security/informationProtectionPolicies/read | 리소스에 대한 정보 보호 정책을 가져옵니다. |
> | 실행력 | Microsoft.Security/informationProtectionPolicies/write | 리소스에 대한 정보 보호 정책을 업데이트합니다. |
> | 실행력 | Microsoft.Security/locations/alerts/activate/action | 보안 경고를 활성화합니다. |
> | 실행력 | Microsoft.Security/locations/alerts/dismiss/action | 보안 경고를 해제합니다. |
> | 실행력 | Microsoft.Security/locations/alerts/read | 모든 사용 가능한 보안 경고를 가져옵니다. |
> | 실행력 | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Just-In-Time 네트워크 액세스 정책을 삭제합니다. |
> | 실행력 | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Just-In-Time 네트워크 액세스 정책 요청을 시작합니다. |
> | 실행력 | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Just-In-Time 네트워크 액세스 정책을 가져옵니다. |
> | 실행력 | Microsoft.Security/locations/jitNetworkAccessPolicies/write | 새 Just-In-Time 네트워크 액세스 정책을 만들거나 기존 Just-In-Time 네트워크 액세스 정책을 업데이트합니다. |
> | 실행력 | Microsoft.Security/locations/read | 보안 데이터 위치를 가져옵니다. |
> | 실행력 | Microsoft.Security/locations/tasks/activate/action | 보안 권장 사항을 활성화합니다. |
> | 실행력 | Microsoft.Security/locations/tasks/dismiss/action | 보안 권장 사항을 해제합니다. |
> | 실행력 | Microsoft.Security/locations/tasks/read | 모든 사용 가능한 보안 권장 사항을 가져옵니다. |
> | 실행력 | Microsoft.Security/locations/tasks/resolve/action | 보안 권장 사항을 해결합니다. |
> | 실행력 | Microsoft.Security/locations/tasks/start/action | 보안 권장 사항을 시작합니다. |
> | 실행력 | Microsoft.Security/policies/read | 보안 규칙을 가져옵니다. |
> | 실행력 | Microsoft.Security/policies/write | 보안 규칙을 업데이트합니다. |
> | 실행력 | Microsoft.Security/pricings/delete | 범위에 대한 가격 책정 설정을 삭제합니다. |
> | 실행력 | Microsoft.Security/pricings/read | 범위에 대한 가격 책정 설정을 가져옵니다. |
> | 실행력 | Microsoft.Security/pricings/write | 범위에 대한 가격 책정 설정을 업데이트합니다. |
> | 실행력 | Microsoft.Security/register/action | Azure Security Center에 대한 구독을 등록합니다. |
> | 실행력 | Microsoft.Security/securityContacts/delete | 보안 연락처를 삭제합니다. |
> | 실행력 | Microsoft.Security/securityContacts/read | 보안 연락처를 가져옵니다. |
> | 실행력 | Microsoft.Security/securityContacts/write | 보안 연락처를 업데이트합니다. |
> | 실행력 | Microsoft.Security/securitySolutions/delete | 보안 솔루션을 삭제합니다. |
> | 실행력 | Microsoft.Security/securitySolutions/read | 보안 솔루션을 가져옵니다. |
> | 실행력 | Microsoft.Security/securitySolutions/write | 새 보안 솔루션을 만들거나 기존 보안 솔루션을 업데이트합니다. |
> | 실행력 | Microsoft.Security/securitySolutionsReferenceData/read | 보안 솔루션 참조 데이터를 가져옵니다. |
> | 실행력 | Microsoft.Security/securityStatuses/read | Azure 리소스의 보안 상태를 가져옵니다. |
> | 실행력 | Microsoft.Security/securityStatusesSummaries/read | 범위에 대한 보안 상태 요약 정보를 가져옵니다. |
> | 실행력 | Microsoft.Security/settings/read | 범위에 대한 설정을 가져옵니다. |
> | 실행력 | Microsoft.Security/settings/write | 범위에 대한 설정을 업데이트합니다. |
> | 실행력 | Microsoft.Security/tasks/read | 모든 사용 가능한 보안 권장 사항을 가져옵니다. |
> | 실행력 | Microsoft.Security/unregister/action | Azure Security Center에서 구독을 등록 취소합니다. |
> | 실행력 | Microsoft.Security/webApplicationFirewalls/delete | 웹 애플리케이션 방화벽을 삭제합니다. |
> | 실행력 | Microsoft.Security/webApplicationFirewalls/read | 웹 애플리케이션 방화벽을 가져옵니다. |
> | 실행력 | Microsoft.Security/webApplicationFirewalls/write | 새 웹 애플리케이션 방화벽을 만들거나 기존 웹 애플리케이션 방화벽을 업데이트합니다. |
> | 실행력 | Microsoft.Security/workspaceSettings/connect/action | 작업 영역 설정 재연결 설정을 변경합니다. |
> | 실행력 | Microsoft.Security/workspaceSettings/delete | 작업 영역 설정을 삭제합니다. |
> | 실행력 | Microsoft.Security/workspaceSettings/read | 작업 영역 설정을 가져옵니다. |
> | 실행력 | Microsoft.Security/workspaceSettings/write | 작업 영역 설정을 업데이트합니다. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.SecurityGraph/diagnosticsettings/delete | 진단 설정을 삭제합니다. |
> | 실행력 | Microsoft.SecurityGraph/diagnosticsettings/read | 진단 설정을 읽습니다. |
> | 실행력 | Microsoft.SecurityGraph/diagnosticsettings/write | 진단 설정을 작성합니다. |
> | 실행력 | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 진단 설정 범주를 읽습니다. |

## <a name="microsoftsecurityinsights"></a>Microsoft SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft SecurityInsights/집계/읽기 | 집계 된 정보를 가져옵니다. |
> | 실행력 | AlertRules/작업/삭제 | 경고 규칙의 응답 작업을 삭제 합니다. |
> | 실행력 | AlertRules/작업/읽기 | 경고 규칙의 응답 작업을 가져옵니다. |
> | 실행력 | Microsoft SecurityInsights/alertRules/actions/write | 경고 규칙의 응답 작업을 업데이트 합니다. |
> | 실행력 | Microsoft SecurityInsights/alertRules/delete | 경고 규칙 삭제 |
> | 실행력 | Microsoft SecurityInsights/alertRules/read | 경고 규칙을 가져옵니다. |
> | 실행력 | Microsoft SecurityInsights/alertRules/write | 경고 규칙 업데이트 |
> | 실행력 | Microsoft SecurityInsights/책갈피/삭제 | 책갈피를 삭제 합니다. |
> | 실행력 | Microsoft SecurityInsights/책갈피/확장/작업 | 특정 확장에 의해 엔터티의 관련 엔터티를 가져옵니다. |
> | 실행력 | Microsoft SecurityInsights/책갈피/읽기 | 책갈피를 가져옵니다. |
> | 실행력 | Microsoft SecurityInsights/책갈피/쓰기 | 책갈피 업데이트 |
> | 실행력 | Microsoft SecurityInsights/사례/설명/읽기 | 사례 주석을 가져옵니다. |
> | 실행력 | Microsoft SecurityInsights/case/comments/write | 사례 주석을 만듭니다. |
> | 실행력 | Microsoft SecurityInsights/case/delete | 사례를 삭제 합니다. |
> | 실행력 | Microsoft SecurityInsights/사례/조사/읽기 | 대/소문자 조사를 가져옵니다. |
> | 실행력 | Microsoft SecurityInsights/사례/조사/쓰기 | 사례의 메타 데이터를 업데이트 합니다. |
> | 실행력 | Microsoft SecurityInsights/사례/읽기 | 대/소문자를 가져옵니다. |
> | 실행력 | Microsoft SecurityInsights/case/write | 사례를 업데이트 합니다. |
> | 실행력 | Microsoft SecurityInsights/dataConnectors/delete | 데이터 커넥터를 삭제 합니다. |
> | 실행력 | Microsoft SecurityInsights/dataConnectors/read | 데이터 커넥터를 가져옵니다. |
> | 실행력 | Microsoft SecurityInsights/dataConnectors/write | 데이터 커넥터를 업데이트 합니다. |
> | 실행력 | Microsoft SecurityInsights/register/action | Azure 센티널에 구독을 등록 합니다. |
> | 실행력 | Microsoft SecurityInsights/설정/읽기 | 설정 가져오기 |
> | 실행력 | Microsoft SecurityInsights/settings/write | 업데이트 설정 |
> | 실행력 | Microsoft SecurityInsights/등록 취소/작업 | Azure 센티널에서 구독의 등록을 취소 합니다. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ServiceBus/checkNameAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. |
> | 실행력 | Microsoft.ServiceBus/checkNamespaceAvailability/action | 지정된 구독에서 네임스페이스의 가용성을 확인합니다. 이 API는 사용되지 않으므로 CheckNameAvailability를 대신 사용하세요. |
> | 실행력 | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | 지정된 VNet에 대한 ServiceBus 리소스 공급자의 VNet 규칙을 삭제합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/authorizationRules/action | 네임스페이스 권한 부여 규칙을 업데이트합니다. 이 API는 더 이상 사용되지 않습니다. 그 대신 PUT 호출을 사용하여 네임스페이스 권한 부여 규칙을 업데이트하세요. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다.  |
> | 실행력 | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 네임스페이스 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/authorizationRules/read | 네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/authorizationRules/write | 네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/Delete | 네임스페이스 리소스를 삭제합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 재해 복구 기본 네임스페이스의 권한 부여 규칙 키를 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | 재해 복구 기본 네임스페이스의 권한 부여 규칙을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | 재해 복구를 사용하지 않도록 설정하고 기본 네임스페이스에서 보조 네임스페이스로의 변경 복제를 중지합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 지정된 구독에서 네임스페이스 별칭의 가용성을 확인합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 네임스페이스와 연결된 재해 복구 구성을 삭제합니다. 이 작업은 기본 네임스페이스를 통해서만 호출할 수 있습니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | GEO DR 장애 조치(failover)를 호출하고, 네임스페이스 별칭이 보조 네임스페이스를 가리키도록 다시 구성합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 네임스페이스와 연결된 재해 복구 구성을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 네임스페이스와 연결된 재해 복구 구성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 네임스페이스와 연결된 Event Grid 필터를 삭제합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 네임스페이스와 연결된 Event Grid 필터를 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 네임스페이스와 연결된 Event Grid 필터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/eventhubs/read | EventHub 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | IP 필터 리소스 삭제 |
> | 실행력 | Microsoft.ServiceBus/namespaces/ipFilterRules/read | IP 필터 리소스 가져오기 |
> | 실행력 | Microsoft.ServiceBus/namespaces/ipFilterRules/write | IP 필터 리소스 만들기 |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | 메시지 받기 |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | 메시지 보내기 |
> | 실행력 | Microsoft.ServiceBus/namespaces/messagingPlan/read | 네임스페이스에 대한 메시지 계획을 가져옵니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/messagingPlan/write | 네임스페이스에 대한 메시지 계획을 업데이트합니다.<br>이 API는 더 이상 사용되지 않습니다.<br>MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다.<br>이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/migrate/action | 네임스페이스 작업을 마이그레이션합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 마이그레이션 구성을 삭제합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 마이그레이션 및 보류 중인 복제 작업 상태를 나타내는 마이그레이션 구성을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | 표준을 프리미엄 네임스페이스 마이그레이션으로 되돌립니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | 표준 네임스페이스와 연결된 DNS를 마이그레이션을 완료하고 표준에서 프리미엄 네임스페이스로의 리소스 동기화를 중지하는 프리미엄 네임스페이스에 할당합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 마이그레이션 구성을 만들거나 업데이트합니다. 이 작업을 수행하면 표준에서 프리미엄 네임스페이스로의 리소스 동기화가 시작됩니다. |
> | 실행력 | ServiceBus/네임 스페이스/네트워크 규칙 집합/삭제 | VNET 규칙 리소스 삭제 |
> | 실행력 | ServiceBus/네임 스페이스/네트워크 규칙 집합/읽기 | NetworkRuleSet 집합 리소스를 가져옵니다. |
> | 실행력 | ServiceBus/네임 스페이스/네트워크 규칙 집합/쓰기 | VNET 규칙 리소스 만들기 |
> | 실행력 | Microsoft.ServiceBus/namespaces/networkrulesets/delete | VNET 규칙 리소스 삭제 |
> | 실행력 | Microsoft.ServiceBus/namespaces/networkrulesets/read | NetworkRuleSet 집합 리소스를 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/networkrulesets/write | VNET 규칙 리소스 만들기 |
> | 실행력 | Microsoft.ServiceBus/namespaces/operationresults/read | 네임스페이스 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 네임스페이스 로그 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | 큐를 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> | 실행력 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | 큐 권한 부여 규칙 삭제 작업 |
> | 실행력 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | 큐의 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  큐 권한 부여 규칙 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | 큐 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/queues/Delete | 큐 리소스 삭제 작업 |
> | 실행력 | Microsoft.ServiceBus/namespaces/queues/read | 큐 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/queues/write | 큐 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/read | 네임스페이스 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS 네임스페이스를 제거합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | 토픽을 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | 토픽 권한 부여 규칙 삭제 작업 |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | 토픽의 연결 문자열을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  토픽 권한 부여 규칙 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | 리소스에 대한 기본 또는 보조 키를 다시 생성합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | 토픽 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/Delete | 토픽 리소스 삭제 작업 |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/read | 토픽 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | TopicSubscription 리소스 삭제 작업 |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | TopicSubscription 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 규칙 리소스 삭제 작업 |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 규칙 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 규칙 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | TopicSubscription 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/topics/write | 토픽 속성을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | VNET 규칙 리소스 삭제 |
> | 실행력 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | VNET 규칙 리소스 가져오기 |
> | 실행력 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | VNET 규칙 리소스 만들기 |
> | 실행력 | Microsoft.ServiceBus/namespaces/write | 네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다. |
> | 실행력 | Microsoft.ServiceBus/operations/read | 작업을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/register/action | ServiceBus 리소스 공급자에 대한 구독을 등록하고 ServiceBus 리소스를 만들도록 설정합니다. |
> | 실행력 | Microsoft.ServiceBus/sku/read | Sku 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/sku/regions/read | SkuRegions 리소스 설명의 목록을 가져옵니다. |
> | 실행력 | Microsoft.ServiceBus/unregister/action | ServiceBus 리소스 공급자에 대한 구독을 등록하고 ServiceBus 리소스를 만들도록 설정합니다. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.ServiceFabric/clusters/applications/delete | 애플리케이션을 삭제합니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applications/read | 애플리케이션을 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applications/services/delete | 서비스를 삭제합니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | 파티션을 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | 복제본을 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applications/services/read | 서비스를 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | 서비스 상태를 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applications/services/write | 서비스를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applications/write | 애플리케이션을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applicationTypes/delete | 애플리케이션 유형을 삭제합니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applicationTypes/read | 애플리케이션 유형을 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | 애플리케이션 유형 버전을 삭제합니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | 애플리케이션 유형 버전을 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | 애플리케이션 유형 버전을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/applicationTypes/write | 애플리케이션 유형을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/delete | 클러스터를 삭제합니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/nodes/read | 노드를 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/read | 클러스터를 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/statuses/read | 클러스터 상태를 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/clusters/write | 클러스터를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.ServiceFabric/locations/clusterVersions/read | 클러스터 버전을 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 특정 환경에 대한 클러스터 버전을 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/locations/operationresults/read | 작업 결과를 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/locations/operations/read | 위치별로 작업을 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/operations/read | 사용 가능한 작업을 읽습니다. |
> | 실행력 | Microsoft.ServiceFabric/register/action | 작업을 등록합니다. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.SignalRService/locations/checknameavailability/action | 새 SignalR 서비스에서 이름을 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.SignalRService/locations/operationresults/signalr/read | 비동기 작업의 상태 쿼리 |
> | 실행력 | Microsoft.SignalRService/locations/operationStatuses/operationId/read | 비동기 작업의 상태 쿼리 |
> | 실행력 | Microsoft.SignalRService/locations/usages/read | Azure SignalR 서비스에 대한 할당량 사용량을 가져옵니다. |
> | 실행력 | Microsoft.SignalRService/operationresults/read | 비동기 작업의 상태 쿼리 |
> | 실행력 | SignalRService/작업/읽기 | Azure SignalR service에 대 한 작업을 나열 합니다. |
> | 실행력 | Microsoft.SignalRService/operationstatus/read | 비동기 작업의 상태 쿼리 |
> | 실행력 | Microsoft.SignalRService/register/action | ‘Microsoft.SignalRService’ 리소스 공급자를 구독에 등록합니다. |
> | 실행력 | Microsoft.SignalRService/SignalR/delete | 전체 SignalR 서비스를 삭제합니다. |
> | 실행력 | Microsoft.SignalRService/SignalR/eventGridFilters/delete | SignalR에서 event grid 필터를 삭제 합니다. |
> | 실행력 | Microsoft.SignalRService/SignalR/eventGridFilters/read | 지정 된 event grid 필터의 속성을 가져오거나 지정 된 SignalR에 대 한 모든 event grid 필터를 나열 합니다. |
> | 실행력 | Microsoft.SignalRService/SignalR/eventGridFilters/write | 지정 된 매개 변수를 사용 하 여 SignalR에 대 한 event grid 필터를 만들거나 업데이트 합니다. |
> | 실행력 | Microsoft.SignalRService/SignalR/listkeys/action | 관리 포털에서 또는 API를 통해 SignalR 액세스 키 값을 봅니다. |
> | 실행력 | SignalRService/SignalR/privateEndpointConnectionProxies/delete | 개인 엔드포인트 연결 프록시를 삭제 합니다. |
> | 실행력 | SignalRService/SignalR/privateEndpointConnectionProxies/read | 개인 엔드포인트 연결 프록시 읽기 |
> | 실행력 | SignalRService/SignalR/privateEndpointConnectionProxies/validate/action | 개인 엔드포인트 연결 프록시 유효성 검사 |
> | 실행력 | SignalRService/SignalR/privateEndpointConnectionProxies/write | 개인 엔드포인트 연결 프록시 만들기 |
> | 실행력 | SignalRService/SignalR/privateEndpointConnections/read | 개인 엔드포인트 연결 읽기 |
> | 실행력 | SignalRService/SignalR/privateEndpointConnections/write | 개인 엔드포인트 연결 승인 또는 거부 |
> | 실행력 | SignalRService/SignalR/privateLinkResources/read | 모든 SignalR 개인 링크 리소스 나열 |
> | 실행력 | Microsoft.SignalRService/SignalR/read | 관리 포털 또는 API를 통해 SignalR의 설정과 구성을 확인합니다. |
> | 실행력 | Microsoft.SignalRService/SignalR/regeneratekey/action | 관리 포털에서 또는 API를 통해 SignalR 액세스 키 값을 변경합니다. |
> | 실행력 | Microsoft.SignalRService/SignalR/restart/action | 관리 포털 또는 API를 통해 Azure SignalR 서비스를 다시 시작합니다. 가동 중지 시간이 있을 것입니다. |
> | 실행력 | Microsoft.SignalRService/SignalR/write | 관리 포털 또는 API를 통해 SignalR의 설정과 구성을 수정합니다. |
> | 실행력 | Microsoft.SignalRService/unregister/action | ‘Microsoft.SignalRService’ 리소스 공급자를 구독에서 등록 취소합니다. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | 응용 프로그램 정의의 응용 프로그램 아티팩트를 나열 합니다. |
> | 실행력 | Microsoft.Solutions/applicationDefinitions/delete | 애플리케이션 정의를 제거합니다. |
> | 실행력 | Microsoft.Solutions/applicationDefinitions/read | 애플리케이션 정의 목록을 검색합니다. |
> | 실행력 | Microsoft.Solutions/applicationDefinitions/write | 애플리케이션 정의를 추가 또는 수정합니다. |
> | 실행력 | Microsoft.Solutions/applications/applicationArtifacts/read | 응용 프로그램 아티팩트를 나열 합니다. |
> | 실행력 | Microsoft.Solutions/applications/delete | 애플리케이션을 제거합니다. |
> | 실행력 | Microsoft.Solutions/applications/read | 애플리케이션 목록을 검색합니다. |
> | 실행력 | Microsoft.Solutions/applications/refreshPermissions/action | 응용 프로그램 사용 권한을 새로 고칩니다. |
> | 실행력 | Microsoft.Solutions/applications/updateAccess/action | 응용 프로그램 액세스를 업데이트 합니다. |
> | 실행력 | Microsoft.Solutions/applications/write | 애플리케이션을 만듭니다. |
> | 실행력 | Microsoft.Solutions/jitRequests/delete | JitRequest를 제거합니다. |
> | 실행력 | Microsoft.Solutions/jitRequests/read | JitRequests 목록을 검색합니다. |
> | 실행력 | Microsoft.Solutions/jitRequests/write | JitRequest를 만듭니다. |
> | 실행력 | Microsoft.Solutions/locations/operationStatuses/read | 리소스의 작업 상태를 읽습니다. |
> | 실행력 | Microsoft. 솔루션/작업/읽기 | 작업 목록을 가져옵니다. |
> | 실행력 | Microsoft.Solutions/register/action | 솔루션에 등록합니다. |
> | 실행력 | Microsoft.Solutions/unregister/action | 솔루션에서 등록을 취소 합니다. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Sql/checkNameAvailability/action | 지정된 서버 이름을 지정된 구독에 대해 전 세계적으로 프로비전에 사용할 수 있는지 확인합니다. |
> | 실행력 | Microsoft.Sql/instancePools/delete | 인스턴스 풀을 삭제합니다. |
> | 실행력 | Microsoft.Sql/instancePools/read | 인스턴스 풀을 가져옵니다. |
> | 실행력 | Microsoft.Sql/instancePools/usages/read | 인스턴스 풀의 사용 정보를 가져옵니다. |
> | 실행력 | Microsoft.Sql/instancePools/write | 인스턴스 풀을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 확장된 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/locations/auditingSettingsOperationResults/read | 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/locations/capabilities/read | 지정된 위치에서 이 구독에 대한 기능을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | 데이터베이스 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/databaseOperationResults/read | 데이터베이스 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 삭제된 서버에서 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/deletedServerOperationResults/read | 삭제된 서버에서 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/deletedServers/read | 삭제된 서버 목록을 반환하거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/deletedServers/recover/action | 삭제된 서버를 복구합니다. |
> | 실행력 | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | 탄력적 풀 비동기 작업에 대한 azure 비동기 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/elasticPoolOperationResults/read | 탄력적 풀 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | 투명 한 데이터 암호화 암호화 보호기에서 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | 투명 한 데이터 암호화 암호화 보호기에서 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 확장된 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 확장된 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | 방화벽 규칙 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/firewallRulesOperationResults/read | 방화벽 규칙 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/instanceFailoverGroups/delete | 기존 인스턴스 장애 조치(failover) 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 기존 인스턴스 장애 조치(failover) 그룹에서 계획된 장애 조치(failover)를 실행합니다. |
> | 실행력 | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 기존 인스턴스 장애 조치(failover) 그룹에서 강제 장애 조치(failover)를 실행합니다. |
> | 실행력 | Microsoft.Sql/locations/instanceFailoverGroups/read | 인스턴스 장애 조치(failover) 그룹 목록을 반환하거나 지정된 인스턴스 장애 조치(failover) 그룹의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/instanceFailoverGroups/write | 지정한 매개 변수를 사용 하 여 인스턴스 장애 조치 (failover) 그룹을 만들거나 지정 된 인스턴스 장애 조치 (failover) 그룹의 속성 또는 태그를 업데이트 합니다 |
> | 실행력 | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | 인스턴스 풀 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/instancePoolOperationResults/read | 인스턴스 풀 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | 특정 인터페이스 엔드포인트 Azure 비동기 작업의 세부 정보를 반환합니다. |
> | 실행력 | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | 지정된 인터페이스 엔드포인트 프로필 작업의 세부 정보를 반환합니다. |
> | 실행력 | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | 작업 에이전트 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/jobAgentOperationResults/read | 작업 에이전트 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/longTermRetentionBackups/read | 위치에 있는 모든 서버의 모든 데이터베이스에 대한 장기 보존 백업을 나열합니다. |
> | 실행력 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 서버의 모든 데이터베이스에 대한 장기 보존 백업을 나열합니다. |
> | 실행력 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 장기 보존 백업을 삭제합니다. |
> | 실행력 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 데이터베이스의 장기 보존 백업을 나열합니다. |
> | 실행력 | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 관리되는 데이터베이스 복원 작업을 완료합니다. |
> | 실행력 | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | 투명 한 데이터 암호화 관리 되는 인스턴스 암호화 보호기에서 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | 투명 한 데이터 암호화 관리 되는 인스턴스 암호화 보호기에서 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | 투명 한 데이터 암호화 관리 되는 인스턴스 키에서 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | 투명 한 데이터 암호화 관리 되는 인스턴스 키에서 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft .Sql/위치/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/읽기 | 관리 되는 데이터베이스에 대 한 장기 보존 정책 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft .Sql/위치/managedInstanceLongTermRetentionPolicyOperationResults/읽기 | 관리 되는 데이터베이스에 대 한 장기 보존 정책 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft .Sql/위치/managedShortTermRetentionPolicyOperationResults/읽기 | 단기 보존 정책 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 관리되는 데이터베이스 투명한 데이터 암호화에 대해 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 관리되는 데이터베이스 투명한 데이터 암호화에 대해 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | 개인 엔드포인트 연결 작업에 대 한 결과를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | 개인 엔드포인트 연결 작업에 대 한 결과를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | 개인 엔드포인트 연결 프록시 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | 개인 엔드포인트 연결 프록시 작업의 결과를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/read | 지정된 구독에 사용 가능한 위치를 가져옵니다. |
> | 실행력 | Microsoft .Sql/위치/serverAdministratorAzureAsyncOperation/읽기 | 서버 Azure Active Directory 관리자의 비동기 작업 결과 |
> | 실행력 | Microsoft .Sql/위치/Server관리자 Operationresults/read | 서버 Azure Active Directory 관리자 작업 결과 |
> | 실행력 | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | 투명 한 데이터 암호화 서버 키에 대 한 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/serverKeyOperationResults/read | 투명 한 데이터 암호화 서버 키에 대 한 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft .Sql/위치/shortTermRetentionPolicyOperationResults/읽기 | 단기 보존 정책 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/syncAgentOperationResults/read | 동기화 에이전트 리소스 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/locations/syncDatabaseIds/read | 특정 지역 및 구독에 대한 동기화 데이터베이스 id를 검색합니다. |
> | 실행력 | Microsoft.Sql/locations/syncGroupOperationResults/read | 동기화 그룹 리소스 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/locations/syncMemberOperationResults/read | 동기화 멤버 리소스 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/locations/usages/read | 한 위치에서 이 구독의 사용량 메트릭 컬렉션을 가져옵니다. |
> | 실행력 | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 지정된 가상 네트워크 규칙 azure 비동기 작업의 세부 정보를 반환합니다.  |
> | 실행력 | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 지정된 가상 네트워크 규칙 작업의 세부 정보를 반환합니다.  |
> | 실행력 | Microsoft.Sql/managedInstances/administrators/delete | 관리되는 인스턴스의 기존 관리자를 삭제합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/administrators/read | 관리되는 인스턴스 관리자 목록을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/administrators/write | 지정된 매개 변수를 사용하여 관리되는 인스턴스 관리자를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft .Sql/managedInstances/databases/backupLongTermRetentionPolicies/read | 관리 되는 데이터베이스에 대 한 장기 보존 정책을 가져옵니다. |
> | 실행력 | Microsoft .Sql/managedInstances/databases/backupLongTermRetentionPolicies/write | 관리 되는 데이터베이스에 대 한 장기 보존 정책을 업데이트 합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | 관리되는 데이터베이스에 대한 단기 보존 정책을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | 관리되는 데이터베이스에 대한 단기 보존 정책을 업데이트합니다. |
> | 실행력 | Microsoft .Sql/managedInstances/데이터베이스/열/읽기 | 관리 되는 데이터베이스에 대 한 열 목록 반환 |
> | 실행력 | Microsoft .Sql/managedInstances/databases/completeRestore/action | 관리되는 데이터베이스 복원 작업을 완료합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | 일괄 업데이트 민감도 레이블 |
> | 실행력 | Microsoft.Sql/managedInstances/databases/delete | 기존 관리되는 데이터베이스를 삭제합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | 관리형 인스턴스 데이터베이스에 사용 가능한 로그를 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/read | 기존 관리되는 데이터베이스를 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 실행력 | Microsoft .Sql/managedInstances/databases/recommendedSensitivityLabels/write | Batch 업데이트 권장 민감도 레이블 |
> | 실행력 | Microsoft .Sql/managedInstances/databases/restoreDetails/read | 복원이 진행 되는 동안 관리 되는 데이터베이스 복원 세부 정보를 반환 합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/schemas/read | 관리 되는 데이터베이스 스키마를 가져옵니다. (스키마 전용) |
> | 실행력 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | 관리 되는 데이터베이스 열 가져오기 (스키마 전용) |
> | 실행력 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | 지정된 열의 민감도 레이블을 삭제합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | 지정 된 열에서 민감도 권장 사항을 사용 하지 않도록 설정 |
> | 실행력 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | 지정 된 열에 대 한 민감도 권장 사항 사용 |
> | 실행력 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | 지정된 열의 민감도 레이블을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | 지정된 열의 민감도 레이블을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/schemas/tables/read | 관리 되는 데이터베이스 테이블 가져오기 (스키마 전용) |
> | 실행력 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 지정 된 서버에 대해 구성 된 관리 되는 데이터베이스 위협 검색 정책의 목록을 검색 합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 지정된 관리되는 데이터베이스에 대한 데이터베이스 위협 검색 정책을 변경합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/securityEvents/read | 관리되는 데이터베이스 보안 이벤트를 검색합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 지정된 관리되는 데이터베이스에서 데이터베이스 투명한 데이터 암호화의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 지정된 관리되는 데이터베이스에 대한 데이터베이스 투명한 데이터 암호화를 변경합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 지정된 데이터베이스에 대한 취약성 평가를 제거합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | givendatabase에서 취약성 평가 정책을 검색합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 제거합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 변경합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 기존 검사 결과를 사람이 읽을 수 있는 형식으로 변환합니다. 이미 있는 경우 아무 일도 발생하지 않습니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | 취약점 평가 데이터베이스 검사를 실행합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | 데이터베이스 취약성 평가 검사 레코드 목록을 반환하거나 지정된 검사 ID에 대한 검사 레코드를 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 지정된 데이터베이스에 대한 취약성 평가를 변경합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/databases/write | 새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/delete | 기존 관리되는 인스턴스를 삭제합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/encryptionProtector/read | 서버 암호화 보호기 목록을 반환하거나 지정된 서버 암호화 보호기에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft .Sql/managedInstances/Cr프로텍터/유효성 재검사/작업 | 지정된 서버 암호화 보호기에 대한 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/encryptionProtector/write | 지정된 서버 암호화 보호기에 대한 속성을 업데이트합니다. |
> | 실행력 | Microsoft .Sql/managedInstances/inaccessibleManagedDatabases/read | 관리 되는 인스턴스에서 액세스할 수 없는 관리 되는 데이터베이스 목록을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/keys/delete | 기존 Azure SQL Managed Instance 키를 삭제합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/keys/read | 관리되는 인스턴스 키 목록을 반환하거나 지정된 관리되는 인스턴스 키의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/keys/write | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 관리되는 인스턴스 키의 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/metricDefinitions/read | 관리되는 인스턴스 메트릭 정의 가져오기 |
> | 실행력 | Microsoft.Sql/managedInstances/metrics/read | 관리되는 인스턴스 메트릭 가져오기 |
> | 실행력 | Microsoft .Sql/managedInstances/작업/취소/동작 | 아직 완료 되지 않은 Azure SQL Managed Instance 보류 중인 비동기 작업을 취소 합니다. |
> | 실행력 | Microsoft .Sql/managedInstances/작업/읽기 | 관리 되는 인스턴스 작업 가져오기 |
> | 실행력 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | 관리형 인스턴스에 사용 가능한 로그를 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | 관리되는 인스턴스에 사용 가능한 메트릭 유형을 반환합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/read | 관리되는 인스턴스 목록을 반환하거나 지정된 관리되는 인스턴스에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/recoverableDatabases/read | 복구 가능한 관리되는 데이터베이스 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | 삭제된 관리되는 데이터베이스에 대한 단기 보존 정책을 가져옵니다. |
> | 실행력 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | 삭제된 관리되는 데이터베이스에 대한 단기 보존 정책을 업데이트합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | 복원 가능한 삭제된 관리되는 데이터베이스 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 지정 된 서버에 대해 구성 된 관리 되는 서버 위협 검색 정책 목록을 검색 합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 지정된 관리되는 서버에 대한 관리되는 서버 위협 검색 정책을 변경합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/tdeCertificates/action | TDE 인증서를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | 주어진 관리되는 인스턴스에 대한 취약성 평가를 제거합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | 지정된 관리되는 인스턴스에서 취약성 평가 정책을 검색합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | 주어진 관리되는 인스턴스에 대한 취약성 평가를 변경합니다. |
> | 실행력 | Microsoft.Sql/managedInstances/write | 지정된 매개 변수를 사용하여 관리되는 인스턴스를 만들거나 지정된 관리되는 인스턴스에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/operations/read | 사용 가능한 REST 작업을 가져옵니다. |
> | 실행력 | Microsoft .Sql/privateEndpointConnectionsApproval/action | 사용자가 개인 엔드포인트 연결을 승인할 수 있는지 여부를 결정 합니다. |
> | 실행력 | Microsoft.Sql/register/action | Microsoft SQL Database 리소스 공급자에 대한 구독을 등록하고 Microsoft SQL Databases를 만들도록 설정합니다. |
> | 실행력 | Microsoft.Sql/servers/administratorOperationResults/read | 서버 관리자에 대해 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/administrators/delete | 특정 Azure Active Directory 관리자 개체를 삭제 합니다. |
> | 실행력 | Microsoft.Sql/servers/administrators/read | 특정 Azure Active Directory 관리자 개체를 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/administrators/write | 특정 Azure Active Directory 관리자 개체를 추가 하거나 업데이트 합니다. |
> | 실행력 | Microsoft.Sql/servers/advisors/read | 서버에 사용할 수 있는 Advisor의 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/advisors/recommendedActions/read | 서버에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/advisors/recommendedActions/write | 서버에 권장 작업을 적용합니다. |
> | 실행력 | Microsoft.Sql/servers/advisors/write | 서버 수준에서 Advisor의 자동 실행 상태를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/auditingPolicies/read | 지정된 서버에 구성된 기본 서버 테이블 감사 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/auditingPolicies/write | 지정된 서버에 대한 기본 서버 테이블 감사를 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/auditingSettings/operationResults/read | 서버 blob 감사 정책 집합 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/auditingSettings/read | 지정된 서버에 구성된 서버 Blob 감사 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/auditingSettings/write | 지정된 서버에 대한 서버 Blob 감사를 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/automaticTuning/read | 서버에 대한 자동 조정 설정을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/automaticTuning/write | 서버에 대한 자동 조정 설정을 업데이트하고 업데이트된 설정을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | 기존 백업 보관 자격 증명 모음을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | 이 작업은 백업 장기 보존 자격 증명 모음을 가져오는 데 사용됩니다. 서버에 등록된 자격 증명 모음에 대한 정보를 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | 이 작업은 백업 장기 보존 자격 증명 모음을 서버에 등록하는 데 사용됩니다. |
> | 실행력 | Microsoft.Sql/servers/communicationLinks/delete | 기존 서버 통신 연결을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/communicationLinks/read | 지정된 서버의 통신 연결 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/communicationLinks/write | 서버 통신 연결을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/connectionPolicies/read | 지정된 서버의 서버 연결 정책 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/connectionPolicies/write | 서버 연결 정책을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/advisors/read | 데이터베이스에 사용할 수 있는 Advisor의 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | 데이터베이스에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | 데이터베이스에 권장 작업을 적용합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/advisors/write | 데이터베이스 수준에서 Advisor의 자동 실행 상태를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/auditingPolicies/read | 지정된 데이터베이스에 구성된 테이블 감사 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/auditingPolicies/write | 지정된 데이터베이스에 대한 테이블 감사 정책을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/auditingSettings/read | 지정된 데이터베이스에 구성된 Blob 감사 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/auditingSettings/write | 지정된 데이터베이스에 대한 Blob 감사 정책을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/auditRecords/read | 데이터베이스 blob 감사 레코드를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/automaticTuning/read | 데이터베이스에 대한 자동 조정 설정을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/automaticTuning/write | 데이터베이스에 대한 자동 조정 설정을 업데이트하고 업데이트된 설정을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/azureAsyncOperation/read | 데이터베이스 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 지정된 데이터베이스의 백업 보관 정책 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | 데이터베이스 백업 보관 정책을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | 데이터베이스에 대 한 단기 보존 정책을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | 데이터베이스에 대 한 단기 보존 정책을 업데이트 합니다. |
> | 실행력 | Microsoft .Sql/servers/databases/columns/read | 데이터베이스에 대 한 열 목록 반환 |
> | 실행력 | Microsoft.Sql/servers/databases/connectionPolicies/read | 지정된 데이터베이스에 구성된 연결 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/connectionPolicies/write | 지정된 데이터베이스에 대한 연결 정책을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | 일괄 업데이트 민감도 레이블 |
> | 실행력 | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | 데이터베이스 데이터 마스킹 정책 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 지정된 데이터베이스에 대한 데이터 마스킹 정책 규칙을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 지정된 데이터베이스에 구성된 데이터 마스킹 정책 규칙의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 지정된 데이터베이스에 대한 데이터 마스킹 정책 규칙을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 지정된 데이터베이스에 대한 데이터 마스킹 정책을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 선택한 단계 ID에 대한 데이터 웨어하우스 쿼리의 분산 쿼리 단계 정보를 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 선택한 쿼리 ID에 대한 데이터 웨어하우스 배포 쿼리 정보를 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 실행 중인 쿼리와 일시 중단된 쿼리를 포함하고 있는 SQL Data Warehouse 인스턴스의 사용자 작업을 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/delete | 기존 데이터베이스를 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/export/action | Azure SQL Database를 내보냅니다. |
> | 실행력 | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 지정된 데이터베이스에 구성된 확장 Blob 감사 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 지정된 데이터베이스에 대한 확장 Blob 감사 정책을 변경합니다. |
> | 실행력 | Microsoft .Sql/servers/databases/extensions/importExtensionOperationResults/read | 진행 중인 가져오기 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/extensions/read | 데이터베이스에 대한 확장 컬렉션을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/extensions/write | 지정된 데이터베이스에 대한 확장을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/failover/action | 고객이 데이터베이스 장애 조치를 시작 했습니다. |
> | 실행력 | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 지정된 데이터베이스에 대한 지역 백업 정책을 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/geoBackupPolicies/write | 데이터베이스 지역 백업 정책을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/importExportOperationResults/read | 진행 중인 가져오기/내보내기 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 선택한 데이터베이스에 사용 가능한 유지 관리 기간 목록을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/maintenanceWindows/read | 선택한 데이터베이스에 대한 유지 관리 기간 설정을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/maintenanceWindows/write | 선택한 데이터베이스에 대한 유지 관리 기간 설정을 지정합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/metrics/read | 데이터베이스에 대한 메트릭을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/move/action | 기존 데이터베이스의 이름을 변경 합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/operationResults/read | 데이터베이스 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/operations/cancel/action | 아직 완료되지 않은 Azure SQL Database 보류 중인 비동기 작업을 취소합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/operations/read | 데이터베이스에서 수행된 작업 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/pause/action | Azure SQL Datawarehouse 데이터베이스를 일시 중지합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | 데이터베이스에 대해 사용 가능한 로그를 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | 데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 지정된 매개 변수에 해당하는 쿼리 텍스트 컬렉션을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/queryStore/read | 데이터베이스에 대한 쿼리 저장소 설정의 현재 값을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/queryStore/write | 데이터베이스에 대한 쿼리 저장소 설정을 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/read | 데이터베이스 목록을 가져오거나 지정된 데이터베이스에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 실행력 | Microsoft .Sql/servers/databases/recommendedSensitivityLabels/write | Batch 업데이트 권장 민감도 레이블 |
> | 실행력 | Microsoft.Sql/servers/databases/replicationLinks/delete | 잠재적 데이터 손실이 발생하는 상태로 강제로 복제 관계를 종료합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/replicationLinks/failover/action | 주 데이터베이스의 모든 변경 내용을 동기화한 후에 장애 조치(failover)하고, 이 데이터베이스를 복제 관계의 주 데이터베이스로 만들고, 원격 주 데이터베이스를 보조 데이터베이스로 만듭니다. |
> | 실행력 | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 잠재적 데이터 손실이 발생하는 상태로 즉시 장애 조치(failover)하고, 이 데이터베이스를 복제 관계의 주 데이터베이스로 만들고, 원격 주 데이터베이스를 보조 데이터베이스로 만듭니다. |
> | 실행력 | Microsoft.Sql/servers/databases/replicationLinks/read | 복제 링크 목록을 반환하거나 지정된 복제 링크의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | 강제로 또는 파트너와 동기화한 후에 복제 관계를 종료합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | 연결에 대한 복제 모드를 동기 또는 비동기 모드로 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/restorePoints/action | 새 복원 지점을 만듭니다. |
> | 실행력 | Microsoft.Sql/servers/databases/restorePoints/delete | 데이터베이스에 대한 복원 지점을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/restorePoints/read | 데이터베이스의 복원 지점을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/resume/action | Azure SQL Datawarehouse 데이터베이스를 다시 시작합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/read | 데이터베이스 스키마를 가져옵니다 (스키마에만 해당). |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 데이터베이스 열을 가져옵니다 (스키마에만 해당). |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 지정된 열의 민감도 레이블을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | 지정 된 열에서 민감도 권장 사항을 사용 하지 않도록 설정 |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | 지정 된 열에 대 한 민감도 권장 사항 사용 |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 지정된 열의 민감도 레이블을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 지정된 열의 민감도 레이블을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/tables/read | 데이터베이스 테이블을 가져옵니다 (스키마에만 해당). |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | 데이터베이스에 대한 인덱스 권장 사항 목록을 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | 인덱스 권장 사항을 적용합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 지정 된 서버에 대해 구성 된 데이터베이스 위협 검색 정책 목록을 검색 합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 지정된 데이터베이스에 대한 데이터베이스 위협 탐지 정책을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/securityMetrics/read | 데이터베이스 보안 메트릭 컬렉션을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/sensitivityLabels/read | 지정된 데이터베이스의 민감도 레이블을 나열합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | 성능을 향상시키거나 비용을 절감하기 위해 쿼리 실행 통계를 기준으로 데이터베이스를 확장하거나 축소하기 위한 제안 사항을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/skus/read | 데이터베이스에 사용 가능한 SKU 컬렉션을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 동기화 그룹 동기화를 취소합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/delete | 기존 동기화 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 동기화 허브 데이터베이스 스키마 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/logs/read | 동기화 그룹 로그 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/read | 동기화 그룹 목록을 가져오거나 지정된 동기화 그룹에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 동기화 허브 데이터베이스 스키마를 새로 고칩니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 동기화 허브 스키마 새로 고침 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 기존 동기화 멤버를 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 동기화 멤버 목록을 가져오거나 지정된 동기화 멤버에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 동기화 멤버 스키마를 새로 고칩니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 동기화 멤버 스키마 새로 고침 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 동기화 멤버 데이터베이스 스키마 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 지정된 매개 변수를 사용하여 동기화 멤버를 만들거나 지정된 동기화 멤버에 대한 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 동기화 그룹 동기화를 트리거합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/syncGroups/write | 지정된 매개 변수를 사용하여 동기화 그룹을 만들거나 지정된 동기화 그룹에 대한 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/topQueries/queryText/action | 선택한 쿼리 ID에 대한 Transact-SQL 텍스트를 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/topQueries/read | 선택한 기간에 선택한 쿼리에 대해 집계된 런타임 통계를 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/topQueries/statistics/read | 선택한 기간에 선택한 쿼리에 대해 집계된 런타임 통계를 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | 투명한 데이터 암호화에 대해 진행 중인 작업을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 지정된 데이터베이스에 대한 투명한 데이터 암호화 보안 기능의 상태 및 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/transparentDataEncryption/write | 투명한 데이터 암호화 상태를 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Azure SQL Datawarehouse 데이터베이스를 업그레이드합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/usages/read | Azure SQL Database 사용 정보를 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 지정된 데이터베이스에 대한 취약성 평가를 제거합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | givendatabase에서 취약성 평가 정책을 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 제거합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 기존 검사 결과를 사람이 읽을 수 있는 형식으로 변환합니다. 이미 있는 경우 아무 일도 발생하지 않습니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | 취약점 평가 데이터베이스 검사를 실행합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | 데이터베이스 취약성 평가 검사 레코드 목록을 반환하거나 지정된 검사 ID에 대한 검사 레코드를 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 지정된 데이터베이스에 대한 취약성 평가를 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | 취약점 평가 데이터베이스 검사를 실행합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | 데이터베이스 취약성 평가 검사 실행 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 지정된 데이터베이스에 구성된 취약성 평가 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 지정된 데이터베이스에 대한 취약성 평가를 변경합니다. |
> | 실행력 | Microsoft .Sql/servers/databases/workloadGroups/delete | 특정 작업 그룹을 삭제 합니다. |
> | 실행력 | Microsoft .Sql/servers/databases/workloadGroups/read | 선택한 데이터베이스에 대 한 작업 그룹을 나열 합니다. |
> | 실행력 | Microsoft .Sql/servers/databases/workloadGroups/workloadClassifiers/delete | 특정 작업 분류자를 삭제 합니다. |
> | 실행력 | Microsoft .Sql/servers/databases/workloadGroups/workloadClassifiers/read | 선택한 데이터베이스에 대 한 작업 분류자를 나열 합니다. |
> | 실행력 | Microsoft .Sql/servers/databases/workloadGroups/workloadClassifiers/write | 특정 작업 분류자의 속성을 설정 합니다. |
> | 실행력 | Microsoft .Sql/servers/databases/workloadGroups/write | 특정 작업 그룹의 속성을 설정 합니다. |
> | 실행력 | Microsoft.Sql/servers/databases/write | 지정된 매개 변수를 사용하여 데이터베이스를 만들거나 지정된 데이터베이스에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/delete | 기존 서버를 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 지정된 서버에 대한 기존 재해 복구 구성을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | DisasterRecoveryConfiguration을 장애 조치(failover)합니다. |
> | 실행력 | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | DisasterRecoveryConfiguration을 강제 장애 조치(failover)합니다. |
> | 실행력 | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | 이 서버를 포함하는 재해 복구 구성 컬렉션을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | 서버 재해 복구 구성을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPoolEstimates/read | 이 서버에 대해 이미 작성된 탄력적 풀 예상치의 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPoolEstimates/write | 제공된 데이터베이스 목록에 대한 새 탄력적 풀 예상치를 만듭니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/advisors/read | 탄력적 풀에 사용할 수 있는 Advisor의 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | 탄력적 풀에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | 탄력적 풀에 권장 작업을 적용합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/advisors/write | 탄력적 풀 수준에서 Advisor의 자동 실행 상태를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/databases/read | 탄력적 풀에 대한 데이터베이스 목록을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/delete | 기존 탄력적 풀을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 지정된 Elastic Database 풀에 대한 활동 및 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Elastic Database 풀에 속하는 지정된 데이터베이스에 대한 활동 및 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/failover/action | 고객이 탄력적 풀 장애 조치 (failover)를 시작 했습니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Elastic Database 풀에 사용할 수 있는 메트릭 형식을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/metrics/read | 탄력적 데이터베이스 풀에 대한 메트릭을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/operations/cancel/action | 아직 완료되지 않은 Azure SQL 탄력적 풀 보류 중인 비동기 작업을 취소합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/operations/read | 탄력적 풀에서 수행된 작업 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | 리소스에 대한 진단 설정을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | 리소스에 대한 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Elastic Database 풀에 사용할 수 있는 메트릭 형식을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/read | 지정된 서버에서 탄력적 풀의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/skus/read | 탄력적 풀에 사용 가능한 SKU 컬렉션을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/elasticPools/write | 탄력적 풀을 새로 만들거나 기존 탄력적 풀의 속성을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/encryptionProtector/read | 서버 암호화 보호기 목록을 반환하거나 지정된 서버 암호화 보호기에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft .Sql/servers///또는 유효성 재검사/작업 | 지정된 서버 암호화 보호기에 대한 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/encryptionProtector/write | 지정된 서버 암호화 보호기에 대한 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/extendedAuditingSettings/read | 지정된 서버에 구성된 확장 서버 Blob 감사 정책의 세부 정보를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/extendedAuditingSettings/write | 지정된 서버에 대한 확장 서버 Blob 감사를 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/failoverGroups/delete | 기존 장애 조치(failover) 그룹을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/failoverGroups/failover/action | 기존 장애 조치(failover) 그룹에서 계획된 장애 조치(failover)를 실행합니다. |
> | 실행력 | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 기존 장애 조치(failover) 그룹에서 강제 장애 조치(failover)를 실행합니다. |
> | 실행력 | Microsoft.Sql/servers/failoverGroups/read | 장애 조치(failover) 그룹 목록을 가져오거나 지정된 장애 조치(failover) 그룹에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/failoverGroups/write | 지정된 매개 변수를 사용하여 장애 조치(failover) 그룹을 만들거나 지정된 장애 조치(failover) 그룹에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/firewallRules/delete | 기존 서버 방화벽 규칙을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/firewallRules/read | 서버 방화벽 규칙 목록을 반환하거나 지정된 서버 방화벽 규칙에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/firewallRules/write | 지정된 매개 변수를 사용하여 서버 방화벽 규칙을 만들고 지정된 규칙에 대한 속성을 업데이트하거나 모든 기존 규칙을 새로운 서버 방화벽 규칙으로 덮어씁니다. |
> | 실행력 | Microsoft.Sql/servers/import/action | 서버에서 새 데이터베이스를 만들고 DacPac 패키지의 스키마 및 데이터를 배포합니다. |
> | 실행력 | Microsoft.Sql/servers/importExportOperationResults/read | 진행 중인 가져오기/내보내기 작업을 가져옵니다. |
> | 실행력 | Microsoft .Sql/servers/inaccessibleDatabases/read | 논리 서버에서 액세스할 수 없는 데이터베이스 목록을 반환 합니다. |
> | 실행력 | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | 지정된 인터페이스 엔드포인트 프로필을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/interfaceEndpointProfiles/read | 지정된 인터페이스 엔드포인트 프로필의 특성을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/interfaceEndpointProfiles/write | 지정된 매개 변수를 사용하여 인터페이스 엔드포인트 프로필을 만들거나 지정된 인터페이스 엔드포인트에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/jobAgents/delete | Azure SQL DB 작업 에이전트를 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/jobAgents/read | Azure SQL DB 작업 에이전트를 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/jobAgents/write | Azure SQL DB 작업 에이전트를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/keys/delete | 기존 서버 키를 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/keys/read | 서버 키 목록을 가져오거나 지정된 서버 키에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/keys/write | 지정된 매개 변수를 사용하여 키를 만들거나 지정된 서버 키에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/operationResults/read | 진행 중인 서버 작업을 가져옵니다. |
> | 실행력 | Microsoft .Sql/servers/작업/읽기 | 서버에서 수행 된 작업 목록을 반환 합니다. |
> | 실행력 | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | 기존 개인 엔드포인트 연결 프록시를 삭제 합니다. |
> | 실행력 | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | 개인 엔드포인트 연결 프록시의 목록을 반환 하거나, 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | NRP 쪽에서 개인 엔드포인트 연결 만들기 호출의 유효성을 검사 합니다. |
> | 실행력 | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | 지정 된 매개 변수를 사용 하 여 개인 엔드포인트 연결 프록시를 만들거나 지정 된 개인 엔드포인트 연결 프록시에 대 한 속성 또는 태그를 업데이트 합니다. |
> | 실행력 | Microsoft.Sql/servers/privateEndpointConnections/delete | 기존 개인 엔드포인트 연결을 삭제 합니다. |
> | 실행력 | Microsoft.Sql/servers/privateEndpointConnections/read | 개인 엔드포인트 연결의 목록을 반환 하거나, 지정 된 개인 엔드포인트 연결에 대 한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/privateEndpointConnections/write | 기존 개인 엔드포인트 연결을 승인 하거나 거부 합니다. |
> | 실행력 | Microsoft .Sql/servers/privateEndpointConnectionsApproval/action | 사용자가 개인 엔드포인트 연결을 승인할 수 있는지 여부를 결정 합니다. |
> | 실행력 | Microsoft.Sql/servers/privateLinkResources/read | 해당 sql server에 대 한 개인 링크 리소스 가져오기 |
> | 실행력 | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | 서버에 사용할 수 있는 메트릭 형식을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 지정된 서버에 대해 권장되는 Elastic Database 풀의 메트릭을 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/recommendedElasticPools/read | 기록 리소스 사용률에 따라 비용을 절감하거나 성능을 향상시키기 위한 Elastic Database 풀 권장 사항을 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/recoverableDatabases/read | 이 작업은 라이브 데이터베이스의 재해 복구 기능이 데이터베이스를 마지막 정상 지점으로 복원하는 데 사용합니다. 마지막 정상 백업에 대한 정보가 반환되지만 실제로 데이터베이스는 복원되지 않습니다. |
> | 실행력 | Microsoft.Sql/servers/replicationLinks/read | 복제 링크 목록을 반환하거나 지정된 복제 링크의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/restorableDroppedDatabases/read | 지정된 서버에서 삭제되었지만 여전히 보존 정책에 속하는 데이터베이스 목록을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | 서버 위협 검색 정책 쓰기 작업의 결과를 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/securityAlertPolicies/read | 지정 된 서버에 대해 구성 된 서버 위협 검색 정책 목록을 검색 합니다. |
> | 실행력 | Microsoft.Sql/servers/securityAlertPolicies/write | 지정된 서버에 대한 서버 위협 검색 정책을 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/serviceObjectives/read | 지정된 서버에서 사용할 수 있는 서비스 수준 목표(성능 계층이라고도 함) 목록을 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/syncAgents/delete | 기존 동기화 에이전트를 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/syncAgents/generateKey/action | 동기화 에이전트 등록 키를 생성합니다. |
> | 실행력 | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 동기화 에이전트 연결 데이터베이스 목록을 반환합니다. |
> | 실행력 | Microsoft.Sql/servers/syncAgents/read | 동기화 에이전트 목록을 가져오거나 지정된 동기화 에이전트에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/syncAgents/write | 지정된 매개 변수를 사용하여 동기화 에이전트를 만들거나 지정된 동기화 에이전트에 대한 속성을 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/tdeCertificates/action | TDE 인증서를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/usages/read | 서버 내의 모든 데이터베이스에서 서버 DTU 할당량 및 현재 DTU 사용을 반환 합니다. |
> | 실행력 | Microsoft.Sql/servers/virtualNetworkRules/delete | 기존 가상 네트워크 규칙을 삭제합니다. |
> | 실행력 | Microsoft.Sql/servers/virtualNetworkRules/read | 가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/servers/virtualNetworkRules/write | 지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/servers/vulnerabilityAssessments/delete | 주어진 서버에 대한 취약성 평가를 제거합니다. |
> | 실행력 | Microsoft.Sql/servers/vulnerabilityAssessments/read | 지정된 서버에서 취약성 평가 정책을 검색합니다. |
> | 실행력 | Microsoft.Sql/servers/vulnerabilityAssessments/write | 주어진 서버에 대한 취약성 평가를 변경합니다. |
> | 실행력 | Microsoft.Sql/servers/write | 지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다. |
> | 실행력 | Microsoft.Sql/unregister/action | Microsoft SQL Database 리소스 공급자에 대한 구독을 등록 취소하고 Microsoft SQL Databases를 만들도록 설정합니다. |
> | 실행력 | Microsoft.Sql/virtualClusters/delete | 기존 가상 클러스터를 삭제 합니다. |
> | 실행력 | Microsoft.Sql/virtualClusters/read | 가상 클러스터 목록을 반환하거나 지정된 가상 클러스터에 대한 속성을 가져옵니다. |
> | 실행력 | Microsoft.Sql/virtualClusters/write | 가상 클러스터 태그를 업데이트합니다. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | SqlVirtualMachine/위치/availabilityGroupListenerOperationResults/읽기 | 가용성 그룹 수신기 작업의 결과를 가져옵니다. |
> | 실행력 | SqlVirtualMachine/위치/sqlVirtualMachineGroupOperationResults/읽기 | SQL 가상 컴퓨터 그룹 작업의 결과를 가져옵니다. |
> | 실행력 | SqlVirtualMachine/위치/m s s o r e r | SQL 가상 컴퓨터 작업의 결과를 가져옵니다. |
> | 실행력 | SqlVirtualMachine/작업/읽기 |  |
> | 실행력 | SqlVirtualMachine/register/action | SqlVirtualMachine 리소스 공급자에 구독을 등록 합니다. |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/delete | 기존 가용성 그룹 수신기 삭제 |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/read | 지정 된 SQL 가상 컴퓨터 그룹의 SQL 가용성 그룹 수신기에 대 한 세부 정보를 검색 합니다. |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/write | 기존 SQL 가용성 그룹 수신기의 새 속성 또는 변경 속성을 만듭니다. |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachineGroups/delete | 기존 SQL 가상 컴퓨터 그룹 삭제 |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachineGroups/read | SQL 가상 컴퓨터 그룹의 세부 정보를 검색 합니다. |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/read | 특정 sql 가상 컴퓨터 그룹 별로 Sql 가상 컴퓨터 나열 |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachineGroups/write | 기존 SQL 가상 컴퓨터 그룹의 새 속성 또는 변경 속성을 만듭니다. |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachines/delete | 기존 SQL 가상 컴퓨터 삭제 |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachines/읽기 | SQL 가상 컴퓨터의 세부 정보 검색 |
> | 실행력 | SqlVirtualMachine/sqlVirtualMachines/write | 기존 SQL 가상 머신의 새 속성 또는 변경 속성을 만듭니다. |
> | 실행력 | SqlVirtualMachine/등록 취소/작업 | SqlVirtualMachine 리소스 공급자를 사용 하 여 구독 등록 취소 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.Storage/checknameavailability/read | 해당 계정 이름이 올바른지와 사용되고 있지 않은지 확인합니다. |
> | 실행력 | Microsoft.Storage/locations/checknameavailability/read | 해당 계정 이름이 올바른지와 사용되고 있지 않은지 확인합니다. |
> | 실행력 | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 가상 네트워크 또는 서브넷을 삭제 중임을 Microsoft.Storage에 알립니다. |
> | 실행력 | Microsoft.Storage/locations/usages/read | 지정한 구독의 리소스에 대한 한도 및 현재 사용 수를 반환합니다. |
> | 실행력 | Microsoft.Storage/operations/read | 비동기 작업의 상태를 폴링합니다. |
> | 실행력 | Microsoft.Storage/register/action | 스토리지 리소스 공급자에 대한 구독을 등록하고 스토리지 계정을 만들도록 설정합니다. |
> | 실행력 | Microsoft.Storage/skus/read | Microsoft Storage에서 지원하는 SKU를 나열합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Blob 콘텐츠 추가 결과를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Blob 삭제 결과를 반환합니다. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/Deleteblob 버전/작업 | Blob 버전 삭제 결과를 반환 합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | 일치 하는 태그 필터를 사용 하 여 계정에서 blob 목록을 반환 합니다. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/manageOwnership/작업 | Blob의 소유권을 변경 합니다. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/modifyPermissions/작업 | Blob의 사용 권한을 수정 합니다. |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/작업 | Blob을 한 경로에서 다른 경로로 이동 합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blob 또는 Blob 목록을 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Blob 명령의 결과를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Blob 태그를 읽은 결과를 반환 합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Blob 태그 작성 결과를 반환 합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob 쓰기 결과 반환 |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Blob 컨테이너 법적 보존을 지웁니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 컨테이너 삭제 결과를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Blob 컨테이너 불변성 정책을 삭제합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Blob 컨테이너 불변성 정책을 확장합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Blob 컨테이너 불변성 정책을 잠급니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Blob 컨테이너 불변성 정책을 가져옵니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Blob 컨테이너 불변성 정책을 넣습니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Blob 컨테이너 임대 결과를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 컨테이너를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 컨테이너 목록을 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Blob 컨테이너 법적 보존을 설정합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/write | Blob 컨테이너 패치 결과를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/containers/write | Blob 컨테이너 넣기의 결과를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Blob Service에 대한 사용자 위임 키를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/read | Blob service 속성 또는 통계를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/blobServices/write | Blob service 속성 넣기의 결과를 반환합니다. |
> | 실행력 | Microsoft. Storage/storageAccounts/dataSharePolicies/delete |  |
> | 실행력 | Microsoft. Storage/storageAccounts/dataSharePolicies/read |  |
> | 실행력 | Microsoft. Storage/storageAccounts/dataSharePolicies/read |  |
> | 실행력 | Microsoft. Storage/storageAccounts/dataSharePolicies/write |  |
> | 실행력 | Microsoft.Storage/storageAccounts/delete | 기존 스토리지 계정을 삭제합니다. |
> | 실행력 | Microsoft. Storage/storageAccounts/. 범위/읽기 |  |
> | 실행력 | Microsoft. Storage/storageAccounts/. 범위/읽기 |  |
> | 실행력 | Microsoft. Storage/storageAccounts/대 범위/쓰기 |  |
> | 실행력 | Microsoft. Storage/storageAccounts/대 범위/쓰기 |  |
> | 실행력 | Microsoft.Storage/storageAccounts/failover/action | 가용성 문제가 발생 하는 경우 고객은 장애 조치 (failover)를 제어할 수 있습니다. |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | 파일 관리자 권한 가져오기 |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | 파일/폴더 삭제 결과 반환 |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | 파일/폴더에 대 한 수정 권한이 있는 결과를 반환 합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | 파일/폴더 또는 파일/폴더 목록을 반환 합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | 파일을 쓰거나 폴더를 만든 결과를 반환 합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | 실행력 | Microsoft.Storage/storageAccounts/fileServices/read | 파일 서비스 속성 가져오기 |
> | 실행력 | Microsoft. Storage/storageAccounts/fileServices/공유/작업 |  |
> | 실행력 | Microsoft. Storage/storageAccounts/fileServices/공유/삭제 |  |
> | 실행력 | Microsoft. Storage/storageAccounts/fileServices/공유/읽기 |  |
> | 실행력 | Microsoft. Storage/storageAccounts/fileServices/공유/읽기 |  |
> | 실행력 | Microsoft. Storage/storageAccounts/fileServices/공유/쓰기 |  |
> | 실행력 | Microsoft. Storage/storageAccounts/fileServices/write |  |
> | 실행력 | Microsoft.Storage/storageAccounts/listAccountSas/Action | 지정된 스토리지 계정에 대한 계정 SAS 토큰을 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/listkeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/listServiceSas/Action | 지정된 스토리지 계정에 대한 서비스 SAS 토큰을 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/managementPolicies/delete | 저장소 계정 관리 정책 삭제 |
> | 실행력 | Microsoft.Storage/storageAccounts/managementPolicies/read | 저장소 관리 계정 정책 가져오기 |
> | 실행력 | Microsoft.Storage/storageAccounts/managementPolicies/write | 저장소 계정 관리 정책 배치 |
> | 실행력 | Microsoft. Storage/storageAccounts/objectReplicationPolicies/delete |  |
> | 실행력 | Microsoft. Storage/storageAccounts/objectReplicationPolicies/read |  |
> | 실행력 | Microsoft. Storage/storageAccounts/objectReplicationPolicies/read |  |
> | 실행력 | Microsoft. Storage/storageAccounts/objectReplicationPolicies/write |  |
> | 실행력 | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | 개인 엔드포인트 연결 프록시 삭제 |
> | 실행력 | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/read | 개인 엔드포인트 연결 프록시 가져오기 |
> | 실행력 | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | 개인 엔드포인트 연결 프록시를 추가 합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | 개인 엔드포인트 연결 삭제 |
> | 실행력 | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | 개인 엔드포인트 연결 가져오기 |
> | 실행력 | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | 개인 엔드포인트 연결 배치 |
> | 실행력 | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/action | 개인 엔드포인트 연결 승인 |
> | 실행력 | Microsoft.Storage/storageAccounts/privateLinkResources/read | StorageAccount groupids 가져오기 |
> | 실행력 | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 큐 삭제 결과를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | 메시지 추가 결과를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 메시지 삭제 결과를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | 메시지 처리 결과를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 메시지를 반환합니다. |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 메시지 작성 결과 반환 |
> | 실행력 | Microsoft.Storage/storageAccounts/queueServices/queues/read | 큐 또는 큐 목록을 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/queueServices/queues/write | 큐 쓰기 결과를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/queueServices/read | 큐 서비스 속성 가져오기 |
> | 실행력 | Microsoft.Storage/storageAccounts/queueServices/read | 큐 서비스 속성 또는 통계를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/queueServices/write | 큐 서비스 속성 설정 결과를 반환합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/regeneratekey/action | 지정된 스토리지 계정에 대한 액세스 키를 다시 생성합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | 지정 된 시간 상태로 blob 범위 복원 |
> | 실행력 | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | 지정된 스토리지 계정에 대한 모든 사용자 위임 키를 취소합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | 스토리지 계정 진단 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Storage/storageAccounts/tableServices/read | Table service 속성 가져오기 |
> | 실행력 | Microsoft.Storage/storageAccounts/write | 지정된 매개 변수를 사용하여 스토리지 계정을 만들거나, 속성 또는 태그를 업데이트하거나, 지정된 스토리지 계정의 사용자 지정 도메인을 추가합니다. |
> | 실행력 | Microsoft.Storage/usages/read | 지정한 구독의 리소스에 대한 한도 및 현재 사용 수를 반환합니다. |

## <a name="microsoftstoragesync"></a>microsoft.storagesync

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | microsoft.storagesync/locations/checkNameAvailability/action | 해당 스토리지 동기화 서비스 이름이 유효하며 사용되지 않는지 확인합니다. |
> | 실행력 | microsoft.storagesync/locations/workflows/operations/read | 비동기 작업의 상태를 가져옵니다. |
> | 실행력 | microsoft.storagesync/작업/읽기 | 지원 되는 작업 목록을 가져옵니다. |
> | 실행력 | microsoft.storagesync/register/action | 저장소 동기화 공급자에 대 한 구독을 등록 합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/delete | Storage Sync Services를 삭제합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Storage Sync Services에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/read | Storage Sync Services를 읽습니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/registeredServers/delete | 등록된 서버를 삭제합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | 등록된 서버에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/registeredServers/read | 등록된 서버를 읽습니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/registeredServers/write | 등록된 서버를 만들거나 업데이트합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | 클라우드 엔드포인트를 삭제합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 비동기 백업/복원 작업의 상태를 가져오기 |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | 백업 후 이 작업을 호출합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | 복원 후 이 작업을 호출합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | 백업 전에 이 작업을 호출합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | 복원 전에 이 작업을 호출합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | 클라우드 엔드포인트를 준비합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | 하트비트를 복원합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/작업 | 이 작업을 호출 하 여 클라우드 엔드포인트의 파일 공유에 대 한 변경 내용 검색을 트리거합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | 클라우드 엔드포인트를 만들거나 업데이트합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/delete | 동기화 그룹을 삭제합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | 동기화 그룹에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/read | 동기화 그룹을 읽습니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | 서버 엔드포인트를 삭제합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | 서버 엔드포인트에 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | 서버 엔드포인트를 읽습니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | 파일을 서버로 회수하려면 이 작업을 호출합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | 서버 엔드포인트를 만들거나 업데이트합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/syncGroups/write | 동기화 그룹을 만들거나 업데이트합니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | 비동기 작업의 상태를 가져옵니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/workflows/operations/read | 비동기 작업의 상태를 가져옵니다. |
> | 실행력 | microsoft.storagesync/storageSyncServices/workflows/read | 워크플로 읽기 |
> | 실행력 | microsoft.storagesync/storageSyncServices/write | Storage Sync Services를 만들거나 업데이트합니다. |
> | 실행력 | microsoft.storagesync/등록 취소/작업 | 저장소 동기화 공급자에 대 한 구독의 등록을 취소 합니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.StorSimple/managers/accessControlRecords/delete | Access Control 레코드를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/accessControlRecords/read | Access Control 레코드를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/accessControlRecords/write | Access Control 레코드를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/alerts/read | 경고를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/backups/read | Backup 세트를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/bandwidthSettings/delete | 기존 대역폭 설정을 삭제합니다(8000 시리즈만 해당). |
> | 실행력 | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/bandwidthSettings/read | 대역폭 설정을 나열합니다(8000 시리즈만 해당). |
> | 실행력 | Microsoft.StorSimple/managers/bandwidthSettings/write | 새 대역폭 설정을 만들거나 대역폭 설정을 업데이트합니다(8000 시리즈만 해당). |
> | 실행력 | Microsoft.StorSimple/managers/certificates/write | 인증서를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/Managers/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/clearAlerts/action | 디바이스 관리자와 관련된 모든 경고를 선택 취소합니다. |
> | 실행력 | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | 클라우드 어플라이언스 지원 구성을 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/configureDevice/action | 디바이스를 구성합니다. |
> | 실행력 | Microsoft.StorSimple/managers/delete | 디바이스 관리자를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/Managers/delete | 자격 증명 모음 삭제 작업에서는 '자격 증명 모음' 형식의 지정된 Azure 리소스를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/alertSettings/read | 경고 설정을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/alertSettings/write | 경고 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | 디바이스의 서비스 암호화 키 롤오버에 대한 권한을 부여합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 정책으로 보호되는 모든 볼륨의 요청 시 백업을 만들기 위해 수동 백업을 수행합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 기존 Backup 정책을 삭제합니다(8000 시리즈만 해당). |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupPolicies/read | Backup 정책을 나열합니다(8000 시리즈만 해당). |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 기존 일정을 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | 일정을 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 새 일정을 만들거나 일정을 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupPolicies/write | 새 Backup 정책을 만들거나 Backup 정책을 업데이트합니다(8000 시리즈만 해당). |
> | 실행력 | Microsoft.StorSimple/managers/devices/backups/delete | Backup 세트를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | 백업 요소를 사용하여 공유 또는 볼륨을 복제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backups/read | Backup 세트를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backups/restore/action | 백업 세트에서 모든 볼륨을 복원합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Backup 일정 그룹을 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Backup 일정 그룹을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Backup 일정 그룹을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/chapSettings/delete | CHAP 설정을 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/chapSettings/read | CHAP 설정을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/chapSettings/write | CHAP 설정을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/deactivate/action | 디바이스를 비활성화합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/delete | 디바이스를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/disks/read | 디스크를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/download/action | 장치에 대 한 업데이트를 다운로드 합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/failover/action | 디바이스의 장애 조치(Failover)입니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/failoverTargets/read | 디바이스의 장애 조치(Failover) 대상을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/backup/action | 파일 서버 백업을 수행합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/delete | 파일 서버를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/read | 파일 서버를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 공유를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 공유를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 공유를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/fileservers/write | 파일 서버를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | 하드웨어 구성 요소 그룹의 컨트롤러 전원 상태를 변경합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | 하드웨어 구성 요소 그룹을 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/install/action | 디바이스에 업데이트를 설치합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/installUpdates/action | 디바이스에 업데이트를 설치합니다(8000 시리즈만). |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | ISCSI 서버 백업을 수행합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/delete | ISCSI 서버를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | 디스크를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | 디스크를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | 디스크를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/read | iSCSI 서버를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/iscsiservers/write | iSCSI 서버를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 실행 중인 작업을 취소합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/jobs/read | 작업을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/listFailoverSets/action | 기존 디바이스에 대한 장애 조치(failover) 집합을 나열합니다(8000 시리즈만). |
> | 실행력 | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | 디바이스의 장애 조치(failover) 대상을 나열합니다(8000 시리즈만). |
> | 실행력 | Microsoft.StorSimple/managers/devices/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 성공적인 마이그레이션을 확인하고 커밋합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 마이그레이션 확인 상태를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 마이그레이션 확인 상태를 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 마이그레이션 예측 작업의 상태를 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 마이그레이션의 상태를 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 마이그레이션 위한 원본 구성을 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 마이그레이션 추정을 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 마이그레이션 상태를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | 원본 구성을 사용하여 마이그레이션을 시작합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 마이그레이션 프로세스 기간을 예측하는 작업을 시작합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/networkSettings/read | 네트워크 설정을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/networkSettings/write | 새 네트워크 설정을 만들거나 네트워크 설정을 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | 디바이스 관리자의 공용 암호화 키를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | 기존 디바이스의 지원 패키지를 게시합니다. StorSimple 지원 패키지는 StorSimple 디바이스 문제를 해결하는 데 있어서 Microsoft 지원을 지원하기 위해 모든 관련 로그를 수집하는 사용하기 쉬운 메커니즘입니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/read | 디바이스를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/scanForUpdates/action | 디바이스에서 업데이트를 검색합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/securitySettings/read | 보안 설정을 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | 디바이스에 대한 원격 관리 인증서를 동기화합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/securitySettings/update/action | 보안 설정을 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/securitySettings/write | 새 보안 설정을 만들거나 보안 설정을 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 구성된 전자 메일 받는 사람에게 테스트 경고 전자 메일을 보냅니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/shares/read | 공유를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/timeSettings/read | 시간 설정을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/timeSettings/write | 새 시간 설정을 만들거나 시간 설정을 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/updateSummary/read | 업데이트 요약을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 기존 볼륨 컨테이너를 삭제합니다(8000 시리즈만 해당). |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | 메트릭을 나열합니다 |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | 메트릭 정의를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/read | 볼륨 컨테이너를 나열합니다(8000 시리즈만 해당). |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 기존 볼륨을 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | 메트릭을 나열합니다 |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | 메트릭 정의를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 작업 결과를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | 볼륨을 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 새 볼륨을 만들거나 볼륨을 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumeContainers/write | 새 볼륨 컨테이너를 만들거나 볼륨 컨테이너를 업데이트합니다(8000 시리즈만 해당). |
> | 실행력 | Microsoft.StorSimple/managers/devices/volumes/read | 볼륨을 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/devices/write | 디바이스를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/encryptionSettings/read | 암호화 설정을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/extendedInformation/delete | 확장 자격 증명 모음 정보를 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/Managers/extendedInformation/delete | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/extendedInformation/read | 확장 자격 증명 모음 정보를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/Managers/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/extendedInformation/write | 확장 자격 증명 모음 정보를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/Managers/extendedInformation/write | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/features/read | 기능을 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/fileservers/read | 파일 서버를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/getEncryptionKey/action | 디바이스 관리자에 대한 암호화 키를 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/iscsiservers/read | iSCSI 서버를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/jobs/read | 작업을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/listActivationKey/action | StorSimple 디바이스 관리자의 활성화 키를 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | StorSimple 디바이스 관리자의 공용 암호화 키를 나열합니다. |
> | 실행력 | Microsoft.StorSimple/managers/metrics/read | 메트릭을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/metricsDefinitions/read | 메트릭 정의를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | 클래식에서 리소스 관리자로 마이그레이션 |
> | 실행력 | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 마이그레이션 원본 구성을 나열합니다(8000 시리즈만). |
> | 실행력 | Microsoft.StorSimple/managers/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/provisionCloudAppliance/action | 새 클라우드 어플라이언스를 만듭니다. |
> | 실행력 | Microsoft.StorSimple/managers/read | 디바이스 관리자를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/Managers/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/regenerateActivationKey/action | 기존 StorSimple 디바이스 관리자의 활성화 키를 다시 생성합니다. |
> | 실행력 | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Storage 계정 자격 증명을 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/storageAccountCredentials/read | Storage 계정 자격 증명을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/storageAccountCredentials/write | Storage 계정 자격 증명을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/storageDomains/delete | 스토리지 도메인을 삭제합니다. |
> | 실행력 | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 작업 결과를 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/storageDomains/read | 스토리지 도메인을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/managers/storageDomains/write | 스토리지 도메인을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/managers/write | 디바이스 관리자를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.StorSimple/Managers/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | 실행력 | Microsoft.StorSimple/operations/read | 작업을 나열하거나 가져옵니다. |
> | 실행력 | Microsoft.StorSimple/register/action | Microsoft.StorSimple 공급자를 등록합니다. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.StreamAnalytics/locations/quotas/Read | Stream Analytics 구독 할당량을 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/operations/Read | Stream Analytics 작업을 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/Register/action | 구독을 Stream Analytics 리소스 공급자에 등록합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/Delete | Stream Analytics 작업을 삭제합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Stream Analytics 작업 함수를 삭제합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Stream Analytics 작업 함수에 대한 작업 결과를 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Stream Analytics 작업 함수를 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Stream Analytics 작업 함수의 기본 정의를 검색합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Stream Analytics 작업 함수를 테스트합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Stream Analytics 작업 함수를 씁니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Stream Analytics 작업 입력을 삭제합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Stream Analytics 작업 입력에 대한 작업 결과를 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Stream Analytics 작업 입력을 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Stream Analytics 작업 입력을 샘플링합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Stream Analytics 작업 입력을 테스트합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Stream Analytics 작업 입력을 씁니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | 메트릭 정의를 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Stream Analytics 작업에 대한 작업 결과를 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Stream Analytics 작업 출력을 삭제합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Stream Analytics 작업 출력에 대한 작업 결과를 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Stream Analytics 작업 출력을 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Stream Analytics 작업 출력을 테스트합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Stream Analytics 작업 출력을 씁니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 진단 설정을 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 진단 설정을 씁니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | streamingjobs에 대해 사용 가능한 로그를 가져옵니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | streamingjobs에 대해 사용 가능한 메트릭을 가져옵니다. |
> | 실행력 | Microsoft. StreamAnalytics/streamingjobs/PublishEdgePackage/action | Stream Analytics 작업에 대 한 edge 패키지 게시 |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/Read | Stream Analytics 작업을 읽습니다. |
> | 실행력 | Microsoft. StreamAnalytics/streamingjobs/Scale/action | 크기 조정 Stream Analytics 작업 |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/Start/action | Stream Analytics 작업을 시작합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stream Analytics 작업을 중지합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Stream Analytics 작업 변환을 삭제합니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Stream Analytics 작업 변환을 읽습니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Stream Analytics 작업 변환을 씁니다. |
> | 실행력 | Microsoft.StreamAnalytics/streamingjobs/Write | Stream Analytics 작업을 씁니다. |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft 구독/취소/작업 | 구독을 취소 합니다. |
> | 실행력 | Microsoft.Subscription/CreateSubscription/action | Azure 구독 만들기 |
> | 실행력 | Microsoft.Subscription/register/action | Microsoft.Subscription 리소스 공급자에 구독을 등록합니다. |
> | 실행력 | Microsoft. 구독/이름 바꾸기/작업 | 구독 이름 바꾸기 |
> | 실행력 | Microsoft.Subscription/SubscriptionDefinitions/read | 관리 그룹의 Azure 구독 정의를 가져옵니다. |
> | 실행력 | Microsoft.Subscription/SubscriptionDefinitions/write | Azure 구독 정의를 만듭니다. |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft. Support/checkNameAvailability/action | 이름이 유효 하 고 리소스 종류에 사용 되 고 있지 않은지 확인 합니다. |
> | 실행력 | Microsoft. Support/operationresults/read | 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft. 지원/작업/읽기 | Microsoft 지원 리소스 공급자에서 사용할 수 있는 작업을 나열 합니다. |
> | 실행력 | Microsoft. Support/operationsstatus/read | 작업 상태 가져오기 |
> | 실행력 | Microsoft.Support/register/action | 등록 지원 리소스 공급자 |
> | 실행력 | Microsoft. 지원/서비스/problemClassifications/읽기 | Azure 서비스에 사용할 수 있는 문제 분류 목록을 가져옵니다. |
> | 실행력 | Microsoft. 지원/서비스/읽기 | 지원 가능한 Azure 서비스 목록을 가져옵니다. |
> | 실행력 | Microsoft. 지원/지원 티켓/통신/읽기 | 지원 티켓 통신 목록을 가져옵니다. |
> | 실행력 | Microsoft. 지원/지원 티켓/통신/쓰기 | 지원 티켓 통신을 만듭니다. |
> | 실행력 | Microsoft.Support/supportTickets/read | 지원 티켓 목록을 가져옵니다. |
> | 실행력 | Microsoft.Support/supportTickets/write | 지원 티켓을 비동기적으로 만들거나 업데이트 합니다. 기술, 청구, 할당량 또는 구독 관리 관련 문제에 대한 지원 티켓을 만들 수 있습니다. 기존 지원 티켓에 대 한 심각도 및 연락처 세부 정보를 업데이트할 수 있습니다. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | 액세스 정책을 삭제합니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | 액세스 정책 속성을 가져옵니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 환경에 대한 새 액세스 정책을 만들거나 기존 액세스 정책을 업데이트합니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/delete | 환경을 삭제합니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/eventsources/delete | 이벤트 원본을 삭제합니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/eventsources/read | 이벤트 원본 속성을 가져옵니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/eventsources/write | 환경에 대한 새 이벤트 원본을 만들거나 기존 이벤트 원본을 업데이트합니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/read | 환경 속성을 가져옵니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 참조 데이터 집합을 삭제합니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 참조 데이터 집합의 속성을 가져옵니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 환경에 대한 새 참조 데이터 집합을 만들거나 기존 참조 데이터 집합을 업데이트합니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/status/read | 환경의 상태 및 환경과 연결된 작업 상태(예: 수신)를 가져옵니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/environments/write | 새 환경을 만들거나 기존 환경을 업데이트합니다. |
> | 실행력 | Microsoft.TimeSeriesInsights/register/action | Time Series Insights 리소스 공급자에 대한 구독을 등록하고 Time Series Insights 환경을 만들도록 설정합니다. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.VisualStudio/Account/Delete | 계정을 삭제합니다. |
> | 실행력 | Microsoft.VisualStudio/Account/Extension/Read | 계정/확장을 읽습니다. |
> | 실행력 | Microsoft.VisualStudio/Account/Project/Read | 계정/프로젝트를 읽습니다. |
> | 실행력 | Microsoft.VisualStudio/Account/Project/Write | 계정/프로젝트를 설정합니다. |
> | 실행력 | Microsoft.VisualStudio/Account/Read | 계정을 읽습니다. |
> | 실행력 | Microsoft.VisualStudio/Account/Write | 계정을 설정합니다. |
> | 실행력 | Microsoft.VisualStudio/Extension/Delete | 확장을 삭제합니다. |
> | 실행력 | Microsoft.VisualStudio/Extension/Read | 확장을 읽습니다. |
> | 실행력 | Microsoft.VisualStudio/Extension/Write | 확장을 설정합니다. |
> | 실행력 | Microsoft.VisualStudio/Project/Delete | 프로젝트를 삭제합니다. |
> | 실행력 | Microsoft.VisualStudio/Project/Read | 프로젝트를 읽습니다. |
> | 실행력 | Microsoft.VisualStudio/Project/Write | 프로젝트를 설정합니다. |
> | 실행력 | Microsoft.VisualStudio/Register/Action | Azure 구독을 Microsoft.VisualStudio 공급자에 등록합니다. |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | microsoft.web/apimanagementaccounts/apiacls/read | API Management 계정 Apiacls를 가져옵니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/apiacls/delete | API Management 계정 API Apiacls를 삭제합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/apiacls/read | API Management 계정 API Apiacls를 가져옵니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/apiacls/write | API Management 계정 API Apiacls를 업데이트합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connectionacls/read | API Management 계정 API Connectionacls를 가져옵니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 동의 코드 API Management 계정 API 연결을 확인합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | API Management 계정 API 연결 Connectionacls를 삭제합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | API Management 계정 API 연결 Connectionacls를 가져옵니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | API Management 계정 API 연결 Connectionacls를 업데이트합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/delete | API Management 계정 API 연결을 삭제합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | API Management 계정 API 연결에 대한 동의 링크를 가져옵니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 연결 키 API Management 계정 API 연결을 나열합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | 비밀 API Management 계정 API 연결을 나열합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/read | API Management 계정 API 연결을 가져옵니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/connections/write | API Management 계정 API 연결을 업데이트합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/delete | API Management 계정 API를 삭제합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | API Management 계정 API 지역화된 정의를 삭제합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | API Management 계정 API 지역화된 정의를 가져옵니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | API Management 계정 API 지역화된 정의를 업데이트합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/read | API Management 계정 API를 가져옵니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/apis/write | API Management 계정 API를 업데이트합니다. |
> | 실행력 | microsoft.web/apimanagementaccounts/connectionacls/read | API Management 계정 Connectionacls를 가져옵니다. |
> | 실행력 | microsoft.web/availablestacks/read | 사용 가능한 스택을 가져옵니다. |
> | 실행력 | Microsoft.Web/certificates/Delete | 기존 인증서를 삭제합니다. |
> | 실행력 | Microsoft.Web/certificates/Read | 인증서 목록을 가져옵니다. |
> | 실행력 | Microsoft.Web/certificates/Write | 새 인증서를 추가하거나 기존 인증서를 업데이트합니다. |
> | 실행력 | microsoft.web/checknameavailability/read | 리소스 이름이 사용 가능한지 확인합니다. |
> | 실행력 | microsoft.web/classicmobileservices/read | 클래식 Mobile Services를 가져옵니다. |
> | 실행력 | Microsoft.Web/connectionGateways/Delete | 연결 게이트웨이를 삭제합니다. |
> | 실행력 | Microsoft.Web/connectionGateways/Join/Action | 연결 게이트웨이를 연결합니다. |
> | 실행력 | Microsoft.Web/connectionGateways/ListStatus/Action | 연결 게이트웨이 상태를 나열합니다. |
> | 실행력 | Microsoft.Web/connectionGateways/Move/Action | 연결 게이트웨이를 이동합니다. |
> | 실행력 | Microsoft.Web/connectionGateways/Read | 연결 게이트웨이 목록을 가져옵니다. |
> | 실행력 | Microsoft.Web/connectionGateways/Write | 연결 게이트웨이를 만들거나 업데이트합니다. |
> | 실행력 | microsoft.web/connections/confirmconsentcode/action | 연결 동의 코드를 확인합니다. |
> | 실행력 | Microsoft.Web/connections/Delete | 연결을 삭제합니다. |
> | 실행력 | Microsoft.Web/connections/Join/Action | 연결을 연결합니다. |
> | 실행력 | microsoft.web/connections/listconsentlinks/action | 연결에 대한 동의 링크를 나열합니다. |
> | 실행력 | Microsoft.Web/connections/Move/Action | 연결을 이동합니다. |
> | 실행력 | Microsoft.Web/connections/Read | 연결 목록을 가져옵니다. |
> | 실행력 | Microsoft.Web/connections/Write | 연결을 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Web/customApis/Delete | 사용자 지정 API를 삭제합니다. |
> | 실행력 | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | WSDL에서 API 정의를 추출합니다. |
> | 실행력 | Microsoft.Web/customApis/Join/Action | 사용자 지정 API를 조인합니다. |
> | 실행력 | Microsoft.Web/customApis/listWsdlInterfaces/Action | 사용자 지정 API에 대한 WSDL 인터페이스를 나열합니다. |
> | 실행력 | Microsoft.Web/customApis/Move/Action | 사용자 지정 API를 이동합니다. |
> | 실행력 | Microsoft.Web/customApis/Read | 사용자 지정 API 목록을 가져옵니다. |
> | 실행력 | Microsoft.Web/customApis/Write | 사용자 지정 API를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Web/deletedSites/Read | 삭제된 웹앱의 속성을 가져옵니다. |
> | 실행력 | microsoft.web/deploymentlocations/read | 배포 위치를 가져옵니다. |
> | 실행력 | Microsoft.Web/geoRegions/Read | 지리적 영역 목록을 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/capacities/read | 호스팅 환경 용량을 가져옵니다. |
> | 실행력 | Microsoft.Web/hostingEnvironments/Delete | App Service 환경을 삭제합니다. |
> | 실행력 | microsoft.web/hostingenvironments/detectors/read | 호스팅 환경 탐지기를 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/diagnostics/read | 호스팅 환경 진단을 가져옵니다. |
> | 실행력 | Microsoft 웹/hostingEnvironments/eventGridFilters/delete | 호스팅 환경에서 Event Grid 필터를 삭제 합니다. |
> | 실행력 | Microsoft 웹/hostingEnvironments/eventGridFilters/read | 호스팅 환경에 대 한 Event Grid 필터를 가져옵니다. |
> | 실행력 | Microsoft 웹/hostingEnvironments/eventGridFilters/write | 호스팅 환경에 Event Grid 필터를 추가 합니다. |
> | 실행력 | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | 모든 인바운드 종속성의 네트워크 엔드포인트를 가져옵니다. |
> | 실행력 | Microsoft.Web/hostingEnvironments/Join/Action | App Service Environment를 조인 합니다. |
> | 실행력 | microsoft.web/hostingenvironments/metricdefinitions/read | 호스팅 환경 메트릭 정의를 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | 호스팅 환경 MultiRole 풀 메트릭 정의를 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/multirolepools/metrics/read | 호스팅 환경 MultiRole 풀 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Web/hostingEnvironments/multiRolePools/Read | App Service 환경의 FrontEnd 풀 속성을 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/multirolepools/skus/read | 호스팅 환경 MultiRole 풀 SKU를 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/multirolepools/usages/read | 호스팅 환경 MultiRole 풀 사용 현황을 가져옵니다. |
> | 실행력 | Microsoft.Web/hostingEnvironments/multiRolePools/Write | App Service 환경에 새 FrontEnd 풀을 만들거나 기존 FrontEnd 풀을 업데이트합니다. |
> | 실행력 | microsoft.web/hostingenvironments/operations/read | 호스팅 환경 작업을 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | 모든 아웃바운드 종속성의 네트워크 엔드포인트를 가져옵니다. |
> | 실행력 | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | 개인 엔드포인트 연결 승인 |
> | 실행력 | Microsoft.Web/hostingEnvironments/Read | App Service 환경의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Web/hostingEnvironments/reboot/Action | App Service 환경의 모든 컴퓨터를 다시 부팅합니다. |
> | 실행력 | microsoft.web/hostingenvironments/resume/action | 호스팅 환경을 계속합니다. |
> | 실행력 | microsoft.web/hostingenvironments/serverfarms/read | 호스팅 환경 App Service 계획을 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/sites/read | 호스팅 환경 Web Apps를 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/suspend/action | 호스팅 환경을 일시 중단합니다. |
> | 실행력 | microsoft.web/hostingenvironments/usages/read | 호스팅 환경 사용 현황을 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | 호스팅 환경 Workerpools 메트릭 정의를 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/workerpools/metrics/read | 호스팅 환경 Workerpools 메트릭을 가져옵니다. |
> | 실행력 | Microsoft.Web/hostingEnvironments/workerPools/Read | App Service 환경의 작업자 풀 속성을 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/workerpools/skus/read | 호스팅 환경 Workerpools SKU를 가져옵니다. |
> | 실행력 | microsoft.web/hostingenvironments/workerpools/usages/read | 호스팅 환경 Workerpools 사용 현황을 가져옵니다. |
> | 실행력 | Microsoft.Web/hostingEnvironments/workerPools/Write | App Service 환경에 새 작업자 풀을 만들거나 기존 작업자 풀을 업데이트합니다. |
> | 실행력 | Microsoft.Web/hostingEnvironments/Write | 새 App Service 환경을 만들거나 기존 App Service 환경을 업데이트합니다. |
> | 실행력 | microsoft.web/ishostingenvironmentnameavailable/read | 호스팅 환경 이름을 사용할 수 있는지 여부를 가져옵니다. |
> | 실행력 | microsoft.web/ishostnameavailable/read | 호스트 이름을 사용할 수 있는지를 확인합니다. |
> | 실행력 | microsoft.web/isusernameavailable/read | 사용자 이름을 사용할 수 있는지를 확인합니다. |
> | 실행력 | Microsoft.Web/listSitesAssignedToHostName/read | 호스트 이름에 할당된 사이트의 이름을 가져옵니다. |
> | 실행력 | microsoft.web/locations/apioperations/read | 위치 API 작업을 가져옵니다. |
> | 실행력 | microsoft.web/locations/connectiongatewayinstallations/read | 위치 연결 게이트웨이 설치를 가져옵니다. |
> | 실행력 | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | 위치에 대한 Vnet 또는 서브넷 삭제 알림입니다. |
> | 실행력 | microsoft.web/locations/extractapidefinitionfromwsdl/action | 위치에 대한 WSDL에서 Api 정의를 추출합니다. |
> | 실행력 | microsoft.web/locations/listwsdlinterfaces/action | 위치에 대한 WSDL 인터페이스를 나열합니다. |
> | 실행력 | microsoft.web/locations/managedapis/apioperations/read | 위치 관리되는 API 작업을 가져옵니다. |
> | 실행력 | Microsoft.Web/locations/managedapis/Join/Action | 관리되는 API를 조인합니다. |
> | 실행력 | microsoft.web/locations/managedapis/read | 위치 관리되는 AAPI를 가져옵니다. |
> | 실행력 | microsoft. 웹/위치/operationResults/read | 작업을 가져옵니다. |
> | 실행력 | microsoft. 웹/위치/작업/읽기 | 작업을 가져옵니다. |
> | 실행력 | microsoft.web/operations/read | 작업을 가져옵니다. |
> | 실행력 | microsoft.web/publishingusers/read | 게시 사용자를 가져옵니다. |
> | 실행력 | microsoft.web/publishingusers/write | 게시 사용자를 업데이트합니다. |
> | 실행력 | Microsoft.Web/recommendations/Read | 구독에 대한 권장 사항 목록을 가져옵니다. |
> | 실행력 | microsoft.web/register/action | 구독에 대한 Microsoft.Web 리소스 공급자를 등록합니다. |
> | 실행력 | microsoft.web/resourcehealthmetadata/read | Resource Health 메타데이터를 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/capabilities/read | App Service 계획 기능을 가져옵니다. |
> | 실행력 | Microsoft.Web/serverfarms/Delete | 기존 App Service 계획 삭제 |
> | 실행력 | Microsoft 웹/serverfarms/eventGridFilters/delete | 서버 팜에서 Event Grid 필터를 삭제 합니다. |
> | 실행력 | Microsoft 웹/serverfarms/eventGridFilters/read | 서버 팜에서 Event Grid 필터를 가져옵니다. |
> | 실행력 | Microsoft 웹/serverfarms/eventGridFilters/write | 서버 팜에 Event Grid 필터를 추가 합니다. |
> | 실행력 | microsoft.web/serverfarms/firstpartyapps/settings/delete | App Service 계획 자사 앱 설정을 삭제합니다. |
> | 실행력 | microsoft.web/serverfarms/firstpartyapps/settings/read | App Service 계획 자사 앱 설정을 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/firstpartyapps/settings/write | App Service 계획 자사 앱 설정을 업데이트합니다. |
> | 실행력 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | App Service 계획 하이브리드 연결 네임스페이스 릴레이를 삭제합니다. |
> | 실행력 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | App Service 계획 하이브리드 연결 네임스페이스 릴레이를 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | App Service 계획 하이브리드 연결 네임스페이스 릴레이 Web Apps를 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/hybridconnectionplanlimits/read | App Service 계획 하이브리드 연결 계획 제한을 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/hybridconnectionrelays/read | App Service 계획 하이브리드 연결 릴레이를 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/metricdefinitions/read | App Service 계획 메트릭 정의를 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/metrics/read | App Service 계획 메트릭을 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/operationresults/read | App Service 계획 작업 결과를 가져옵니다. |
> | 실행력 | Microsoft.Web/serverfarms/Read | App Service 계획의 속성을 가져옵니다. |
> | 실행력 | Microsoft.Web/serverfarms/restartSites/Action | App Service 계획의 모든 Web Apps를 다시 시작합니다. |
> | 실행력 | microsoft.web/serverfarms/sites/read | App Service 계획 Web Apps를 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/skus/read | App Service 계획 SKU를 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/usages/read | App Service 계획 사용 현황을 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | App Service 계획 Virtual Network 연결 게이트웨이를 업데이트합니다. |
> | 실행력 | microsoft.web/serverfarms/virtualnetworkconnections/read | App Service 계획 Virtual Network 연결을 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | App Service 계획 Virtual Network 연결 경로를 삭제합니다. |
> | 실행력 | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | App Service 계획 Virtual Network 연결 경로를 가져옵니다. |
> | 실행력 | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | App Service 계획 Virtual Network 연결 경로를 업데이트합니다. |
> | 실행력 | microsoft.web/serverfarms/workers/reboot/action | App Service 계획 작업자를 다시 부팅합니다. |
> | 실행력 | Microsoft.Web/serverfarms/Write | 새 App Service 계획을 만들거나 기존 App Service 계획을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/analyzecustomhostname/read | 사용자 지정 호스트 이름을 분석합니다. |
> | 실행력 | Microsoft.Web/sites/applySlotConfig/Action | 대상 슬롯의 웹앱 슬롯 구성을 현재 웹앱에 적용합니다. |
> | 실행력 | Microsoft.Web/sites/backup/Action | 새 웹앱 백업을 만듭니다. |
> | 실행력 | microsoft.web/sites/backup/read | Web Apps Backup을 가져옵니다. |
> | 실행력 | microsoft.web/sites/backup/write | Web Apps Backup을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/backups/action | Azure Storage의 Blob에서 복원할 수 있는 기존 앱 백업을 검색합니다. |
> | 실행력 | microsoft.web/sites/backups/delete | Web Apps Backup을 삭제합니다. |
> | 실행력 | microsoft.web/sites/backups/list/action | Web Apps Backup을 나열합니다. |
> | 실행력 | Microsoft.Web/sites/backups/Read | 웹앱 백업의 속성을 가져옵니다. |
> | 실행력 | microsoft.web/sites/backups/restore/action | Web Apps Backup을 복원합니다. |
> | 실행력 | microsoft.web/sites/backups/write | Web Apps 백업을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/config/delete | Web Apps 구성을 삭제합니다. |
> | 실행력 | Microsoft.Web/sites/config/list/Action | 게시 자격 증명, 앱 설정 및 연결 문자열과 같은 웹앱 보안 중요 설정을 나열합니다. |
> | 실행력 | Microsoft.Web/sites/config/Read | 웹앱 구성 설정을 가져옵니다. |
> | 실행력 | microsoft.web/sites/config/snapshots/read | Web Apps 구성 스냅샷을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/config/Write | 웹앱 구성 설정을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/containerlogs/action | Web App에 대한 압축된 컨테이너 로그를 가져옵니다. |
> | 실행력 | microsoft.web/sites/containerlogs/download/action | Web Apps 컨테이너 로그를 다운로드합니다. |
> | 실행력 | microsoft.web/sites/continuouswebjobs/delete | Web Apps 연속 웹 작업을 삭제합니다. |
> | 실행력 | microsoft.web/sites/continuouswebjobs/read | Web Apps 연속 웹 작업을 가져옵니다. |
> | 실행력 | microsoft.web/sites/continuouswebjobs/start/action | Web Apps 연속 웹 작업을 시작합니다. |
> | 실행력 | microsoft.web/sites/continuouswebjobs/stop/action | Web Apps 연속 웹 작업을 중지합니다. |
> | 실행력 | Microsoft.Web/sites/Delete | 기존 웹앱 삭제 |
> | 실행력 | microsoft.web/sites/deployments/delete | Web Apps 배포를 삭제합니다. |
> | 실행력 | microsoft.web/sites/deployments/log/read | Web Apps 배포 로그를 가져옵니다. |
> | 실행력 | microsoft.web/sites/deployments/read | Web Apps 배포를 가져옵니다. |
> | 실행력 | microsoft.web/sites/deployments/write | Web Apps 배포를 업데이트합니다. |
> | 실행력 | microsoft.web/sites/detectors/read | Web Apps 탐지기를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/analyses/execute/Action | Web Apps 진단 분석을 실행합니다. |
> | 실행력 | microsoft.web/sites/diagnostics/analyses/read | Web Apps 진단 분석을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/aspnetcore/read | ASP.NET Core 앱용 Web Apps 진단을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/autoheal/read | Web Apps 진단 Autoheal을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/deployment/read | Web Apps 진단 배포를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/deployments/read | Web Apps 진단 배포를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/detectors/execute/Action | Web Apps 진단 감지기를 실행합니다. |
> | 실행력 | microsoft.web/sites/diagnostics/detectors/read | Web Apps 진단 감지기를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Web Apps 진단 Uri당 실패한 요청을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/frebanalysis/read | Web Apps 진단 FREB 분석을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/loganalyzer/read | Web Apps 진단 로그 분석기를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/read | Web Apps 진단 로그 범주를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/runtimeavailability/read | Web Apps 진단 런타임 가용성을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/servicehealth/read | Web Apps 진단 서비스 상태를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Web Apps 진단 사이트 CPU 분석을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/sitecrashes/read | Web Apps 진단 사이트 크래시를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/sitelatency/read | Web Apps 진단 사이트 대기 시간을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Web Apps 진단 사이트 메모리 분석을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Web Apps 진단 사이트 다시 시작 설정 업데이트를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Web Apps 진단 사이트 시작 사용자 다시 시작을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/siteswap/read | Web Apps 진단 사이트 스왑을 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/threadcount/read | Web Apps 진단 사이트 스레드 수를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/workeravailability/read | Web Apps 진단 Workeravailability를 가져옵니다. |
> | 실행력 | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Web Apps 진단 작업자 프로세스 재활용을 가져옵니다. |
> | 실행력 | microsoft.web/sites/domainownershipidentifiers/read | Web Apps 도메인 소유권 식별자를 가져옵니다. |
> | 실행력 | microsoft.web/sites/domainownershipidentifiers/write | Web Apps 도메인 소유권 식별자를 업데이트합니다. |
> | 실행력 | Microsoft. 웹/사이트/i s d i d 필터/삭제 | 웹 앱에서 Event Grid 필터를 삭제 합니다. |
> | 실행력 | Microsoft. 웹/사이트/i s d e r/r e d 필터/읽기 | 웹 앱에서 Event Grid 필터를 가져옵니다. |
> | 실행력 | Microsoft. 웹/사이트/i s d i d 필터/쓰기 | 웹 앱에 Event Grid 필터를 추가 합니다. |
> | 실행력 | microsoft.web/sites/extensions/delete | Web Apps 사이트 확장을 삭제합니다. |
> | 실행력 | microsoft.web/sites/extensions/read | Web Apps 사이트 확장을 가져옵니다. |
> | 실행력 | microsoft.web/sites/extensions/write | Web Apps 사이트 확장을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/functions/action | 합수 Web Apps입니다. |
> | 실행력 | microsoft.web/sites/functions/delete | Web Apps 함수를 삭제합니다. |
> | 실행력 | microsoft 웹/사이트/함수/키/삭제 | 함수 키를 삭제 합니다. |
> | 실행력 | microsoft 웹/사이트/함수/키/쓰기 | 기능 키를 업데이트 합니다. |
> | 실행력 | microsoft 웹/사이트/함수/listkeys/동작 | 함수 키를 나열 합니다. |
> | 실행력 | microsoft.web/sites/functions/listsecrets/action | 함수 암호를 나열 합니다. |
> | 실행력 | microsoft.web/sites/functions/masterkey/read | Web Apps 함수 Masterkey를 가져옵니다. |
> | 실행력 | microsoft 웹/사이트/함수/속성/읽기 | Web Apps 함수 속성을 읽습니다. |
> | 실행력 | microsoft 웹/사이트/함수/속성/쓰기 | Web Apps 함수 속성을 업데이트 합니다. |
> | 실행력 | microsoft.web/sites/functions/read | Web Apps 함수를 가져옵니다. |
> | 실행력 | microsoft.web/sites/functions/token/read | Web Apps 함수 토큰을 가져옵니다. |
> | 실행력 | microsoft.web/sites/functions/write | Web Apps 함수를 업데이트합니다. |
> | 실행력 | microsoft 웹/사이트/호스트/functionkeys/delete | 함수 호스트 함수 키를 삭제 합니다. |
> | 실행력 | microsoft. 웹/사이트/호스트/functionkeys/write | 업데이트 함수는 함수 키를 호스트 합니다. |
> | 실행력 | microsoft 웹/사이트/호스트/listkeys/작업 | 함수 호스트 키를 나열 합니다. |
> | 실행력 | microsoft. 웹/사이트/호스트/listsyncstatus/작업 | 동기화 함수 트리거 상태를 나열 합니다. |
> | 실행력 | microsoft 웹/사이트/호스트/속성/읽기 | Web Apps 함수 호스트 속성을 읽습니다. |
> | 실행력 | microsoft 웹/사이트/호스트/동기화/작업 | 함수 트리거를 동기화 합니다. |
> | 실행력 | microsoft. 웹/사이트/호스트/m p r/systemkeys/delete | 함수 호스트 시스템 키를 삭제 합니다. |
> | 실행력 | microsoft. 웹/사이트/호스트/m p r/시스템 키/쓰기 | 업데이트 함수는 시스템 키를 호스트 합니다. |
> | 실행력 | microsoft.web/sites/hostnamebindings/delete | Web Apps 호스트 이름 바인딩을 삭제합니다. |
> | 실행력 | microsoft.web/sites/hostnamebindings/read | Web Apps 호스트 이름 바인딩을 가져옵니다. |
> | 실행력 | microsoft.web/sites/hostnamebindings/write | Web Apps 호스트 이름 바인딩을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/hostruntime/functions/keys/read | Web Apps Hostruntime 함수 키를 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/hostruntime/host/_master/read | 관리 작업에 대한 함수 앱의 마스터 키를 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/hostruntime/host/action | 트리거 동기화, 함수 추가, 함수 호출, 함수 삭제 등과 같은 함수 앱 런타임 작업을 수행합니다. |
> | 실행력 | microsoft.web/sites/hostruntime/host/read | Web Apps Hostruntime 호스트를 가져옵니다. |
> | 실행력 | microsoft.web/sites/hybridconnection/delete | Web Apps 하이브리드 연결을 삭제합니다. |
> | 실행력 | microsoft.web/sites/hybridconnection/read | Web Apps 하이브리드 연결을 가져옵니다. |
> | 실행력 | microsoft.web/sites/hybridconnection/write | Web Apps 하이브리드 연결을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Web Apps 하이브리드 연결 네임스페이스 릴레이를 삭제합니다. |
> | 실행력 | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | 키 Web Apps 하이브리드 연결 네임스페이스 릴레이를 나열합니다. |
> | 실행력 | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Web Apps 하이브리드 연결 네임스페이스 릴레이를 가져옵니다. |
> | 실행력 | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Web Apps 하이브리드 연결 네임스페이스 릴레이를 업데이트합니다. |
> | 실행력 | microsoft.web/sites/hybridconnectionrelays/read | Web Apps 하이브리드 연결 릴레이를 가져옵니다. |
> | 실행력 | microsoft.web/sites/instances/deployments/delete | Web Apps 인스턴스 배포를 삭제합니다. |
> | 실행력 | microsoft.web/sites/instances/deployments/read | Web Apps 인스턴스 배포를 가져옵니다. |
> | 실행력 | microsoft.web/sites/instances/extensions/log/read | Web Apps 인스턴스 확장 로그를 가져옵니다. |
> | 실행력 | microsoft.web/sites/instances/extensions/processes/read | Web Apps 인스턴스 확장 프로세스를 가져옵니다. |
> | 실행력 | microsoft.web/sites/instances/extensions/read | Web Apps 인스턴스 확장을 가져옵니다. |
> | 실행력 | microsoft.web/sites/instances/processes/delete | Web Apps 인스턴스 프로세스를 삭제합니다. |
> | 실행력 | microsoft.web/sites/instances/processes/modules/read | Web Apps 인스턴스 프로세스 모듈을 가져옵니다. |
> | 실행력 | microsoft.web/sites/instances/processes/read | Web Apps 인스턴스 프로세스를 가져옵니다. |
> | 실행력 | microsoft.web/sites/instances/processes/threads/read | Web Apps 인스턴스 프로세스 스레드를 가져옵니다. |
> | 실행력 | microsoft.web/sites/instances/read | Web Apps 인스턴스를 가져옵니다. |
> | 실행력 | microsoft.web/sites/listsyncfunctiontriggerstatus/action | 동기화 함수 트리거 상태를 나열 합니다. |
> | 실행력 | microsoft.web/sites/metricdefinitions/read | Web Apps 메트릭 정의를 가져옵니다. |
> | 실행력 | microsoft.web/sites/metrics/read | Web Apps 메트릭을 가져옵니다. |
> | 실행력 | microsoft.web/sites/metricsdefinitions/read | Web Apps 메트릭 정의를 가져옵니다. |
> | 실행력 | microsoft.web/sites/migratemysql/action | MySql Web Apps를 마이그레이션합니다. |
> | 실행력 | microsoft.web/sites/migratemysql/read | Web Apps 마이그레이션 MySql을 가져옵니다. |
> | 실행력 | microsoft.web/sites/networktrace/action | 네트워크 추적 Web Apps입니다. |
> | 실행력 | microsoft.web/sites/networktraces/operationresults/read | Web Apps 네트워크 추적 작업 결과를 가져옵니다. |
> | 실행력 | microsoft.web/sites/newpassword/action | Newpassword Web Apps입니다. |
> | 실행력 | microsoft.web/sites/operationresults/read | Web Apps 작업 결과를 가져옵니다. |
> | 실행력 | microsoft.web/sites/operations/read | Web Apps 작업을 가져옵니다. |
> | 실행력 | microsoft.web/sites/perfcounters/read | Web Apps 성능 카운터를 가져옵니다. |
> | 실행력 | microsoft.web/sites/premieraddons/delete | Web Apps 프리미어 추가 기능을 삭제합니다. |
> | 실행력 | microsoft.web/sites/premieraddons/read | Web Apps 프리미어 추가 기능을 가져옵니다. |
> | 실행력 | microsoft.web/sites/premieraddons/write | Web Apps 프리미어 추가 기능을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/privateaccess/read | 프라이빗 사이트 액세스 사용 및 사이트에 액세스할 수 있는 승인된 Virtual Networks 관련 데이터를 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | 개인 엔드포인트 연결 승인 |
> | 실행력 | microsoft.web/sites/processes/modules/read | Web Apps 프로세스 모듈을 가져옵니다. |
> | 실행력 | microsoft.web/sites/processes/read | Web Apps 프로세스를 가져옵니다. |
> | 실행력 | microsoft.web/sites/processes/threads/read | 스레드를 가져오기 Web Apps를 처리 합니다. |
> | 실행력 | microsoft.web/sites/publiccertificates/delete | Web Apps 공용 인증서를 삭제합니다. |
> | 실행력 | microsoft.web/sites/publiccertificates/read | Web Apps 공용 인증서를 가져옵니다. |
> | 실행력 | microsoft.web/sites/publiccertificates/write | Web Apps 공용 인증서를 업데이트합니다. |
> | 실행력 | Microsoft.Web/sites/publish/Action | 웹앱을 게시합니다. |
> | 실행력 | Microsoft.Web/sites/publishxml/Action | 웹앱에 대한 게시 프로필 xml을 가져옵니다. |
> | 실행력 | microsoft.web/sites/publishxml/read | Web Apps 게시 XML을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/Read | 웹앱의 속성을 가져옵니다. |
> | 실행력 | microsoft.web/sites/recommendationhistory/read | Web Apps 권장 사항 기록을 가져옵니다. |
> | 실행력 | microsoft.web/sites/recommendations/disable/action | Web Apps 권장 사항을 사용하지 않도록 설정합니다. |
> | 실행력 | Microsoft.Web/sites/recommendations/Read | 웹앱에 대한 권장 사항 목록을 가져옵니다. |
> | 실행력 | microsoft.web/sites/recover/action | Web Apps을 복구합니다. |
> | 실행력 | Microsoft.Web/sites/resetSlotConfig/Action | 웹앱 구성을 다시 설정합니다. |
> | 실행력 | microsoft.web/sites/resourcehealthmetadata/read | Web Apps Resource Health 메타데이터를 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/restart/Action | 웹앱을 다시 시작합니다. |
> | 실행력 | microsoft.web/sites/restore/read | Web Apps 복원을 가져옵니다. |
> | 실행력 | microsoft.web/sites/restore/write | Web Apps을 복원합니다. |
> | 실행력 | microsoft.web/sites/restorefrombackupblob/action | 백업 Blob에서 웹앱을 복원합니다. |
> | 실행력 | microsoft 웹/사이트/restorefromdeletedapp/작업 | 삭제된 앱에서 Web Apps를 복원합니다. |
> | 실행력 | microsoft.web/sites/restoresnapshot/action | Web Apps 스냅샷을 복원합니다. |
> | 실행력 | microsoft.web/sites/siteextensions/delete | Web Apps 사이트 확장을 삭제합니다. |
> | 실행력 | microsoft.web/sites/siteextensions/read | Web Apps 사이트 확장을 가져옵니다. |
> | 실행력 | microsoft.web/sites/siteextensions/write | Web Apps 사이트 확장을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/analyzecustomhostname/read | Web Apps 슬롯 사용자 지정 호스트 이름 분석을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/slots/applySlotConfig/Action | 대상 슬롯의 웹앱 슬롯 구성을 현재 슬롯에 적용합니다. |
> | 실행력 | Microsoft.Web/sites/slots/backup/Action | 새 웹앱 슬롯 백업을 만듭니다. |
> | 실행력 | microsoft.web/sites/slots/backup/read | Web Apps 슬롯 백업을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/backup/write | Web Apps 슬롯 백업을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/backups/action | Web Apps 슬롯 백업을 검색합니다. |
> | 실행력 | microsoft.web/sites/slots/backups/delete | Web Apps 슬롯 백업을 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/backups/list/action | Web Apps 슬롯 백업을 나열합니다. |
> | 실행력 | Microsoft.Web/sites/slots/backups/Read | 웹앱 슬롯 백업의 속성을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/backups/restore/action | Web Apps 슬롯 백업을 복원합니다. |
> | 실행력 | microsoft.web/sites/slots/config/delete | Web Apps 슬롯 구성을 삭제합니다. |
> | 실행력 | Microsoft.Web/sites/slots/config/list/Action | 게시 자격 증명, 앱 설정 및 연결 문자열과 같은 웹앱 슬롯 보안 중요 설정을 나열합니다. |
> | 실행력 | Microsoft.Web/sites/slots/config/Read | 웹앱 슬롯의 구성 설정을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/slots/config/Write | 웹앱 슬롯의 구성 설정을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/containerlogs/action | 웹앱 슬롯에 대한 압축된 컨테이너 로그를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/containerlogs/download/action | Web Apps 슬롯 컨테이너 로그를 다운로드합니다. |
> | 실행력 | microsoft.web/sites/slots/continuouswebjobs/delete | Web Apps 슬롯 연속 웹 작업을 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/continuouswebjobs/read | Web Apps 슬롯 연속 웹 작업을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/continuouswebjobs/start/action | Web Apps 슬롯 연속 웹 작업을 시작합니다. |
> | 실행력 | microsoft.web/sites/slots/continuouswebjobs/stop/action | Web Apps 슬롯 연속 웹 작업을 중지합니다. |
> | 실행력 | Microsoft.Web/sites/slots/Delete | 기존 웹앱 슬롯을 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/deployments/delete | Web Apps 슬롯 배포를 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/deployments/log/read | Web Apps 슬롯 배포 로그를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/deployments/read | Web Apps 슬롯 배포를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/deployments/write | Web Apps 슬롯 배포를 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/detectors/read | Web Apps 슬롯 감지기를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Web Apps 슬롯 진단 분석을 실행합니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/analyses/read | Web Apps 슬롯 진단 분석을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/aspnetcore/read | ASP.NET Core 앱용 Web Apps 슬롯 진단을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/autoheal/read | Web Apps 슬롯 진단 Autoheal을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/deployment/read | Web Apps 슬롯 진단 배포를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/deployments/read | Web Apps 슬롯 진단 배포를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Web Apps 슬롯 진단 감지기를 실행합니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/detectors/read | Web Apps 슬롯 진단 감지기를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Web Apps 슬롯 진단 FREB 분석을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Web Apps 슬롯 진단 로그 분석기를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/read | Web Apps 슬롯 진단을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Web Apps 슬롯 진단 런타임 가용성을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/servicehealth/read | Web Apps 슬롯 진단 서비스 상태를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Web Apps 슬롯 진단 사이트 CPU 분석을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Web Apps 슬롯 진단 사이트 크래시를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/sitelatency/read | Web Apps 슬롯 진단 사이트 대기 시간을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Web Apps 슬롯 진단 사이트 메모리 분석을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Web Apps 슬롯 진단 사이트 다시 시작 설정 업데이트를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Web Apps 슬롯 진단 사이트 시작된 사용자 다시 시작을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/siteswap/read | Web Apps 슬롯 진단 사이트 스왑을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/threadcount/read | Web Apps 슬롯 진단 스레드 수를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/workeravailability/read | Web Apps 슬롯 진단 Workeravailability를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Web Apps 슬롯 진단 작업자 프로세스 재활용을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/domainownershipidentifiers/read | Web Apps 슬롯 도메인 소유권 식별자를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/functions/listsecrets/action | 비밀 Web Apps 슬롯 함수를 나열합니다. |
> | 실행력 | microsoft.web/sites/slots/functions/read | Web Apps 슬롯 함수를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/hostnamebindings/delete | Web Apps 슬롯 호스트 이름 바인딩을 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/hostnamebindings/read | Web Apps 슬롯 호스트 이름 바인딩을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/hostnamebindings/write | Web Apps 슬롯 호스트 이름 바인딩을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/hybridconnection/delete | Web Apps 슬롯 하이브리드 연결을 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/hybridconnection/read | Web Apps 슬롯 하이브리드 연결을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/hybridconnection/write | Web Apps 슬롯 하이브리드 연결을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Web Apps 슬롯 하이브리드 연결 네임스페이스 릴레이를 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Web Apps 슬롯 하이브리드 연결 네임스페이스 릴레이를 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/hybridconnectionrelays/read | Web Apps 슬롯 하이브리드 연결 릴레이를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/instances/deployments/read | Web Apps 슬롯 인스턴스 배포를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/instances/processes/delete | Web Apps 슬롯 인스턴스 프로세스를 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/instances/processes/read | Web Apps 슬롯 인스턴스 프로세스를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/instances/read | Web Apps 슬롯 인스턴스를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/metricdefinitions/read | Web Apps 슬롯 메트릭 정의를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/metrics/read | Web Apps 슬롯 메트릭을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/migratemysql/read | Web Apps 슬롯 마이그레이션 MySql을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/networktrace/action | 네트워크 추적 Web Apps 슬롯입니다. |
> | 실행력 | microsoft.web/sites/slots/networktraces/operationresults/read | Web Apps 슬롯 네트워크 추적 작업 결과를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps 슬롯입니다. |
> | 실행력 | microsoft.web/sites/slots/operationresults/read | Web Apps 슬롯 작업 결과를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/operations/read | Web Apps 슬롯 작업을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/perfcounters/read | Web Apps 슬롯 성능 카운터를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/phplogging/read | Web Apps 슬롯 Phplogging을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/premieraddons/delete | Web Apps 슬롯 프리미어 추가 기능을 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/premieraddons/read | Web Apps 슬롯 프리미어 추가 기능을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/premieraddons/write | Web Apps 슬롯 프리미어 추가 기능을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/processes/read | Web Apps 슬롯 프로세스를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/publiccertificates/delete | Web Apps 슬롯 공용 인증서를 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/publiccertificates/read | Web Apps 슬롯 공용 인증서를 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/publiccertificates/write | Web Apps 슬롯 공용 인증서를 만들거나 업데이트합니다. |
> | 실행력 | Microsoft.Web/sites/slots/publish/Action | 웹앱 슬롯을 게시합니다. |
> | 실행력 | Microsoft.Web/sites/slots/publishxml/Action | 웹앱 슬롯에 대한 게시 프로필 xml을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/slots/Read | 웹앱 배포 슬롯의 속성을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/recover/action | Web Apps 슬롯을 복구합니다. |
> | 실행력 | Microsoft.Web/sites/slots/resetSlotConfig/Action | 웹앱 슬롯 구성을 다시 설정합니다. |
> | 실행력 | microsoft.web/sites/slots/resourcehealthmetadata/read | Web Apps 슬롯 Resource Health 메타데이터를 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/slots/restart/Action | 웹앱 슬롯을 다시 시작합니다. |
> | 실행력 | microsoft.web/sites/slots/restore/read | Web Apps 슬롯 복원을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/restore/write | Web Apps 슬롯을 복원합니다. |
> | 실행력 | microsoft.web/sites/slots/restorefrombackupblob/action | 백업 Blob에서 Web Apps 슬롯을 복원합니다. |
> | 실행력 | microsoft 웹/사이트/슬롯/restorefromdeletedapp/작업 | 삭제된 앱에서 웹앱 슬롯을 복원합니다. |
> | 실행력 | microsoft.web/sites/slots/restoresnapshot/action | Web Apps 슬롯 스냅샷을 복원합니다. |
> | 실행력 | microsoft.web/sites/slots/siteextensions/delete | Web Apps 슬롯 사이트 확장을 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/siteextensions/read | Web Apps 슬롯 사이트 확장을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/siteextensions/write | Web Apps 슬롯 사이트 확장을 업데이트합니다. |
> | 실행력 | Microsoft.Web/sites/slots/slotsdiffs/Action | 웹앱 및 슬롯 간 구성 차이를 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/slots/slotsswap/Action | 웹앱 배포 슬롯을 교환합니다. |
> | 실행력 | microsoft.web/sites/slots/snapshots/read | Web Apps 슬롯 스냅샷을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/slots/sourcecontrols/Delete | 웹앱 슬롯의 소스 제어 구성 설정을 삭제합니다. |
> | 실행력 | Microsoft.Web/sites/slots/sourcecontrols/Read | 웹앱 슬롯의 소스 제어 구성 설정을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/slots/sourcecontrols/Write | 웹앱 슬롯의 소스 제어 구성 설정을 업데이트합니다. |
> | 실행력 | Microsoft.Web/sites/slots/start/Action | 웹앱 슬롯을 시작합니다. |
> | 실행력 | Microsoft.Web/sites/slots/stop/Action | 웹앱 슬롯을 중지합니다. |
> | 실행력 | microsoft.web/sites/slots/sync/action | Web Apps 슬롯을 동기화합니다. |
> | 실행력 | microsoft.web/sites/slots/triggeredwebjobs/delete | Web Apps 슬롯 트리거 Webjob을 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/triggeredwebjobs/read | Web Apps 슬롯 트리거 Webjob을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/triggeredwebjobs/run/action | Web Apps 슬롯 트리거 Webjob을 실행합니다. |
> | 실행력 | microsoft.web/sites/slots/usages/read | Web Apps 슬롯 사용 현황을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/virtualnetworkconnections/delete | Web Apps 슬롯 Virtual Network 연결을 삭제합니다. |
> | 실행력 | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Web Apps 슬롯 Virtual Network 연결 게이트웨이를 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/virtualnetworkconnections/read | Web Apps 슬롯 Virtual Network 연결을 가져옵니다. |
> | 실행력 | microsoft.web/sites/slots/virtualnetworkconnections/write | Web Apps 슬롯 Virtual Network 연결을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/slots/webjobs/read | Web Apps 슬롯 Webjob을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/slots/Write | 새 웹앱 슬롯을 만들거나 기존 웹앱 슬롯을 업데이트합니다. |
> | 실행력 | Microsoft.Web/sites/slotsdiffs/Action | 웹앱 및 슬롯 간 구성 차이를 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/slotsswap/Action | 웹앱 배포 슬롯을 교환합니다. |
> | 실행력 | microsoft.web/sites/snapshots/read | Web Apps 스냅샷을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/sourcecontrols/Delete | 웹앱의 소스 제어 구성 설정을 삭제합니다. |
> | 실행력 | Microsoft.Web/sites/sourcecontrols/Read | 웹앱의 소스 제어 구성 설정을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/sourcecontrols/Write | 웹앱의 소스 제어 구성 설정을 업데이트합니다. |
> | 실행력 | Microsoft.Web/sites/start/Action | 웹앱을 시작합니다. |
> | 실행력 | Microsoft.Web/sites/stop/Action | 웹앱을 중지합니다. |
> | 실행력 | microsoft.web/sites/sync/action | Web Apps를 동기화합니다. |
> | 실행력 | microsoft.web/sites/syncfunctiontriggers/action | 함수 트리거를 동기화 합니다. |
> | 실행력 | microsoft.web/sites/triggeredwebjobs/delete | Web Apps 트리거 Webjob을 삭제합니다. |
> | 실행력 | microsoft.web/sites/triggeredwebjobs/history/read | Web Apps 트리거 WebJobs 기록을 가져옵니다. |
> | 실행력 | microsoft.web/sites/triggeredwebjobs/read | Web Apps 트리거 Webjob을 가져옵니다. |
> | 실행력 | microsoft.web/sites/triggeredwebjobs/run/action | Web Apps 트리거 Webjob을 실행합니다. |
> | 실행력 | microsoft.web/sites/usages/read | Web Apps 사용 현황을 가져옵니다. |
> | 실행력 | microsoft.web/sites/virtualnetworkconnections/delete | Web Apps Virtual Network 연결을 삭제합니다. |
> | 실행력 | microsoft.web/sites/virtualnetworkconnections/gateways/read | Web Apps Virtual Network 연결 게이트웨이를 가져옵니다. |
> | 실행력 | microsoft.web/sites/virtualnetworkconnections/gateways/write | Web Apps Virtual Network 연결 게이트웨이를 업데이트합니다. |
> | 실행력 | microsoft.web/sites/virtualnetworkconnections/read | Web Apps Virtual Network 연결을 가져옵니다. |
> | 실행력 | microsoft.web/sites/virtualnetworkconnections/write | Web Apps Virtual Network 연결을 업데이트합니다. |
> | 실행력 | microsoft.web/sites/webjobs/read | Web Apps Webjob을 가져옵니다. |
> | 실행력 | Microsoft.Web/sites/Write | 새 웹앱을 만들거나 기존 웹앱을 업데이트합니다. |
> | 실행력 | microsoft.web/skus/read | SKU를 가져옵니다. |
> | 실행력 | microsoft.web/sourcecontrols/read | 소스 제어를 가져옵니다. |
> | 실행력 | microsoft.web/sourcecontrols/write | 소스 제어를 업데이트합니다. |
> | 실행력 | microsoft.web/unregister/action | 구독에 대한 Microsoft.Web 리소스 공급자를 등록 취소합니다. |
> | 실행력 | microsoft.web/validate/action | 유효성 검사를 수행합니다. |
> | 실행력 | microsoft.web/verifyhostingenvironmentvnet/action | 호스팅 환경 Vnet을 확인합니다. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | 액션 유형 | 작업 | Description |
> | --- | --- | --- |
> | 실행력 | Microsoft.WorkloadMonitor/components/read | 리소스에 대한 구성 요소를 가져옵니다. |
> | 실행력 | Microsoft.WorkloadMonitor/componentsSummary/read | 구성 요소의 요약을 가져옵니다. |
> | 실행력 | Microsoft.WorkloadMonitor/monitorInstances/read | 리소스에 대한 모니터의 인스턴스를 가져옵니다. |
> | 실행력 | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | 모니터 인스턴스의 요약을 가져옵니다. |
> | 실행력 | Microsoft.WorkloadMonitor/monitors/read | 리소스에 대한 모니터를 가져옵니다. |
> | 실행력 | Microsoft.WorkloadMonitor/monitors/write | 리소스에 대한 모니터를 구성합니다. |
> | 실행력 | Microsoft.WorkloadMonitor/notificationSettings/read | 리소스에 대한 알림 설정을 가져옵니다. |
> | 실행력 | Microsoft.WorkloadMonitor/notificationSettings/write | 리소스에 대한 알림 설정을 구성합니다. |
> | 실행력 | Microsoft.WorkloadMonitor/operations/read | 지원되는 작업을 가져옵니다. |

## <a name="next-steps"></a>다음 단계

- [리소스 공급자와 서비스 일치](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)
- [Azure 리소스에 대한 기본 제공 역할](built-in-roles.md)
