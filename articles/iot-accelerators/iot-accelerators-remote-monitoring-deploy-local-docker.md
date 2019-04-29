---
title: 로컬로 원격 모니터링 솔루션 배포 - Docker - Azure | Microsoft Docs
description: 이 방법 가이드에서는 테스트 및 개발을 위해 Docker를 사용하여 원격 모니터링 솔루션 가속기를 로컬 머신에 배포하는 방법을 보여줍니다.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: c00e62e237fe263f54926c8e74fb6211a2e5a4e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447032"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>로컬로 원격 모니터링 솔루션 가속기 배포 - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

이 문서에서는 테스트 및 개발을 위해 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포하는 방법을 보여줍니다. 마이크로 서비스를 로컬 Docker 컨테이너에 배포하는 방법에 대해 알아봅니다. 로컬 마이크로 서비스 배포는 클라우드에서 IoT Hub, Cosmos DB, Azure Streaming Analytics, 클라우드의 Azure Time Series Insights 서비스 등과 같은 클라우드 서비스를 사용합니다.

로컬 머신의 IDE에서 원격 모니터링 솔루션 가속기를 실행하려면 [로컬로 원격 모니터링 솔루션 가속기 배포 - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

원격 모니터링 솔루션 가속기에 사용되는 Azure 서비스를 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

### <a name="machine-setup"></a>머신 설치

로컬 배포를 완료하기 위해 로컬 개발 컴퓨터에 설치되어 있어야 하는 도구는 다음과 같습니다.

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - 마이크로 서비스를 변경하려는 경우
* [Node.js v8](https://nodejs.org/) - 이 소프트웨어는 스크립트에서 Azure 리소스를 만드는 데 사용하는 PCS CLI에 대한 필수 구성 요소입니다. Node.js v10은 사용하지 마세요.

> [!NOTE]
> 이러한 도구는 Windows, Linux 및 iOS를 포함한 다양한 플랫폼에서 사용할 수 있습니다.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Docker에서 마이크로 서비스 실행

**start.cmd** 스크립트에서 설정한 환경 변수에 액세스할 수 있는지 확인하려면 새 명령 프롬프트를 엽니다. Windows에서는 다음 명령을 실행하여 환경 변수가 설정되어 있는지 확인할 수 있습니다.

```cmd
set PCS
```

이 명령은 **start.cmd** 스크립트에서 설정한 모든 환경 변수를 표시합니다.

Docker가 로컬 머신에서 실행되고 있는지 확인합니다.
> [!NOTE]
> Docker가 Windows에서 실행되고 있는 경우 [Linux 컨테이너](https://docs.docker.com/docker-for-windows/)를 실행해야 합니다.

로컬 Docker 컨테이너에서 실행되는 마이크로 서비스에서 Azure 클라우드 서비스에 액세스해야 합니다. 컨테이너 내부에서 인터넷 주소를 ping하려면 다음 명령을 사용하여 Docker 환경의 인터넷 연결을 테스트할 수 있습니다.

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

솔루션 가속기를 실행하려면 명령줄 환경에서 **services\\scripts\\local** 폴더로 이동하고 다음 명령을 실행합니다.

```cmd/sh
docker-compose up
```

> [!NOTE] 
> `docker-compose up`을 실행하기 전에 Docker와 [로컬 드라이브를 공유](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115)해야 합니다.

이 명령을 처음 실행하는 경우 Docker는 Docker 허브에서 마이크로 서비스 이미지를 다운로드하여 컨테이너를 로컬로 빌드합니다. 다음 실행에서 Docker는 컨테이너를 즉시 실행합니다.

> [!TIP]
> Microsoft는 종종 새로운 기능이 포함된 새 Docker 이미지를 게시합니다. 다음 명령 세트를 사용하여 최신 Docker 컨테이너를 가져오기 전에 로컬 Docker 컨테이너와 해당 이미지를 정리할 수 있습니다.

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

별도의 셸을 사용하여 컨테이너에서 로그를 볼 수 있습니다. 먼저 `docker ps` 명령을 사용하여 컨테이너 ID를 찾습니다. 그런 다음, `docker logs {container-id} --tail 1000`을 사용하여 지정된 컨테이너에 대한 마지막 1,000개 항목을 봅니다.

### <a name="start-the-stream-analytics-job"></a>Stream Analytics 작업 시작

다음 단계에 따라 Stream Analytics 작업을 시작합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 솔루션에 대해 만든 **리소스 그룹**으로 이동합니다. 리소스 그룹의 이름은 **start.cmd** 스크립트를 실행할 때 선택한 솔루션 이름입니다.
1. 리소스 목록에서 **Stream Analytics 작업**을 클릭합니다.
1. Stream Analytics 작업 **개요** 페이지에서 **시작** 단추를 클릭합니다. 그런 다음, **시작**을 클릭하여 지금 작업을 시작합니다.

### <a name="connect-to-the-dashboard"></a>대시보드에 연결

원격 모니터링 솔루션 대시보드에 액세스 하려면 `http://localhost:8080` 브라우저에서 합니다. 이제 웹 UI와 로컬 마이크로 서비스를 사용할 수 있습니다.

## <a name="clean-up"></a>정리

불필요한 요금을 방지하려면 테스트를 마친 후 Azure 구독에서 클라우드 서비스를 제거합니다. 서비스를 제거하려면 [Azure Portal](https://ms.portal.azure.com)로 이동하여 **start.cmd** 스크립트에서 만든 리소스 그룹을 삭제합니다.

`docker-compose down --rmi all` 명령을 사용하여 Docker 이미지를 제거하고 로컬 컴퓨터의 공간을 확보합니다. 또한 GitHub에서 소스 코드를 복제할 때 만들어진 원격 모니터링 리포지토리의 로컬 복사본을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

원격 모니터링 솔루션을 배포했으니, 다음 단계는 [솔루션 대시보드의 기능을 탐색](quickstart-remote-monitoring-deploy.md)하는 것입니다.
