---
title: Azure Portal에서 Azure Data Box Gateway 연결, 구성 및 활성화
description: Data Box Gateway 배포에 대한 세 번째 자습서에서는 가상 디바이스에 연결하고, 설정하고, 활성화하는 방법을 안내합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 78c06cc7f08fe94a25ea63d9bf76cc1352d9f2b7
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "82561695"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>자습서: Azure Data Box Gateway 연결, 설정 및 활성화

## <a name="introduction"></a>소개

이 자습서에는 로컬 웹 UI를 사용하여 Data Box Gateway 디바이스에 연결하고, 설정하고, 활성화하는 방법을 설명합니다. 

설정 및 활성화 프로세스를 완료하는 데 10분 정도가 소요됩니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 디바이스에 연결
> * 가상 디바이스 설정 및 활성화

## <a name="prerequisites"></a>사전 요구 사항

Data Box Gateway를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Hyper-V에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-hyperv.md) 또는 [VMware에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-vmware.md)에서 설명한 대로 가상 디바이스를 프로비저닝하고 가상 디바이스에 연결된 URL을 가져왔습니다.
* Data Box Gateway 디바이스를 관리하기 위해 만든 Data Box Gateway 서비스의 활성화 키를 갖고 있습니다. 자세한 내용은 [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)를 참조하세요.


## <a name="connect-to-the-local-web-ui-setup"></a>로컬 웹 UI 설정에 연결 

1. 브라우저 창을 열고 다음 주소에서 디바이스의 로컬 웹 UI에 액세스합니다.
   
   https:\//ip-address-of-network-interface
   
   이전 자습서에서 언급한 연결 URL을 사용합니다. 웹 사이트의 보안 인증서에 문제가 있음을 나타내는 오류 또는 경고가 표시됩니다.

2. **이 웹 페이지에서 계속 진행**을 선택합니다. 이러한 단계는 사용 중인 브라우저에 따라 달라질 수 있습니다.
   
    ![웹 사이트 보안 인증서 오류 메시지](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. 가상 디바이스의 웹 UI에 로그인합니다. 기본 암호는 *Password1*입니다. 
   
    ![로컬 웹 UI에 로그인](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. 프롬프트에서 디바이스 암호를 변경합니다. 새 암호는 8-16자여야 합니다. 대문자, 소문자, 숫자 및 특수 문자 중 3가지가 포함되어야 합니다.

    ![디바이스 암호 변경](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

이제 디바이스의 **대시보드**에 있습니다.

## <a name="set-up-and-activate-the-virtual-device"></a>가상 디바이스 설정 및 활성화
 
대시보드에는 가상 디바이스를 구성하고 Data Box Gateway 서비스에 등록하는 데 필요한 다양한 설정이 표시됩니다. **디바이스 이름**, **네트워크 설정**, **웹 프록시 설정** 및 **시간 설정**은 선택 사항입니다. 유일한 필수 설정은 **클라우드 설정**입니다.
   
![로컬 웹 UI "대시보드" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. 왼쪽 창에서 **디바이스 이름**을 선택하한 다음, 디바이스의 식별 이름을 입력합니다. 식별 이름은 문자, 숫자 및 하이픈을 포함하는 1-15자로 구성되어야 합니다. 

    ![로컬 웹 UI "디바이스 이름" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (선택 사항) 왼쪽 창에서 **네트워크 설정**을 선택한 다음, 설정을 구성합니다. 기본 가상 머신에서 구성한 네트워크 인터페이스 수에 따라 하나 이상의 네트워크 인터페이스가 표시됩니다. 아래와 같이 네트워크 인터페이스 하나가 설정된 가상 디바이스의 **네트워크 설정** 페이지가 표시됩니다.
    
    ![로컬 웹 UI "네트워크 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    네트워크 설정을 구성할 때 다음 사항에 유의해야 합니다.

    - 환경에서 DHCP를 사용하도록 설정하면 네트워크 인터페이스가 자동으로 구성됩니다. IP 주소, 서브넷, 게이트웨이 및 DNS가 자동으로 할당됩니다.
    - DHCP를 사용하지 않는 경우 필요에 따라 고정 IP를 할당할 수 있습니다.
    - 네트워크 인터페이스를 IPv4로 구성할 수 있습니다.

     >[!NOTE] 
     > 디바이스에 연결할 다른 IP 주소가 없다면 네트워크 인터페이스의 로컬 IP 주소를 정적에서 DCHP로 전환하지 않는 것이 좋습니다. 하나의 네트워크 인터페이스를 사용하며 DHCP로 전환하는 경우 DHCP 주소를 확인할 방법이 없습니다. DHCP 주소로 변경하려는 경우 디바이스가 서비스에 등록될 때까지 기다렸다가 변경합니다. 그러면 서비스에 대한 Azure Portal의 **디바이스 속성**에서 모든 어댑터의 IP를 볼 수 있습니다.

3. (선택 사항) 웹 프록시 서버를 구성합니다. 웹 프록시 구성은 선택 사항이지만 웹 프록시를 사용하는 경우 이 페이지에서만 구성할 수 있습니다.
   
   ![로컬 웹 UI "웹 프록시 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   **웹 프록시** 페이지에서 다음을 수행합니다.
   
   1. **웹 프록시 URL** 상자에 `http://&lt;host-IP address or FQDN&gt;:Port number` 형식으로 URL을 입력합니다. HTTPS URL은 지원되지 않습니다.
   2. **인증** 아래에서 **없음** 또는 **NTLM**을 선택합니다.
   3. 인증을 사용하는 경우 **사용자 이름**과 **암호**를 입력합니다.
   4. 구성한 웹 프록시 설정의 유효성을 검사하고 적용하려면 **적용**을 선택합니다.

   > [!NOTE]
   > PAC(프록시 자동 구성) 파일은 지원되지 않습니다. PAC 파일은 웹 브라우저 및 다른 사용자 에이전트가 지정된 URL을 가져오는 데 적절한 프록시 서버(액세스 방법)를 자동으로 선택하는 방법을 정의합니다.
   > 프록시의 인증서를 신뢰할 수 없기 때문에 모든 트래픽을 가로채고 읽는(그런 다음, 자체 인증을 사용하여 모든 항목을 다시 서명함) 프록시는 호환되지 않습니다.
   > 일반적으로 투명 프록시는 Azure Data Box Gateway에서 잘 작동합니다.

4. (선택 사항) 왼쪽 창에서 **시간 설정**을 선택하고 디바이스의 표준 시간대와 기본 및 보조 NTP 서버를 구성합니다. 

    클라우드 서비스 공급자와 인증할 수 있도록 디바이스 시간을 동기화해야 하기 때문에 NTP 서버가 필요합니다.
    
    ![로컬 웹 UI "시간 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    **시간 설정** 페이지에서 다음을 수행합니다.
    
    1. **표준 시간대** 드롭다운 목록에서 디바이스가 배포되는 지리적 위치에 해당하는 표준 시간대를 선택합니다.
        디바이스의 기본 표준 시간대는 PST입니다. 디바이스는 모든 예약된 작업에 대해 이 표준 시간대를 사용합니다.

    2. 디바이스의 **기본 NTP 서버**를 지정하거나 기본값인 `time.windows.com`을 적용합니다.   
        네트워크에서 NTP 트래픽이 데이터 센터에서 인터넷으로 전달되도록 허용하는지 확인합니다.

    3. 필요에 따라 **보조 NTP 서버** 상자에 디바이스의 보조 서버를 입력합니다.

    4. 구성된 시간 설정의 유효성을 검사하고 설정을 적용하려면 **적용**을 선택합니다.

6. 왼쪽 창에서 **클라우드 설정**을 선택한 다음, Azure Portal에서 Data Box Edge 서비스를 사용하여 디바이스를 활성화합니다.
    
    1. **활성화 키** 상자에 Data Box Gateway의 [활성화 키 가져오기](data-box-gateway-deploy-prep.md#get-the-activation-key)에서 얻은 **활성화 키**를 입력합니다.

    2. **활성화**를 선택합니다.
       
         ![로컬 웹 UI "클라우드 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. 디바이스가 활성화되고 중요 업데이트가 있으면 자동으로 적용됩니다. 그 영향에 대한 알림이 표시됩니다. Azure Portal을 통해 업데이트 진행률을 모니터링합니다.

        ![로컬 웹 UI "클라우드 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **대화 상자에 복구 키도 표시되는데, 이 키를 복사하여 안전한 위치에 저장해야 합니다. 이 키는 디바이스를 부팅할 수 없는 상황이 발생하면 데이터를 복구하는 데 사용됩니다.**


    4. 업데이트가 완료될 때까지 몇 분 정도 기다려야 할 수도 있습니다. 업데이트가 완료되면 디바이스에 로그인합니다. **클라우드 설정** 페이지가 업데이트되면 디바이스가 성공적으로 활성화된 것으로 나타납니다.

        ![로컬 웹 UI "클라우드 설정" 페이지 업데이트됨](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

디바이스 설정이 완료되었습니다. 이제 디바이스에서 공유를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 가상 디바이스에 연결
> * 가상 디바이스 설정 및 활성화

Data Box Gateway를 사용하여 데이터를 전송하는 방법을 알아보려면 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [Data Box Gateway를 통해 데이터 전송](./data-box-gateway-deploy-add-shares.md).
