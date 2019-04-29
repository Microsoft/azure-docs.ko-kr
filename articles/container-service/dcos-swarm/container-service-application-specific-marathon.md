---
title: (사용되지 않음) 애플리케이션 또는 사용자 특정 Marathon 서비스
description: 애플리케이션 또는 사용자 특정 Marathon 서비스 만들기
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/12/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 1ba5ca54c0aa604289eab9c4475297b12580b69a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557561"
---
# <a name="deprecated-create-an-application-or-user-specific-marathon-service"></a>(사용되지 않음) 애플리케이션 또는 사용자 특정 Marathon 서비스 만들기

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Azure 컨테이너 서비스는 Apache Mesos 및 Marathon을 미리 구성하는 마스터 서버 집합을 제공합니다. 클러스터에서 애플리케이션을 오케스트레이션하는 데 사용할 수 있지만 이러한 목적으로는 마스터 서버를 사용하지 않는 것이 좋습니다. 예를 들어 Marathon 구성을 조정하려면 마스터 서버 자체에 로그인하고 변경해야 합니다. 이 방법에서는 표준과 약간 다르고 독립적으로 처리 및 관리해야 하는 고유 마스터 서버를 권장합니다. 또한 한 팀에 필요한 구성은 다른 팀에게는 최적의 구성이 아닐 수도 있습니다.

이 문서에서는 애플리케이션 또는 사용자 특정 Marathon 서비스를 추가하는 방법을 설명합니다.

이 서비스는 단일 사용자 또는 팀에 속하므로 원하는 방식으로 자유롭게 구성할 수 있습니다. 또한 Azure 컨테이너 서비스를 사용하여 서비스를 계속해서 실행되도록 합니다. 서비스가 실패하면 Azure 컨테이너 서비스는 서비스를 다시 시작합니다. 중단 시간은 대부분 느끼지 못합니다.

## <a name="prerequisites"></a>필수 조건
DC/OS Orchestrator 유형을 사용하여 [Azure Container Service의 인스턴스를 배포](container-service-deployment.md)하고 [클라이언트가 클러스터에 연결될 수 있는지 확인합니다](../container-service-connect.md). 또한 다음 단계를 수행합니다.

[!INCLUDE [install the DC/OS CLI](../../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>애플리케이션 또는 사용자 특정 Marathon 서비스 만들기
만들려는 애플리케이션 서비스의 이름을 정의하는 JSON 구성 파일을 만드는 것으로 시작합니다. 여기서는 프레임워크 이름으로 `marathon-alice` 를 사용합니다. `marathon-alice.json`과 같이 파일을 저장합니다.

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

다음으로, DC/OS CLI를 사용하여 구성 파일에서 설정한 옵션으로 Marathon 인스턴스를 설치합니다.

```bash
dcos package install --options=marathon-alice.json marathon
```

이제 `marathon-alice` 서비스가 DC/OS UI의 서비스 탭에서 실행되는 것을 확인하게 됩니다. 이 UI는 직접 액세스하려는 경우 `http://<hostname>/service/marathon-alice/` 가 됩니다.

## <a name="set-the-dcos-cli-to-access-the-service"></a>서비스에 액세스하는 DC/OS CLI 설정
다음과 같이 `marathon-alice` 인스턴스를 가리키도록 `marathon.url` 속성을 설정하여 이 새 서비스에 액세스하도록 DC/OS CLI를 선택적으로 구성할 수 있습니다.

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

`dcos config show` 명령을 사용하여 CLI가 작동 중인 Marathon의 인스턴스를 확인할 수 있습니다. `dcos config unset marathon.url`명령으로 마스터 Marathon 서비스를 사용하도록 되돌릴 수 있습니다.

