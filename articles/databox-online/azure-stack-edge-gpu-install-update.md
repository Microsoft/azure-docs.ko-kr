---
title: Azure Stack Edge Pro GPU 장치에 업데이트 설치 | Microsoft Docs
description: Azure Portal 및 장치의 로컬 웹 UI를 사용 하 여 업데이트를 적용 하는 방법에 대해 설명 합니다. Azure Stack Edge Pro GPU 장치 및 장치의 Kubernetes 클러스터
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: ac5ed0e5941c6251d632d029fe4c9f80bbcf12df
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612554"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 업데이트 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 로컬 웹 UI를 통해 Azure Portal를 통해 GPU를 사용 하 여 Azure Stack Edge Pro에 업데이트를 설치 하는 데 필요한 단계를 설명 합니다. 장치에 Azure Stack Edge Pro 장치와 연결 된 Kubernetes 클러스터를 최신 상태로 유지 하기 위해 소프트웨어 업데이트나 핫픽스를 적용 합니다.

이 문서에서 설명 하는 절차는 다른 버전의 소프트웨어를 사용 하 여 수행 되었지만 프로세스는 현재 소프트웨어 버전에 대해서도 동일 하 게 유지 됩니다.

> [!IMPORTANT]
> - 업데이트 **2103** 은 현재 업데이트 이며 다음에 해당 합니다.
>   - 장치 소프트웨어 버전- **2.2.1540.2890**
>   - Kubernetes server 버전- **v 1.17.3**
>   - IoT Edge 버전: **0.1.0-beta13**
>   - GPU 드라이버 버전: **460.32.03**
>   - VERDA 버전: **11.2**
>    
>    이 업데이트의 새로운 기능에 대 한 자세한 내용은 [릴리스 정보](azure-stack-edge-gpu-2103-release-notes.md)를 참조 하세요.
> - 2103 업데이트를 적용 하려면 장치에서 2010를 실행 해야 합니다. 지원 되는 최소 버전을 실행 하지 않는 경우에는 *해당 종속성이 충족 되지 않으므로 업데이트 패키지를 설치할 수 없습니다*. 라는 오류가 표시 됩니다.
> - 이 업데이트를 사용하려면 두 개의 업데이트를 순차적으로 적용해야 합니다. 먼저 장치 소프트웨어 업데이트를 적용 한 다음 Kubernetes 업데이트를 적용 합니다.
> - 업데이트 또는 핫픽스를 설치하면 디바이스가 다시 시작됩니다. 이 업데이트에는 장치 소프트웨어 업데이트 및 Kubernetes 업데이트가 포함 되어 있습니다. Azure Stack Edge Pro가 단일 노드 장치인 경우 진행 중인 모든 i/o가 중단 되 고 장치에 업데이트를 위한 최대 1.5 시간 동안 가동 중지 시간이 발생 합니다.

장치에 업데이트를 설치 하려면 먼저 업데이트 서버의 위치를 구성 해야 합니다. 업데이트 서버를 구성한 후 Azure Portal UI 또는 로컬 웹 UI를 통해 업데이트를 적용할 수 있습니다.

이러한 각 단계는 다음 섹션에 설명되어 있습니다.

## <a name="configure-update-server"></a>업데이트 서버 구성

1. 로컬 웹 UI에서 **구성**  >  **업데이트 서버** 로 이동 합니다.
   
    ![업데이트 구성 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. **업데이트 서버 유형 선택** 의 드롭다운 목록에서 Microsoft 업데이트 서버 (기본값) 또는 Windows Server Update Services를 선택 합니다.  
   
    Windows Server Update Services에서 업데이트 하는 경우 서버 URI를 지정 합니다. 해당 URI의 서버는이 서버에 연결 된 모든 장치에 대 한 업데이트를 배포 합니다.

    ![업데이트 구성 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    WSUS 서버는 관리 콘솔을 통해 업데이트를 관리 하 고 배포 하는 데 사용 됩니다. WSUS 서버는 조직 내 다른 WSUS 서버의 업데이트 원본이 수도 있습니다. 업데이트 원본으로 사용되는 WSUS 서버를 업스트림 서버라고 합니다. WSUS 구현에서 하나 이상의 WSUS 서버를 네트워크에서 사용 가능한 업데이트 정보를 얻으려면 Microsoft 업데이트에 연결할 수 있어야 합니다. 관리자로 서 확인할 수 있습니다-네트워크 보안 및 구성-에 따라 다른 WSUS 서버의 수에 직접 연결 Microsoft Update.
    
    자세한 내용은 [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) 로 이동 하세요.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal를 통해 업데이트를 설치 하는 것이 좋습니다. 디바이스는 하루에 한 번 업데이트를 자동으로 검색합니다. 업데이트를 사용할 수 있게 되 면 포털에 알림이 표시 됩니다. 그런 다음 업데이트를 다운로드하여 설치할 수 있습니다.

> [!NOTE]
> 장치가 정상 상태 인지 확인 하 고 **장치가 정상적으로 실행 되** 고 있는 상태를 표시 합니다. 업데이트를 설치 하는 과정을 진행 합니다.

1. 장치에 대 한 업데이트를 사용할 수 있는 경우 알림이 표시 됩니다. 알림을 선택 하거나 맨 위 명령 모음에서 **장치 업데이트** 를 선택 합니다. 이렇게 하면 장치 소프트웨어 업데이트를 적용할 수 있습니다.

    ![업데이트 후 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. **장치 업데이트** 블레이드에서 릴리스 정보의 새 기능과 관련 된 사용 조건을 검토 했는지 확인 합니다.

    업데이트를 다운로드 하 여 **설치** 하거나 업데이트를 **다운로드** 하도록 선택할 수 있습니다. 그런 다음 나중에 이러한 업데이트를 설치하도록 선택할 수 있습니다.

    ![업데이트 2 이후의 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    업데이트를 다운로드 하 여 설치 하려면 다운로드를 완료 한 후 업데이트가 자동으로 설치 되도록 하는 옵션을 선택 합니다.

    ![업데이트 3 이후의 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. 업데이트 다운로드가 시작 됩니다. 다운로드가 진행 중 이라고 표시 되는 알림이 표시 됩니다.

    ![업데이트 4 이후 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    알림 배너가 Azure Portal에도 표시 됩니다. 다운로드 진행률을 나타냅니다.

    ![업데이트 5 이후의 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    이 알림을 선택 하거나 **장치 업데이트** 를 선택 하 여 업데이트의 자세한 상태를 확인할 수 있습니다.

    ![업데이트 6 이후의 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. 다운로드가 완료 되 면 알림 배너가 업데이트 되어 완료 되었음을 표시 합니다. 업데이트를 다운로드 하 여 설치 하도록 선택한 경우 설치가 자동으로 시작 됩니다.

    업데이트만 다운로드 하도록 선택한 경우에는 알림을 선택 하 여 **장치 업데이트** 블레이드를 엽니다. **설치** 를 선택합니다.
  
5. 설치가 진행 중 이라고 표시 되는 알림이 표시 됩니다. 또한 포털에는 설치가 진행 중임을 나타내는 정보 경고도 표시 됩니다. 장치가 오프 라인 상태가 되 고 유지 관리 모드에 있습니다.
   
    ![업데이트 10 이후의 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. 이는 1 개 노드 장치 이므로 업데이트가 설치 된 후 장치가 다시 시작 됩니다. 다시 시작 하는 동안 중요 한 경고는 장치 하트 비트가 손실 되었음을 나타냅니다.

    ![업데이트 11 이후의 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    경고를 선택 하 여 해당 하는 장치 이벤트를 확인 합니다.
    
    ![업데이트 12 이후의 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. 다시 시작한 후 장치 소프트웨어가 업데이트를 완료 합니다. 업데이트가 완료 된 후에는 로컬 웹 UI에서 장치 소프트웨어가 업데이트 되었음을 확인할 수 있습니다. Kubernetes software 버전이 업데이트 되지 않았습니다.

    ![업데이트 13 이후 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-12.png)

8. 장치 업데이트를 사용할 수 있음을 나타내는 알림 배너가 표시 됩니다. 이 배너를 선택 하 여 장치에서 Kubernetes 소프트웨어 업데이트를 시작 합니다. 

    ![업데이트 13a 이후 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-13.png) 


    ![업데이트 14 이후 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-14-a.png) 

    위쪽 명령 모음에서 **업데이트 장치** 를 선택 하면 업데이트의 진행률을 볼 수 있습니다.  

    ![업데이트 15 이후의 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-14-b.png) 


8. 업데이트를 설치한 후 장치에 대 한 장치 상태 업데이트가 **제대로 실행 되** 고 있습니다. 

    ![업데이트 16 이후 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

    로컬 웹 UI로 이동한 다음 **소프트웨어 업데이트** 페이지로 이동 합니다. Kubernetes 업데이트가 성공적으로 설치 되 고 소프트웨어 버전이이를 반영 하는지 확인 합니다.

    ![업데이트 17 이후 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


장치 소프트웨어 및 Kubernetes 업데이트가 성공적으로 설치 되 면 배너 알림이 사라집니다. 장치가 이제 최신 버전의 장치 소프트웨어 및 Kubernetes입니다.


## <a name="use-the-local-web-ui"></a>로컬 웹 UI 사용

로컬 웹 UI를 사용하는 경우 다음 두 단계로 구성됩니다.

* 업데이트 또는 핫픽스 다운로드
* 업데이트 또는 핫픽스 설치

이러한 각 단계는 다음 섹션에서 자세히 설명합니다.


### <a name="download-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 다운로드

업데이트를 다운로드 하려면 다음 단계를 수행 합니다. Microsoft에서 제공 하는 위치 또는 Microsoft 업데이트 카탈로그에서 업데이트를 다운로드할 수 있습니다.


Microsoft 업데이트 카탈로그에서 업데이트를 다운로드 하려면 다음 단계를 수행 합니다.

1. 브라우저를 시작 하 고로 이동 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) 합니다.

    ![카탈로그 검색](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Microsoft 업데이트 카탈로그의 검색 상자에 다운로드 하려는 업데이트에 대 한 기술 자료 (KB) 번호 (KB)를 입력 합니다. 예를 들어 **Azure Stack Edge Pro** 를 입력 한 다음 **검색** 을 클릭 합니다.
   
    업데이트 목록은 **Azure Stack Edge 업데이트 2103** 으로 표시 됩니다.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. **다운로드** 를 선택합니다. 다운로드 하는 데는 두 가지 패키지 (*SoftwareUpdatePackage.exe*(장치 소프트웨어 업데이트)에 해당 하는 kb 4613486 및 kb 46134867, 각각 *Kubernetes_Package.exe*(Kubernetes updates))가 있습니다. 로컬 시스템의 폴더에 패키지를 다운로드 합니다. 디바이스에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.

### <a name="install-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 설치

업데이트 또는 핫픽스를 설치하기 전에 다음을 확인합니다.

 - 업데이트 또는 핫픽스를 호스트에 로컬로 다운로드하거나 네트워크 공유를 통해 액세스할 수 있는지 확인합니다.
 - 로컬 웹 UI의 **개요** 페이지에 표시 된 대로 장치 상태가 정상입니다.

   ![디바이스 업데이트](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

이 절차를 완료 하는 데 약 20 분이 걸립니다. 다음 단계를 수행하여 업데이트 또는 핫픽스를 설치합니다.

1. 로컬 웹 UI에서 **유지 관리**  >  **소프트웨어 업데이트** 로 이동 합니다. 실행 중인 소프트웨어 버전을 기록해 둡니다. 
   
   ![장치 업데이트 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. 업데이트 파일의 경로를 제공 합니다. 네트워크 공유에 배치 된 경우 업데이트 설치 파일로 이동할 수도 있습니다. *SoftwareUpdatePackage.exe* 접미사가 포함 된 소프트웨어 업데이트 파일을 선택 합니다.

   ![장치 업데이트 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. **적용** 을 선택합니다.

   ![업데이트 장치 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. 확인 메시지가 표시 되 면 **예** 를 선택 하 여 계속 합니다. 장치가 단일 노드 장치인 경우 업데이트가 적용 된 후 장치가 다시 시작 되 고 가동 중지 시간이 발생 합니다. 
   
   ![업데이트 장치 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. 업데이트가 시작됩니다. 디바이스가 성공적으로 업데이트된 후 다시 시작됩니다. 이 시간 동안 로컬 UI에 액세스할 수 없습니다.
   
6. 다시 시작이 완료된 후 **로그인** 페이지가 열립니다. 장치 소프트웨어가 업데이트 되었는지 확인 하려면 로컬 웹 UI에서 **유지 관리**  >  **소프트웨어 업데이트** 로 이동 합니다. 최신 버전의 경우 표시 된 소프트웨어 버전은 **Edge 2103 Azure Stack** 되어야 합니다. 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. 이제 Kubernetes 소프트웨어 버전을 업데이트 합니다. 위의 단계를 반복 합니다. *Kubernetes_Package.exe* 접미사로 Kubernetes 업데이트 파일의 경로를 제공 합니다.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. **업데이트 적용** 을 선택 합니다.

   ![장치 업데이트 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. 확인 메시지가 표시 되 면 **예** 를 선택 하 여 계속 합니다.

10. Kubernetes 업데이트를 성공적으로 설치한 후에는 **유지 관리**  >  **소프트웨어 업데이트** 에 표시 된 소프트웨어를 변경 하지 않습니다.


## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro 관리](azure-stack-edge-manage-access-power-connectivity-mode.md)에 대해 자세히 알아보세요.