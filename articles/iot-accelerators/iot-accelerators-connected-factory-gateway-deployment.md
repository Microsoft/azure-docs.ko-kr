---
title: 연결된 팩터리 게이트웨이 배포 - Azure | Microsoft Docs
description: Windows 또는 Linux에서 게이트웨이를 배포하여 연결된 팩터리 솔루션 가속기에 대한 연결을 사용하도록 설정하는 방법.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 99953b486fbd1daa9800aa850684447465eead9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450193"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>연결된 팩터리 솔루션 가속기를 위해 Windows 또는 Linux에 경계 게이트웨이 배포

*연결된 팩터리* 솔루션 가속기의 경계 게이트웨이를 배포하기 위해서는 두 개의 소프트웨어 구성 요소가 필요합니다.

-  *OPC 프록시* 는 연결된 팩터리에 대한 연결을 설정합니다. OPC 프록시는 연결된 팩터리 솔루션 포털에서 실행되는 통합된 OPC 브라우저의 명령 및 제어 메시지를 기다립니다.

-  *OPC 게시자* 는 기존 온-프레미스 OPC UA 서버에 연결하고 해당 서버의 원격 분석 메시지를 연결된 팩터리에 전달합니다. [OPC UA에 대한 OPC 클래식 어댑터](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md)를 사용하여 OPC 클래식 디바이스를 연결할 수 있습니다.

두 구성 요소는 오픈 소스이며 GitHub의 원본 및 DockerHub의 Docker 컨테이너로 사용할 수 있습니다.

| GitHub | DockerHub |
| ------ | --------- |
| [OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher) | [OPC 게시자](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC 프록시](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC 프록시](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

각 구성 요소의 게이트웨이 방화벽에는 공용 IP 주소 또는 개방 인바운드 포트가 필요하지 않습니다. OPC 프록시 및 OPC 게시자 구성 요소는 아웃바운드 포트 443만 사용합니다.

이 문서의 단계에서는 Windows 또는 Linux에서 Docker를 사용하여 경계 게이트웨이를 배포하는 방법을 보여 줍니다. 게이트웨이를 사용하면 연결된 팩터리 솔루션 가속기에 대한 연결을 사용하도록 설정할 수 있습니다. 또한 연결된 팩터리 없이 구성 요소를 사용할 수 있습니다.

> [!NOTE]
> 두 구성 요소는  [Azure IoT Edge](https://github.com/Azure/iot-edge)에서 모듈로 사용할 수 있습니다.

## <a name="choose-a-gateway-device"></a>게이트웨이 디바이스 선택

게이트웨이 디바이스가 아직 없는 경우 파트너로부터 상용 게이트웨이를 구입하는 것이 좋습니다.  [Azure IoT 장치 카탈로그](https://catalog.azureiotsolutions.com/?q=opc)에서 연결된 팩터리 솔루션과 호환되는 게이트웨이 장치 목록을 확인하세요. 디바이스와 함께 제공되는 지침에 따라 게이트웨이를 설정합니다.

기존 게이트웨이 디바이스를 수동으로 구성해야 할 경우 다음 지침을 사용합니다.

## <a name="install-and-configure-docker"></a>Docker 설치 및 구성

 [Windows용 Docker](https://www.docker.com/docker-windows)를 Windows 기반 게이트웨이 장치에 설치하거나 패키지 관리자를 사용하여 Linux 기반 게이트웨이 장치에서 Docker를 설치합니다.

Windows용 Docker를 설치하는 동안 Docker와 공유할 호스트 컴퓨터의 드라이브를 선택합니다. 다음 스크린샷은 Windows 시스템에서 **D** 드라이브를 공유 과정을 보여 합니다. 드라이브를 공유하면 docker 컨테이너 내의 호스트 드라이브에 액세스할 수 있습니다.

![Windows용 Docker 설치](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> 또한  **설정** 대화 상자에서 docker를 설치한 후 이 단계를 수행할 수도 있습니다. Windows 시스템 트레이에서 **Docker** 아이콘을 마우스 오른쪽 단추로 클릭하고 **설정**을 선택합니다. Windows Fall Creators Update와 같은 주요 Windows 업데이트가 시스템에 배포된 경우 드라이브의 공유를 해제한 후 다시 공유하여 액세스 권한을 새로 고칩니다.

Linux를 사용하는 경우 추가 구성 없이 파일 시스템에 대한 액세스를 사용하도록 설정할 수 있습니다.

Windows에서 Docker를 공유하는 드라이브에 폴더를 만듭니다. Linux에서는 루트 파일 시스템에서 폴더를 만듭니다. 이 연습에서는 이 폴더를 `<SharedFolder>`로 참조합니다.

Docker 명령의 `<SharedFolder>`를 참조하는 경우 운영 체제에 올바른 구문을 사용해야 합니다. 다음은 Windows 및 Linux를 위한 두 가지 예입니다.

- Windows에서 `D:\shared` 폴더를 `<SharedFolder>`로 사용하는 경우 Docker 명령 구문은 `d:/shared`입니다.

- Linux에서 `/shared` 폴더를 `<SharedFolder>`로 사용하는 경우 Docker 명령 구문은 `/shared`입니다.

자세한 내용은 [볼륨 사용](https://docs.docker.com/engine/admin/volumes/volumes/) Docker 엔진 참조를 확인하세요.

## <a name="configure-the-opc-components"></a>OPC 구성 요소의 구성

OPC 구성 요소를 설치하기 전에 다음 단계를 완료하여 환경을 준비합니다.

1. 게이트웨이 배포를 완료하려면 연결된 팩터리 배포에서 IoT Hub의  **iothubowner** 연결 문자열이 필요합니다.  [Azure Portal](https://portal.azure.com/)에서 연결된 팩터리 솔루션을 배포할 때 만든 리소스 그룹의 IoT Hub로 이동합니다.  **공유 액세스 정책**  을 클릭하여  **iothubowner**  연결 문자열에 액세스합니다.

    ![IoT Hub 연결 문자열 찾기](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

     **연결 문자열 - 기본 키** 값을 복사합니다. 

1. Docker 컨테이너 간의 통신을 허용하려면 사용자 정의 브리지 네트워크가 필요합니다. 사용자 컨테이너에 대한 브리지 네트워크를 만들려면 명령 프롬프트에서 다음 명령을 실행합니다.

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    **iot_edge** 브리지 네트워크가 만들어졌는지 확인하려면 다음 명령을 실행합니다.

    ```cmd/sh
    docker network ls
    ```

    **iot_edge** 브리지 네트워크는 네트워크 목록에 포함됩니다.

OPC 게시자를 실행하려면 명령 프롬프트에서 다음 명령을 실행합니다.

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- [OPC 게시자 GitHub](https://github.com/Azure/iot-edge-opc-publisher) 및 [Docker 실행 참조](https://docs.docker.com/engine/reference/run/)에 다음에 관한 자세한 정보가 나와 있습니다.

  - 컨테이너 이름(`microsoft/iot-edge-opc-publisher:2.1.4`) 앞에 지정된 Docker 명령줄 옵션
  - 컨테이너 이름(`microsoft/iot-edge-opc-publisher:2.1.4`) 뒤에 지정된 OPC 게시자 명령줄 매개 변수의 의미

- `<IoTHubOwnerConnectionString>`은 Azure Portal의 **iothubowner** 공유 액세스 정책 연결 문자열입니다. 이전 단계에서 이 연결 문자열을 복사했습니다. OPC 게시자를 처음 실행할 때 이 연결 문자열만 필요합니다. 다음 실행 시에는 보안 위험이 제기되므로 이를 생략해야 합니다.

- 사용하는 `<SharedFolder>` 및 해당 구문은 [Docker 설치 및 구성](#install-and-configure-docker) 섹션에 설명되어 있습니다. OPC 게시자는 `<SharedFolder>`를 사용하여 다음을 수행합니다.

    - OPC 게시자 구성 파일에서 읽고 씁니다.
    - 로그 파일에 씁니다.
    - 이러한 두 파일을 컨테이너 외부에서 사용할 수 있도록 합니다.

- OPC 게시자는 `<SharedFolder>/docker` 폴더에서 읽고 쓰는 **publishednodes.json** 파일에서 해당 구성을 읽습니다. 이 구성 파일은 지정된 OPC UA 서버에서 OPC 게시자가 구독해야 하는 OPC UA 노드 데이터가 무엇인지 정의합니다. **publishednodes.json** 파일의 전체 구문은 GitHub의 [OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher) 페이지에 설명되어 있습니다. 게이트웨이를 추가할 때 빈 **publishednodes.json**을 폴더에 배치합니다.

    ```json
    [
    ]
    ```

- OPC UA 서버가 OPC 게시자에게 데이터 변경에 대해 알릴 때마다 새 값이 IoT Hub에 전송됩니다. 일괄 처리 설정에 따라 OPC 게시자는 일괄 처리에서 IoT Hub로 데이터를 보내기 전에 먼저 데이터를 누적할 수도 있습니다.

- Docker는 NetBIOS 이름 확인이 아닌 DNS 이름 확인만 지원합니다. 네트워크에서 DNS 서버가 없다면 이전 명령줄 예제에 나온 해결책을 사용할 수 있습니다. 이전 명령줄 예제는 `--add-host` 매개 변수를 사용하여 컨테이너 호스트 파일에 항목을 추가합니다. 이 항목은 지정한 IP 주소 `<IpAddressOfOpcServerHostname>`을 확인하여 제공된 `<OpcServerHostname>`에 대한 호스트 이름 조회가 가능합니다.

- OPC UA는 인증 및 암호화에 X.509 인증서를 사용합니다. OPC 게시자 인증서를 연결할 OPC UA 서버에 배치하여 OPC 게시자를 신뢰하도록 합니다. OPC 게시자 인증서 저장소는 `<SharedFolder>/CertificateStores` 폴더에 있습니다. OPC 게시자 인증서는 `CertificateStores` 폴더의 `trusted/certs` 폴더에서 찾을 수 있습니다.

  OPC UA 서버를 구성하는 단계는 사용 중인 디바이스에 따라 다릅니다. 이러한 단계는 일반적으로 OPC UA 서버의 설명서에 문서화되어 있습니다.

OPC 프록시를 설치하려면 명령 프롬프트에서 다음 명령을 실행합니다.

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

시스템에서 설치를 한 번 실행하기만 하면 됩니다.

다음 명령을 사용하여 OPC 프록시를 실행합니다.

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

OPC 프록시는 설치하는 동안 연결 문자열을 저장합니다. 다음 실행 시에는 보안 위험이 제기되므로 연결 문자열을 생략해야 합니다.

## <a name="enable-your-gateway"></a>게이트웨이 사용 설정

연결된 팩터리 솔루션 가속기에서 게이트웨이를 사용하도록 설정하려면 다음 단계를 완료합니다.

1. 두 구성 요소를 실행 중인 경우 연결된 팩터리 솔루션 포털에서  **자체 OPC UA 서버 연결** 페이지로 이동합니다. 이 페이지는 솔루션에서 관리자에게만 제공됩니다. 게시자 엔드포인트 URL(opc.tcp://publisher:62222)을 입력하고  **연결**을 클릭합니다.

1. 연결된 팩터리 포털과 OPC 게시자 간의 신뢰 관계를 설정합니다. 인증서 경고가 표시되면  **계속**을 클릭합니다. 다음으로 OPC 게시자가 UA 웹 클라이언트를 신뢰하지 않는다는 오류가 표시됩니다. 이 오류를 해결하려면 `<SharedFolder>/CertificateStores/rejected/certs` 폴더의  **UA 웹 클라이언트** 인증서를 게이트웨이의 `<SharedFolder>/CertificateStores/trusted/certs` 폴더로 복사합니다. 게이트웨이를 다시 시작할 필요는 없습니다.

이제 클라우드에서 게이트웨이로 연결할 수 있으며 솔루션에 OPC UA 서버를 추가할 준비가 되었습니다.

## <a name="add-your-own-opc-ua-servers"></a>사용자 자신의 OPC UA 서버 추가

사용자 자신의 OPC UA 서버를 연결된 팩터리 솔루션 가속기에 추가하려면 다음을 수행합니다.

1. 연결된 팩터리 솔루션 포털에서  **사용자 자신의 OPC UA 서버 연결** 페이지로 이동합니다.

    1. 연결하려는 OPC UA 서버를 시작합니다. OPC 게시자와 컨테이너에서 실행되는 OPC 프록시에서 OPC UA 서버에 연결할 수 있는지 확인합니다. 이름 확인에 대한 이전 설명을 참조하세요.
    1. OPC UA 서버의 엔드포인트 URL(`opc.tcp://<host>:<port>`)을 입력하고 **연결**을 클릭합니다.
    1. 연결 설정의 프로세스 중에 연결된 팩터리 포털(OPC UA 클라이언트)과 연결하려는 OPC UA 서버 간에 트러스트 관계가 설정됩니다. 연결된 팩터리 대시보드에서 **연결하려는 서버의 인증서를 확인할 수 없음** 경고가 표시됩니다. 인증서 경고가 표시되면 **계속**을 클릭합니다.
    1. 설치하기 더 어려운 것은 연결하려는 OPC UA 서버의 인증서 구성입니다. PC 기반 OPC UA 서버의 경우, 대시보드에 승인할 수는 경고 대화 상자만 표시될 수 있습니다. 포함된 OPC UA 서버 시스템의 경우, OPC UA 서버의 설명서를 참조하여 이 작업이 완료되는 방식을 확인할 수 있습니다. 이 작업을 완료하려면 연결된 팩터리 포털의 OPC UA 클라이언트 인증서가 필요할 수 있습니다. 관리자는 **사용자 자신의 OPC UA 서버 연결** 페이지에서 이 인증서를 다운로드할 수 있습니다.

        ![솔루션 포털](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. OPC UA 서버의 OPC UA 노드 트리로 이동한 후 연결된 팩터리로 값을 전송할 OPC 노드를 마우스 오른쪽 단추로 클릭하고  **게시**를 선택합니다.

1. 이제 원격 분석이 게이트웨이 디바이스로 진행됩니다.  **새 팩터리** 아래에 있는 연결된 팩터리 포털의  **팩터리 위치** 보기에서 원격 분석을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

연결된 팩터리 솔루션 가속기의 아키텍처에 대한 자세한 내용은  [연결된 팩터리 솔루션 가속기 연습](iot-accelerators-connected-factory-sample-walkthrough.md)을 참조하세요.

 [OPC 게시자 참조 구현](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher)에 대해 살펴봅니다.