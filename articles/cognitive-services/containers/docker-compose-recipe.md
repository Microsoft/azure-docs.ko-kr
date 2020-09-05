---
title: Docker Compose를 사용하여 여러 컨테이너 배포
titleSuffix: Azure Cognitive Services
description: 여러 Cognitive Services 컨테이너를 배포 하는 방법을 알아봅니다. 이 문서에서는 Docker Compose를 사용 하 여 여러 Docker 컨테이너 이미지를 오케스트레이션 하는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 740311226a662ea3d3f8bba3ee5156e14f74516b
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244298"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Docker Compose를 사용하여 여러 컨테이너 배포

이 문서에서는 여러 Azure Cognitive Services 컨테이너를 배포 하는 방법을 보여 줍니다. 특히 Docker Compose를 사용 하 여 여러 Docker 컨테이너 이미지를 오케스트레이션 하는 방법을 알아봅니다.

> [Docker Compose](https://docs.docker.com/compose/) 는 다중 컨테이너 Docker 응용 프로그램을 정의 하 고 실행 하기 위한 도구입니다. 작성 시 YAML 파일을 사용 하 여 응용 프로그램의 서비스를 구성 합니다. 그런 다음 단일 명령을 실행 하 여 구성에서 모든 서비스를 만들고 시작 합니다.

단일 호스트 컴퓨터에서 여러 컨테이너 이미지를 오케스트레이션 하는 것이 유용할 수 있습니다. 이 문서에서는 읽기 및 폼 인식기 컨테이너를 함께 가져옵니다.

## <a name="prerequisites"></a>필수 구성 요소

이 절차를 수행 하려면 로컬에 설치 하 고 실행 해야 하는 여러 도구가 필요 합니다.

* Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/cognitive-services)을 만드세요.
* [Docker 엔진](https://www.docker.com/products/docker-engine). Docker CLI가 콘솔 창에서 작동 하는지 확인 합니다.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 다음 가격 책정 계층만이 컨테이너에서 작동 합니다.
  * F0 또는 표준 가격 책정 계층을 사용 하 여 리소스를 **Computer Vision** 합니다.
  * F0 또는 표준 가격 책정 계층을 사용 하는 **폼 인식기** 리소스
  * S0 가격 책정 계층이 있는 **Cognitive Services** 리소스

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

[Cognitive Services 음성 컨테이너 요청 양식을](https://aka.ms/speechcontainerspreview/)완료 하 고 제출 합니다. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker Compose 파일

YAML 파일은 배포할 모든 서비스를 정의 합니다. 이러한 서비스는 `DockerFile` 또는 기존 컨테이너 이미지를 사용 합니다. 이 경우 두 개의 미리 보기 이미지를 사용 합니다. 다음 YAML 파일을 복사 하 여 붙여넣고 *docker로 작성 합니다. yaml*로 저장 합니다. 파일에 적절 한 **apikey**, **청구**및 **endpointuri** 값을 제공 합니다.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> 호스트 컴퓨터에서 **볼륨** 노드 아래에 지정 된 디렉터리를 만듭니다. 이 방법은 볼륨 바인딩을 사용 하 여 이미지를 탑재 하기 전에 디렉터리가 있어야 하기 때문에 필요 합니다.

## <a name="start-the-configured-docker-compose-services"></a>구성 된 Docker Compose 서비스 시작

Docker Compose 파일을 사용 하면 정의 된 서비스의 수명 주기 (서비스 시작, 중지 및 다시 작성)의 모든 단계를 관리할 수 있습니다. 서비스 상태 보기 및 로그 스트리밍이 있습니다. 프로젝트 디렉터리 (docker 작성 .yaml 파일이 있는)에서 명령줄 인터페이스를 엽니다.

> [!NOTE]
> 오류를 방지 하려면 호스트 컴퓨터가 Docker 엔진과 드라이브를 올바르게 공유 하는지 확인 합니다. 예를 들어 *E:\publicpreview* 이 *docker-작성 .yaml* 파일에서 디렉터리로 사용 되는 경우 드라이브 **E** 를 docker와 공유 합니다.

명령줄 인터페이스에서 다음 명령을 실행 하 여 *docker-작성 .yaml* 파일에 정의 된 모든 서비스를 시작 하거나 다시 시작 합니다.

```console
docker-compose up
```

Docker는이 구성을 사용 하 여 **docker** 구성 명령을 처음 실행할 때 **서비스** 노드 아래에 구성 된 이미지를 가져와서 다운로드 하 고 탑재 합니다.

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

이미지를 다운로드 한 후 이미지 서비스가 시작 됩니다.

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>서비스 가용성 확인

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

다음은 몇 가지 예제 출력입니다.

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>테스트 컨테이너

호스트 컴퓨터에서 브라우저를 열고 *docker-작성* 파일 (예:)의 지정 된 포트를 사용 하 여 **localhost** 로 이동 http://localhost:5021/swagger/index.html 합니다. 예를 들어 API에서 **사용해 보기** 기능을 사용 하 여 폼 인식기 끝점을 테스트할 수 있습니다. 두 컨테이너 swagger 페이지가 모두 사용 가능 하 고 테스트 가능 해야 합니다.

![양식 인식기 컨테이너](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너](../cognitive-services-container-support.md)
