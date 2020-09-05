---
title: Azure Stack Edge의 컴퓨팅 기능을 사용한 데이터를 필터링하고 분석하기 위한 자습서 | Microsoft Docs
description: Azure Stack Edge에 컴퓨팅 역할을 구성하고 이 역할을 사용하여 데이터를 변환한 후에 Azure로 보내는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: ab9c93dc029498c70a828f38f297c1e02dcb29ee
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "83774137"
---
# <a name="tutorial-transform-data-with-azure-stack-edge"></a>자습서: Azure Stack Edge를 사용하여 데이터 변환

이 자습서에서는 Azure Stack Edge 디바이스에 컴퓨팅 역할을 구성하는 방법에 대해 설명합니다. 컴퓨팅 역할이 구성되면 Azure Stack Edge에서 데이터를 변환한 후에 Azure로 보낼 수 있습니다.

이 절차를 완료하는 데 약 10-15분이 걸릴 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 컴퓨팅 구성
> * 공유 추가
> * 컴퓨팅 모듈 추가
> * 데이터 변환 및 전송 확인

 
## <a name="prerequisites"></a>사전 요구 사항

Azure Stack Edge 디바이스에서 컴퓨팅 역할을 설정하기 전에 다음 사항을 확인합니다.

- [Azure Stack Edge 연결, 설정 및 활성화](azure-stack-edge-deploy-connect-setup-activate.md)에서 설명한 대로 Azure Stack Edge 디바이스를 활성화했습니다.


## <a name="configure-compute"></a>컴퓨팅 구성

Azure Stack Edge에 컴퓨팅을 구성하려면 IoT Hub 리소스를 만들어야 합니다.

1. Azure Stack Edge 리소스의 Azure Portal에서 개요로 이동합니다. 오른쪽에 있는 **컴퓨팅** 타일에서 **시작**을 선택합니다.

    ![컴퓨팅 시작](./media/azure-stack-edge-deploy-configure-compute/configure-compute-1.png)

2. **Edge 컴퓨팅 구성** 타일에서 **컴퓨팅 구성**을 선택합니다.
3. **Edge 컴퓨팅 구성** 블레이드에서 다음 정보를 입력합니다.

   
    |필드  |값  |
    |---------|---------|
    |IoT Hub     | **새로 만들기** 또는 **기존 항목** 중에서 선택합니다. <br> 표준 계층(S1)을 사용하여 IoT 리소스를 만드는 것이 기본입니다. 무료 계층 IoT 리소스를 사용하려면 IoT 리소스를 새로 만든 후 기존 리소스를 선택합니다. <br> 어떤 방법을 선택하든, IoT Hub 리소스는 Azure Stack Edge 리소스에서 사용하는 것과 동일한 구독 및 리소스 그룹을 사용합니다.     |
    |속성     |IoT Hub 리소스의 이름을 입력합니다.         |

    ![컴퓨팅 시작](./media/azure-stack-edge-deploy-configure-compute/configure-compute-2.png)

4. **만들기**를 선택합니다. IoT Hub 리소스 만들기는 몇 분 정도 걸립니다. IoT Hub 리소스가 만들어지면 컴퓨팅 구성을 표시하도록 **컴퓨팅 구성** 타일이 업데이트됩니다. Edge 컴퓨팅 역할이 구성되었는지 확인하려면 **컴퓨팅 구성** 타일의 **컴퓨팅 보기**를 선택합니다.
    
    ![컴퓨팅 시작](./media/azure-stack-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > IoT Hub가 Azure Stack Edge 디바이스와 연결되기 전에 **컴퓨팅 구성** 대화 상자가 닫히면 IoT Hub가 생성되지만 컴퓨팅 구성에는 표시되지 않습니다. 
    
    Edge 컴퓨팅 역할이 Edge 디바이스에 설정되면 두 가지 디바이스, 즉 IoT 디바이스와 IoT Edge 디바이스가 만들어집니다. 이 두 디바이스는 모두 IoT Hub 리소스에서 볼 수 있습니다. IoT Edge 런타임 역시 이 IoT Edge 디바이스에서 실행됩니다. 이 시점에서 IoT Edge 디바이스에는 Linux 플랫폼만 사용할 수 있습니다.


## <a name="add-shares"></a>공유 추가

이 자습서의 간단한 배포 작업을 수행하려면 Edge 공유와 Edge 로컬 공유가 필요합니다.

1. 다음 단계에 따라 디바이스에 Edge 공유를 추가합니다.

    1. Azure Stack Edge 리소스에서 **Edge 컴퓨팅 > 시작**으로 이동합니다.
    2. **공유 추가** 타일에서 **추가**를 선택합니다.
    3. **공유 추가** 블레이드에서 공유 이름을 입력하고 공유 유형을 선택합니다.
    4. Edge 공유를 탑재하려면 **Edge 컴퓨팅과 공유 사용** 확인란을 선택합니다.
    5. **스토리지 계정**, **스토리지 서비스**, 기존 사용자를 선택한 다음, **만들기**를 선택합니다.

        ![Edge 공유 추가](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-1.png) 

    로컬 NFS 공유를 만든 경우 다음 원격 공유(rsync) 명령 옵션을 사용하여 파일을 공유에 복사합니다.

    `rsync <source file path> < destination file path>`

    rsync 명령에 대한 자세한 내용은 [Rsync 설명서](https://www.computerhope.com/unix/rsync.htm)로 이동하세요.

    Edge 공유가 생성되면 성공적으로 생성되었다는 알림이 수신됩니다. 공유 목록이 업데이트될 수 있지만, 공유 만들기가 완료될 때까지 기다려야 합니다.

2. 이전 단계의 모든 과정을 반복하고 **Edge 로컬 공유로 구성** 확인란을 선택하여 Edge 디바이스에 Edge 로컬 공유를 추가합니다. 로컬 공유의 데이터는 디바이스에 계속 남아 있습니다.

    ![Edge 로컬 공유 추가](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. **공유 추가**를 선택하여 업데이트된 공유 목록을 볼 수 있습니다.

    ![업데이트된 공유 목록](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>모듈 추가

사용자 지정 또는 미리 작성된 모듈을 추가할 수 있습니다. 이 Edge 디바이스에는 사용자 지정 모듈이 없습니다. 사용자 지정 모듈을 만드는 방법을 알아보려면 [Azure Stack Edge 디바이스용 C# 모듈 개발](azure-stack-edge-create-iot-edge-module.md)로 이동합니다.

이 섹션에서는 사용자 지정 모듈을 [Azure Stack Edge용 C# 모듈 개발](azure-stack-edge-create-iot-edge-module.md)에서 만든 IoT Edge 디바이스에 추가합니다. 이 사용자 지정 모듈은 Edge 디바이스의 Edge 로컬 공유에서 파일을 가져와 디바이스의 Edge(클라우드) 공유로 이동합니다. 그런 다음, 클라우드 공유에서 파일을 클라우드 공유와 연결된 Azure 스토리지 계정에 푸시합니다.

1. **Edge 컴퓨팅 > 시작**으로 이동합니다. **모듈 추가** 타일에서 시나리오 유형을 **단순**으로 선택합니다. **추가**를 선택합니다.
2. **모듈 구성 및 추가** 블레이드에서 다음 값을 입력합니다.

    
    |필드  |값  |
    |---------|---------|
    |속성     | 모듈의 고유한 이름입니다. 이 모듈은 Azure Stack Edge와 연결된 IoT Edge 디바이스에 배포할 수 있는 Docker 컨테이너입니다.        |
    |이미지 URI     | 모듈의 해당 컨테이너 이미지에 대한 이미지 URI입니다.        |
    |자격 증명 필요     | 이 옵션을 선택하면 사용자 이름과 암호를 사용하여 URL이 일치하는 모듈을 검색하게 됩니다.        |
    |입력 공유     | 입력 공유를 선택합니다. 이 예에서는 Edge 로컬 공유가 입력 공유입니다. 여기에 사용된 모듈은 Edge 로컬 공유의 파일을 클라우드에 업로드되는 Edge 공유로 이동합니다.        |
    |출력 공유     | 출력 공유를 선택합니다. 이 예에서는 Edge 공유가 출력 공유입니다.        |
    |트리거 유형     | **파일** 또는 **예약** 중에서 선택합니다. 입력 공유에 파일이 기록되는 것처럼 파일 이벤트가 발생할 때마다 파일 트리거가 실행됩니다. 예약된 트리거는 사용자가 정의한 일정에 따라 실행됩니다.         |
    |트리거 이름     | 트리거의 고유한 이름입니다.         |
    |환경 변수| 모듈이 실행될 환경을 정의하는 데 도움이 되는 선택적 정보입니다.   |

    ![모듈 추가 및 구성](./media/azure-stack-edge-deploy-configure-compute/add-module-1.png)

3. **추가**를 선택합니다. 모듈이 추가됩니다. 모듈이 배포된 것을 나타내도록 **모듈 추가** 타일이 업데이트됩니다. 

    ![배포된 모듈](./media/azure-stack-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>데이터 변환 및 전송 확인

마지막 단계는 모듈이 예상대로 연결되어 실행되는지 확인하는 것입니다. IoT Hub 리소스에서 IoT Edge 디바이스에 대한 모듈의 런타임 상태가 실행 중이어야 합니다.

모듈이 실행되는지 확인하려면 다음을 수행합니다.

1. **모듈 추가** 타일을 선택합니다. 그러면 **모듈** 블레이드로 이동됩니다. 모듈 목록에서, 배포한 모듈을 찾습니다. 추가한 모듈의 런타임 상태가 *실행 중*일 것입니다.

    ![데이터 변환 확인](./media/azure-stack-edge-deploy-configure-compute/verify-data-1.png)
 
1. 파일 탐색기에서, 이전에 만든 Edge 로컬 및 Edge 공유에 모두 연결합니다.

    ![데이터 변환 확인](./media/azure-stack-edge-deploy-configure-compute/verify-data-2.png) 
 
1. 로컬 공유에 데이터를 추가합니다.

    ![데이터 변환 확인](./media/azure-stack-edge-deploy-configure-compute/verify-data-3.png) 
 
    데이터가 클라우드 공유로 이동합니다.

    ![데이터 변환 확인](./media/azure-stack-edge-deploy-configure-compute/verify-data-4.png)  

    그런 다음, 데이터가 클라우드 공유에서 스토리지 계정으로 푸시됩니다. 데이터를 보려면 Storage Explorer로 이동합니다.

    ![데이터 변환 확인](./media/azure-stack-edge-deploy-configure-compute/verify-data-5.png) 
 
유효성 검사 프로세스를 완료했습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 컴퓨팅 구성
> * 공유 추가
> * 컴퓨팅 모듈 추가
> * 데이터 변환 및 전송 확인

Azure Stack Edge 디바이스를 관리하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [로컬 웹 UI를 사용하여 Azure Stack Edge 관리](azure-stack-edge-manage-access-power-connectivity-mode.md)
