---
title: Windows 가상 데스크톱 호스트 풀 Azure Resource Manager-Azure
description: Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱에서 호스트 풀을 만드는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 23d032a2496e975c7e6ceafb61691c2cb1216218
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605760"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿으로 호스트 풀 만들기

호스트 풀은 Windows 가상 데스크톱 테넌트 환경 내에 있는 하나 이상의 동일한 가상 컴퓨터의 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

이 섹션의 지침에 따라 Microsoft에서 제공 하는 Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱 테넌트의 호스트 풀을 만듭니다. 이 문서에서는 Windows 가상 데스크톱에서 호스트 풀을 만들고, Azure 구독의 Vm을 사용 하 여 리소스 그룹을 만들고, 해당 Vm을 AD 도메인에 조인 하 고, Windows 가상 데스크톱에 Vm을 등록 하는 방법을 설명 합니다.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 실행하기 위해 필요한 것

Azure Resource Manager 템플릿을 실행 하기 전에 다음 사항을 알고 있어야 합니다.

- 사용할 이미지의 소스가 인 경우 Azure 갤러리에서 온 것 입니까, 아니면 사용자 지정 인가요?
- 도메인 가입 자격 증명입니다.
- Windows 가상 데스크톱 자격 증명입니다.

Azure Resource Manager 템플릿을 사용 하 여 Windows 가상 데스크톱 호스트 풀을 만드는 경우 Azure 갤러리, 관리 되는 이미지 또는 관리 되지 않는 이미지에서 가상 컴퓨터를 만들 수 있습니다. VM 이미지를 만드는 방법에 대해 자세히 알아보려면 azure에 [업로드할 WINDOWS VHD 또는 VHDX 준비](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 및 [azure에서 일반화 된 VM의 관리 되는 이미지 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)를 참조 하세요.

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>새 호스트 풀을 프로 비전 하기 위해 Azure Resource Manager 템플릿 실행

시작 하려면 [이 GITHUB URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)로 이동 합니다.

### <a name="deploy-the-template-to-azure"></a>Azure에 템플릿 배포

엔터프라이즈 구독에 배포 하는 경우 아래로 스크롤하여 **Azure에 배포**를 선택한 다음 이미지 원본에 따라 매개 변수를 채웁니다.

클라우드 솔루션 공급자 구독에서 배포 하는 경우 다음 단계를 수행 하 여 Azure에 배포 합니다.

1. 아래로 스크롤하여 **Azure에 배포**를 마우스 오른쪽 단추로 클릭한 다음, **링크 위치 복사**를 선택합니다.
2. 메모장 같은 텍스트 편집기를 열고 링크를 붙여넣습니다.
3. "https://portal.azure.com/" 바로 뒤와 해시 태그 (#) 앞에 @ 기호를 입력 하 고 그 다음에 테넌트 도메인 이름을 입력 합니다. 다음은 사용 해야 하는 형식의 예입니다. https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. 클라우드 솔루션 공급자 구독에 대한 관리자/기여자 권한이 있는 사용자로 Azure Portal에 로그인합니다.
5. 텍스트 편집기에 복사한 링크를 주소 표시줄에 붙여넣습니다.

시나리오에 대해 입력 해야 하는 매개 변수에 대 한 지침은 Windows 가상 데스크톱 [추가 정보 파일](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)을 참조 하세요. 추가 정보는 항상 최신 변경 내용으로 업데이트 됩니다.

## <a name="assign-users-to-the-desktop-application-group"></a>데스크톱 응용 프로그램 그룹에 사용자 할당

GitHub Azure Resource Manager 템플릿이 완료 되 면 가상 머신에서 전체 세션 데스크톱 테스트를 시작 하기 전에 사용자 액세스 권한을 할당 합니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우).

데스크톱 응용 프로그램 그룹에 사용자를 할당 하려면 PowerShell 창을 열고이 cmdlet을 실행 하 여 Windows 가상 데스크톱 환경에 로그인 합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

그런 다음이 cmdlet을 사용 하 여 데스크톱 응용 프로그램 그룹에 사용자를 추가 합니다.

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

사용자의 UPN은 Azure Active Directory의 사용자 ID와 일치해야 합니다(예: user1@contoso.com). 여러 사용자를 추가하려는 경우 각 사용자에 대해 이 cmdlet을 실행해야 합니다.

이러한 단계를 완료하면 데스크톱 애플리케이션 그룹에 추가된 사용자가 지원되는 원격 데스크톱 클라이언트를 사용하여 Windows Virtual Desktop에 로그인하고 세션 데스크톱에 대한 리소스를 확인할 수 있습니다.

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)를 사용하는 것이 좋습니다.