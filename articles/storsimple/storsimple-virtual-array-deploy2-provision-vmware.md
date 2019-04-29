---
title: VMware에서 StorSimple Virtual Array 프로비전 | Microsoft Docs
description: StorSimple Virtual Array 배포 시리즈의 두 번째 자습서에는 VMware에서 가상 디바이스를 프로비전하는 내용이 포함됩니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c9fe597957057dc61da5c2b1cf6f9216711764a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61419157"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>StorSimple 가상 배열 배포 - VMware에서 프로비전
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>개요
이 자습서는 VMware ESXi 5.0, 5.5, 6.0 또는 6.5를 실행하는 호스트 시스템에 StorSimple 가상 배열을 프로비전하고 연결하는 방법을 설명합니다. 이 문서는 Azure Portal 및 Microsoft Azure Government 클라우드에서 StorSimple 가상 배열의 배포에 적용됩니다.

가상 디바이스를 프로비전하고 연결하려면 관리자 권한이 필요합니다. 프로비전 및 초기 설정을 완료하는 데 10분 정도가 소요됩니다.

## <a name="provisioning-prerequisites"></a>프로비전 필수 조건
VMware ESXi 5.0, 5.5, 6.0 또는 6.5를 실행하는 호스트 시스템에 가상 디바이스를 프로비전하기 위한 필수 조건은 다음과 같습니다.

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple 디바이스 관리자 서비스의 경우
시작하기 전에 다음 사항을 확인합니다.

* [StorSimple 가상 배열용 포털 준비](storsimple-virtual-array-deploy1-portal-prep.md)의 모든 단계를 완료했습니다.
* Azure Portal에서 VMware용 가상 디바이스 이미지를 다운로드했습니다. 자세한 내용은 **3단계: 가상 디바이스 이미지 다운로드**([StorSimple Virtual Array를 위한 포털 준비 가이드](storsimple-virtual-array-deploy1-portal-prep.md))를 참조하세요.

### <a name="for-the-storsimple-virtual-device"></a>StorSimple 가상 디바이스의 경우
가상 디바이스를 배포하기 전에 다음 사항을 확인해야 합니다.

* 디바이스 프로비전에 사용될 수 있는 Hyper-V(2008 R2 이상)를 실행하는 호스트 시스템에 액세스할 수 있습니다.
* 가상 디스크 프로비전을 위해 호스트 시스템에서 다음 리소스를 전용할 수 있습니다.

  * 코어 4개 이상
  * RAM 8GB 이상 가상 배열을 파일 서버로서 구성하려는 경우 8GB는 2백만 개 미만의 파일을 지원합니다. 2 - 4백만 개의 파일을 지원하려면 16GB RAM이 필요합니다.
  * 네트워크 인터페이스 하나
  * 시스템 데이터용 가상 디스크 500GB

### <a name="for-the-network-in-datacenter"></a>데이터 센터 네트워크의 경우
시작하기 전에 다음 사항을 확인합니다.

* StorSimple 가상 디바이스를 배포하기 위한 네트워킹 요구 사항을 검토하고, 요구 사항에 따라 데이터 센터 네트워크를 구성했습니다. 

## <a name="step-by-step-provisioning"></a>단계별 프로비전
가상 디바이스를 프로비전하고 연결하려면 다음 단계를 수행해야 합니다.

1. 호스트 시스템에 최소 가상 디바이스 요구 사항을 충족하기에 충분한 리소스가 있는지 확인합니다.
2. 하이퍼바이저에서 가상 디바이스를 프로비전합니다.
3. 가상 디바이스를 시작하고 IP 주소를 가져옵니다.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>1단계: 호스트 시스템이 최소 가상 장치 요구 사항을 충족하는지 확인
가상 디바이스를 만들려면 다음이 필요합니다.

* VMware ESXi Server 5.0, 5.5, 6.0 또는 6.5를 실행하는 호스트 시스템에 대한 액세스
* ESXi 호스트를 관리하기 위한 시스템의 VMware vSphere 클라이언트

  * 코어 4개 이상
  * RAM 8GB 이상 가상 배열을 파일 서버로서 구성하려는 경우 8GB는 2백만 개 미만의 파일을 지원합니다. 2 - 4백만 개의 파일을 지원하려면 16GB RAM이 필요합니다.
  * 인터넷으로 트래픽을 라우팅할 수 있는 네트워크에 연결된 네트워크 인터페이스 하나. 디바이스가 최적으로 작동할 수 있도록 허용하는 최소 인터넷 대역폭은 5Mbps입니다.
  * 데이터용 가상 디스크 500GB

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>2단계: 하이퍼바이저에서 가상 디바이스 프로비전
하이퍼바이저에서 가상 디바이스를 프로비전하려면 다음 단계를 수행합니다.

1. 시스템에서 가상 디바이스 이미지를 복사합니다. Azure Portal을 통해 이 가상 이미지를 다운로드합니다.

   1. 최신 이미지 파일을 다운로드했는지 확인합니다. 이전에 이미지를 다운로드한 경우 최신 이미지인지 확인하기 위해 다시 다운로드합니다. 최신 이미지에는 하나가 아니라 두 개의 파일이 있습니다.
   2. 나중에 절차에서 이 이미지를 사용하므로 이미지를 복사한 위치를 적어 둡니다.

2. vSphere 클라이언트를 사용하여 ESXi 서버에 로그인합니다. 가상 머신을 만들려면 관리자 권한이 필요합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. vSphere 클라이언트 왼쪽 창의 인벤토리 섹션에서 ESXi 서버를 선택합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. ESXi 서버에 VMDK를 업로드합니다. 오른쪽 창의 **구성** 탭으로 이동합니다. **하드웨어** 아래에서 **저장소**를 선택합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. 오른쪽 창의 **데이터 저장소**아래에서 VMDK를 업로드할 데이터 저장소를 선택합니다. 데이터 저장소에는 OS 및 데이터 디스크에 충분한 여유 공간이 있어야 합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. **데이터 저장소 찾아보기**를 마우스 오른쪽 단추를 클릭하고 선택합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. **데이터 저장소 브라우저** 창이 표시됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. 도구 모음에서 ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) 아이콘을 클릭하여 새 폴더를 만듭니다. 폴더 이름을 지정하고 해당 이름을 적어둡니다. 나중에 가상 머신을 만들 때 이 폴더 이름을 사용합니다(권장 모범 사례). **확인**을 클릭합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. 새 폴더가 **데이터 저장소 브라우저**의 왼쪽 창에 표시됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. 업로드 아이콘 ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png)을 클릭하고 **파일 업로드**를 선택합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. 다운로드한 VMDK 파일을 찾아서 가리킵니다. 두 가지 파일이 있습니다. 업로드할 파일을 선택합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. **열기**를 클릭합니다. VMDK 파일을 지정된 데이터 저장소에 업로드하는 작업이 시작됩니다. 파일 업로드에 몇 분 정도 걸릴 수 있습니다.
13. 업로드가 완료되면 사용자가 만든 폴더의 데이터 저장소에 파일이 표시됩니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    두 번째 VMDK 파일을 동일한 데이터 저장소에 업로드합니다.
14. vSphere 클라이언트 창으로 돌아갑니다. ESXi 서버를 선택한 상태에서 마우스 오른쪽 단추를 클릭하고 **새 Virtual Machine**을 선택합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. **새 Virtual Machine 만들기** 창이 나타납니다. **구성** 페이지에서 **사용자 지정** 옵션을 선택합니다. **다음**을 클릭합니다.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. **이름 및 위치** 페이지에서 가상 머신의 이름을 지정합니다. 이 이름은 앞서 8단계에서 지정한 폴더 이름(권장 모범 사례)과 일치해야 합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. **저장소** 페이지에서 VM을 프로비전하는 데 사용할 데이터 저장소를 선택합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. **Virtual Machine 버전** 페이지에서 **Virtual Machine 버전: 8**을 선택합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. **게스트 운영 체제** 페이지에서 **게스트 운영 체제**를 **Windows**로 선택합니다. **버전**은 드롭다운 목록에서 **Microsoft Windows Server 2012(64비트)** 를 선택합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. **CPU** 페이지에서 **총 코어 수**가 4(또는 그 이상)가 되도록 **가상 소켓의 수** 및 **가상 소켓 당 코어의 수**를 조정합니다. **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. **메모리** 페이지에서 RAM을 8GB(또는 그 이상)로 지정합니다. **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. **네트워크** 페이지에서 네트워크 인터페이스의 수를 지정합니다. 최소 요구 사항은 네트워크 인터페이스 하나입니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. **SCSI 컨트롤러** 페이지에서 기본 **LSI Logic SAS 컨트롤러**를 그대로 둡니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. **디스크 선택** 페이지에서 **기존 가상 디스크 사용**을 선택합니다. **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. **기존 디스크 선택** 페이지의 **디스크 파일 경로** 아래에서 **찾아보기**를 클릭합니다. **Browse Datastores** (데이터 저장소 찾아보기) 대화 상자가 열립니다. VMDK를 업로드한 위치로 이동합니다. 이제 처음에 업로드한 두 개의 파일이 병합되었으므로 데이터 저장소에 하나의 파일만 표시됩니다. 파일을 선택하고 **확인**을 클릭합니다. **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. **고급 옵션** 페이지에서 기본값을 적용하고 **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. **Ready to Complete** (완료 준비) 페이지에서 새 가상 머신과 관련된 모든 설정을 검토합니다. **Edit the virtual machine settings before completion**(완료하기 전에 가상 머신 설정 편집)을 클릭합니다. **계속**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. **Virtual Machines 속성** 페이지의 **하드웨어** 탭에서 디바이스 하드웨어를 찾습니다. **새 하드 디스크**를 선택합니다. **추가**를 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. **하드웨어 추가** 창이 표시됩니다. **디바이스 유형** 페이지의 **추가할 디바이스 유형 선택**에서 **하드 디스크**를 선택하고 **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. **디스크 선택** 페이지에서 **새 가상 디스크 만들기**를 선택합니다. **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. **디스크 만들기** 페이지에서 **디스크 크기**를 500GB(또는 그 이상)로 변경합니다. 500GB가 최소 요구 사항이지만 언제나 더 큰 디스크를 프로비전할 수 있습니다. 프로비전된 후에는 디스크를 확장하거나 축소할 수 없습니다. 프로비전할 디스크의 크기에 대한 자세한 정보는 [모범 사례 문서](storsimple-ova-best-practices.md)의 크기 조정 섹션을 검토하세요. **디스크 프로비전**에서 **씬 프로비전**을 선택합니다. **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. **고급 옵션** 페이지에서 기본값을 적용합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. **Ready to Complete** (완료 준비) 페이지에서 디스크 옵션을 검토합니다. **Finish**를 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Virtual Machine 속성 페이지로 돌아갑니다. 새 하드 디스크가 가상 머신에 추가되어 있습니다. **Finish**를 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. 오른쪽 창에 가상 컴퓨터가 선택된 상태에서 **요약** 탭으로 이동합니다. 가상 머신에 대한 설정을 검토합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

가상 머신이 프로비전되어 있습니다. 다음 단계는 이 컴퓨터의 전원을 켜고 IP 주소를 가져오는 것입니다.

> [!NOTE]
> 가상 배열에는 VMware 도구를 설치하지 않는 것이 좋습니다(위에서 프로비전됨). VMware 도구를 설치하면 지원되지 않는 구성이 설정됩니다.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>3단계: 가상 디바이스 시작 및 IP 가져오기
가상 디바이스를 시작하여 연결하려면 다음 단계를 수행합니다.

#### <a name="to-start-the-virtual-device"></a>가상 디바이스를 시작하려면
1. 가상 디바이스를 시작합니다. vSphere 구성 관리자의 왼쪽 창에서 디바이스를 선택하고 마우스 오른쪽 단추를 클릭하여 상황에 맞는 메뉴를 표시합니다. **전원**을 선택한 후 **전원 켜기**를 선택합니다. 이렇게 하면 가상 컴퓨터의 전원이 켜집니다. vSphere 클라이언트의 아래쪽 **최근 태스크** 창에서 상태를 볼 수 있습니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. 설정 태스크를 완료하려면 몇 분 정도 걸립니다. 디바이스가 실행되면 **콘솔** 탭으로 이동합니다. Ctrl+Alt+Delete 키를 눌러서 디바이스에 로그온합니다. 또는, 콘솔 창의 커서를 가리키고 Ctrl+Alt+Insert 키를 누릅니다. 기본 사용자는 *StorSimpleAdmin*이고 기본 암호는 *Password1*입니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. 보안상의 이유로 디바이스 관리자 암호는 처음 로그인하면 만료됩니다. 암호를 변경하라는 메시지가 표시됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. 8자 이상을 포함하는 암호를 입력합니다. 암호는 4가지 요구 사항(대문자, 소문자, 숫자 및 특수 문자) 중 3가지를 포함해야 합니다. 확인을 위해 암호를 다시 입력합니다. 암호가 변경되었다는 메시지가 표시됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. 암호 변경이 완료되면 가상 디바이스가 다시 부팅될 수 있습니다. 재부팅이 완료될 때가지 기다립니다. 디바이스의 Windows PowerShell 콘솔이 진행률 표시줄과 함께 표시될 수 있습니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. 6-8단계는 DHCP 환경이 아닌 곳에서 부팅하는 경우에만 적용됩니다. DHCP 환경인 경우에는 이 단계를 건너뛰고 9단계로 이동하세요. DHCP 환경이 아닌 곳에서 디바이스를 부팅하는 경우에는 다음 화면이 표시됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   다음으로 네트워크를 구성합니다.
7. `Get-HcsIpAddress` 명령을 사용하여 가상 디바이스에 사용하도록 설정된 네트워크 인터페이스 목록을 표시합니다. 디바이스에 사용하도록 설정된 네트워크 인터페이스가 하나인 경우에는 `Ethernet`이라는 기본 이름이 인터페이스에 할당됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. `Set-HcsIpAddress` cmdlet을 사용하여 네트워크를 구성합니다. 아래에 예가 나와 있습니다.

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. 초기 설정이 완료된 후 디바이스가 부팅되면 디바이스 배너 텍스트가 표시됩니다. 디바이스 관리를 위해 배너 텍스트에 표시되는 IP 주소와 URL을 기록해 둡니다. IP 주소를 사용하여 가상 디바이스의 웹 UI를 연결하고 로컬 설정 및 등록을 완료합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (선택 사항) 이 단계는 정부 클라우드에서 디바이스를 배포하는 경우에만 수행합니다. 이제 디바이스에서 미국 FIPS(Federal Information Processing Standard) 모드를 사용할 수 있습니다. FIPS 140 표준은 중요한 데이터의 보호를 위해 미국 연방 정부 컴퓨터 시스템에서 사용할 수 있도록 승인된 암호화 알고리즘을 정의합니다.

    1. FIPS 모드를 사용하도록 설정하려면 다음 cmdlet을 실행합니다.

        `Enable-HcsFIPSMode`
    2. 암호화 유효성 검사에 적용되도록 FIPS 모드를 사용하도록 설정한 후 디바이스를 다시 부팅합니다.

       > [!NOTE]
       > 디바이스에서 FIPS 모드를 사용 또는 사용하지 않도록 설정할 수 있습니다. 디바이스에서 FIPS 모드와 비 FIPS 모드가 교대로 반복되는 기능은 지원되지 않습니다.
       >
       >

디바이스가 최소 구성 요구 사항을 충족하지 못하면 배너 텍스트에 오류(아래 참고)가 표시됩니다. 최소 요구 사항을 충족하기에 충분한 리소스를 확보하도록 디바이스 구성을 수정해야 합니다. 그런 다음, 다시 시작하고 디바이스에 연결합니다. [1단계: 호스트 시스템이 최소 가상 디바이스 요구 사항을 충족하는지 확인](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements)에서 최소 구성 요구 사항을 참조하세요.

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

로컬 웹 UI를 사용하여 초기 구성을 하는 동안 다른 오류가 발생하면 다음 워크플로를 참조하세요.

* 진단 테스트를 실행하여 [웹 UI 설정 문제를 해결](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)합니다.
* [로그 패키지를 생성하고 로그 파일을 살펴봅니다](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>다음 단계
* [StorSimple 가상 배열을 파일 서버로 설정](storsimple-virtual-array-deploy3-fs-setup.md)
* [StorSimple 가상 배열을 iSCSI 서버로 설정](storsimple-virtual-array-deploy3-iscsi-setup.md)
