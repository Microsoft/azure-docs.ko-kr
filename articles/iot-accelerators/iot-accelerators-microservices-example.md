---
title: 마이크로 서비스 변경 및 다시 배포 - Azure | Microsoft Docs
description: 이 자습서에서는 Remote Monitoring에서 마이크로 서비스를 변경하고 다시 배포하는 방법을 보여줍니다.
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447627"
---
# <a name="customize-and-redeploy-a-microservice"></a>마이크로 서비스 사용자 지정 및 다시 배포

이 자습서에서는 원격 모니터링 솔루션에서 하나의 [마이크로서비스](https://azure.com/microservices)를 편집하고, 마이크로서비스의 이미지를 빌드하고, 이미지를 도커 허브에 배포한 다음, 원격 모니터링 솔루션에서 사용하는 방법을 보여줍니다. 이 개념을 소개하기 위해 자습서는 마이크로 서비스 API를 호출하고 상태 메시지를 "Alive and Well"에서 "New Edits Made Here!"로 변경하는 기본 시나리오를 사용합니다.

원격 모니터링 솔루션은 도커 허브에서 가져온 도커 이미지를 사용하여 빌드된 마이크로 서비스를 사용합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

>[!div class="checklist"]
> * 원격 모니터링 솔루션에서 마이크로 서비스 편집 및 빌드
> * 도커 이미지 빌드
> * 도커 허브에 도커 이미지 푸시
> * 새로운 도커 이미지 가져오기
> * 변경 사항 시각화 

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 다음이 필요합니다.

>[!div class="checklist"]
> * [로컬에 원격 모니터링 솔루션 가속기 배포](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Docker 계정](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) - API 응답을 보는 데 필요함

## <a name="call-the-api-and-view-response-status"></a>API 호출 및 응답 상태 보기

이 부분에서는 기본 IoT 허브 관리자 마이크로 서비스 API를 호출합니다. API는 마이크로 서비스를 사용자 지정하여 나중에 변경할 상태 메시지를 반환합니다.

1. 원격 모니터링 솔루션이 컴퓨터에서 로컬로 실행되고 있는지 확인합니다.
2. Postman을 다운로드한 곳을 찾아 엽니다.
3. Postman에서 GET에 다음 값을 입력합니다. `http://localhost:8080/iothubmanager/v1/status`.
4. 반환을 보고 "상태": "OK:Alive and Well"을 확인해야 합니다.

    ![Alive and Well Postman 메시지](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>상태 변경 및 이미지 빌드

이제 Iot Hub Manager 마이크로 서비스의 상태 메시지를 "New Edits Made Here!"로 변경합니다. 그런 후 이 새로운 상태로 도커 이미지를 다시 빌드합니다. 문제가 발생하면 [문제 해결](#Troubleshoot) 섹션을 참조하세요.

1. 터미널이 열려 있는지 확인하고 원격 모니터링 솔루션을 복제한 디렉토리로 변경합니다. 
1. 디렉터리를 "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services"로 변경합니다.
1. 원하는 텍스트 편집기나 IDE에서 StatusService.cs를 엽니다. 
1. 다음 코드를 찾습니다.

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    아래의 코드로 변경하여 저장합니다.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. 터미널로 돌아가서 이제 다음 디렉터리로 변경합니다. “azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker”
6. 새로운 도커 이미지를 빌드하려면 다음을 입력합니다.

    ```sh
    sh build
    ```
    
    또는 Windows에서 다음을 수행합니다.
    
    ```cmd
    ./build.cmd
    ```

7. 새 이미지가 성공적으로 만들어졌는지 확인하려면 다음을 입력합니다.

    ```cmd/sh
    docker images 
    ```

리포지토리는 "azureiotpcs/iothub-manager-dotnet"이어야 합니다.

![성공적인 Docker 이미지](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>이미지 태그 지정 및 밀어넣기
새로운 도커 이미지를 도커 허브에 푸시할 수 있으려면 먼저 Docker는 태그된 이미지를 기대합니다. 문제가 발생하면 [문제 해결](#Troubleshoot) 섹션을 참조하세요.

1. 다음을 입력하여 만든 도커 이미지의 이미지 ID를 찾습니다.

    ```cmd/sh
    docker images
    ```

2. "테스트" 유형으로 이미지에 태그 하려면

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. 새로 태그가 지정된 이미지를 도커 허브에 푸시 하려면 다음을 입력합니다.

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. 인터넷 브라우저를 열고 [도커 허브](https://hub.docker.com/)로 이동하여 로그인합니다.
5. 이제 도커 허브에 새로 푸시 된 도커 이미지가 표시됩니다.
![도커 허브에 있는 도커 이미지](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>원격 모니터링 솔루션 업데이트
이제 도커 허브에서 새로운 도커 이미지를 가져오려면 로컬 docker-compose.yml을 업데이트해야 합니다. 문제가 발생하면 [문제 해결](#Troubleshoot) 섹션을 참조하세요.

1. 터미널로 돌아가서 다음 디렉터리로 변경합니다. “azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local”
2. 원하는 텍스트 편집기나 IDE에서 docker-compose.yml을 엽니다.
3. 다음 코드를 찾습니다.

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    아래의 이미지처럼 보이도록 변경하여 저장합니다.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>새 응답 상태보기
원격 모니터링 솔루션의 로컬 인스턴스를 재배포하고 Postman에서 새로운 상태 응답을 확인하여 마칩니다.

1. 터미널로 돌아가서 이제 다음 디렉터리로 변경합니다. “azure-iot-pcs-remote-monitoring-dotnet/scripts/local”
2. 터미널에 다음 명령을 입력하여 원격 모니터링 솔루션의 로컬 인스턴스를 시작합니다.

    ```cmd/sh
    docker-compose up
    ```

3. Postman을 다운로드한 곳을 찾아 엽니다.
4. Postman에서 GET에 다음 요청을 입력합니다. `http://localhost:8080/iothubmanager/v1/status`. 이제 "상태": "OK: New Edits Made Here!"가 표시됩니다.

![New Edits Made Here postman 메시지](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>문제 해결

문제가 발생하는 경우 로컬 컴퓨터에서 도커 이미지 및 컨테이너 제거를 시도합니다.

1. 모든 컨테이너를 제거하려면 먼저 실행 중인 모든 컨테이너를 중지해야 합니다. 터미널을 열고 입력합니다.

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. 모든 이미지를 제거하려면 터미널을 열고 다음을 입력합니다. 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. 다음을 입력하여 기계에 컨테이너가 있는지 확인할 수 있습니다.

    ```cmd/sh
    docker ps -aq 
    ```

    모든 컨테이너를 성공적으로 제거하면 아무것도 표시되지 않습니다.

4. 다음을 입력하면 머신에 이미지가 있는지 확인할 수 있습니다.

    ```cmd/sh
    docker images
    ```

    모든 컨테이너를 성공적으로 제거하면 아무것도 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아봅니다.

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 원격 모니터링 솔루션에서 마이크로 서비스 편집 및 빌드
> * 도커 이미지 빌드
> * 도커 허브에 도커 이미지 푸시
> * 새로운 도커 이미지 가져오기
> * 변경 사항 시각화 

다음으로 시도할 것은 [원격 모니터링 솔루션에서 디바이스 시뮬레이터 마이크로서비스를 사용자 지정](iot-accelerators-microservices-example.md)하는 것입니다.

원격 모니터링 솔루션에 대한 자세한 개발자 정보는 다음을 참조하세요.

* [개발자 참조 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

