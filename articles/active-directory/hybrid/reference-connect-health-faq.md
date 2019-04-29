---
title: Azure Active Directory Connect Health FAQ - Azure | Microsoft Docs
description: 이 FAQ는 Azure AD Connect Health에 대한 질문에 답변합니다. 이 FAQ는 요금 청구 모델, 기능, 제한 및 지원을 포함한 서비스 사용에 대한 질문을 다룹니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec88caafa9a6168860a8e9e2ff9e2abe0cfd0e77
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096124"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health에 대한 질문과 대답
이 문서에는 Azure AD(Azure Active Directory) Connect Health에 대한 FAQ(질문과 대답)가 포함되어 있습니다. 이 FAQ에서는 청구 모델, 기능, 제한 및 지원을 포함한 서비스 사용 방법에 대해 다룹니다.

## <a name="general-questions"></a>일반적인 질문
**Q: 여러 Azure AD 디렉터리를 관리하고 있습니다. Azure Active Directory Premium을 사용하는 디렉터리로 전환하려면 어떻게 해야 하나요?**

오른쪽 위 모서리에 있는 현재 로그인한 **사용자 이름**을 선택하고 적절한 계정을 선택하면 여러 Azure AD 테넌트 간에 전환할 수 있습니다. 계정이 여기에 나열되어 있지 않으면 **로그아웃**을 선택한 다음 Azure Active Directory Premium을 사용하도록 설정된 디렉터리의 글로벌 관리자 자격 증명을 사용하여 로그인합니다.

**Q: Azure AD Connect Health에서 지원하는 ID 역할의 버전은 어떻게 되나요?**

다음 테이블에는 역할 및 지원되는 운영 체제 버전이 나열되어 있습니다.

|역할| 운영 체제/버전|
|--|--|
|AD FS(Active Directory Federation Services)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | 버전 1.0.9125 이상|
|AD DS(Active Directory Domain Services)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

서비스에서 제공하는 기능은 역할 및 운영 체제에 따라 다를 수 있습니다. 즉, 운영 체제 버전에 따라 일부 기능이 제공되지 않을 수도 있습니다. 자세한 내용은 기능 설명을 참조하세요.

**Q: 내 인프라를 모니터링하는 데 필요한 라이선스는 몇 개인가요?**

* 첫 번째 Connect Health 에이전트에는 하나 이상의 Azure AD Premium 라이선스가 필요합니다.
* 등록된 추가 에이전트에는 각각 25개의 추가 Azure AD Premium 라이선스가 필요합니다.
* 에이전트 수는 모든 모니터링된 역할(AD FS, Azure AD Connect 및/또는 AD DS)에 등록된 에이전트의 총 수와 같습니다.
* AAD Connect 상태 라이선스의 경우 특정 사용자에게 라이선스를 할당할 필요가 없습니다. 필요한 수의 유효한 라이선스만 있으면 됩니다.

라이선스 정보는 [Azure AD 가격 책정 페이지](https://aka.ms/aadpricing)에서 찾을 수 있습니다.

예제:

| 등록된 에이전트 | 필요한 라이선스 | 모니터링 구성 예제 |
| ------ | --------------- | --- |
| 1 | 1 | Azure AD Connect 서버 1개 |
| 2 | 26| Azure AD Connect 서버 1개 및 도메인 컨트롤러 1개 |
| 3 | 51 | AD FS(Active Directory Federation Services) 서버 1개, AD FS 프록시 1개, 도메인 컨트롤러 1개 |
| 4 | 76 | AD FS 서버 1개, AD FS 프록시 1개, 도메인 컨트롤러 2개 |
| 5 | 101 | Azure AD Connect 서버 1개, AD FS 서버 1개, AD FS 프록시 1개, 도메인 컨트롤러 2개 |

**Q: Azure AD Connect Health에서 Azure 독일 클라우드를 지원하나요?**

[동기화 오류 보고서 기능](how-to-connect-health-sync.md#object-level-synchronization-error-report)을 제외하면 Germany 클라우드에서 Azure AD Connect Health가 지원되지 않습니다.

| 역할 | 기능 | 독일 클라우드에서 지원됨 |
| ------ | --------------- | --- |
| 동기화용 Connect Health | 모니터링/인사이트/경고/분석 | 아닙니다. |
|  | 동기화 오류 보고서 | 예 |
| ADFS용 Connect Health | 모니터링/인사이트/경고/분석 | 아닙니다. |
| ADDS용 Connect Health | 모니터링/인사이트/경고/분석 | 아닙니다. |

동기화용 Connect Health의 에이전트 연결을 보장하려면 그에 따라 [설치 요구 사항](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)을 구성하세요.

## <a name="installation-questions"></a>설치 관련 질문

**Q: Azure AD Connect Health Agent를 개별 서버에 설치하면 어떤 영향이 있나요?**

Microsoft Azure AD Connect Health 에이전트, AD FS, 웹 애플리케이션 프록시 서버, Azure AD Connect(sycn) 서버, 도메인 컨트롤러를 설치해도 CPU, 메모리 사용량, 네트워크 대역폭 및 저장소에는 최소한의 영향만 미칩니다.

다음 숫자는 근사값입니다.

* CPU 사용량: 1-5% 증가
* 메모리 사용량: 전체 시스템 메모리의 최대 10%

> [!NOTE]
> 에이전트가 Azure와 통신할 수 없는 경우 에이전트는 정의된 최대 한도까지 데이터를 로컬로 저장합니다. Agent는 "최소 최근 서비스" 단위로 "캐시된" 데이터를 덮어씁니다.
>
>

* Azure AD Connect Health 에이전트의 로컬 버퍼 저장소: ~20MB.
* AD FS 서버의 경우 모든 감사 데이터를 덮어쓰기 전에 처리하려면 Azure AD Connect Health 에이전트의 AD FS 감사 채널에 1,024MB(1GB)의 디스크 공간을 프로비전하는 것이 좋습니다.

**Q: Azure AD Connect Health Agent를 설치하는 동안 내 서버를 다시 부팅해야 하나요?**

아니요. 에이전트를 설치하는 데 서버를 재부팅할 필요는 없습니다. 그러나 일부 필수 구성 요소 설치 단계에서 서버를 재부팅해야 할 수 있습니다.

예를 들어 Windows Server 2008 R2에 .NET 4.5 Framework를 설치하는 경우 서버를 재부팅해야 합니다.

**Q: Azure AD Connect Health가 통과 HTTP 프록시를 통해 작동하나요?**

예. 진행 중인 작업의 경우 HTTP 프록시를 사용하여 아웃바운드 http 요청을 전달하도록 Health Agent를 구성할 수 있습니다.
[Health 에이전트에 대한 HTTP 프록시 구성](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)을 자세히 읽어보세요.

에이전트를 등록하는 동안 프록시를 구성해야 하는 경우 Internet Explorer 프록시 설정을 미리 수정해야 할 수도 있습니다.

1. Internet Explorer > **설정** > **인터넷 옵션** > **연결** > **LAN 설정**으로 이동합니다.
2. **사용자 LAN의 프록시 서버 사용**을 선택합니다.
3. HTTP 및 HTTPS/보안의 프록시 포트가 다른 경우 **고급**을 선택합니다.

**Q: HTTP 프록시에 연결할 때 Azure AD Connect Health에서 기본 인증을 지원하나요?**

아니요. 기본 인증에 필요한 임의 사용자 이름/암호를 지정하는 메커니즘은 현재 지원되지 않습니다.

**Q: Azure AD Connect Health Agent가 작동하기 위해 열어야 하는 방화벽 포트는 어떻게 되나요?**

방화벽 포트 및 기타 연결 요구 사항 목록은 [요구 사항 섹션](how-to-connect-health-agent-install.md#requirements)을 참조하세요.

**Q: Azure AD Connect Health 포털에서 두 개의 서버가 동일한 이름으로 표시되는 이유는 무엇인가요?**

서버에서 에이전트를 제거해도 해당 서버가 Azure AD Connect Health 포털에서 자동으로 제거되지 않습니다. 서버에서 에이전트를 수동으로 제거하거나 서버 자체를 제거한 경우 Azure AD Connect Health 포털에서 서버 항목을 수동으로 삭제해야 합니다.

서버를 이미지로 다시 설치하거나 같은 세부 정보(예: 컴퓨터 이름)를 사용하여 새 서버를 만들 수 있습니다. Azure AD Connect Health 포털에서 이미 등록된 서버를 제거하지 않고 새 서버에 에이전트를 설치하면 이름이 같은 두 개의 항목이 표시될 수 있습니다.

이 경우 이전 서버에 소속된 항목을 수동으로 삭제해야 합니다. 이 서버에 대한 데이터는 오래된 정보입니다.

## <a name="health-agent-registration-and-data-freshness"></a>Health Agent 등록 및 데이터 새로 고침

**Q: Health Agent 등록 오류가 발생하는 일반적인 원인은 무엇이며 어떻게 해결해야 하나요?**

Health Agent는 다음과 같은 원인으로 등록에 실패할 수 있습니다.

* 방화벽이 트래픽을 차단하고 있어서 에이전트가 필수 엔드포인트와 통신할 수 없습니다. 특히 웹 애플리케이션 프록시 서버에서 자주 발생하는 문제입니다. 필수 엔드포인트 및 포트에 아웃바운드 통신을 허용해야 합니다. 자세한 내용은 [요구 사항 섹션](how-to-connect-health-agent-install.md#requirements)을 참조하세요.
* 아웃바운드 통신은 네트워크 계층에서 SSL 검사를 받습니다. 이로 인해 에이전트에서 사용하는 인증서가 검사 서버/엔터티로 교체되고, 에이전트 등록을 완료하는 단계가 실패합니다.
* 사용자는 에이전트의 등록을 수행하기 위한 액세스 권한이 없습니다. 전역 관리자는 기본적으로 액세스 권한이 있습니다. [역할 기반 Access Control](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)을 사용하여 다른 사용자에 대한 액세스를 위임할 수 있습니다.

**Q: "상태 관리 서비스의 데이터가 최신 상태가 아닙니다."라는 경고 메시지가 표시됩니다. 이 문제를 어떻게 해결하나요?**

Azure AD Connect Health는 2시간 동안 서버에서 데이터 지점을 수신하지 않으면 이 경고를 생성합니다. [자세히 알아보기](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>작업 관련 질문
**Q: 웹 애플리케이션 프록시 서버에서 감사를 사용하도록 설정해야 하나요?**

아니요, 웹 애플리케이션 프록시 서버에서 감사를 사용할 필요가 없습니다.

**Q: Azure AD Connect Health 경고는 어떻게 해결하나요?**

Azure AD Connect Health 경고는 성공 조건에서 해결됩니다. Azure AD Connect Health 에이전트가 정기적으로 성공 조건을 검색하여 서비스에 보고합니다. 일부 경고의 경우 시간을 기준으로 경고가 제거됩니다. 즉, 동일한 오류 조건이 경고 생성으로부터 72시간 내에 관찰되지 않으면 경고가 자동으로 해결됩니다.

**Q: "테스트 인증 요청(가상 트랜잭션)에서 토큰을 가져오지 못했습니다."라는 경고 메시지가 표시됩니다. 이 문제를 어떻게 해결하나요?**

AD FS용 Azure AD Connect Health는 AD FS 서버에 설치된 상태 에이전트가 상태 에이전트에서 시작되는 가상 트랜잭션의 일부로 토큰을 가져오는 데 실패한 경우 이 경고를 생성합니다. 상태 에이전트는 로컬 시스템 컨텍스트를 사용하고 자체 신뢰 당사자에 대한 토큰을 가져오려고 합니다. 이는 AD FS가 토큰을 발급 중인 상태인지 확인하는 범용 테스트입니다.

종종 이 테스트는 상태 에이전트가 AD FS 팜 이름을 확인할 수 없기 때문에 실패합니다. 이는 AD FS 서버가 네트워크 부하 분산 장치 뒤에 있고 요청이 부하 분산 장치 뒤에 있는 노드에서 시작하는 경우 발생할 수 있습니다(부하 분산 장치 앞에 있는 일반 클라이언트와는 대조적으로). 이는 AD FS 팜 이름(예: sts.contoso.com)에 대해 AD FS 서버의 IP 주소 또는 루프백 IP 주소(127.0.0.1)를 포함하도록 "C:\Windows\System32\drivers\etc" 아래에 있는 "hosts" 파일을 업데이트하여 해결할 수 있습니다. 호스트 파일을 추가하면 네트워크 호출을 단락(short-circuit)하므로 상태 에이전트에서 토큰을 가져올 수 있도록 합니다.

**Q: 내 컴퓨터가 최근 랜 섬 웨어 공격에 대 한 패치가 적용 되지 않은 되었음을 나타내는 전자 메일이 받았습니다. 이 전자 메일을 받은 이유는 무엇인가요?**

Azure AD Connect Health 서비스는 필요한 패치가 설치되었는지 확인하기 위해 모니터링하는 모든 컴퓨터를 검색했습니다. 하나 이상의 컴퓨터에 중요한 패치가 없는 경우 테넌트 관리자에게 전자 메일이 전송되었습니다. 이를 결정하기 위해 다음 논리가 사용되었습니다.
1. 컴퓨터에 설치된 모든 핫픽스를 찾습니다.
2. 정의된 목록에서 핫픽스 중 하나 이상이 있는지 확인합니다.
3. 있는 경우 컴퓨터가 보호되고 있습니다. 그렇지 않은 경우 컴퓨터가 공격에 대해 위험에 노출됩니다.

다음 PowerShell 스크립트를 사용하여 이 검사를 수동으로 수행할 수 있습니다. 위의 논리를 구현합니다.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**Q: 결과에서 <i>Get-MsolDirSyncProvisioningError</i> PowerShell cmdlet의 동기화 오류가 적게 나타나는 이유는 무엇인가요?**

<i>Get-MsolDirSyncProvisioningError</i>는 DirSync 프로비전 오류를 반환합니다. 뿐만 아니라 Connect Health 포털에는 내보내기 오류와 같은 다른 동기화 오류 형식이 표시됩니다. Azure AD Connect 델타 결과와 일치합니다. [Azure AD Connect 동기화 오류](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)에 대해 자세히 알아봅니다.

**Q: 내 ADFS 감사가 생성되지 않는 이유는 무엇인가요?**

PowerShell cmdlet <i>Get-AdfsProperties -AuditLevel</i>을 사용하여 감사 로그가 사용하지 않음 상태가 되도록 합니다. [ADFS 감사 로그](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016)에 대해 자세히 알아보세요. ADFS 서버에 푸시된 고급 감사 설정이 있을 경우 auditpol.exe 관련 변경 내용이 덮어써진다는 점을 참고하세요(애플리케이션 생성됨이 구성되지 않은 경우의 이벤트). 이 경우 애플리케이션 생성됨 실패 및 성공을 기록하도록 로컬 보안 정책을 설정하세요.

**Q: 에이전트 인증서 자동 갱신된 전에 만료를 언제 게 되나요?**
에이전트 인증 수 자동 갱신 **6 개월** 만료 날짜 전에 합니다. 갱신 하지 않으면 에이전트의 네트워크 연결이 안정적이 지 확인 하세요. 에이전트 서비스를 다시 시작 하거나 최신 버전으로 업데이트는 문제를 해결할 수도 수 있습니다.


## <a name="related-links"></a>관련 링크
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health 에이전트 설치](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health 작업](how-to-connect-health-operations.md)
* [AD FS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adfs.md)
* [동기화에 대한 Azure AD Connect Health 사용](how-to-connect-health-sync.md)
* [AD DS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adds.md)
* [Azure AD Connect Health 버전 내역](reference-connect-health-version-history.md)
