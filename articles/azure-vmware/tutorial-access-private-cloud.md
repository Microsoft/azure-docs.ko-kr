---
title: 자습서 - 프라이빗 클라우드에 액세스하는 방법 알아보기
description: Azure VMware Solution 프라이빗 클라우드에 액세스하는 방법 알아보기
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 73226c6aa567dc5fbe18251bed4812637664a02c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750540"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-private-cloud"></a>자습서: Azure VMware Solution 프라이빗 클라우드에 액세스하는 방법 알아보기

미리 보기 중에 Azure VMware Solution을 사용하면 온-프레미스 vCenter에서 프라이빗 클라우드를 관리할 수 없습니다. Jumpbox를 통해 추가 설정 및 로컬 vCenter 인스턴스에 연결해야 합니다. 

이 자습서에서는 이전의 [자습서: Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성](tutorial-configure-networking.md) 자습서에서 만든 리소스 그룹의 점프 상자에 대한 Windows 가상 머신을 만들고, vCenter에 로그인합니다. 이는 사용자가 만든 것과 동일한 가상 네트워크의 VM이며, vCenter 및 NSX 관리자에 대한 액세스를 제공합니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * vCenter에 연결하는 데 사용할 Windows 가상 머신 만들기
> * 가상 머신에서 vCenter에 로그인

## <a name="create-a-new-windows-virtual-machine"></a>새 Windows 가상 머신 만들기

1. 리소스 그룹에서 **+ 추가**를 선택하고, **Microsoft Windows 10**을 검색하여 선택한 다음, **만들기**를 클릭합니다.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Jumpbox에 대한 새 Windows 10 VM을 추가합니다." border="true":::

1. 필드에서 필요한 정보를 입력한 다음, **검토 + 만들기**를 선택합니다. 필드에 대한 자세한 내용은 다음 표를 참조하세요.

   | 필드 | 값 |
   | --- | --- |
   | **구독** | 이 값은 이미 리소스 그룹이 속한 구독으로 채워져 있습니다. |
   | **리소스 그룹** | 이 값은 현재 리소스 그룹에 대해 이미 채워져 있습니다. 이전 자습서에서 만든 리소스 그룹입니다. |
   | **가상 머신 이름** | VM에 대한 고유한 이름을 입력합니다. |
   | **지역** | VM의 지리적 위치를 선택합니다. |
   | **가용성 옵션** | 선택된 기본값을 그대로 둡니다. |
   | **이미지** | VM 이미지를 선택합니다. |
   | **크기** | 기본 크기 값을 그대로 둡니다. |
   | **인증 유형**  | **암호**를 선택합니다. |
   | **사용자 이름** | VM에 로그온하기 위한 사용자 이름을 입력합니다. |
   | **암호** | VM에 로그온하기 위한 암호를 입력합니다. |
   | **암호 확인** | VM에 로그온하기 위한 암호를 입력합니다. |
   | **퍼블릭 인바운드 포트** | **없음**을 선택합니다. [없음]이 선택되면 [JIT 액세스](../security-center/security-center-just-in-time.md#jit-configure)를 사용하여 VM에 액세스하려는 경우에만 VM에 대한 액세스를 제어할 수 있습니다.  |

1. 적절한 정보가 입력되었으면 **검토 + 만들기**를 클릭합니다. 
1. 유효성 검사가 통과되면 **만들기**를 선택하여 가상 머신 만들기 프로세스를 시작합니다.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Jumpbox에 대한 새 Windows 10 VM을 만듭니다." border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>프라이빗 클라우드의 로컬 vCenter에 연결

1. Jumpbox에서 VMware vCenter SSO를 사용하여 vSphere Client에 로그인합니다. 클라우드 관리자 사용자 이름을 사용하여 vSphere Client에 로그인합니다. 보안 위험에 동의하고 잠재적인 보안 위험에 대한 경고가 표시되면 계속합니다. Single Sign-On 자격 증명을 사용하여 VMware vCenter에 로그인하고, 사용자 인터페이스가 성공적으로 표시되는지 확인합니다.

1. Azure Portal에서 프라이빗 클라우드를 선택한 다음, **개요** 보기에서 **ID > 기본값**을 차례로 선택합니다. 프라이빗 클라우드 vCenter 및 NSX-T 관리자에 대한 URL 및 로그인 자격 증명이 표시됩니다.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="프라이빗 클라우드 vCenter와 NSX 관리자의 URL 및 자격 증명을 표시합니다." border="true":::

1. 이전 단계에서 만든 가상 머신으로 이동하여 가상 머신에 연결합니다. 가상 머신에 연결하는 방법에 대한 자세한 단계는 [가상 머신에 연결](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)을 참조하세요.

1. Windows VM에서 브라우저를 열고, 두 개의 탭에서 vCenter 및 NSX-T 관리자 URL로 이동합니다. vCenter 탭에서 이전 단계의 `cloudadmin@vmcp.local` 사용자 자격 증명을 입력합니다.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="프라이빗 클라우드 vCenter에 로그인합니다." border="true":::

   :::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter 포털입니다." border="true":::

1. 브라우저의 두 번째 탭에서 NSX-T 관리자에 로그인합니다.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="브라우저의 두 번째 탭에서 NSX-T 관리자에 로그인합니다." border="true":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * vCenter에 연결하는 데 사용할 Windows 가상 머신 만들기
> * 가상 머신에서 vCenter에 로그인

Azure VMware Solution 프라이빗 클라우드를 확장하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure VMware Solution 프라이빗 클라우드 스케일링](tutorial-scale-private-cloud.md)
