---
title: Ubuntu Virtual Machines에서 Azure IoT Edge 실행 | Microsoft Docs
description: Ubuntu 16.04 Azure Marketplace Virtual Machines에 대한 Azure IoT Edge 설정 지침
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: philmea
ms.openlocfilehash: 49a783e1360aeddc8eeaadba442acf578d9d6f7f
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046040"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ubuntu Virtual Machines에서 Azure IoT Edge 실행

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

IoT Edge 런타임의 작동 방식 및 포함되는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에는 [Ubuntu Azure Marketplace 제품에 미리 구성된 Azure IoT Edge](https://aka.ms/azure-iot-edge-ubuntuvm)를 사용하여 Ubuntu 16.04 Virtual Machine에서 Azure IoT Edge 런타임을 실행하는 단계가 나와 있습니다.

첫 번째 부팅 시 Ubuntu VM의 Azure IoT Edge는 Azure IoT Edge 런타임의 최신 버전을 사전 설치합니다. 또한 연결 문자열을 설정한 다음, 런타임을 다시 시작하는 스크립트가 포함되어 있습니다. 이 스크립트는 Azure VM 포털이나 Azure 명령줄을 통해 원격으로 트리거될 수 있으며, SSH 또는 원격 데스크톱 세션을 시작하지 않고 IoT Edge 디바이스를 쉽게 구성하고 연결할 수 있도록 합니다. 이 스크립트는 IoT Edge 클라이언트가 완전히 설치될 때까지 기다렸다가 연결 문자열을 설정하므로 자동화에 빌드할 필요가 없습니다.

## <a name="deploy-from-the-azure-marketplace"></a>Azure Marketplace에서 배포

1. [Azure IoT Edge on Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) Marketplace 제품으로 이동하거나, [Azure Marketplace](https://azuremarketplace.microsoft.com/)에서 “Azure IoT Edge on Ubuntu”를 검색합니다.
2. **지금 가져오기**를 선택하고 다음 대화 상자에서 **계속**을 선택합니다.
3. Azure Portal에서 **만들기**를 선택하고 마법사를 따라 VM을 배포합니다.
    * VM을 처음으로 시도하는 경우, 암호를 사용하고 공용 인바운드 포트 메뉴에서 SSH를 사용하도록 설정하는 것이 가장 쉬운 방법입니다.
    * 리소스를 많이 사용하는 워크로드가 있는 경우 CPU 및/또는 메모리를 더 추가하여 가상 머신 크기를 업그레이드해야 합니다.
4. 가상 머신이 배포되고 나면, IoT Hub에 연결되도록 구성합니다.
    1. IoT Hub에서 만든 IoT Edge 장치에서 장치 연결 문자열을 복사 합니다 .이 프로세스에 익숙하지 않은 경우 [Azure Portal 절차에서 연결 문자열 검색](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal) 을 수행할 수 있습니다.
    1. Azure Portal에서 새로 만든 가상 머신 리소스를 선택하고 **명령 실행** 옵션을 엽니다.
    1. **RunShellScript** 옵션을 선택합니다.
    1. 디바이스 연결 문자열 `/etc/iotedge/configedge.sh "{device_connection_string}"`을 사용하여 명령 창을 통해 아래 스크립트를 실행합니다.
    1. **실행**을 선택합니다.
    1. 잠시 기다리면, 연결 문자열이 설정되었음을 나타내는 성공 메시지가 화면에 표시됩니다.

## <a name="deploy-from-the-azure-portal"></a>Azure Portal에서 배포

Azure Portal에서 “Azure IoT Edge”를 검색하고 **Ubuntu Server 16.04 LTS + Azure IoT Edge 런타임**을 선택하여 VM 생성 워크플로를 시작합니다. 여기에서, 위의 “Azure Marketplace에서 배포” 지침에 있는 3-4단계를 완료합니다.

## <a name="deploy-from-azure-cli"></a>Azure CLI에서 배포

1. 데스크톱에서 Azure CLI를 사용하는 경우 다음에 로그인하여 시작합니다.

   ```azurecli-interactive
   az login
   ```

1. 구독이 여러 개인 경우 사용할 구독을 선택합니다.
   1. 구독 나열:

      ```azurecli-interactive
      az account list --output table
      ```

   1. 사용 하려는 구독에 대 한 SubscriptionID 필드를 복사 합니다.

   1. 방금 복사한 ID를 사용 하 여 작업 구독을 설정 합니다.

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. 새 리소스 그룹을 만들거나, 다음 단계에서 기존 리소스 그룹을 지정합니다.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 가상 컴퓨터에 대 한 사용 약관에 동의 합니다. 먼저 약관을 검토 하려면 [Azure Marketplace에서 배포](#deploy-from-the-azure-marketplace)의 단계를 따르세요.

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. 새 가상 머신을 만듭니다.

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. 장치 연결 문자열을 설정 합니다 .이 프로세스에 익숙하지 않은 경우 Azure CLI 절차를 [사용 하 여 연결 문자열 검색](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) 을 수행할 수 있습니다.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

설정 후에 이 VM에 SSH를 수행하려면 `ssh azureuser@{publicIpAddress}` 명령에 publicIpAddress를 사용합니다.

## <a name="next-steps"></a>다음 단계

런타임을 설치하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge 런타임에 제대로 설치 하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인 하세요.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

SSH 또는 다른 인바운드 연결을 통해 VM에 액세스 하는 포트를 열려면 [LINUX VM에 대 한 포트 및 엔드포인트을 여](../virtual-machines/linux/nsg-quickstart.md) 는 방법에 대 한 Azure 가상 머신 설명서를 참조 하세요.
