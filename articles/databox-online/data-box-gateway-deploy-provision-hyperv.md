---
title: Hyper-V에서 Azure Data Box Gateway 프로비전 자습서 | Microsoft Docs
description: Azure Data Box Gateway 배포의 두 번째 자습서에서는 Hyper-V에서 가상 디바이스를 프로비전합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: ffbfd3214242d8df5fe33faf465bc1da3eb9986d
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196630"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>자습서: Hyper-V에서 Azure Data Box Gateway 프로비저닝

## <a name="overview"></a>개요

이 자습서에서는 Windows Server 2016, Windows Server 2012 R2 또는 Windows Server 2012에서 Hyper-V를 실행하는 호스트 시스템에 Data Box Gateway를 프로비전하는 방법을 설명합니다.

가상 디바이스를 프로비전하고 구성하려면 관리자 권한이 필요합니다. 프로비전 및 초기 설정을 완료하는 데 10분 정도가 소요됩니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 호스트가 최소 디바이스 요구 사항을 충족하는지 확인
> * 하이퍼바이저에서 가상 디바이스 프로비전
> * 가상 디바이스 시작 및 IP 주소 가져오기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Windows Server 2016 또는 Windows Server 2012 R2에서 Hyper-V를 실행하는 호스트 시스템에 가상 디바이스를 프로비전하기 위한 필수 조건은 다음과 같습니다.

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway 리소스의 경우

시작하기 전에 다음 사항을 확인합니다.

* [Data Box Gateway에 대한 포털 준비](data-box-gateway-deploy-prep.md)의 모든 단계를 완료했습니다.
* [Data Box Gateway에 대한 포털 준비](data-box-gateway-deploy-prep.md)에 설명된 대로 Azure Portal에서 Hyper-V에 대한 가상 디바이스 이미지를 다운로드했습니다.

  > [!IMPORTANT]
  > Data Box Gateway에서 실행되는 소프트웨어는 Data Box Gateway 리소스에서만 사용할 수 있습니다.

### <a name="for-the-data-box-gateway-virtual-device"></a>Data Box Gateway 가상 디바이스의 경우

디바이스를 배포하기 전에 다음 사항을 확인합니다.

* 디바이스 프로비전에 사용할 수 있는, Windows Server 2012 R2 이상에서 Hyper-V를 실행하는 호스트 시스템에 액세스할 수 있습니다.
* 가상 디스크 프로비전을 위해 호스트 시스템에서 다음 리소스를 전용할 수 있습니다.

  * 코어 4개 이상
  * RAM 8GB 이상
  * 네트워크 인터페이스 하나
  * 250GB OS 디스크
  * 데이터용 2TB 가상 디스크

### <a name="for-the-network-in-the-datacenter"></a>데이터 센터에서 네트워크의 경우

시작하기 전에

* Data Box Gateway를 배포하기 위한 네트워킹 요구 사항을 검토하고, 요구 사항에 따라 데이터 센터 네트워크를 구성합니다. 자세한 내용은 [Data Box Gateway 네트워킹 요구 사항](data-box-gateway-system-requirements.md#networking-port-requirements)을 참조하세요.
* 디바이스가 최적으로 작동할 수 있도록 최소 인터넷 대역폭이 20Mbps인지 확인합니다.

## <a name="check-the-host-system"></a>호스트 시스템 확인

가상 디바이스를 만들려면 다음이 필요합니다.

* Windows Server 2016, Windows Server 2012 R2 또는 Windows Server 2012에 설치되어 있는 Hyper-V 역할
* 호스트에 연결된 Microsoft Windows 클라이언트의 Microsoft Hyper-V 관리자
* 가상 디바이스를 만드는 기본 하드웨어(호스트 시스템)에서 가상 디바이스에 대해 다음 리소스를 전용으로 사용할 수 있는지 확인합니다.

  * 최소 4개의 가상 프로세서
  * RAM 8GB 이상
  * 인터넷으로 트래픽을 라우팅할 수 있는 네트워크에 연결된 네트워크 인터페이스 하나.
  * 250GB OS 디스크
  * 시스템 데이터용 가상 디스크 2TB

## <a name="bitlocker-considerations"></a>BitLocker 고려 사항

* Data Box Gateway 가상 머신에서 BitLocker를 사용하도록 설정하는 것이 좋습니다. 기본적으로 BitLocker가 활성화되지 않았습니다. 자세한 내용은 다음을 참조하세요.
  * [Hyper-V 관리자의 암호화 지원 설정](h https://docs.microsoft.com/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [가상 머신의 BitLocker 지원](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>하이퍼바이저에서 가상 디바이스 프로비전

하이퍼바이저에서 디바이스를 프로비전하려면 다음 단계를 수행합니다.

1. Windows Server 호스트에서 로컬 드라이브에 가상 디바이스 이미지를 복사합니다. Azure Portal을 통해 이 VHDX 이미지를 다운로드합니다. 나중에 절차에서 이 이미지를 사용하므로 이미지를 복사한 위치를 적어 둡니다.
2. **서버 관리자**를 엽니다. 오른쪽 위 모서리에서 **도구**를 클릭하고 **Hyper-V 관리자**를 선택합니다.

    ![서버 관리자에서 Hyper-V 관리자 선택](./media/data-box-gateway-deploy-provision-hyperv/image1.png)  
  
3. **Hyper-V 관리자**의 범위 창에서 시스템 노드를 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 연 다음 **새로 만들기** > **Virtual Machine**을 클릭합니다.

   ![Hyper-V 관리자에서 새 가상 머신 만들기](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. 새 Virtual Machine 마법사의 **시작하기 전에** 페이지에서 **다음**을 클릭합니다.
5. **이름 및 위치 지정** 페이지에서 가상 디바이스의 **이름**을 입력합니다. **다음**을 클릭합니다.

   ![이름 및 위치 지정 페이지](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. **세대 지정** 페이지에서 .vhdx 디바이스 이미지 유형으로 **세대 2**를 선택하고 **다음**을 클릭합니다.    

   ![세대 지정 페이지](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. **메모리 할당** 페이지에서 **시작 메모리**를 **8192MB** 이상으로 지정하고, 동적 메모리는 사용하도록 설정하지 않은 후 **다음**을 클릭합니다.

   ![메모리 할당 페이지](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. **네트워킹 구성** 페이지에서 인터넷에 연결된 가상 스위치를 지정하고 **다음**을 클릭합니다.

   ![네트워킹 구성 페이지](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. **가상 하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용**을 선택하고 가상 디바이스 이미지의 위치를 지정한 후 **다음**을 클릭합니다.

   ![가상 하드 디스크 연결 페이지](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. **요약**을 검토하고 **마침**을 클릭하여 가상 머신을 만듭니다.

    ![새 가상 머신 마법사 완료 페이지](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. 최소 요구 사항을 충족하려면 가상 프로세서 4개가 필요합니다. 4개의 가상 프로세서를 추가하려면 **Hyper-V 관리자** 창에서 호스트 시스템을 선택합니다. **Virtual Machines** 목록의 오른쪽 창에서 방금 만든 Virtual Machines를 찾습니다. 컴퓨터 이름을 선택하고 마우스 오른쪽 단추로 클릭한 후 **설정**을 선택합니다.

    ![가상 머신 설정](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. **설정** 페이지의 왼쪽 창에서 **프로세서**를 클릭합니다. 오른쪽 창에서 **가상 프로세서 수**를 4(또는 그 이상)로 설정합니다. **적용**을 클릭합니다.

    ![설정 페이지에서 가상 프로세서 수 설정](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. 최소 요구 사항을 충족하려면 가상 데이터 디스크를 2TB 추가해야 합니다. **설정** 페이지에서:

    1. 왼쪽 창의 **SCSI 컨트롤러**를 선택합니다.
    2. 오른쪽 창의 **하드 드라이브**를 선택하고 **추가**를 클릭합니다.

    ![설정 페이지에 하드 드라이브 추가](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. **하드 드라이브** 페이지에서 **가상 하드 디스크** 옵션을 선택하고 **새로 만들기**를 클릭합니다. **새 가상 하드 디스크 마법사**가 시작됩니다.

    ![새 가상 하드 디스크 마법사](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. 새 가상 하드 디스크 마법사의 **시작하기 전에** 페이지에서 **다음**을 클릭합니다.
16. **디스크 형식 선택** 페이지에서 **VHDX** 형식의 기본 옵션을 적용합니다. **다음**을 클릭합니다.
17. **디스크 유형 선택** 페이지에서 가상 하드 디스크 유형을 **동적 확장**(권장)으로 설정합니다. **고정 크기** 디스크는 작동은 되지만 오래 기다려야 할 수 있습니다. **차이점 보관용** 옵션은 사용하지 않는 것이 좋습니다. **다음**을 클릭합니다.

    ![디스크 유형 선택 페이지](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. **이름 및 위치 지정** 페이지에서 데이터 디스크의 **이름** 및 **위치**(해당 위치로 이동 가능)를 입력합니다. **다음**을 클릭합니다.

    ![이름 및 위치 지정 페이지](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. **디스크 구성** 페이지에서 **비어 있는 새 가상 하드 디스크 만들기** 옵션을 선택하고 크기를 **2TB**(또는 그 이상)로 지정합니다.

    2TB가 최소 요구 사항이지만 언제든지 더 큰 디스크를 프로비전할 수 있습니다. 프로비전한 후에는 디스크를 축소할 수 없습니다. 디스크를 축소하려고 하면 디바이스의 모든 로컬 데이터가 손실됩니다. 데이터 디스크 확장은 지원되지 않습니다. **다음**을 클릭합니다.

    ![디스크 구성 페이지](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. **요약** 페이지에서 가상 데이터 디스크의 세부 정보를 검토한 후 만족스러우면 **마침**을 클릭하여 디스크를 만듭니다. 마법사가 닫히고 가상 하드 디스크가 컴퓨터에 추가됩니다.

    ![새 가상 하드 디스크 마법사 완료 페이지](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. **설정** 페이지로 돌아갑니다. **확인**을 클릭하여 **설정** 페이지를 닫고 Hyper-V 관리자 창으로 돌아갑니다.

    ![설정 페이지](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

## <a name="start-the-virtual-device-and-get-the-ip"></a>가상 디바이스 시작 및 IP 가져오기

가상 디바이스를 시작하여 연결하려면 다음 단계를 수행합니다.

#### <a name="to-start-the-virtual-device"></a>가상 디바이스를 시작하려면

1. 가상 디바이스를 시작합니다.

   ![가상 디바이스 시작](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. 디바이스가 실행된 후에 디바이스를 선택하고 마우스 오른쪽 단추를 클릭하여 **연결**을 선택합니다.

3. 디바이스가 준비되기까지 10~15분 정도 걸릴 수 있습니다. 콘솔에 진행률을 나타내는 상태 메시지가 표시됩니다. 디바이스가 준비되면 **작업**으로 이동합니다. `Ctrl + Alt + Delete`를 눌러서 가상 디바이스에 로그인합니다. 기본 사용자는 *EdgeUser*이고, 기본 암호는 *Password1*입니다.

   ![가상 디바이스에 로그인](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. 5-7단계는 DHCP 환경이 아닌 곳에서 부팅하는 경우에만 적용됩니다. DHCP 환경인 경우 이 단계를 건너뜁니다. DHCP 환경이 아닌 곳에서 디바이스를 부팅한 경우에는 다음 메시지가 표시됩니다.

5. 네트워크를 구성하려면 `Get-HcsIpAddress` 명령을 사용하여 가상 디바이스에서 사용하도록 설정된 네트워크 인터페이스 목록을 표시합니다. 디바이스에 사용하도록 설정된 네트워크 인터페이스가 하나인 경우에는 `Ethernet`이라는 기본 이름이 인터페이스에 할당됩니다.

6. `Set-HcsIpAddress` cmdlet을 사용하여 네트워크를 구성합니다. 다음 예제를 참조하십시오.

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. 초기 설정이 완료된 후 디바이스가 부팅되면 디바이스 배너 텍스트가 표시됩니다. 디바이스 관리를 위해 배너 텍스트에 표시되는 IP 주소와 URL을 기록해 둡니다. IP 주소를 사용하여 가상 디바이스의 웹 UI에 연결하고 로컬 설정 및 활성화를 완료합니다.

   ![IP 주소 및 연결 URL이 있는 가상 디바이스 배너](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

디바이스가 최소 구성 요구 사항을 충족하지 않으면 배너 텍스트에 오류가 표시됩니다. 머신이 최소 요구 사항을 충족하기에 충분한 리소스를 확보하도록 디바이스 구성을 수정합니다. 그런 다음, 다시 시작하고 디바이스에 연결합니다. [호스트 시스템이 최소 가상 디바이스 요구 사항을 충족하는지 확인](#check-the-host-system)의 최소 구성 요구 사항을 참조하세요.

로컬 웹 UI를 사용하여 초기 구성을 하는 동안 다른 오류가 발생하면 다음 워크플로를 참조하세요.

* [진단 테스트를 실행하여 웹 UI 설정 문제를 해결](data-box-gateway-troubleshoot.md#run-diagnostics)합니다.
* [로그 패키지를 생성하고 로그 파일을 살펴봅니다](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Data Box Gateway 토픽에 대해 알아보았습니다.

> [!div class="checklist"]
> * 호스트가 최소 디바이스 요구 사항을 충족하는지 확인
> * 하이퍼바이저에서 가상 디바이스 프로비전
> * 가상 디바이스 시작 및 IP 주소 가져오기

다음 자습서로 이동하여 가상 디바이스를 연결하고, 설정하고, 활성화하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Data Box Gateway 연결 및 설정](./data-box-gateway-deploy-connect-setup-activate.md)
