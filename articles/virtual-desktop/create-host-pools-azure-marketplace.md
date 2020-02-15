---
title: Windows Virtual Desktop 호스트 풀 Azure Marketplace - Azure
description: Azure Marketplace를 사용하여 Windows Virtual Desktop 호스트 풀을 만드는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: 25dd4810cf8cccab8bcbf211da4f6abbcd147056
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020032"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>자습서: Azure Marketplace를 사용하여 호스트 풀 만들기

호스트 풀은 Windows Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

이 자습서에서는 Microsoft Azure Marketplace 제품을 사용하여 Windows Virtual Desktop 테넌트 내에서 호스트 풀을 만드는 방법을 설명합니다. 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Windows Virtual Desktop에서 호스트 풀 만들기
> * Azure 구독에서 VM으로 리소스 그룹 만들기
> * VM을 Active Directory 도메인에 조인
> * VM을 Windows Virtual Desktop에 등록

시작하기 전에 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 없는 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>새 호스트 풀을 프로비저닝하기 위해 Azure Marketplace 제품 실행

새 호스트 풀을 프로비저닝하기 위해 Azure Marketplace 제품을 실행하려면 다음을 수행합니다.

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.
2. Marketplace 검색 창에 **Windows Virtual Desktop**을 입력합니다.
3. **Windows Virtual Desktop - 호스트 풀 프로비전**을 선택한 다음, **만들기** 선택합니다.

그런 후, 다음 섹션의 지침에 따라 적절한 블레이드에 대한 정보를 입력합니다.

### <a name="basics"></a>기본 사항

**기본 사항** 블레이드에서 수행해야 하는 내용은 다음과 같습니다.

1. Windows Virtual Desktop 테넌트 내에서 고유한 호스트 풀의 이름을 입력합니다.
2. 개인 데스크톱에 적합한 옵션을 선택합니다. **예**를 선택하는 경우 이 호스트 풀에 연결하는 각 사용자가 가상 머신에 영구적으로 할당됩니다.
3. Windows Virtual Desktop 클라이언트에 로그인하고 Azure Marketplace 제품이 완료된 후 데스크톱에 액세스할 수 있는 사용자의 목록을 쉼표로 구분된 형식으로 입력합니다. 예를 들어 user1@contoso.com 및 user2@contoso.com 액세스를 할당하려면 “user1@contoso.com,user2@contoso.com”을 입력합니다.
4. **새로 만들기**를 선택하고 새 리소스 그룹의 이름을 입력합니다.
5. **위치**의 경우 Active Directory 서버에 연결되어 있는 가상 네트워크와 동일한 위치를 선택합니다.
6. **다음: 가상 머신 구성 >** 을 선택합니다.

>[!IMPORTANT]
>순수 Azure Active Directory Domain Services 및 Azure Active Directory 솔루션을 사용하는 경우 도메인 가입 및 자격 증명 오류를 방지하기 위해 Azure Active Directory Domain Services와 동일한 지역에 호스트 풀을 배포해야 합니다.

### <a name="configure-virtual-machines"></a>가상 머신 구성

**가상 머신 구성** 블레이드의 경우:

1. 기본값을 그대로 적용하거나 VM의 수 및 크기를 사용자 지정합니다.
2. 가상 머신의 이름에 사용할 접두사를 입력합니다. 예를 들어, “prefix”라는 이름을 입력하는 경우 가상 머신은 “prefix-0”, “prefix-1” 등으로 불립니다.
3. **다음: 가상 머신 설정**을 선택합니다.

### <a name="virtual-machine-settings"></a>가상 머신 설정

**가상 머신 설정** 블레이드의 경우:

>[!NOTE]
> Azure AD DS(Azure Active Directory Domain Services) 환경에 VM을 조인하는 경우 도메인 가입 사용자가 [AAD DC 관리자 그룹](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)의 멤버인지 확인합니다.
>
> 또한 이 계정은 Azure AD DS 관리형 도메인 또는 Azure AD 테넌트의 일부여야 합니다. 즉, Azure AD 테넌트와 연결된 외부 디렉터리의 계정은 도메인 가입 프로세스 중에 올바르게 인증되지 않습니다. 

1. **이미지 원본**의 경우 원본을 선택하고 원본을 찾고 저장할 방법에 대한 적절한 정보를 입력합니다. 관리 디스크를 사용하지 않으려면 .vhd 파일이 포함된 스토리지 계정을 선택합니다.
2. Active Directory 도메인에 VM을 조인하는 도메인 계정에 대한 사용자 계정 이름 및 암호를 입력합니다. 이 동일한 사용자 이름과 암호는 로컬 계정으로 가상 머신에서 생성됩니다. 이러한 로컬 계정은 나중에 다시 설정할 수 있습니다.
3. Active Directory 서버에 연결되어 있는 가상 네트워크를 선택한 다음, 가상 머신을 호스트하는 서브넷을 선택합니다.
4. 완료되면 **다음: Windows Virtual Desktop 테넌트 정보**를 선택합니다.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows Virtual Desktop 테넌트 정보

**Windows Virtual Desktop 테넌트 정보** 블레이드의 경우:

1. **Windows Virtual Desktop 테넌트 그룹 이름**의 경우, 테넌트를 포함하는 테넌트 그룹의 이름을 입력합니다. 특정 테넌트 그룹 이름이 제공되지 않은 경우 기본값을 그대로 유지합니다.
2. **Windows Virtual Desktop 테넌트 이름**의 경우, 이 호스트 풀을 만들 테넌트의 이름을 입력합니다.
3. Windows Virtual Desktop 테넌트 RDS 소유자로 인증하는 데 사용할 자격 증명 유형을 지정합니다. [PowerShell 자습서를 사용하여 서비스 주체 및 역할 할당 만들기](./create-service-principal-role-powershell.md)를 완료했으면 **서비스 주체**를 선택합니다. **Azure AD 테넌트 ID**가 나타나면, 서비스 주체를 포함하는 Azure Active Directory 인스턴스의 ID를 입력합니다.
4. 테넌트 관리자 계정에 사용할 자격 증명을 입력합니다. 암호 자격 증명을 사용하는 서비스 주체만 지원됩니다.
5. **다음: 리뷰 + 만들기**를 클릭합니다.

## <a name="complete-setup-and-create-the-virtual-machine"></a>설치 완료 및 가상 머신 만들기

마지막 두 블레이드의 경우:

1. **검토 및 만들기** 블레이드에서 설치 정보를 검토합니다. 변경해야 할 경우 계속하기 전에 적절한 블레이드로 돌아가서 해당 내용을 변경합니다. 정보가 올바르면 **확인**을 선택합니다.
2. **만들기**를 선택하여 호스트 풀을 배포합니다.

만들 VM 개수에 따라 이 프로세스를 완료하는 데 30분 이상 걸릴 수 있습니다.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(선택 사항) 데스크톱 애플리케이션 그룹에 추가 사용자 할당

Azure Marketplace 제품이 완료된 후, 가상 머신에서 전체 세션 데스크톱을 테스트하기 전에 데스크톱 애플리케이션 그룹에 사용자를 더 할당할 수 있습니다. Azure Marketplace 제품에 이미 기본 사용자를 추가했고 더 추가하고 싶지 않다면 이 섹션을 건너뛸 수 있습니다.

데스크톱 애플리케이션 그룹에 사용자를 할당하려면 먼저 PowerShell 창을 열어야 합니다. 그 후, 다음 두 개의 cmdlet을 입력해야 합니다.

다음 cmdlet을 실행하여 Windows Virtual Desktop 환경에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

다음 cmdlet을 사용하여 데스크톱 애플리케이션 그룹에 사용자를 추가합니다.

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

사용자의 UPN은 Azure Active Directory의 사용자 ID와 일치해야 합니다(예: user1@contoso.com). 여러 사용자를 추가하려는 경우 각 사용자에 대해 이 cmdlet을 실행해야 합니다.

이러한 단계를 완료하면 데스크톱 애플리케이션 그룹에 추가된 사용자가 지원되는 원격 데스크톱 클라이언트를 사용하여 Windows Virtual Desktop에 로그인하고 세션 데스크톱에 대한 리소스를 확인할 수 있습니다.

현재 지원되는 클라이언트는 다음과 같습니다.

- [Windows 7 및 Windows 10용 원격 데스크톱 클라이언트](connect-windows-7-and-10.md)
- [Windows Virtual Desktop 웹 클라이언트](connect-web.md)

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

호스트 풀을 만들고 해당 데스크톱에 액세스할 사용자를 할당했으면, RemoteApp 프로그램을 사용하여 호스트 풀을 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대해 알아보려면 다음 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
