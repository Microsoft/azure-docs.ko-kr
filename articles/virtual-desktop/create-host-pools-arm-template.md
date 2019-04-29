---
title: Azure-Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱 미리 보기 호스트 풀 만들기
description: Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱 미리 보기에서 호스트 풀을 만드는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: ba98328002cafbcede855b1187881d39f1de8fc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870560"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿으로 호스트 풀 만들기

호스트 풀은 Windows Virtual Desktop 미리 보기 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

Microsoft에서 제공 하는 Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱 테 넌 트에 대 한 호스트 풀을 만들려면이 섹션의이 지침을 따릅니다. 이 문서에서는 Windows 가상 데스크톱에 호스트 풀을 만듭니다, Azure 구독에서 Vm을 사용 하 여 리소스 그룹 만들기, AD 도메인에 Vm 가입 및 Windows 가상 데스크톱을 사용 하 여 Vm을 등록 하는 방법을 알려줍니다.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 실행 해야 하는 항목

Azure Resource Manager 템플릿을 실행 하기 전에 다음 작업을 알고 있는지 확인 합니다.

- 여기서 사용 하려는 이미지의 원본은입니다. Azure 갤러리에서 되었거나 사용자 지정은?
- 도메인 가입 자격 증명입니다.
- 가상 데스크톱 Windows 자격 증명입니다.

Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱 호스트 풀을 만들 때 Azure 갤러리에서 관리 되는 이미지 또는 관리 되지 않는 이미지에서 가상 컴퓨터를 만들 수 있습니다. VM 이미지를 만드는 방법에 대 한 자세한 내용은 참조 하세요 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 하 고 [Azure에서 일반화 된 VM의 관리 이미지 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)합니다.

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>새 호스트 풀을 프로 비전을 위한 Azure Resource Manager 템플릿을 실행합니다

를 시작 하려면로 이동 [이 GitHub URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)합니다.

### <a name="deploy-the-template-to-azure"></a>Azure에 템플릿 배포

Enterprise 구독에 배포 하는 경우 아래로 스크롤하여 선택 **Azure에 배포**, 후 이미지 원본에 따라 미리 작성 매개 변수를 건너뜁니다.

클라우드 솔루션 공급자 구독에 배포 하는 경우 Azure에 배포 하려면 다음이 단계를 수행 합니다.

1. 아래로 스크롤하여 마우스 오른쪽 단추로 클릭 **Azure에 배포**을 선택한 후 **복사 링크 위치**합니다.
2. 메모장과 같은 텍스트 편집기를 열고 링크를 붙여 넣습니다.
3. 바로 뒤 "https://portal.azure.com/" 해시 태그 (#) 하기 전에 다음을 입력 하 고는 at 기호 (@) 뒤에 테 넌 트 도메인 이름입니다. 다음은 사용 해야 하는 형식의 예: https://portal.azure.com/@Contoso.onmicrosoft.com#create/합니다.
4. Azure portal에 클라우드 솔루션 공급자 구독 관리자/참가자 권한 가진 사용자로 로그인 합니다.
5. 주소 표시줄에 텍스트 편집기에 복사한 링크를 붙여 넣습니다.

Windows 가상 데스크톱이 표시는 매개 변수에 대 한 시나리오에 대 한 입력 해야 하는 지침을 보려면 [Readme 파일](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)합니다. 추가 정보는 항상 최신 변경 내용으로 업데이트 됩니다.

## <a name="assign-users-to-the-desktop-application-group"></a>데스크톱 응용 프로그램 그룹에 사용자 할당

GitHub의 Azure Resource Manager 템플릿을 완료 된 후 가상 머신에서 전체 세션 데스크톱을 테스트 하려면 먼저 사용자 액세스를 할당 합니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우).

데스크톱 응용 프로그램 그룹에 사용자를 할당 하려면 PowerShell 창을 열고 Windows 가상 데스크톱 환경에 로그인 하려면이 cmdlet을 실행 합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

다음으로이 cmdlet 사용 하 여 Azure Resource Manager 템플릿에 지정 된 테 넌 트 그룹에 컨텍스트를 설정:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

그런 다음이 cmdlet 사용 하 여 데스크톱 응용 프로그램 그룹에 사용자를 추가 합니다.

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

사용자의 UPN은 Azure Active Directory의 사용자 ID와 일치해야 합니다(예: user1@contoso.com). 여러 사용자를 추가하려는 경우 각 사용자에 대해 이 cmdlet을 실행해야 합니다.

이러한 단계를 완료하면 데스크톱 애플리케이션 그룹에 추가된 사용자가 지원되는 원격 데스크톱 클라이언트를 사용하여 Windows Virtual Desktop에 로그인하고 세션 데스크톱에 대한 리소스를 확인할 수 있습니다.

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time)를 사용하는 것이 좋습니다.