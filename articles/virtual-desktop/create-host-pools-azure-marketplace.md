---
title: Windows Virtual Desktop 호스트 풀 - Azure Portal - Azure
description: Azure Portal을 사용하여 Windows Virtual Desktop 호스트 풀을 만드는 방법입니다.
author: Heidilohr
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 30101d4e9125b0ac283710ebb26205c2bb120766
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88755486"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>자습서: Azure Portal로 호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md)를 참조하세요. Windows Virtual Desktop(클래식)으로 만든 개체는 Azure Portal로 관리할 수 없습니다.

호스트 풀은 Windows Virtual Desktop 환경 내에서 하나 이상의 동일한 VM(가상 머신) 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

이 문서에서는 Azure Portal을 통해 Windows Virtual Desktop 환경에 대한 호스트 풀을 만들기 위한 설정 프로세스를 안내합니다. 이 방법은 Windows Virtual Desktop에 호스트 풀을 만들고, Azure 구독에서 VM으로 구성된 리소스 그룹을 만들고, 해당 VM을 Azure AD(Active Directory) 도메인에 조인하고, VM을 Windows Virtual Desktop에 등록하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

호스트 풀을 만들려면 다음 매개 변수를 입력해야 합니다.

- VM 이미지 이름
- VM 구성
- 도메인 및 네트워크 속성
- Windows Virtual Desktop 호스트 풀 속성

또한 다음 사항을 알고 있어야 합니다.

- 사용하려는 이미지의 원본이 있는 위치. Azure 갤러리에서 가져온 이미지인가요, 아니면 사용자 지정 이미지인가요?
- 도메인 조인 자격 증명

또한 Microsoft.DesktopVirtualization 리소스 공급자를 등록했는지 확인합니다. 아직 수행하지 않은 경우 **구독**으로 이동하여 구독 이름을 선택한 다음, **리소스 공급자**를 선택합니다. DesktopVirtualization를 검색하고, Microsoft.DesktopVirtualization을 선택한 다음, 등록을 선택합니다.

Azure Resource Manager 템플릿을 사용하여 Windows Virtual Desktop 호스트 풀을 만드는 경우 Azure 갤러리, 관리 이미지 또는 비관리 이미지에서 가상 머신을 만들 수 있습니다. VM 이미지를 만드는 방법에 대한 자세한 내용은 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 및 [Azure에서 일반화된 VM의 관리 이미지 만들기](../virtual-machines/windows/capture-image-resource.md)를 참조하세요.

Azure 구독이 아직 없는 경우 먼저 [계정을 만든](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 후에 다음 지침을 수행해야 합니다.

## <a name="begin-the-host-pool-setup-process"></a>호스트 풀 설정 프로세스 시작

새 호스트 풀 만들기를 시작하려면 다음을 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

2. 검색 창에서 **Windows Virtual Desktop**을 입력한 다음, [서비스] 아래에서 **Windows Virtual Desktop**을 찾아서 선택합니다.

3. **Windows Virtual Desktop** 개요 페이지에서 **호스트 풀 만들기**를 선택합니다.

4. **기본** 탭의 [프로젝트 세부 정보] 아래에서 올바른 구독을 선택합니다.

5. **새로 만들기**를 선택하여 새 리소스 그룹을 만들거나 드롭다운 메뉴에서 기존 리소스 그룹을 선택합니다.

6. 호스트 풀에 대한 고유한 이름을 입력합니다.

7. [위치] 필드의 드롭다운 메뉴에서 호스트 풀을 만들려는 지역을 선택합니다.

   선택한 지역과 연결된 Azure 지역은 이 호스트 풀 및 관련 개체에 대한 메타데이터가 저장되는 위치입니다. 서비스 메타데이터를 저장하려는 지리적 위치 내의 지역을 선택해야 합니다.

     > [!div class="mx-imgBorder"]
     > ![미국 동부 위치가 선택된 위치 필드를 보여주는 Azure Portal의 스크린샷. 필드 옆에는 "메타데이터가 미국 동부에 저장됩니다."라는 텍스트가 있습니다.](media/portal-location-field.png)

8. [호스트 풀 유형] 아래에서 호스트 풀이 **개인** 또는 **풀링됨**인지를 선택합니다.

    - **개인**을 선택하는 경우 [할당 유형] 필드에서 **자동** 또는 **직접**을 선택합니다.

      > [!div class="mx-imgBorder"]
      > ![할당 유형 필드 드롭다운 메뉴의 스크린샷. 사용자가 자동을 선택했습니다.](media/assignment-type-field.png)

9. **풀링됨**을 선택하는 경우 다음 정보를 입력합니다.

     - **최대 세션 제한**에 대해 단일 세션 호스트로 부하가 분산되도록 하려는 최대 사용자 수를 입력합니다.
     - **부하 분산 알고리즘**에 대해 사용 패턴에 따라 [너비 우선] 또는 [깊이 우선] 중 하나를 선택합니다.

       > [!div class="mx-imgBorder"]
       > !["풀링됨"이 선택된 할당 유형 필드의 스크린샷. 사용자가 부하 분산 드롭다운 메뉴에서 커서로 너비 우선 위를 가리키고 있습니다.](media/pooled-assignment-type.png)

10. **다음: Virtual Machines >** 을 선택합니다.

11. 가상 머신을 이미 만들어 새 호스트 풀에서 사용하려는 경우 **아니요**를 선택하고, **다음: 작업 영역 >** 을 선택한 다음, [작업 영역 정보](#workspace-information) 섹션으로 이동합니다. 새 가상 머신을 만들어 새 호스트 풀에 등록하려면 **예**를 선택합니다.

이제 첫 번째 부분이 완료되었으므로 VM을 만드는 설정 프로세스의 다음 부분으로 이동하겠습니다.

## <a name="virtual-machine-details"></a>가상 머신 세부 정보

이제 첫 번째 단계가 완료되었으므로 VM을 설정해야 합니다.

호스트 풀 설정 프로세스 내에서 가상 머신을 설정하려면 다음을 수행합니다.

1. **리소스 그룹** 아래에서 가상 머신을 만들려는 리소스 그룹을 선택합니다. 이는 호스트 풀에 사용한 것과 다른 리소스 그룹일 수 있습니다.

2. 가상 머신을 만들려는 **가상 머신 위치**를 선택합니다. 호스트 풀에 대해 선택한 지역과 같거나 다를 수 있습니다.

3. 다음으로, 만들려는 **가상 머신 크기**를 선택합니다. 기본 크기를 그대로 유지하거나 **크기 변경**을 선택하여 크기를 변경할 수 있습니다. 표시되는 창에서 **크기 변경**을 선택하는 경우 워크로드에 적합한 가상 머신의 크기를 선택합니다.

4. **VM 수** 아래에서 호스트 풀에 대해 만들려는 VM 수를 제공합니다.

    >[!NOTE]
    >설정 프로세스는 호스트 풀을 설정하는 동안 최대 400개의 VM을 만들 수 있으며, 각 VM 설정 프로세스에서 4개의 개체를 리소스 그룹에 만듭니다. 만들기 프로세스는 구독 할당량을 확인하지 않으므로 입력하는 VM 수가 리소스 그룹 및 구독에 대한 Azure VM 및 API 제한 내에 있는지 확인합니다. 호스트 풀 만들기가 완료되면 VM을 더 추가할 수 있습니다.

5. 그런 다음, **이름 접두사**를 제공하여 설정 프로세스에서 만드는 가상 머신의 이름을 지정합니다. 접미사는 0부터 시작하는 숫자가 포함된 `-`입니다.

6. 다음으로, 가상 머신을 만드는 데 사용해야 하는 이미지를 선택합니다. **갤러리** 또는 **스토리지 Blob** 중 하나를 선택할 수 있습니다.

    - **갤러리**를 선택하는 경우 드롭다운 메뉴에서 다음과 같은 추천 이미지 중 하나를 선택합니다.

      - Windows 10 Enterprise 다중 세션 버전 1909
      - Windows 10 Enterprise 다중 세션 버전 1909 + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise 다중 세션 버전 2004
      - Windows 10 Enterprise 다중 세션 버전 2004 + Microsoft 365 Apps

     원하는 이미지가 표시되지 않으면 **모든 이미지 및 디스크 찾아보기**를 선택합니다. 그러면 갤러리의 다른 이미지 또는 Microsoft 및 다른 게시자가 제공한 이미지를 선택할 수 있습니다.

     > [!div class="mx-imgBorder"]
     > ![Microsoft의 이미지 목록이 표시된 Marketplace의 스크린샷.](media/marketplace-images.png)

     **내 항목**으로 이동하여 이미 업로드한 사용자 지정 이미지를 선택할 수도 있습니다.

     > [!div class="mx-imgBorder"]
     > ![내 항목 탭의 스크린샷.](media/my-items.png)

    - **스토리지 Blob**을 선택하는 경우 Hyper-V 또는 Azure VM을 통해 고유한 이미지 빌드를 활용할 수 있습니다. 스토리지 Blob에 있는 이미지의 위치를 URI로 입력하면 됩니다.

7. VM에서 사용할 OS 디스크의 종류(표준 SSD, 프리미엄 SSD 또는 표준 HDD)를 선택합니다.

8. 네트워크 및 보안 아래에서 사용자가 만든 가상 머신을 배치하려는 **가상 네트워크** 및 **서브넷**을 선택합니다. 가상 네트워크 내의 가상 머신을 도메인에 조인해야 하므로 가상 네트워크에서 도메인 컨트롤러에 연결할 수 있는지 확인합니다. 다음으로, 공용 IP를 가상 머신에 사용할지 여부를 선택합니다. 개인 IP가 더 안전하므로 **아니요**를 선택하는 것이 좋습니다.

9. 원하는 보안 그룹의 종류(**기본** , **고급**  또는 **없음**)를 선택합니다.

    **기본**을 선택하는 경우 인바운드 포트를 열지 여부를 선택해야 합니다. **예**를 선택하는 경우 인바운드 연결을 허용하는 표준 포트의 목록에서 선택합니다.

    >[!NOTE]
    >보안을 강화하려면 퍼블릭 인바운드 포트를 열지 않는 것이 좋습니다.

    > [!div class="mx-imgBorder"]
    > ![드롭다운 메뉴의 사용 가능한 포트 목록을 보여주는 보안 그룹 페이지의 스크린샷.](media/available-ports.png)

    **고급**을 선택하는 경우 이미 구성한 기존 네트워크 보안 그룹을 선택합니다.

10. 그런 다음, 가상 머신을 특정 도메인 및 조직 구성 단위에 조인할지 여부를 선택합니다. **예**를 선택하는 경우 조인할 도메인을 지정합니다. 필요에 따라 가상 머신을 배치하려는 특정 조직 구성 단위를 추가할 수 있습니다. **아니요**를 선택하면 VM이 **AD 도메인 조인 UPN**의 접미사와 일치하는 도메인에 조인됩니다.

11. [관리자 계정] 아래에서 선택한 가상 네트워크의 Active Directory 도메인 관리자에 대한 자격 증명을 입력합니다.

12. **다음: 작업 영역 >** 을 선택합니다.

이제 호스트 풀을 설정하는 다음 단계로서 앱 그룹을 작업 영역에 등록할 준비가 되었습니다.

## <a name="workspace-information"></a>작업 영역 정보

호스트 풀 설정 프로세스는 기본적으로 데스크톱 애플리케이션 그룹을 만듭니다. 호스트 풀이 의도한 대로 작동하려면 이 앱 그룹을 사용자 또는 사용자 그룹에 게시해야 하고 해당 앱 그룹을 작업 영역에 등록해야 합니다.

데스크톱 앱 그룹을 작업 영역에 등록하려면 다음을 수행합니다.

1. **예**를 선택합니다.

   **아니요**를 선택하는 경우 나중에 앱 그룹을 등록할 수 있지만 호스트 풀이 제대로 작동하도록 가능한 한 빨리 작업 영역을 등록하는 것이 좋습니다.

2. 다음으로, 새 작업 영역을 만들지 아니면 기존 작업 영역에서 선택할지를 선택합니다. 호스트 풀과 동일한 위치에 만든 작업 영역에만 앱 그룹을 등록할 수 있습니다.

3. 필요에 따라 **다음: 태그 >** 를 선택할 수 있습니다.

    여기서는 관리자가 작업을 더 쉽게 수행할 수 있도록 태그를 추가하여 개체를 메타데이터로 그룹화할 수 있습니다.

4. 완료되면 **검토 + 만들기**를 선택합니다.

     >[!NOTE]
     >검토 + 만들기 유효성 검사 프로세스에서는 암호가 보안 표준을 충족하는지 또는 아키텍처가 올바른지 확인하지 않으므로 이러한 항목 중 하나에 문제가 있는지 직접 확인해야 합니다.

5. 배포 정보를 검토하여 모든 것이 올바르게 표시되는지 확인합니다. 완료되면 **만들기**를 선택합니다. 그러면 배포 프로세스가 시작되어 다음과 같은 개체가 만들어집니다.

     - 새 호스트 풀
     - 데스크톱 앱 그룹
     - 작업 영역(만들도록 선택한 경우)
     - 데스크톱 앱 그룹을 등록하도록 선택한 경우 등록이 완료됩니다.
     - 가상 머신(가상 머신을 만들도록 선택한 경우 도메인에 조인되어 새 호스트 풀에 등록됨)
     - 구성에 기반한 Azure 리소스 관리 템플릿에 대한 다운로드 링크

그러면 모든 작업이 완료되었습니다!

## <a name="next-steps"></a>다음 단계

이제 호스트 풀이 만들어졌으므로 이 풀을 RemoteApp 프로그램으로 채울 수 있습니다. Windows Virtual Desktop에서 앱을 관리하는 방법에 대해 자세히 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
