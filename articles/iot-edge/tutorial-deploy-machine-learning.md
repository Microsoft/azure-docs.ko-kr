---
title: 자습서 - Azure IoT Edge를 사용하여 디바이스에 Azure Machine Learning 배포
description: 이 자습서에서는 Azure Machine Learning을 만든 다음, Edge 디바이스에 모듈로 배포합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: fd297ec5cfb7831a438fc51e72e3c2fc163eff49
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271280"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>자습서: Azure Machine Learning을 IoT Edge 모듈로 배포(미리 보기)

Azure Notebooks를 사용하여 기계 학습 모듈을 개발하고 Azure IoT Edge를 실행하는 Linux 디바이스에 배포합니다.
비즈니스 논리를 직접 IoT Edge 디바이스에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 시뮬레이션된 컴퓨터 온도 데이터에 따라 디바이스가 실패하는 경우를 예측하는 Azure Machine Learning 모듈을 배포하는 과정을 안내합니다. IoT Edge의 Azure Machine Learning에 대한 자세한 내용은 [Azure Machine Learning 설명서](../machine-learning/how-to-deploy-and-where.md)를 참조하세요.

>[!NOTE]
>Azure IoT Edge의 Azure Machine Learning 모듈은 공개 미리 보기로 있습니다.

이 자습서에서 만드는 Azure Machine Learning 모듈은 디바이스에서 생성된 환경 데이터를 읽고, 메시지에 비정상 레이블을 지정하기도 합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Azure Machine Learning 모듈 만들기
> * Azure 컨테이너 레지스트리에 모듈 컨테이너 푸시
> * Azure Machine Learning 모듈을 IoT Edge 디바이스에 배포
> * 생성된 데이터를 봅니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure IoT Edge 디바이스:

* [Linux](quickstart-linux.md)의 빠른 시작에 설명된 단계에 따라 Azure 가상 머신을 IoT Edge 디바이스로 사용할 수 있습니다.
* Azure Machine Learning 모듈은 Windows 컨테이너를 지원하지 않습니다.
* Azure Machine Learning 모듈은 ARM 프로세서를 지원하지 않습니다.

클라우드 리소스:

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Azure Machine Learning 작업 영역 [Azure Portal을 사용하여 Azure Machine Learning 시작](../machine-learning/tutorial-1st-experiment-sdk-setup.md)의 지침에 따라 작업 영역을 만들어서 사용하는 방법을 알아봅니다.
  * 작업 영역 이름, 리소스 그룹 및 구독 ID를 적어둡니다. 이러한 값은 모두 Azure Portal의 작업 영역 개요에서 확인할 수 있습니다. 이러한 값은 자습서의 뒷부분에서 Azure Notebooks 파일을 작업 영역 리소스에 연결하는 데 사용됩니다.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning 모듈 만들기 및 배포

이 섹션에서는 학습된 Machine Learning 모델 파일을 Azure Machine Learning 컨테이너로 변환합니다. Docker 이미지에 필요한 모든 구성 요소는 [Azure IoT Edge용 AI 도구 키트](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)에 있습니다. 다음 단계에 따라 Microsoft Azure Notebook에 해당 리포지토리를 업로드하고 컨테이너를 만들어서 Azure Container Registry에 푸시합니다.

1. Azure Notebooks 프로젝트로 이동합니다. [Azure Portal](https://portal.azure.com)의 Azure Machine Learning 작업 영역에서 또는 Azure 계정으로 [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects)에 로그인하여 가져올 수 있습니다.

2. **GitHub 리포지토리 업로드**를 선택합니다.

3. 다음 GitHub 리포지토리 이름을 제공합니다. `Azure/ai-toolkit-iot-edge`. 프로젝트를 프라이빗으로 유지하려면 **공개** 확인란 선택을 취소합니다. **가져오기**를 선택합니다.

4. 가져오기를 마친 후에 새 **ai-toolkit-iot-edge** 프로젝트로 이동하여 **IoT Edge 변칙 검색 자습서** 폴더를 엽니다.

5. 프로젝트가 실행되고 있는지 확인합니다. 그렇지 않으면 **Run on Free Compute**(무료 컴퓨팅에서 실행)을 선택합니다.

   ![무료 컴퓨팅에서 실행](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. **aml_config/config.json** 파일을 엽니다.

7. Azure 구독 ID, 구독의 리소스 그룹 및 Azure Machine Learning 작업 영역 이름의 값이 포함되도록 구성 파일을 편집합니다. 이 값은 Azure 작업 영역의 **개요** 섹션에서 찾을 수 있습니다.

8. 구성 파일을 저장합니다.

9. **00-anomaly-detection-tutorial.ipynb** 파일을 엽니다.

10. 묻는 메시지가 표시되면 **Python 3.6** 커널과 **Set Kernel**(커널 설정)을 차례로 선택합니다.

11. 설명의 지침에 따라 Notebook의 첫 번째 셀을 편집합니다. 구성 파일에 추가한 것과 동일한 리소스 그룹, 구독 ID 및 작업 영역 이름을 사용합니다.

12. 셀을 선택하고 **Run**을 선택하거나 `Shift + Enter` 키를 눌러서 Notebook의 셀을 실행합니다.

    >[!TIP]
    >변칙 검색 자습서 Notebook의 일부 셀은 선택 사항입니다. IoT Hub처럼 일부 사용자에게 있거나 없을 수 있는 리소스를 생성하기 때문입니다. 첫 번째 셀에 기존 리소스 정보를 넣으면 Azure에서 중복 리소스를 만들지 않기 때문에 새 리소스를 만드는 셀을 실행하면 오류가 발생합니다. 이것은 문제가 되지 않으며, 오류를 무시하거나 선택적 섹션을 전체를 건너뛸 수 있습니다.

Notebook의 모든 단계를 완료하여 변칙 검색 모델을 학습하고, Docker 컨테이너 이미지로 빌드하고, 해당 이미지를 Azure Container Registry에 푸시했습니다. 그런 다음, 모델을 테스트하고 마지막으로 IoT Edge 디바이스에 배포합니다.

## <a name="view-container-repository"></a>컨테이너 리포지토리 보기

컨테이너 이미지가 성공적으로 생성되어 기계 학습 환경과 연결된 Azure 컨테이너 레지스트리에 저장되었는지 확인합니다. 이전 섹션에서 사용한 노트북이 컨테이너 이미지와 레지스트리 자격 증명을 IoT Edge 디바이스에 자동으로 제공했지만, 나중에 직접 정보를 찾을 수 있도록 저장 위치를 알아야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Machine Learning 서비스 작업 영역으로 이동합니다.

2. **개요** 섹션에는 작업 영역 세부 정보는 물론 연결된 리소스가 나열됩니다. **레지스트리** 값을 선택합니다. 이 값은 작업 영역 이름 뒤에 난수가 와야 합니다.

3. 컨테이너 레지스트리의 **서비스** 아래에서 **리포지토리**를 선택합니다. 이전 섹션에서 실행한 Notebook에 의해 생성된 **tempanomalydetection**라는 리포지토리가 표시됩니다.

4. **tempanomalydetection**을 선택합니다. 리포지토리에 태그가 하나 표시됩니다. **1**.

   레지스트리 이름, 리포지토리 이름 및 태그를 알았으니, 컨테이너의 전체 이미지 경로를 알 수 있습니다. 이미지 경로는 **\<registry_name\>.azurecr.io/tempanomalydetection:1** 형식입니다. 이미지 경로를 사용하여 이 컨테이너를 IoT Edge 디바이스에 배포할 수 있습니다.

5. 컨테이너 레지스트리의 **설정** 아래에서 **액세스 키**를 선택합니다. **로그인 서버** 및 관리 사용자의 **사용자 이름**과 **암호**를 비롯한 다수의 액세스 자격 증명이 표시됩니다.

   이러한 자격 증명은 레지스트리에서 컨테이너 이미지를 가져올 수 있는 액세스 권한을 IoT Edge 디바이스에 부여하기 위해 배포 매니페스트에 포함될 수 있습니다.

이제 Machine Learning 컨테이너 이미지가 저장된 위치를 알 수 있습니다. 다음 섹션에서는 IoT Edge 디바이스에서 모듈로 실행 중인 컨테이너를 보기 위해 단계별로 설명합니다.

## <a name="view-the-generated-data"></a>생성된 데이터 보기

각 IoT Edge 모듈에서 생성된 메시지를 볼 수 있으며 IoT 허브에 전달된 메시지도 볼 수 있습니다.

### <a name="view-data-on-your-iot-edge-device"></a>IoT Edge 디바이스에서 데이터 보기

IoT Edge 디바이스에서 모든 개별 모듈에서 보낸 메시지를 볼 수 있습니다.

`iotedge` 명령을 실행하기 위해 관리자 권한으로 `sudo`를 사용해야 할 수도 있습니다. 디바이스에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다.

1. IoT Edge 디바이스에서 모든 모듈을 봅니다.

   ```cmd/sh
   iotedge list
   ```

2. 특정 디바이스에서 보낸 메시지를 봅니다. 이전 명령의 출력에서 나온 모듈 이름을 사용합니다.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>IoT 허브에 도착한 데이터 보기

[Visual Studio Code용 Azure IoT Hub 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 IoT 허브가 받는 디바이스-클라우드 메시지를 볼 수 있습니다.

다음 단계에서는 IoT 허브에 도착한 디바이스-클라우드 메시지를 모니터링하도록 Visual Studio Code를 설정하는 방법을 보여줍니다.

1. Visual Studio Code 탐색기의 **Azure IoT Hub** 섹션에서 **디바이스**를 확장하여 IoT 디바이스 목록을 표시합니다.

2. IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택합니다.

3. 5초마다 tempSensor에서 보낸 메시지를 관찰합니다. 메시지 본문에는 machinelearningmodule이 true 또는 false 값으로 제공하는 **변칙**이라는 속성이 포함되어 있습니다. **AzureMLResponse** 속성에는 모델이 성공적으로 실행된 경우 값 "OK"가 포함됩니다.

   ![메시지 본문의 Azure Machine Learning 응답](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>리소스 정리

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 디바이스와 동일한 IoT Edge 디바이스를 계속 사용해도 됩니다.

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Machine Learning에서 제공하는 IoT Edge 모듈을 배포했습니다. Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다른 자습서 중 하나를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Custom Vision 서비스를 사용하여 이미지 분류](tutorial-deploy-custom-vision.md)
