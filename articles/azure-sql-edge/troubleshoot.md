---
title: Azure SQL Edge 배포 문제 해결
description: Azure SQL Edge를 배포할 때 발생할 수 있는 오류에 대해 알아봅니다.
keywords: SQL Edge, 문제 해결, 배포 오류
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 517fed0dd9eb1736344546bde9f79e52ee17182f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333106"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Azure SQL Edge 배포 문제 해결 

이 문서에서는 Azure SQL Edge 컨테이너를 배포 및 사용할 때 발생할 수 있는 오류에 대한 정보를 제공하고 해당 이슈를 해결하는 데 도움이 되는 문제 해결 기법을 제공합니다. 

Azure SQL Edge는 다음 두 가지 배포 모델을 지원합니다. 
- Azure IoT Edge를 통해 연결된 배포: Azure SQL Edge는 Azure Marketplace에서 제공되며 [Azure IoT Edge](../iot-edge/about-iot-edge.md)용 모듈로 배포할 수 있습니다. 자세한 내용은 [Azure SQL Edge 배포](deploy-portal.md)를 참조하세요.<br>

- 연결이 끊긴 배포: Docker 허브에서 Azure SQL Edge 컨테이너 이미지를 끌어와 독립 실행형 Docker 컨테이너로 배포하거나 Kubernetes 클러스터에 배포할 수 있습니다. 자세한 내용은 [Docker를 사용하여 Azure SQL Edge 배포](disconnected-deployment.md) 및 [Kubernetes에 Azure SQL Edge 컨테이너 배포](deploy-kubernetes.md)를 참조하세요.

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>IoT Edge 디바이스 및 배포 문제 해결

Azure IoT Edge를 통해 SQL Edge를 배포하는 동안 오류가 발생할 경우 `iotedge` 서비스가 올바르게 구성되어 실행되고 있는지 확인합니다. 다음 문서는 Azure IoT Edge와 관련된 문제를 해결할 때 유용할 수 있습니다.
- [Azure IoT Edge에 대한 일반적인 문제 및 해결 방법](../iot-edge/troubleshoot-common-errors.md).
- [IoT Edge 디바이스 문제 해결](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Docker 명령 오류

`docker` 명령과 관련된 오류가 발생할 경우 docker 서비스가 실행되고 있는지 확인하고 관리자 권한으로 실행해 봅니다.

예를 들어 Linux에서 `docker` 명령을 실행할 때 다음과 같은 오류가 발생할 수 있습니다.

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Linux에서 이 오류가 발생할 경우 앞에 `sudo`를 추가하여 동일한 명령을 실행해 봅니다. 이 명령이 실패하면 docker 서비스가 실행되고 있는지 확인하고, 필요한 경우 서비스를 시작합니다.

```bash
sudo systemctl status docker
sudo systemctl start docker
```

Windows에서 PowerShell 또는 명령 프롬프트를 관리자 권한으로 시작했는지 확인합니다.

## <a name="azure-sql-edge-container-startup-errors"></a>Azure SQL Edge 컨테이너 시작 오류

SQL Edge 컨테이너가 실행되지 않으면 다음 테스트를 시도합니다.

- Azure IoT Edge를 사용하는 경우 모듈 이미지가 성공적으로 다운로드되었는지, 환경 변수 및 컨테이너 만들기 옵션이 모듈 매니페스트에서 올바르게 지정되었는지 확인합니다.

- Docker 또는 kubernetes 기반 배포를 사용하는 경우 `docker run` 명령이 올바르게 구성되었는지 확인합니다. 자세한 내용은 [Docker를 사용하여 Azure SQL Edge 배포](disconnected-deployment.md) 및 [Kubernetes에 Azure SQL Edge 컨테이너 배포](deploy-kubernetes.md)를 참조하세요.

- `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` 등과 같은 오류가 발생하면 컨테이너 포트 1433을 이미 사용 중인 포트에 매핑하려고 시도하는 것입니다. 이 문제는 호스트 머신에서 SQL Edge를 로컬로 실행하는 경우에 발생할 수 있습니다. 두 개의 SQL Edge 컨테이너를 시작하고 둘 다 동일한 호스트 포트에 매핑하는 경우에도 발생할 수 있습니다. 이 문제가 발생할 경우 `-p` 매개 변수를 사용하여 컨테이너 포트 1433을 다른 호스트 포트에 매핑합니다. 예를 들면 다음과 같습니다. 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- 컨테이너를 시작하려고 할 때 `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` 등과 같은 오류가 발생하는 경우 Ubuntu의 Docker 그룹에 사용자를 추가합니다. 이 변경 내용은 새 세션에 영향을 주기 때문에 로그아웃한 후 다시 로그인합니다. 

   ```bash
    usermod -aG docker $USER
   ```

- 컨테이너의 오류 메시지가 있는지 확인합니다.

   ```bash
   docker logs e69e056c702d
   ```

- 컨테이너 관리 소프트웨어를 사용하는 경우 소프트웨어가 루트로 실행 중인 컨테이너 프로세스를 지원하는지 확인합니다. 컨테이너의 sqlservr.exe 프로세스가 루트로 실행됩니다.

- 기본적으로 Azure SQL Edge 컨테이너는 루트가 아닌 `mssql` 사용자로 실행됩니다. 탑재 지점이나 데이터 볼륨을 사용하여 데이터를 유지하는 경우 사용자에게 `mssql` 볼륨에 대한 적절한 권한이 있는지 확인합니다. 자세한 내용은 [루트가 아닌 사용자로 실행](configure.md#run-azure-sql-edge-as-non-root-user) 및 [데이터 유지](configure.md#persist-your-data)를 참조하세요.

- SQL Edge Docker 컨테이너가 시작 후 즉시 종료되는 경우 Docker 로그를 확인합니다. `docker run` 명령을 사용하여 Windows에서 PowerShell을 사용하는 경우 작은따옴표 대신 큰따옴표를 사용합니다. PowerShell Core를 사용하는 경우 작은따옴표를 사용합니다.

- [SQL Edge 오류 로그](#errorlogs)를 검토합니다.

## <a name="sql-edge-connection-failures"></a>SQL Edge 연결 오류

컨테이너에서 실행되는 SQL Edge 인스턴스에 연결할 수 없는 경우 다음 테스트를 시도합니다.

- `docker ps -a` 출력의 **STATUS** 열을 검사하여 SQL Edge 컨테이너가 실행되고 있는지 확인합니다. 컨테이너가 실행되고 있지 않으면 `docker start <Container ID>`를 사용하여 시작합니다.

- 기본값이 아닌 호스트 포트(1433이 아님)에 매핑한 경우 연결 문자열에 포트를 지정해야 합니다. `docker ps -a` 출력의 **PORTS** 열에서 포트 매핑을 확인할 수 있습니다. Azure SQL Edge에 연결하는 방법에 대한 자세한 내용은 [Azure SQL Edge 연결 및 쿼리](connect.md)를 참조하세요.

- 이전에 매핑된 데이터 볼륨 또는 데이터 볼륨 컨테이너를 사용하여 SQL Edge를 배포했으며 현재 매핑된 기존 데이터 볼륨 또는 데이터 볼륨 컨테이너를 사용하는 경우 SQL Edge는 `MSSQL_SA_PASSWORD` 환경 변수의 값을 무시합니다. 대신, 이전에 구성한 SA 사용자 암호가 사용됩니다. 이 문제는 SQL Edge가 매핑된 볼륨 또는 데이터 볼륨 컨테이너의 기존 master 데이터베이스 파일을 재사용하기 때문에 발생합니다. 해당 이슈가 발생할 경우 다음 옵션을 사용할 수 있습니다.

    - 여전히 사용 가능한 경우 이전에 사용한 암호로 연결합니다.
    - 다른 매핑된 볼륨 또는 데이터 볼륨 컨테이너를 사용하도록 SQL Edge를 구성합니다.
    - 매핑된 볼륨 또는 데이터 볼륨 컨테이너에서 기존 master 데이터베이스 파일(master.mdf 및 mastlog.mdf)을 제거합니다.

- [SQL Edge 오류 로그](#errorlogs)를 검토합니다.

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> SQL Edge 설치 및 오류 로그

기본적으로 SQL Edge 오류 로그는 컨테이너 내의 **/var/opt/mssql/log** 디렉터리에 있으며 다음 방법 중 하나를 사용하여 액세스할 수 있습니다.

- 컨테이너를 만들 때 호스트 디렉터리를 **/var/opt/mssql** 에 탑재한 경우, 대신 호스트의 매핑된 경로에 있는 **log** 하위 디렉터리에서 확인할 수 있습니다.
- 대화형 명령 프롬프트를 사용하여 컨테이너에 연결. 컨테이너가 실행되고 있지 않으면 먼저 컨테이너를 시작합니다. 그런 다음, 대화형 명령 프롬프트를 사용하여 로그를 검사합니다. `docker ps` 명령을 실행하여 컨테이너 ID를 가져올 수 있습니다.

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    컨테이너 내부 bash 세션에서 다음 명령을 실행합니다.

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- SQL Edge 컨테이너가 시작되어 실행되고 있으며 클라이언트 도구를 사용하여 인스턴스에 연결할 수 있는 경우 저장 프로시저 `sp_readerrorlog`를 사용하여 SQL Edge 오류 로그의 내용을 읽을 수 있습니다.

## <a name="execute-commands-in-a-container"></a>컨테이너에서 명령 실행

실행 중인 컨테이너가 있는 경우 호스트 터미널을 통해 컨테이너 내에서 명령을 실행할 수 있습니다.

컨테이너 ID를 가져오려면 다음 명령을 실행합니다.

```bash
docker ps -a
```

컨테이너에서 bash 터미널을 시작하려면 다음 명령을 실행합니다.

```bash
docker exec -it <Container ID> /bin/bash
```

이제 컨테이너 내부 터미널에서 실행하는 것처럼 명령을 실행할 수 있습니다. 완료되면 `exit`을 입력합니다. 대화형 명령 세션이 종료되지만 컨테이너는 계속 실행됩니다.

### <a name="enabling-verbose-logging"></a>자세한 정보 로깅 사용

스트리밍 엔진의 기본 로그 수준에서 충분한 정보를 제공하지 않는 경우 SQL Edge에서 스트리밍 엔진에 대한 디버그 로깅을 사용하도록 설정할 수 있습니다. 디버그 로깅을 사용하도록 설정하려면 SQL Edge 배포에 `RuntimeLogLevel=debug` 환경 변수를 추가합니다. 디버그 로깅을 사용하도록 설정한 후 문제를 재현하고 로그에서 관련 메시지 또는 예외를 확인합니다. 

> [!NOTE]
> 자세한 정보 로깅 옵션은 문제 해결 목적으로만 사용해야 하며 일반적인 프로덕션 워크로드에는 사용하면 안 됩니다. 


## <a name="next-steps"></a>다음 단계

- [SQL Edge의 ONNX를 사용하는 Machine Learning 및 AI](onnx-overview.md)
- [Azure SQL Edge에서 데이터 스트리밍](stream-data.md)
- [데이터 보존 및 정리](data-retention-overview.md)
- [시간 간격을 채우고 누락 값 대체](imputing-missing-values.md)







