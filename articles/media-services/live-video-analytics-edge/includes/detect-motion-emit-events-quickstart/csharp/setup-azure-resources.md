---
ms.openlocfilehash: 67d90836c382728f989ab2cb4fde4d81bac9eb25
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691053"
---
이 자습서를 사용하려면 다음과 같은 Azure 리소스가 필요합니다.

* IoT Hub
* 스토리지 계정
* Azure Media Services 계정
* [IoT Edge 런타임](../../../../../iot-edge/how-to-install-iot-edge-linux.md)이 설치된 Azure의 Linux VM

이 빠른 시작에서는 [Live Video Analytics 리소스 설치 스크립트](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)를 사용하여 Azure 구독에 필요한 리소스를 배포하는 것이 좋습니다. 이렇게 하려면 다음 단계를 따르십시오.

1. [Azure Cloud Shell](https://shell.azure.com)을 엽니다.
1. Cloud Shell을 처음 사용하는 경우 스토리지 계정 및 Microsoft Azure Files 공유를 만들 구독을 선택하라는 메시지가 표시됩니다. **스토리지 만들기**를 선택하여 Cloud Shell 세션 정보에 대한 스토리지 계정을 만듭니다. 이 스토리지 계정은 Azure Media Services 계정에서 사용하기 위해 스크립트에서 만드는 계정과는 다릅니다.
1. Cloud Shell 창 왼쪽의 드롭다운 메뉴에서 사용자 환경으로 **Bash**를 선택합니다.

    ![환경 선택기](../../../media/quickstarts/env-selector.png)
1. 다음 명령을 실행합니다.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    스크립트가 성공적으로 완료되면 구독에 필요한 모든 리소스가 표시됩니다.
1. 스크립트가 완료되면 중괄호를 선택하여 폴더 구조를 표시합니다. *~/clouddrive/lva-sample* 디렉터리에 몇 개의 파일이 표시됩니다. 이 빠른 시작에서 유의해야 할 사항은 다음과 같습니다.

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** - 이 파일에는 Visual Studio Code가 에지 디바이스에 모듈을 배포하는 데 사용하는 속성이 포함되어 있습니다.
     * ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code에서 샘플 코드를 실행하는 데 이 파일을 사용합니다.
     
    다음 섹션의 Visual Studio Code에서 개발 환경을 설정할 때 이러한 파일이 필요합니다. 지금은 로컬 파일에 복사하는 것이 좋습니다.
    
    ![앱 설정](../../../media/quickstarts/clouddrive.png)
    