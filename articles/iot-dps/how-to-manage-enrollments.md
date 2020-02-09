---
title: Azure Portal에서 Azure IoT Hub 장치 프로 비전 서비스에 대 한 장치 등록 관리
description: Azure Portal에서 DPS (장치 프로 비전 서비스)에 대 한 장치 등록을 관리 하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974941"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Azure Portal을 사용 하 여 장치 등록을 관리 하는 방법

*장치 등록* 은 특정 시점에 Azure IoT Hub 장치 프로 비전 서비스에 등록할 수 있는 단일 장치 또는 장치 그룹의 레코드를 만듭니다. 등록 레코드에는 원하는 IoT hub를 포함 하 여 해당 등록의 일부로 장치에 대 한 초기 desired 구성이 포함 됩니다. 이 문서에서는 프로 비전 서비스에 대 한 장치 등록을 관리 하는 방법을 보여 줍니다.


## <a name="create-a-device-enrollment"></a>장치 등록 만들기

프로 비전 서비스를 사용 하 여 장치를 등록 하는 방법에는 두 가지가 있습니다.

* **등록 그룹** 은 물리적 장치에서 장치 인증서를 생성 하는 데 사용 되는 [루트 인증서](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) 또는 [중간 인증서](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)일 수 있는 동일한 서명 인증서로 서명 된 x.509 인증서의 일반적인 증명 메커니즘을 공유 하는 장치 그룹에 대 한 항목입니다. 원하는 초기 구성을 공유 하는 많은 수의 장치 또는 동일한 테넌트로 이동 하는 장치에 대해 등록 그룹을 사용 하는 것이 좋습니다. X.509 증명 메커니즘을 사용 하는 장치만 *등록 그룹*으로 등록할 수 있습니다. 

    다음 단계를 사용 하 여 포털에서 장치 그룹에 대 한 등록 그룹을 만들 수 있습니다.

  1. Azure Portal에 로그인 하 고 왼쪽 메뉴에서 **모든 리소스** 를 클릭 합니다.  
  1. 리소스 목록에서 장치를 등록 하려는 장치 프로 비전 서비스를 클릭 합니다.  
  1. 프로 비전 서비스에서 다음을 수행 합니다.  
     a. **등록 관리**를 클릭 한 다음 **등록 그룹** 탭을 선택 합니다.  
     b. Kattintson a felül lévő **Hozzáadás** gombra.  
     c. "등록 그룹 추가" 패널이 표시 되 면 등록 목록 항목에 대 한 정보를 입력 합니다.  **그룹 이름은** 필수입니다. 또한 **인증서 유형에**대해 "CA 또는 중간"을 선택 하 고 장치 그룹에 대 한 루트 **기본 인증서** 를 업로드 합니다.  
     d. Kattintson a **Save** (Mentés) gombra. 등록 그룹을 성공적으로 만들면 **등록** 그룹 탭 아래에 그룹 이름이 표시 됩니다.  

     [포털에서 ![등록 그룹](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **개별 등록** 은 등록할 수 있는 단일 장치에 대 한 항목입니다. 개별 등록은 증명 메커니즘으로 x509 인증서 또는 SAS 토큰 (실제 또는 가상 TPM) 중 하나를 사용할 수 있습니다. 고유한 초기 구성이 필요한 장치 또는 증명 메커니즘으로 TPM 또는 가상 TPM을 통해 SAS 토큰만을 사용할 수 있는 장치에 대해 개별 등록를 사용 하는 것이 좋습니다. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    다음 단계를 사용 하 여 포털에서 개별 등록을 만들 수 있습니다.

    1. Azure Portal에 로그인 하 고 왼쪽 메뉴에서 **모든 리소스** 를 클릭 합니다.
    1. 리소스 목록에서 장치를 등록 하려는 장치 프로 비전 서비스를 클릭 합니다.
    1. 프로 비전 서비스에서 다음을 수행 합니다.  
       a. **등록 관리**를 클릭 하 고 **개별 등록** 탭을 선택 합니다.  
       b. Kattintson a felül lévő **Hozzáadás** gombra.   
       c. "등록 추가" 패널이 표시 되 면 등록 목록 항목에 대 한 정보를 입력 합니다. 먼저 장치에 대 한 증명 **메커니즘** (X.509 또는 TPM)을 선택 합니다. X.509 증명을 사용 하려면 장치에 대 한 리프 **기본 인증서** 를 업로드 해야 합니다. TPM을 사용 하려면 장치에 대 한 **증명 키** 및 **등록 ID** 를 입력 해야 합니다.  
       d. Kattintson a **Save** (Mentés) gombra. 등록 그룹이 성공적으로 만들어지면 장치가 **개별 등록** 탭 아래에 표시 됩니다.  

       [포털에서 개별 등록 ![](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>등록 항목 업데이트
다음 단계를 사용 하 여 포털에서 기존 등록 항목을 업데이트할 수 있습니다.

1. Azure Portal에서 장치 프로 비전 서비스를 열고 **등록 관리**를 클릭 합니다. 
1. 수정 하려는 등록 항목으로 이동 합니다. 장치 등록에 대 한 요약 정보를 여는 항목을 클릭 합니다. 
1. 이 페이지에서 장치 ID 뿐만 아니라 장치를 연결 해야 하는 IoT hub와 같은 보안 유형 및 자격 증명 이외의 항목을 수정할 수 있습니다. 초기 장치 쌍 상태를 수정할 수도 있습니다. 
1. 완료 되 면 **저장** 을 클릭 하 여 장치 등록을 업데이트 합니다. 

    ![포털에서 등록 업데이트](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>장치 등록 제거
IoT hub에 장치를 프로 비전 할 필요가 없는 경우 다음 단계를 사용 하 여 포털에서 관련 등록 항목을 제거할 수 있습니다.

1. Azure Portal에서 장치 프로 비전 서비스를 열고 **등록 관리**를 클릭 합니다. 
1. 제거 하려는 등록 항목으로 이동 하 여 선택 합니다. 
1. 위쪽의 **삭제** 단추를 클릭 한 다음 확인 메시지가 표시 되 면 **예** 를 선택 합니다. 
1. 작업이 완료 되 면 장치 등록 목록에서 제거 된 항목이 표시 됩니다. 
 
    ![포털에서 등록 제거](./media/how-to-manage-enrollments/remove-enrollment.png)


