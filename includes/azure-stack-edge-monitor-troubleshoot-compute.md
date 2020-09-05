---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89272159"
---
계산 역할이 구성 된 Azure Stack Edge 장치에서 두 가지 명령 집합을 사용 하 여 장치를 문제를 해결 하거나 모니터링할 수 있습니다.

- `iotedge`명령을 사용 합니다. 이러한 명령은 장치에 대 한 기본 작업에 사용할 수 있습니다.
- `dkrdbe`명령을 사용 합니다. 이러한 명령은 장치에 대 한 광범위 한 작업 집합에서 사용할 수 있습니다.

위의 명령 집합 중 하나를 실행 하려면 [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)해야 합니다.

### <a name="use-iotedge-commands"></a>`iotedge`명령 사용

사용 가능한 명령 목록을 보려면 [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface) 하 고 함수를 사용 `iotedge` 합니다.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

다음 표에는에서 사용할 수 있는 명령에 대 한 간략 한 설명이 나와 있습니다 `iotedge` .

|명령을 사용합니다.  |설명 |
|---------|---------|
|`check`     | 일반적인 구성 및 연결 문제에 대 한 자동화 된 검사 수행       |
|`list`     | 모듈 목록 표시         |
|`logs`     | 모듈의 로그 가져오기        |
|`restart`     | 모듈을 중지 했다가 다시 시작 합니다.         |


### <a name="use-dkrdbe-commands"></a>`dkrdbe`명령 사용

사용 가능한 명령 목록을 보려면 [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface) 하 고 함수를 사용 `dkrdbe` 합니다.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
다음 표에는에서 사용할 수 있는 명령에 대 한 간략 한 설명이 나와 있습니다 `dkrdbe` .

|명령을 사용합니다.  |설명 |
|---------|---------|
|`image`     | 이미지를 관리 합니다. 사용 하지 않는 이미지를 제거 하려면 다음을 사용 합니다. `dkrdbe image prune -a -f`       |
|`images`     | 이미지 나열         |
|`inspect`     | Docker 개체에 대 한 하위 수준 정보 반환         |
|`login`     | Docker 레지스트리에 로그인         |
|`logout`     | Docker 레지스트리에서 로그 아웃         |
|`logs`     | 컨테이너의 로그 가져오기        |
|`port`     | 컨테이너에 대 한 포트 매핑 또는 특정 매핑을 나열 합니다.        |
|`ps`     | 컨테이너 나열        |
|`pull`     | 레지스트리에서 이미지 또는 리포지토리 끌어오기         |
|`start`     | 하나 이상의 중지 된 컨테이너 시작         |
|`stats`     | 컨테이너 리소스 사용 통계의 라이브 스트림 표시         |
|`stop`     | 하나 이상의 실행 중인 컨테이너를 중지 합니다.        |
|`system`     | Docker 관리         |
|`top`     | 컨테이너의 실행 중인 프로세스를 표시 합니다.         |

사용 가능한 명령에 대 한 도움말을 보려면를 사용 `dkrdbe <command-name> --help` 합니다.

예를 들어 명령 사용법을 이해 하려면 `port` 다음을 입력 합니다.

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

함수에 사용할 수 있는 명령은 `dkrdbe` 일반 docker 명령에 사용 된 것과 동일한 매개 변수를 사용 합니다. Docker 명령에 사용 되는 옵션 및 매개 변수를 보려면 [docker Commandline 사용](https://docs.docker.com/engine/reference/commandline/docker/)으로 이동 합니다.

### <a name="to-check-if-the-module-deployed-successfully"></a>모듈이 성공적으로 배포 되었는지 확인 하려면

계산 모듈은 비즈니스 논리를 구현 하는 컨테이너입니다. 계산 모듈이 성공적으로 배포 되었는지 확인 하려면 명령을 실행 하 `ps` 고 컨테이너 (계산 모듈에 해당)가 실행 중인지 확인 합니다.

일시 중지 된 모든 컨테이너를 포함 하 여 모든 컨테이너의 목록을 가져오려면 `ps -a` 명령을 실행 합니다.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

컨테이너 이미지를 만드는 동안 또는 이미지를 끌어오는 동안 오류가 발생 한 경우를 실행 `logs edgeAgent` 합니다.  `EdgeAgent` 는 다른 컨테이너의 프로 비전을 담당 하는 IoT Edge 런타임 컨테이너입니다.

는 `logs edgeAgent` 모든 로그를 덤프 하므로 최근 오류를 확인 하는 좋은 방법은 옵션을 사용 하는 것입니다 `--tail 20` .


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>컨테이너 로그를 가져오려면

특정 컨테이너에 대 한 로그를 가져오려면 먼저 컨테이너를 나열 한 다음 관심 있는 컨테이너에 대 한 로그를 가져옵니다.

1. [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. 실행 중인 컨테이너 목록을 가져오려면 `ps` 명령을 실행 합니다.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. 로그가 필요한 컨테이너의 컨테이너 ID를 적어 둡니다.

4. 특정 컨테이너에 대 한 로그를 가져오려면 `logs` 컨테이너 ID를 제공 하는 명령을 실행 합니다.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>장치의 사용 통계를 모니터링 하려면

장치에서 메모리, CPU 사용량 및 IO를 모니터링 하려면 `stats` 명령을 사용 합니다.

1. [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. 명령을 실행 `stats` 하 여 라이브 스트림을 사용 하지 않도록 설정 하 고 첫 번째 결과만 끌어옵니다.

   ```powershell
   dkrdbe stats --no-stream
   ```

   다음 예에서는이 cmdlet을 사용 하는 방법을 보여 줍니다.

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

