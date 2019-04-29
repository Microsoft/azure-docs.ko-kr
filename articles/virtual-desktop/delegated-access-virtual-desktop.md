---
title: Windows 가상 데스크톱 미리 보기-Azure에서에서 위임 된 액세스
description: 예를 비롯 한 가상 데스크톱 미리 보기 Windows 배포에서 관리 기능을 위임 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 250aea52de63a6397ce00e9cadcadf3a8ba39858
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870509"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Windows 가상 데스크톱 미리 보기에서 위임 된 액세스

Windows 가상 데스크톱 미리 보기에는 위임 된 액세스 모델이 정도의 액세스는 특정 사용자가 역할을 할당 하 여 수를 정의할 수 있습니다. 역할 할당은 세 가지 구성 요소: 보안 주체, 역할 정 및 범위입니다. Windows 가상 데스크톱 위임 된 액세스 모델은 Azure RBAC 모델을 기반으로 합니다. 특정 역할 할당 및 해당 구성 요소에 대 한 자세한 내용은 참조 하세요 [Azure 역할 기반 access control 개요](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)합니다.

Windows 가상 데스크톱 액세스 지원 역할 할당의 각 요소에 대해 다음 값을 위임 합니다.

* 보안 주체
    * 사용자
    * 서비스 원칙
* 역할 정의
    * 기본 제공 역할
* 범위
    * 테 넌 트 그룹
    * 테 넌 트
    * 호스트 풀
    * 앱 그룹

## <a name="built-in-roles"></a>기본 제공 역할

Windows 가상 데스크톱에 위임 된 액세스에 사용자 및 서비스 주체를 할당할 수 있는 몇 가지 기본 제공 역할 정의가 있습니다.

* RDS 소유자를 리소스에 대 한 액세스를 제외한 모든 것을 관리할 수 있습니다.
* RDS 참가자는 리소스에 대 한 액세스를 제외한 모든 항목을 관리할 수 있습니다.
* RDS 판독기는 모든 항목을 볼 수 있지만 변경할 수 없습니다.
* RDS 연산자 진단 활동을 볼 수 있습니다.

## <a name="powershell-cmdlets-for-role-assignments"></a>역할 할당에 대 한 PowerShell cmdlet

만들기, 보기 및 역할 할당을 편집 하려면 다음 cmdlet을 실행할 수 있습니다.

* **Get-RdsRoleAssignment** 역할 할당 목록을 표시 합니다.
* **새 RdsRoleAssignment** 새 역할 할당을 만듭니다.
* **집합 RdsRoleAssignment** 역할 할당을 편집 합니다.

### <a name="accepted-parameters"></a>허용 된 매개 변수

다음 매개 변수를 사용 하 여 세 개의 기본 cmdlet을 수정할 수 있습니다.

* **AadTenantId**: 서비스 사용자가 멤버인은 Azure Active Directory 테 넌 트 ID를 지정 합니다.
* **AppGroupName**: 원격 데스크톱 앱 그룹의 이름입니다.
* **진단**: 진단 범위를 나타냅니다. (사용 하 여 쌍으로 연결할 수 있어야 합니다 **인프라** 또는 **테 넌 트** 매개 변수.)
* **HostPoolName**: 원격 데스크톱 호스트 풀의 이름입니다.
* **인프라**: 인프라 범위를 나타냅니다.
* **RoleDefinitionName**: 사용자, 그룹 또는 앱에 할당 된 원격 데스크톱 서비스 역할 기반 액세스 제어 역할의 이름입니다. (예: 원격 데스크톱 서비스 소유자, 원격 데스크톱 서비스 판독기 및 등.)
* **ServerPrincipleName**: Azure Active Directory 응용 프로그램의 이름입니다.
* **SignInName**: 사용자의 전자 메일 주소 또는 사용자 계정 이름입니다.
* **TenantName**: 원격 데스크톱 테 넌 트의 이름입니다.

## <a name="next-steps"></a>다음 단계

각 역할에서 사용할 수는 PowerShell cmdlet의 자세한 목록에 대 한 참조를 [PowerShell 참조](/powershell/windows-virtual-desktop/overview)합니다.

Windows 가상 데스크톱 환경을 설정 하는 방법에 대 한 지침을 참조 하세요 [Windows 가상 데스크톱 미리 보기 환경](environment-setup.md)합니다.
