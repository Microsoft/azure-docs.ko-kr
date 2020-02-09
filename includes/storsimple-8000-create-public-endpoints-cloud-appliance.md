---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 1cf5bbdad555c50c418851904f36a578522843b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182265"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>클라우드 어플라이언스에 공용 엔드포인트를 만들려면

1. Azure 포털에 로그인합니다.
2. **Virtual Machines**로 이동한 후 클라우드 어플라이언스로 사용 중인 가상 머신을 선택합니다.
    
3. 가상 컴퓨터 내부 및 외부로 흐름을 제어하는 NSG(네트워크 보안 그룹) 규칙을 만들어야 합니다. NSG 규칙을 만들려면 다음 단계를 수행합니다.
    1. **네트워크 보안 그룹**을 선택합니다.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. 표시되는 기본 네트워크 보안 그룹을 클릭합니다.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. **인바운드 보안 규칙**을 선택합니다.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. **+ 추가**를 클릭하여 인바운드 보안 규칙을 만듭니다.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        인바운드 보안 규칙 추가 블레이드에서:

        1. 에 대 한 합니다 **이름**, 엔드포인트에 대해 다음 이름을 입력 합니다. WinRMHttps.
        
        2. **우선 순위**로는 1000(기본 규칙에 대한 우선 순위임)보다 작은 숫자를 선택합니다. 값이 높을수록 우선 순위는 더 낮아집니다.

        3. **원본**을 **모두**로 설정합니다.

        4. **서비스**로는 **WinRM**을 선택합니다. **프로토콜**은 **TCP**로 자동으로 설정되고 **포트 범위**는 **5986**으로 설정됩니다.

        5. **확인**을 클릭하여 규칙을 만듭니다.

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. 마지막 단계는 네트워크 보안 그룹을 서브넷 또는 특정 네트워크 인터페이스에 연결하는 것입니다. 네트워크 보안 그룹을 서브넷과 연결하려면 다음 단계를 수행합니다.
    1. **서브넷**으로 이동합니다.
    2. **+ 연결**을 클릭합니다.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. 가상 네트워크를 선택하고 적절한 서브넷을 선택합니다.
    4. **확인**을 클릭하여 규칙을 만듭니다.

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

규칙이 생성되면, 세부 정보를 보고 VIP(공용 가상 IP) 주소를 확인할 수 있습니다. 이 주소를 기록합니다.


