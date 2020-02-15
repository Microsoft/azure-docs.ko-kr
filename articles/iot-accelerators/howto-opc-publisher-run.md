---
title: OPC 게시자 실행 - Azure | Microsoft Docs
description: 이 문서에서는 OPC 게시자를 실행하고 디버그하는 방법을 설명합니다. 또한 성능 및 메모리 고려 사항에 대해서도 설명합니다.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4f5d57bab51d537b64ce4b800737219663c3d7a7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198789"
---
# <a name="run-opc-publisher"></a>OPC 게시자 실행

이 문서에서는 AD 디버그 OPC 게시자를 실행하는 방법에 대해 설명합니다. 또한 성능 및 메모리 고려 사항에 대해서도 설명합니다.

## <a name="command-line-options"></a>명령줄 옵션

애플리케이션 사용량은 다음과 같이 `--help` 명령줄 옵션을 사용하여 표시됩니다.

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

일반적으로 애플리케이션을 처음 실행할 때만 IoT Hub 소유자 연결 문자열을 지정합니다. 연결 문자열은 암호화되어 플랫폼 인증서 저장소에 저장됩니다. 나중에 실행되면 애플리케이션에서 인증서 저장소로부터 연결 문자열을 읽습니다. 각 실행에서 연결 문자열을 지정하면 IoT Hub 디바이스 레지스트리에서 애플리케이션에 대해 만든 디바이스가 제거되고 다시 만들어집니다.

## <a name="run-natively-on-windows"></a>Windows에서 기본적으로 실행

Visual Studio에서 **opcpublisher.sln** 프로젝트를 열고, 솔루션을 빌드하고, 이를 게시합니다. 다음과 같이 게시한 **대상 디렉터리**에서 애플리케이션을 시작할 수 있습니다.

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>자체 기본 제공 컨테이너 사용

사용자 고유의 컨테이너를 만들고 다음과 같이 시작합니다.

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Microsoft Container Registry에서 컨테이너 사용

Microsoft Container Registry에서 사용할 수 있는 미리 빌드된 컨테이너가 있습니다. 이 컨테이너를 다음과 같이 시작합니다.

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

지원되는 운영 체제 및 프로세서 아키텍처를 보려면 [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)를 확인합니다. OS 및 CPU 아키텍처가 지원되면 Docker에서 올바른 컨테이너를 자동으로 선택합니다.

## <a name="run-as-an-azure-iot-edge-module"></a>Azure IoT Edge 모듈로 실행

OPC 게시자는 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) 모듈로 사용할 준비가 되었습니다. OPC 게시자를 IoT Edge 모듈로 사용하는 경우 지원되는 전송 프로토콜은 **Amqp_Tcp_Only** 및 **Mqtt_Tcp_Only**뿐입니다.

OPC 게시자를 모듈로 IoT Edge 배포에 추가하려면 Azure Portal에서 IoT Hub 설정으로 이동하여 다음 단계를 완료합니다.

1. **IoT Edge**로 이동하여 IoT Edge 디바이스를 만들거나 선택합니다.
1. **모듈 설정**을 선택합니다.
1. **배포 모듈** 아래에서 **추가**를 선택한 다음, **IoT Edge 모듈**을 선택합니다.
1. **이름** 필드에서 **publisher**를 입력합니다.
1. **이미지 URI** 필드에서 `mcr.microsoft.com/iotedge/opc-publisher:<tag>`를 입력합니다.
1. 사용 가능한 태그는 [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)에서 찾을 수 있습니다.
1. 다음 JSON을 **컨테이너 만들기 옵션** 필드에 붙여넣습니다.

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    이 구성은 OPC 게시자 이미지를 사용하여 **publisher**라는 컨테이너를 시작하도록 IoT Edge를 구성합니다. 컨테이너 시스템의 호스트 이름이 **publisher**로 설정되어 있습니다. OPC 게시자는 `--aa` 명령줄 인수로 호출됩니다. 이 옵션을 사용하면 OPC 게시자에서 연결하는 OPC UA 서버의 인증서를 신뢰합니다. OPC 게시자 명령줄 옵션은 모두 사용할 수 있습니다. 유일한 제한 사항은 IoT Edge에서 지원되는 **컨테이너 만들기 옵션**의 크기입니다.

1. 다른 설정은 변경하지 않고 **저장**을 선택합니다.
1. 다른 IoT Edge 모듈에서 OPC 게시자의 출력을 로컬로 처리하려면 **모듈 설정** 페이지로 돌아갑니다. 그런 다음, **경로 지정** 탭으로 이동하여 다음 JSON과 같은 새 경로를 추가합니다.

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. **모듈 설정** 페이지로 돌아가서 구성의 마지막 페이지에 도달할 때까지 **다음**을 선택합니다.
1. **제출**을 선택하여 구성을 IoT Edge로 보냅니다.
1. 에지 디바이스에서 IoT Edge를 시작했고 **publisher** Docker 컨테이너가 실행 중이면, `docker logs -f publisher`를 사용하거나 로그 파일을 확인하여 OPC 게시자의 로그 출력을 확인할 수 있습니다. 이전 예제에서 로그 파일은 `d:\iiotegde\publisher-publisher.log`보다 큽니다. 또한 [iot-edge-opc-publisher-diagnostics 도구](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)도 사용할 수 있습니다.

### <a name="make-the-configuration-files-accessible-on-the-host"></a>호스트에서 구성 파일에 액세스할 수 있도록 설정

호스트 파일 시스템에서 IoT Edge 모듈 구성 파일에 액세스할 수 있도록 하려면 다음 **컨테이너 만들기 옵션**을 사용합니다. Windows용 Linux 컨테이너를 사용하는 배포의 예제는 다음과 같습니다.

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

이러한 옵션을 사용하면 OPC 게시자에서 `./pn.json` 파일로부터 게시해야 하는 노드를 읽고, 시작할 때 컨테이너의 작업 디렉터리가 `/appdata`로 설정됩니다. 이러한 설정을 사용하면 OPC 게시자에서 컨테이너로부터 `/appdata/pn.json` 파일을 읽어 해당 구성을 가져옵니다. `--pf` 옵션을 사용하지 않으면 OPC 게시자에서 기본 구성 파일인 `./publishednodes.json`을 읽으려고 합니다.

`publisher-publisher.log` 기본 이름을 사용하는 로그 파일이 `/appdata`에 기록되고, `CertificateStores` 디렉터리도 이 디렉터리에 만들어집니다.

이러한 모든 파일을 호스트 파일 시스템에서 사용할 수 있게 하려면 컨테이너 구성에 바인딩 탑재 볼륨이 필요합니다. `d://iiotedge:/appdata` 바인딩은 컨테이너 시작 시 현재 작업 디렉터리인 `/appdata` 디렉터리를 `d://iiotedge` 호스트 디렉터리에 매핑합니다. 이 옵션을 사용하지 않으면 다음에 컨테이너가 시작될 때 파일 데이터가 유지되지 않습니다.

Windows 컨테이너를 실행하는 경우 `Binds` 매개 변수의 구문이 다릅니다. 컨테이너 시작 시 작업 디렉터리가 `c:\appdata`입니다. 구성 파일을 호스트의 `d:\iiotedge` 디렉터리에 넣으려면 `HostConfig` 섹션에서 다음 매핑을 지정합니다.

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Linux에서 Linux 컨테이너를 실행하는 경우 `Binds` 매개 변수의 구문이 또 다시 다릅니다. 컨테이너 시작 시 작업 디렉터리가 `/appdata`입니다. 구성 파일을 호스트의 `/iiotedge` 디렉터리에 넣으려면 `HostConfig` 섹션에서 다음 매핑을 지정합니다.

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>컨테이너 사용 시 고려 사항

다음 섹션에는 컨테이너를 사용할 때 주의해야 할 몇 가지 사항이 나와 있습니다.

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>OPC 게시자 OPC UA 서버에 대한 액세스

기본적으로 OPC 게시자 OPC UA 서버는 62222 포트에서 수신 대기합니다. 이 인바운드 포트를 컨테이너에 공개하려면 다음 명령을 사용합니다.

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>컨테이너 간 이름 확인 사용

컨테이너 내에서 다른 컨테이너로 이름 확인을 사용하도록 설정하려면 사용자 정의 Docker 브리지 네트워크를 만들고 `--network` 옵션을 사용하여 컨테이너를 이 네트워크에 연결합니다. 또한 다음과 같이 `--name` 옵션을 사용하여 컨테이너에 이름을 할당합니다.

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

이제 동일한 네트워크의 다른 컨테이너에서 `publisher` 이름을 사용하여 컨테이너에 연결할 수 있습니다.

### <a name="access-other-systems-from-within-the-container"></a>컨테이너 내에서 다른 시스템에 액세스

이전 섹션에서 설명한 매개 변수를 사용하여 다른 컨테이너에 연결할 수 있습니다. Docker가 호스팅되는 운영 체제에서 DNS를 사용하도록 설정된 경우 DNS에 알려진 모든 시스템에 액세스할 수 있습니다.

NetBIOS 이름 확인을 사용하는 네트워크에서 `--add-host` 옵션을 통해 컨테이너를 시작하여 다른 시스템에 대한 액세스를 사용하도록 설정합니다. 이 옵션은 다음과 같이 컨테이너의 호스트 파일에 항목을 효과적으로 추가합니다.

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>호스트 이름 할당

OPC 게시자는 인증서 및 엔드포인트 생성을 위해 실행 중인 머신의 호스트 이름을 사용합니다. `-h` 옵션으로 설정되지 않으면 Docker에서 임의의 호스트 이름을 선택합니다. 다음 예제에서는 컨테이너의 내부 호스트 이름을 `publisher`로 설정하는 방법을 보여 줍니다.

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>바인딩 탑재 사용(공유 파일 시스템)

컨테이너 파일 시스템을 사용하는 대신 구성 정보와 로그 파일을 저장할 호스트 파일 시스템을 선택할 수 있습니다. 이 옵션을 구성하려면 바인딩 탑재 모드에서 `-v`의 `docker run` 옵션을 사용합니다.

## <a name="opc-ua-x509-certificates"></a>OPC UA X.509 인증서

OPC UA는 X.509 인증서를 사용하여 OPC UA 클라이언트와 서버에서 연결을 설정할 때 이 클라이언트와 서버를 인증하고 상호 간의 통신을 암호화합니다. OPC 게시자는 OPC UA 스택에서 유지 관리하는 인증서 저장소를 사용하여 모든 인증서를 관리합니다. 시작 시 OPC 게시자에서 자체적으로 인증서가 있는지 확인합니다. 인증서가 인증서 저장소에 없고 명령줄에서 전달된 인증서가 아닌 경우 OPC 게시자에서 자체 서명된 인증서를 만듭니다. 자세한 내용은 **의** InitApplicationSecurityAsync`OpcApplicationConfigurationSecurity.cs` 메서드를 참조하세요.

자체 서명된 인증서는 신뢰할 수 있는 CA에서 서명하지 않았으므로 보안을 제공하지 않습니다.

OPC 게시자에서 제공하는 명령줄 옵션은 다음을 수행할 수 있습니다.

- OPC 게시자에서 사용하는 현재 애플리케이션 인증서의 CSR 정보를 검색합니다.
- CA 서명 인증서를 사용하여 OPC 게시자를 프로비저닝합니다.
- 새 키 쌍과 일치하는 CA 서명 인증서를 사용하여 OPC 게시자를 프로비저닝합니다.
- 신뢰할 수 있는 피어 또는 신뢰할 수 있는 발급자 인증서 저장소에 인증서를 추가합니다.
- CRL을 추가합니다.
- 신뢰할 수 있는 피어 또는 신뢰할 수 있는 발급자 인증서 저장소에서 인증서를 제거합니다.

이러한 모든 옵션을 사용하면 파일 또는 base64로 인코딩된 문자열을 사용하여 매개 변수를 전달할 수 있습니다.

모든 인증서 저장소에 대한 기본 저장소 형식은 명령줄 옵션을 사용하여 변경할 수 있는 파일 시스템입니다. 컨테이너에서 영구 스토리지를 파일 시스템에 제공하지 않으므로 다른 저장소 형식을 선택해야 합니다. 인증서 저장소를 호스트 파일 시스템 또는 Docker 볼륨에서 유지하려면 `-v` Docker 옵션을 사용합니다. Docker 볼륨을 사용하는 경우 인증서는 base64로 인코딩된 문자열을 사용하여 전달할 수 있습니다.

런타임 환경은 인증서를 유지하는 방법에 영향을 줍니다. 다음과 같이 애플리케이션을 실행할 때마다 새 인증서 저장소를 만들지 않도록 합니다.

- Windows에서 기본적으로 실행되면 프라이빗 키에 대한 액세스가 실패하므로 `Directory` 형식의 애플리케이션 인증서 저장소를 사용할 수 없습니다. 이 경우 `--at X509Store` 옵션을 사용하세요.
- Linux Docker 컨테이너로 실행되면 `-v <hostdirectory>:/appdata` Docker 실행 옵션을 사용하여 인증서 저장소를 호스트 파일 시스템에 매핑할 수 있습니다. 이 옵션을 사용하면 인증서가 애플리케이션 실행 간에 영구적으로 지속됩니다.
- Linux Docker 컨테이너로 실행되고 애플리케이션 인증서에 X509 저장소를 사용하려면 `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` Docker 실행 옵션 및 `--at X509Store` 애플리케이션 옵션을 사용합니다.

## <a name="performance-and-memory-considerations"></a>성능 및 메모리 고려 사항

이 섹션에서는 메모리 및 성능 관리 옵션에 대해 설명합니다.

### <a name="command-line-parameters-to-control-performance-and-memory"></a>성능 및 메모리를 제어하는 명령줄 매개 변수

OPC 게시자를 실행하는 경우 성능 요구 사항과 호스트에서 사용할 수 있는 메모리 리소스를 알고 있어야 합니다.

메모리와 성능은 상호 종속적이며, 둘 다 게시하도록 구성한 노드 수의 구성에 따라 달라집니다. 다음 매개 변수에서 요구 사항을 충족하는지 확인하세요.

- IoT Hub 보내기 간격: `--si`
- IoT Hub 메시지 크기(기본값 `1`): `--ms`
- 모니터링되는 항목 큐 용량: `--mq`

`--mq` 매개 변수는 모든 OPC 노드 값 변경 알림을 버퍼링하는 내부 큐의 용량에 대한 상한 값을 제어합니다. OPC 게시자에서 메시지를 IoT Hub에 충분히 빨리 보낼 수 없는 경우 이 큐는 알림을 버퍼링합니다. 매개 변수는 버퍼링할 수 있는 알림 수를 설정합니다. 테스트 실행에서 이 큐의 항목 수가 증가하면 메시지가 손실되지 않도록 다음을 수행해야 합니다.

- IoT Hub 보내기 간격 줄이기
- IoT Hub 메시지 크기 늘리기

`--si` 매개 변수는 OPC 게시자에서 메시지를 지정된 간격으로 IoT Hub에 보내도록 합니다. OPC 게시자는 `--ms` 매개 변수로 지정된 메시지 크기에 도달하는 즉시 또는 `--si` 매개 변수로 지정된 간격에 도달하는 즉시 메시지를 보냅니다. 메시지 크기 옵션을 사용하지 않도록 설정하려면 `--ms 0`을 사용합니다. 이 경우 OPC 게시자는 가능한 최대 크기인 256KB의 IoT Hub 메시지를 사용하여 데이터를 일괄 처리합니다.

`--ms` 매개 변수를 사용하면 IoT Hub로 보낸 메시지를 일괄 처리할 수 있습니다. 사용하는 프로토콜에 따라 메시지를 IoT Hub에 보내는 오버헤드가 페이로드를 보낸 실제 시간에 비해 높은지 여부가 결정됩니다. 시나리오에서 IoT Hub로부터 데이터를 수집할 때 대기 시간이 허용되는 경우 최대 메시지 크기인 256KB를 사용하도록 OPC 게시자를 구성합니다.

프로덕션 시나리오에서 OPC 게시자를 사용하기 전에 프로덕션 조건에서 성능 및 메모리 사용량을 테스트합니다. `--di` 매개 변수를 사용하여 OPC 게시자에서 진단 정보를 기록하는 간격(초)을 지정할 수 있습니다.

### <a name="test-measurements"></a>테스트 측정값

다음 예제 진단에서는 OPC 게시 간격이 1초인 500개의 노드를 게시하는 `--si` 및 `--ms` 매개 변수에 대해 서로 다른 측정값을 보여 줍니다.  이 테스트에는 OPC 게시자 디버그 빌드가 기본적으로 Windows 10에서 120초 동안 사용되었습니다. IoT Hub 프로토콜은 기본 MQTT 프로토콜이었습니다.

#### <a name="default-configuration---si-10---ms-262144"></a>기본 구성(--si 10 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

기본 구성은 10초마다 또는 IoT Hub에서 수집하는 데 256KB의 데이터를 사용할 수 있을 때 데이터를 IoT Hub에 보냅니다. 이 구성은 보통 약 10초의 대기 시간을 추가하지만 메시지 크기가 크므로 데이터가 손실될 가능성이 가장 낮습니다. 진단 출력에서는 손실된 OPC 노드 업데이트가 없음(`monitored item notifications enqueue failure: 0`)을 보여 줍니다.

#### <a name="constant-send-interval---si-1---ms-0"></a>지속적인 보내기 간격(--si 1 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

메시지 크기가 0으로 설정되면 OPC 게시자에서 지원되는 최대 IoT Hub 메시지 크기(256KB)를 사용하여 데이터를 내부적으로 일괄 처리합니다. 진단 출력에서는 평균 메시지 크기가 115,019바이트임을 보여 줍니다. 이 구성에서 OPC 게시자는 OPC 노드 값 업데이트를 손실하지 않으며, 대기 시간이 기본값에 비해 더 짧습니다.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>각 OPC 노드 값 업데이트 보내기(--si 0 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

이 구성에서는 각 OPC 노드 값 변경에 대한 메시지를 IoT Hub에 보냅니다. 진단 결과에서 평균 메시지 크기가 작은 크기인 234바이트임을 보여 줍니다. 이 구성의 장점은 OPC 게시자에서 대기 시간을 추가하지 않는다는 것입니다. 손실된 OPC 노드 값 업데이트 수(`monitored item notifications enqueue failure: 44624`)가 많으므로 많은 양의 원격 분석이 게시되는 시나리오에는 이 구성이 적합하지 않습니다.

### <a name="maximum-batching---si-0---ms-262144"></a>최대 일괄 처리(--si 0 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

이 구성에서는 가능한 한 많은 OPC 노드 값 업데이트를 일괄 처리합니다. 최대 IoT Hub 메시지 크기는 여기에 구성된 256KB입니다. 보내기 간격이 요청되지 없습니다. 즉, IoT Hub에서 수집할 데이터의 양에 따라 대기 시간이 결정됩니다. 이 구성은 OPC 노드 값을 손실할 가능성이 가장 낮으며 많은 수의 노드를 게시하는 데 적합합니다. 이 구성을 사용하는 경우 메시지 크기가 256KB에 도달하지 않으면 시나리오에 긴 대기 시간이 도입되는 조건이 없어야 합니다.

## <a name="debug-the-application"></a>애플리케이션 디버그

애플리케이션을 디버그하려면 Visual Studio에서 **opcpublisher.sln** 솔루션 파일을 열고 Visual Studio 디버깅 도구를 사용합니다.

OPC 게시자에서 OPC UA 서버에 액세스해야 하는 경우 방화벽이 서버에서 수신 대기하는 포트에 대한 액세스를 허용하는지 확인합니다. 기본 포트는 62222입니다.

## <a name="control-the-application-remotely"></a>원격으로 애플리케이션 제어

게시할 노드는 IoT Hub 직접 메서드를 사용하여 구성할 수 있습니다.

OPC 게시자는 다음 정보를 읽는 몇 가지 추가 IoT Hub 직접 메서드 호출을 구현합니다.

- 일반 정보
- OPC 세션, 구독 및 모니터링된 항목에 대한 진단 정보
- IoT Hub 메시지 및 이벤트에 대한 진단 정보
- 시작 로그
- 로그의 마지막 100줄
- 애플리케이션 종료

GitHub 리포지토리에는 [게시할 노드를 구성](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration)하고 [진단 정보를 읽는](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) 도구가 있습니다. 또한 두 도구는 모두 Docker Hub에서 컨테이너로 사용할 수도 있습니다.

## <a name="use-a-sample-opc-ua-server"></a>샘플 OPC UA 서버 사용

실제 OPC UA 서버가 없는 경우 [샘플 OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc)를 사용하여 시작할 수 있습니다. 또한 이 샘플 PLC는 Docker Hub에서도 사용할 수 있습니다.

임의 데이터와 잘못된 부분이 있는 태그를 생성하는 여러 개의 태그를 구현합니다. 추가 태그 값을 시뮬레이션해야 하는 경우 샘플을 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 OPC 게시자를 실행하는 방법을 알아보았으므로 다음 단계에서는 [OPC 쌍](overview-opc-twin.md) 및 [OPC 자격 증명 모음](overview-opc-vault.md)에 대해 알아보는 것이 좋습니다.
