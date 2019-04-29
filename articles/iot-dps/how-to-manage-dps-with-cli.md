---
title: Azure CLI 및 IoT 확장을 사용하여 IoT Hub Device Provisioning Service를 관리하는 방법 | Microsoft Docs
description: Azure CLI 및 IoT 확장을 사용하여 IoT Hub Device Provisioning Service를 관리하는 방법 알아보기
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 59d2277bd99fac1e8357c1b0d7336ca7451bf8dc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122878"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Azure CLI 및 IoT 확장을 사용하여 IoT Hub Device Provisioning Service를 관리하는 방법

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)는 IoT Edge 같은 Azure 리소스를 관리하기 위한 오픈 소스 교차 플랫폼 명령줄 도구입니다. Azure CLI는 Windows, Linux 및 MacOS에서 사용할 수 있습니다. Azure CLI를 사용하면 Azure IoT Hub 리소스, Device Provisioning 서비스 인스턴스 및 연결된 허브를 즉시 관리할 수 있습니다.

IoT 확장은 디바이스 관리 및 전체 IoT Edge 같은 기능으로 Azure CLI를 강화합니다.

이 자습서에서는 먼저 Azure CLI 및 IoT 확장을 설치하는 단계를 완료합니다. 그런 다음 CLI 명령을 실행하여 기본 Device Provisioning 서비스 작업을 수행하는 방법을 배웁니다. 

## <a name="installation"></a>설치 

### <a name="step-1---install-python"></a>1단계 - Python 설치

[Python 2.7x 또는 Python 3.x](https://www.python.org/downloads/)가 필요합니다.

### <a name="step-2---install-the-azure-cli"></a>2단계 - Azure CLI 설치

[설치 지침](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)에 따라 환경에 Azure CLI를 설치합니다. Azure CLI 버전이 2.0.24 이상이어야 합니다. `az –version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다. Windows에 설치하는 간단한 방법 중 하나는 [MSI](https://aka.ms/InstallAzureCliWindows)를 다운로드하여 설치하는 것입니다.

### <a name="step-3---install-iot-extension"></a>3단계 - IoT 확장 설치

[IoT 확장 추가 정보](https://github.com/Azure/azure-iot-cli-extension)에는 확장을 설치하는 여러 가지 방법이 설명되어 있습니다. 가장 간단한 방법은 `az extension add --name azure-cli-iot-ext` 명령을 사용하는 것입니다. 설치 후 `az extension list` 명령을 사용하여 현재 설치된 확장의 유효성을 검사하거나 `az extension show --name azure-cli-iot-ext` 명령을 사용하여 IoT 확장에 대한 세부 정보를 볼 수 있습니다. 확장을 제거하려면 `az extension remove --name azure-cli-iot-ext` 명령을 사용합니다.


## <a name="basic-device-provisioning-service-operations"></a>기본 Device Provisioning 서비스 작업
이 예제에서는 Azure 계정에 로그인하고, Azure 리소스 그룹(Azure 솔루션과 관련된 리소스를 보관하는 컨테이너)을 만들고, IoT Hub를 만들고, Device Provisioning 서비스를 만들고, 기존 Device Provisioning 서비스를 나열하고, CLI 명령을 사용하여 연결된 IoT Hub를 만드는 방법을 보여 줍니다. 

시작하기 전에 앞에서 설명한 설치 단계를 완료하세요. Azure 계정이 없으면 지금 [무료 계정](https://azure.microsoft.com/free/?v=17.39a)을 만듭니다. 


### <a name="1-log-in-to-the-azure-account"></a>1. Azure 계정에 로그인합니다.
  
    az login

![로그인][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. eastus에 IoTHubBlogDemo라는 리소스 그룹 만들기

    az group create -l eastus -n IoTHubBlogDemo

![리소스 그룹 만들기][2]


### <a name="3-create-two-device-provisioning-services"></a>3. 두 Device Provisioning 서비스 만들기

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Device Provisioning 서비스 만들기][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. 이 리소스 그룹의 모든 기존 Device Provisioning 서비스 나열

    az iot dps list --resource-group IoTHubBlogDemo

![Device Provisioning 서비스 나열][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. 새로 만든 리소스 그룹 아래에 blogDemoHub라는 IoT Hub 만들기

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub 만들기][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. 기존 IoT Hub 하나를 Device Provisioning 서비스에 연결

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![허브 연결][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 디바이스 등록
> * 디바이스 시작
> * 디바이스가 등록되어 있는지 확인

부하가 분산된 허브 간 여러 디바이스를 프로비전하는 방법에 알아보려면 다음 자습서를 진행합니다. 

> [!div class="nextstepaction"]
> [부하가 분산된 IoT Hub 간 디바이스 프로비전](./tutorial-provision-multiple-hubs.md)
