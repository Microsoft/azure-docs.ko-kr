---
title: Hyper-V에서 StorSimple Virtual Array 프로비전 | Microsoft Docs
description: StorSimple 가상 배열 배포에 대한 두 번째 자습서에는 Hyper-V에서 가상 배열을 프로비전하는 내용이 포함됩니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5104d630e2b4e97b80a6fedfb6d863061c2722fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416342"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>StorSimple 가상 배열 배포 - Hyper-V에서 프로비전
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>개요
이 자습서에서는 Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2에서 Hyper-V를 실행하는 호스트 시스템의 StorSimple 가상 배열을 프로비전하는 방법을 설명합니다. 이 문서는 Azure Portal 및 Microsoft Azure Government 클라우드에서 StorSimple 가상 배열의 배포에 적용됩니다.

가상 배열을 프로비전하고 구성하려면 관리자 권한이 필요합니다. 프로비전 및 초기 설정을 완료하는 데 10분 정도가 소요됩니다.

## <a name="provisioning-prerequisites"></a>프로비전 필수 조건
여기서는 Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2에서 Hyper-V를 실행하는 호스트 시스템에서 가상 배열을 프로비전하기 위한 필수 구성 요소를 확인할 수 있습니다.

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple 디바이스 관리자 서비스의 경우
시작하기 전에 다음 사항을 확인합니다.

* [StorSimple 가상 배열용 포털 준비](storsimple-virtual-array-deploy1-portal-prep.md)의 모든 단계를 완료했습니다.
* Azure Portal에서 Hyper-V용 가상 배열 이미지를 다운로드했습니다. 자세한 내용은 **3단계: 가상 어레이 이미지 다운로드**([StorSimple Virtual Array를 위한 포털 준비 가이드](storsimple-virtual-array-deploy1-portal-prep.md))를 참조하세요.

  > [!IMPORTANT]
  > StorSimple 가상 배열에서 실행되는 소프트웨어는 StorSimple 디바이스 관리자 서비스와 함께 사용해야 합니다.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>StorSimple 가상 배열의 경우
가상 배열을 배포하기 전에 다음 사항을 확인해야 합니다.

* 디바이스 프로비전에 사용될 수 있는 Windows Server 2008 R2 이상에서 Hyper-V를 실행하는 호스트 시스템에 액세스할 수 있습니다.
* 가상 배열을 프로비전하려면 호스트 시스템에서 다음 리소스를 전용으로 사용할 수 있습니다.

  * 코어 4개 이상
  * RAM 8GB 이상 가상 배열을 파일 서버로서 구성하려는 경우 8GB는 2백만 개 미만의 파일을 지원합니다. 2 - 4백만 개의 파일을 지원하려면 16GB RAM이 필요합니다.
  * 네트워크 인터페이스 하나
  * 데이터용 가상 디스크 500GB

### <a name="for-the-network-in-the-datacenter"></a>데이터 센터에서 네트워크의 경우
시작하기 전에 StorSimple 가상 배열을 배포하고 데이터 센터 네트워크를 적절히 구성하기 위한 네트워킹 요구 사항을 검토하세요. 자세한 내용은 [StorSimple 가상 배열 네트워킹 요구 사항](storsimple-ova-system-requirements.md#networking-requirements)을 참조하세요.

## <a name="step-by-step-provisioning"></a>단계별 프로비전
가상 배열을 프로비전하고 연결하려면 다음 단계를 수행해야 합니다.

1. 호스트 시스템에 최소 가상 배열 요구 사항을 충족하기에 충분한 리소스가 있는지 확인합니다.
2. 하이퍼바이저에서 가상 배열을 프로비전합니다.
3. 가상 배열을 시작하고 IP 주소를 가져옵니다.

각 단계는 다음 섹션에서 설명합니다.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>1단계: 호스트 시스템이 최소 가상 배열 요구 사항을 충족하는지 확인
가상 배열을 만들려면 다음을 수행해야 합니다.

* Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2 SP1에 설치되어 있는 Hyper-V 역할
* 호스트에 연결된 Microsoft Windows 클라이언트의 Microsoft Hyper-V 관리자

가상 배열을 만드는 기본 하드웨어(호스트 시스템)에서 가상 배열에 대해 다음 리소스를 전용으로 사용할 수 있도록 해야 합니다.

* 코어 4개 이상
* RAM 8GB 이상 가상 배열을 파일 서버로서 구성하려는 경우 8GB는 2백만 개 미만의 파일을 지원합니다. 2 - 4백만 개의 파일을 지원하려면 16GB RAM이 필요합니다.
* 네트워크 인터페이스 하나
* 시스템 데이터용 가상 디스크 500GB

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>2단계: 하이퍼바이저에서 가상 배열 프로비저닝
하이퍼바이저에서 디바이스를 프로비전하려면 다음 단계를 수행합니다.

#### <a name="to-provision-a-virtual-array"></a>가상 배열을 프로비전하려면
1. Windows Server 호스트에서 로컬 드라이브에 가상 배열 이미지를 복사합니다. 이 이미지(VHD 또는 VHDX)를 Azure Portal을 통해 다운로드합니다. 나중에 절차에서 이 이미지를 사용하므로 이미지를 복사한 위치를 적어 둡니다.
2. **서버 관리자**를 엽니다. 오른쪽 위 모서리에서 **도구**를 클릭하고 **Hyper-V 관리자**를 선택합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Windows Server 2008 R2를 실행하는 경우에는 Hyper-V 관리자를 엽니다. 서버 관리자에서 **역할 > Hyper-V > Hyper-V 관리자**를 클릭합니다.
3. **Hyper-V 관리자**의 범위 창에서 시스템 노드를 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 연 다음 **새로 만들기** > **Virtual Machine**을 클릭합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. 새 Virtual Machine 마법사의 **시작하기 전에** 페이지에서 **다음**을 클릭합니다.
5. **이름 및 위치 지정** 페이지에서 가상 배열의 **이름**을 입력합니다. **다음**을 클릭합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. **세대 지정** 페이지에서 디바이스 이미지 유형을 선택하고 **다음**을 클릭합니다. Windows Server 2008 R2를 사용하는 경우에는 이 페이지가 표시되지 않습니다.

   * Windows Server 2012 이상에 대한 .vhdx 이미지를 다운로드한 경우 **2세대** 를 선택합니다.
   * Windows Server 2008 R2 이상에 대한 .vhd 이미지를 다운로드한 경우 **1세대** 를 선택합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. **메모리 할당** 페이지에서 **시작 메모리**를 **8192MB** 이상으로 지정하고, 동적 메모리는 사용하도록 설정하지 않은 후 **다음**을 클릭합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. **네트워킹 구성** 페이지에서 인터넷에 연결된 가상 스위치를 지정하고 **다음**을 클릭합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. **가상 하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용**을 선택하고 가상 배열 이미지(.vhdx 또는 .vhd)의 위치를 지정한 후 **다음**을 클릭합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. **요약**을 검토하고 **마침**을 클릭하여 가상 머신을 만듭니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. 최소 요구 사항을 충족하려면 코어 4개가 필요합니다. 4개의 가상 프로세서를 추가하려면 **Hyper-V 관리자** 창에서 호스트 시스템을 선택합니다. **Virtual Machines** 목록의 오른쪽 창에서 방금 만든 Virtual Machines를 찾습니다. 컴퓨터 이름을 선택하고 마우스 오른쪽 단추로 클릭한 후 **설정**을 선택합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. **설정** 페이지의 왼쪽 창에서 **프로세서**를 클릭합니다. 오른쪽 창에서 **가상 프로세서 수**를 4(또는 그 이상)로 설정합니다. **적용**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. 최소 요구 사항을 충족하려면 가상 데이터 디스크를 500GB 추가해야 합니다. **설정** 페이지에서:

    1. 왼쪽 창의 **SCSI 컨트롤러**를 선택합니다.
    2. 오른쪽 창의 **하드 드라이브**를 선택하고 **추가**를 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. **하드 드라이브** 페이지에서 **가상 하드 디스크** 옵션을 선택하고 **새로 만들기**를 클릭합니다. **새 가상 하드 디스크 마법사**가 시작됩니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. 새 가상 하드 디스크 마법사의 **시작하기 전에** 페이지에서 **다음**을 클릭합니다.
16. **디스크 형식 선택** 페이지에서 **VHDX** 형식의 기본 옵션을 적용합니다. **다음**을 클릭합니다. Windows Server 2008 R2를 실행하는 경우에는 이 화면이 표시되지 않습니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. **디스크 유형 선택** 페이지에서 가상 하드 디스크 유형을 **동적 확장**(권장)으로 설정합니다. **고정 크기** 디스크는 작동은 되지만 오래 기다려야 할 수 있습니다. **차이점 보관용** 옵션은 사용하지 않는 것이 좋습니다. **다음**을 클릭합니다. Windows Server 2012 R2 및 Windows Server 2012에서는 **동적 확장**이 기본 옵션이지만 Windows Server 2008 R2에서는 **고정 크기**가 기본값입니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. **이름 및 위치 지정** 페이지에서 데이터 디스크의 **이름** 및 **위치**(해당 위치로 이동 가능)를 입력합니다. **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. **디스크 구성** 페이지에서 **비어 있는 새 가상 하드 디스크 만들기** 옵션을 선택하고 크기를 **500GB**(또는 그 이상)로 지정합니다. 500GB가 최소 요구 사항이지만 언제나 더 큰 디스크를 프로비전할 수 있습니다. 프로비전된 후에는 디스크를 확장하거나 축소할 수 없습니다. 프로비전할 디스크의 크기에 대한 자세한 정보는 [모범 사례 문서](storsimple-ova-best-practices.md)의 크기 조정 섹션을 검토하세요. **다음**을 클릭합니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. **요약** 페이지에서 가상 데이터 디스크의 세부 정보를 검토한 후 만족스러우면 **마침**을 클릭하여 디스크를 만듭니다. 마법사가 닫히고 가상 하드 디스크가 컴퓨터에 추가됩니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. **설정** 페이지로 돌아갑니다. **확인**을 클릭하여 **설정** 페이지를 닫고 Hyper-V 관리자 창으로 돌아갑니다.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>3단계: 가상 배열 시작 및 IP 가져오기
가상 배열을 시작하여 연결하려면 다음 단계를 수행합니다.

#### <a name="to-start-the-virtual-array"></a>가상 배열을 시작하려면
1. 가상 배열을 시작합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. 디바이스가 실행된 후에 디바이스를 선택하고 마우스 오른쪽 단추를 클릭하여 **연결**을 선택합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. 디바이스가 준비되기까지 5-10분 정도 걸릴 수 있습니다. 콘솔에 진행률을 나타내는 상태 메시지가 표시됩니다. 디바이스가 준비되면 **작업**으로 이동합니다. `Ctrl + Alt + Delete` 키를 눌러서 가상 배열에 로그인합니다. 기본 사용자는 *StorSimpleAdmin*이고 기본 암호는 *Password1*입니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. 보안상의 이유로 디바이스 관리자 암호는 처음 로그인하면 만료됩니다. 암호를 변경하라는 메시지가 표시됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   8자 이상을 포함하는 암호를 입력합니다. 암호는 4가지 요구 사항(소문자, 대문자, 숫자 및 특수 문자) 중 3가지 이상을 충족해야 합니다. 확인을 위해 암호를 다시 입력합니다. 암호가 변경되었다는 메시지가 표시됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. 암호 변경이 완료되면 가상 배열이 다시 시작됩니다. 디바이스가 시작할 때까지 기다립니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    디바이스의 Windows PowerShell 콘솔이 진행률 표시줄과 함께 표시됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. 6-8단계는 DHCP 환경이 아닌 곳에서 부팅하는 경우에만 적용됩니다. DHCP 환경인 경우에는 이 단계를 건너뛰고 9단계로 이동하세요. DHCP 환경이 아닌 곳에서 디바이스를 부팅하는 경우에는 다음 화면이 표시됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    다음으로 네트워크를 구성합니다.
7. `Get-HcsIpAddress` 명령을 사용하여 가상 배열에 사용하도록 설정된 네트워크 인터페이스 목록을 표시합니다. 디바이스에 사용하도록 설정된 네트워크 인터페이스가 하나인 경우에는 `Ethernet`이라는 기본 이름이 인터페이스에 할당됩니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. `Set-HcsIpAddress` cmdlet을 사용하여 네트워크를 구성합니다. 다음 예제를 참조하세요.

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. 초기 설정이 완료된 후 디바이스가 부팅되면 디바이스 배너 텍스트가 표시됩니다. 디바이스 관리를 위해 배너 텍스트에 표시되는 IP 주소와 URL을 기록해 둡니다. IP 주소를 사용하여 가상 배열의 웹 UI를 연결하고 로컬 설정 및 등록을 완료합니다.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (선택 사항) 이 단계는 정부 클라우드에서 디바이스를 배포하는 경우에만 수행합니다. 이제 디바이스에서 미국 FIPS(Federal Information Processing Standard) 모드를 사용할 수 있습니다. FIPS 140 표준은 중요한 데이터의 보호를 위해 미국 연방 정부 컴퓨터 시스템에서 사용할 수 있도록 승인된 암호화 알고리즘을 정의합니다.

    1. FIPS 모드를 사용하도록 설정하려면 다음 cmdlet을 실행합니다.

        `Enable-HcsFIPSMode`
    2. 암호화 유효성 검사에 적용되도록 FIPS 모드를 사용하도록 설정한 후 디바이스를 다시 부팅합니다.

       > [!NOTE]
       > 디바이스에서 FIPS 모드를 사용 또는 사용하지 않도록 설정할 수 있습니다. 디바이스에서 FIPS 모드와 비 FIPS 모드가 교대로 반복되는 기능은 지원되지 않습니다.
       >
       >

디바이스가 최소 구성 요구 사항을 충족하지 못하면 아래 표시된 배너 텍스트에 다음 오류가 표시됩니다. 머신이 최소 요구 사항을 충족하기에 충분한 리소스를 확보하도록 디바이스 구성을 수정합니다. 그런 다음, 다시 시작하고 디바이스에 연결합니다. 1단계: 호스트 시스템이 최소 가상 배열 요구 사항을 충족하는지 확인의 최소 구성 요구 사항을 참조합니다.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

로컬 웹 UI를 사용하여 초기 구성을 하는 동안 다른 오류가 발생하면 다음 워크플로를 참조하세요.

* 진단 테스트를 실행하여 [웹 UI 설정 문제를 해결](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)합니다.
* [로그 패키지를 생성하고 로그 파일을 살펴봅니다](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>다음 단계
* [StorSimple 가상 배열을 파일 서버로 설정](storsimple-virtual-array-deploy3-fs-setup.md)
* [StorSimple 가상 배열을 iSCSI 서버로 설정](storsimple-virtual-array-deploy3-iscsi-setup.md)
