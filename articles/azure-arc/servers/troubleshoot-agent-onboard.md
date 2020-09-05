---
title: Azure Arc 사용 서버 (미리 보기) 에이전트 연결 문제 해결
description: 이 문서에서는 서비스에 연결 하려고 할 때 Azure Arc 사용 서버 (미리 보기)에서 발생 하는 연결 된 컴퓨터 에이전트와 관련 된 문제를 해결 하는 방법을 설명 합니다.
ms.date: 07/20/2020
ms.topic: conceptual
ms.openlocfilehash: aeb370fb6cd4eacf20c1acf29e84c03e5e322d39
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213549"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>연결 된 컴퓨터 에이전트 연결 문제 해결

이 문서에서는 Windows 또는 Linux 용 Azure Arc 사용 서버 (미리 보기) 연결 된 컴퓨터 에이전트를 구성 하는 동안 발생할 수 있는 문제를 해결 하 고 해결 하는 방법에 대 한 정보를 제공 합니다. 서비스에 대 한 연결을 구성 하는 경우 대화형 및 확장 설치 방법 모두 포함 됩니다. 일반 정보는 [Arc 사용 서버 개요](./overview.md)를 참조 하세요.

## <a name="agent-verbose-log"></a>에이전트 자세한 정보 표시 로그

이 문서의 뒷부분에 설명 된 문제 해결 단계를 수행 하기 전에 필요한 최소 정보는 자세한 로그입니다. Verbose (-v) 인수를 사용 하는 경우 **azcmagent** tool 명령의 출력을 포함 합니다. 로그 파일은 `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` Windows 및 Linux 용에 기록 됩니다 `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

다음은 대화형 설치를 수행할 때 Windows 용 연결 된 컴퓨터 에이전트에서 자세한 정보 로깅을 사용 하도록 설정 하는 명령의 예입니다.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

다음은 서비스 주체를 사용 하 여 스케일 아웃 설치를 수행할 때 Windows 용 연결 된 컴퓨터 에이전트에서 자세한 정보 로깅을 사용 하도록 설정 하는 명령의 예입니다.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

다음은 대화형 설치를 수행할 때 Linux 용 연결 된 컴퓨터 에이전트에서 자세한 정보 로깅을 사용 하도록 설정 하는 명령의 예입니다.

>[!NOTE]
>**Azcmagent**를 실행 하려면 Linux 컴퓨터에 대 한 *루트* 액세스 권한이 있어야 합니다.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

다음은 서비스 주체를 사용 하 여 확장 설치를 수행할 때 Linux 용 연결 된 컴퓨터 에이전트에서 자세한 정보 로깅을 사용 하도록 설정 하는 명령의 예입니다.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>서비스에 대 한 에이전트 연결 문제

다음 표에는 알려진 오류 및 문제 해결 방법에 대 한 제안 사항이 정리 되어 있습니다.

|메시지 |오류 |가능한 원인: |해결 방법 |
|--------|------|---------------|---------|
|권한 부여 토큰 장치 흐름을 획득 하지 못했습니다. |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |끝점에 연결할 수 없음 `login.windows.net` | 끝점에 대 한 연결을 확인 합니다. |
|권한 부여 토큰 장치 흐름을 획득 하지 못했습니다. |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |프록시 또는 방화벽에서 끝점에 대 한 액세스를 차단 하 고 `login.windows.net` 있습니다. | 끝점에 대 한 연결을 확인 하 고 방화벽이 나 프록시 서버에 의해 차단 되지 않습니다. |
|권한 부여 토큰 장치 흐름을 획득 하지 못했습니다.  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | *시스템 다시 시작 시 지정 된 기간 (일) 보다 오래 된 사용자 프로필을 삭제 하는 그룹 정책 개체 컴퓨터 구성 \ 관리 템플릿 \ 사용자 프로필* 을 사용 합니다. | GPO가 사용 하도록 설정 되어 있고 영향을 받는 컴퓨터를 대상으로 하는지 확인 합니다. 자세한 내용은 각주 <sup>[1](#footnote1)</sup> 을 참조 하세요. |
|SPN에서 권한 부여 토큰을 획득 하지 못했습니다. |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |프록시 또는 방화벽에서 끝점에 대 한 액세스를 차단 하 고 `login.windows.net` 있습니다. |끝점에 대 한 연결을 확인 하 고 방화벽이 나 프록시 서버에 의해 차단 되지 않습니다. |
|SPN에서 권한 부여 토큰을 획득 하지 못했습니다. |`Invalid client secret is provided` |잘못 되었거나 잘못 된 서비스 주체 암호입니다. |서비스 주체 암호를 확인 합니다. |
| SPN에서 권한 부여 토큰을 획득 하지 못했습니다. |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |서비스 주체 및/또는 테 넌 트 ID가 잘못 되었습니다. |서비스 주체 및/또는 테 넌 트 ID를 확인 합니다.|
|ARM 리소스 응답 가져오기 |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |잘못 된 자격 증명 및/또는 사용 권한 |사용자 또는 서비스 주체가 **Azure 연결 된 컴퓨터** 등록 역할의 멤버 인지 확인 합니다. |
|ARM 리소스를 AzcmagentConnect 못했습니다. |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure 리소스 공급자가 등록 되지 않았습니다. |[리소스 공급자](./agent-overview.md#register-azure-resource-providers)를 등록 합니다. |
|ARM 리소스를 AzcmagentConnect 못했습니다. |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |프록시 서버 또는 방화벽에서 끝점에 대 한 액세스를 차단 하 고 `management.azure.com` 있습니다. |끝점에 대 한 연결을 확인 하 고 방화벽이 나 프록시 서버에 의해 차단 되지 않습니다. |

<a name="footnote1"></a><sup>1</sup> 이 GPO가 사용 하도록 설정 되어 있고 연결 된 컴퓨터 에이전트를 사용 하는 컴퓨터에 적용 되는 경우 *himds* 서비스에 지정 된 기본 제공 계정과 연결 된 사용자 프로필을 삭제 합니다. 따라서 30 일간 로컬 인증서 저장소에 캐시 된 서비스와 통신 하는 데 사용 되는 인증 인증서도 삭제 합니다. 30 일 제한 이전에는 인증서를 갱신 하려고 시도 했습니다. 이 문제를 해결 하려면 [컴퓨터의 등록을 취소](manage-agent.md#unregister-machine) 한 다음를 실행 하는 서비스에 다시 등록 하는 단계를 따르세요 `azcmagent connect` .

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Microsoft Q&A](/answers/topics/azure-arc.html)를 통해 Azure 전문가 로부터 답변을 받으세요.

* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.

* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기**를 선택합니다.
