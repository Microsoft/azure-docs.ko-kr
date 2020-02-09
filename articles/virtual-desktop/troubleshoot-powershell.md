---
title: Windows 가상 데스크톱 PowerShell-Azure
description: Windows 가상 데스크톱 테넌트 환경을 설정할 때 PowerShell 관련 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 4e628657f0c2519595ee0fd3c4f845e2e637f27e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975556"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

Windows 가상 데스크톱과 함께 PowerShell을 사용 하는 경우 오류 및 문제를 해결 하려면이 문서를 사용 합니다. 원격 데스크톱 서비스 PowerShell에 대 한 자세한 내용은 [Windows 가상 데스크톱 PowerShell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/)을 참조 하세요.

## <a name="provide-feedback"></a>피드백 제공하기

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 가상 데스크톱 설치 중에 사용 되는 PowerShell 명령

이 섹션에서는 Windows 가상 데스크톱을 설정 하는 동안 일반적으로 사용 되는 PowerShell 명령을 나열 하 고이를 사용 하는 동안 발생할 수 있는 문제를 해결 하는 방법을 제공 합니다.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>오류: RdsAppGroupUser 명령-지정 된 UserPrincipalName가 지정 된 호스트 풀의 RemoteApp 앱 그룹에 이미 할당 되어 있습니다.

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**원인:** 사용 된 사용자 이름이 다른 유형의 앱 그룹에 이미 할당 되었습니다. 동일한 세션 호스트 풀에서 원격 데스크톱 및 원격 앱 그룹에 사용자를 할당할 수 없습니다.

**해결 방법:** 사용자가 원격 앱과 원격 데스크톱이 모두 필요한 경우 다른 호스트 풀을 만들거나 원격 데스크톱에 대 한 사용자 액세스 권한을 부여 하 여 세션 호스트 VM에서 모든 응용 프로그램을 사용 하도록 허용 합니다.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>오류: RdsAppGroupUser 명령--지정 된 UserPrincipalName가 원격 데스크톱 테넌트와 연결 된 Azure Active Directory에 없습니다.

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**원인:** -UserPrincipalName로 지정 된 사용자는 Windows 가상 데스크톱 테넌트에 연결 된 Azure Active Directory에서 찾을 수 없습니다.

**해결 방법:** 다음 목록에서 항목을 확인 합니다.

- 사용자가 Azure Active Directory와 동기화 됩니다.
- 사용자가 B2C (business to consumer) 또는 B2B (기업 간) 상거래에 연결 되지 않았습니다.
- Windows 가상 데스크톱 테넌트가 올바른 Azure Active Directory 연결 되어 있습니다.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>오류: RdsDiagnosticActivities--사용자에 게 관리 서비스를 쿼리할 수 있는 권한이 없습니다.

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**원인:** -tenantname 매개 변수

**해결 방법:** RdsDiagnosticActivities 이름 \<TenantName >를 사용 하 여 Get-help를 실행 합니다.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>오류: RdsDiagnosticActivities--사용자에 게 관리 서비스를 쿼리할 수 있는 권한이 없습니다.

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**원인:** -Deployment 스위치를 사용 합니다.

**해결 방법:** 배포 관리자만 배포 스위치를 사용할 수 있습니다. 이러한 관리자는 일반적으로 원격 데스크톱 서비스/Windows 가상 데스크톱 팀의 구성원입니다. -Deployment 스위치를-TenantName \<TenantName >으로 바꿉니다.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>오류: RdsRoleAssignment--사용자에 게 관리 서비스를 쿼리할 수 있는 권한이 없습니다.

**원인 1:** 사용 중인 계정에 테넌트에 대 한 원격 데스크톱 서비스 소유자 권한이 없습니다.

**수정 1:** 원격 데스크톱 서비스 소유자 권한이 있는 사용자는 역할 할당을 실행 해야 합니다.

**원인 2:** 사용 중인 계정에 원격 데스크톱 서비스 소유자 권한이 있지만 테넌트의 Azure Active Directory에 속하지 않거나 사용자가 있는 Azure Active Directory를 쿼리할 수 있는 권한이 없습니다.

**수정 2:** Active Directory 권한이 있는 사용자는 역할 할당을 실행 해야 합니다.

>[!Note]
>RdsRoleAssignment는 AD (Azure Active Directory)에 없는 사용자에 게 권한을 부여할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대 한 개요는 [문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.
- Windows 가상 데스크톱 환경에서 테넌트 및 호스트 풀을 만드는 동안 발생 하는 문제를 해결 하려면 [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows 가상 데스크톱에서 VM (가상 컴퓨터)을 구성 하는 동안 발생 하는 문제를 해결 하려면 [세션 호스트 가상 컴퓨터 구성](troubleshoot-vm-configuration.md)을 참조 하세요.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결 하려면 [Windows 가상 데스크톱 서비스 연결](troubleshoot-service-connection.md)을 참조 하세요.
- 원격 데스크톱 클라이언트와 관련 된 문제를 해결 하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md) 을 참조 하세요.
- 서비스에 대 한 자세한 내용은 [Windows 가상 데스크톱 환경](environment-setup.md)을 참조 하세요.
- 문제 해결 자습서를 진행 하려면 [자습서: 템플릿 배포 리소스 관리자 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조 하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
