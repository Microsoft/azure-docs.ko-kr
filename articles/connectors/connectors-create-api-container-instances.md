---
title: Azure Container Instances 배포 & 관리
description: Azure Logic Apps를 사용 하 여 Azure Container Instances에서 컨테이너 배포를 만들고 관리 하는 작업 및 워크플로를 자동화 합니다.
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046293"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 Azure Container Instances 배포 및 관리

Azure Logic Apps 및 Azure Container Instance 커넥터를 사용 하 여 [컨테이너 그룹](../container-instances/container-instances-container-groups.md)을 배포 하 고 관리 하는 자동화 된 작업 및 워크플로를 설정할 수 있습니다. 컨테이너 인스턴스 커넥터는 다음 작업을 지원 합니다.

* 컨테이너 그룹 만들기 또는 삭제
* 컨테이너 그룹의 속성을 가져옵니다.
* 컨테이너 그룹의 목록 가져오기
* 컨테이너 인스턴스의 로그 가져오기

Logic Apps 트리거에 대한 응답으로 컨테이너 작업 실행과 같은 작업을 수행 하려면 논리 앱에서 이러한 작업을 사용 합니다. 컨테이너 인스턴스 작업의 출력을 다른 작업에 사용할 수도 있습니다. 

이 커넥터는 작업만 제공 하므로 논리 앱을 시작 하려면 **되풀이** 트리거와 같은 별도의 트리거를 사용 하 여 정기적 일정에 따라 컨테이너 작업을 실행 합니다. 또는 Outlook 전자 메일 도착과 같은 이벤트 후에 컨테이너 그룹 배포를 트리거해야 할 수도 있습니다. 

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md) 및 [컨테이너 인스턴스를 만들고 관리](../container-instances/container-instances-quickstart.md) 하는 방법에 대한 기본 지식

* 컨테이너 인스턴스에 액세스 하려는 논리 앱입니다. 작업을 사용 하려면 **되풀이** 트리거와 같은 다른 트리거를 사용 하 여 논리 앱을 시작 합니다.

## <a name="add-a-container-instance-action"></a>컨테이너 인스턴스 작업 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 경로를 선택합니다. 

   * 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 

     또는

   * 작업을 추가하려는 단계 사이에서, 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
   표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가**를 선택합니다.

1. 검색 상자에 필터로 "컨테이너 인스턴스"를 입력 합니다. 작업 목록 아래에서 원하는 Azure Container Instance 커넥터 작업을 선택 합니다.

1. 연결 이름을 입력합니다. 

1. 선택한 작업에 대해 필요한 세부 정보를 입력하고 논리 앱의 워크플로를 계속 빌드합니다.

  예를 들어 다음 이미지에 표시 된 것 처럼 **컨테이너 그룹 만들기** 를 선택 하 고 컨테이너 그룹의 속성과 그룹에 있는 하나 이상의 컨테이너 인스턴스를 입력 합니다 (부분 세부 정보).

  ![컨테이너 그룹 만들기](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI (이전의 Swagger) 설명에서 설명 하는 트리거, 작업 및 제한에 대한 기술 세부 정보는 커넥터의 [참조 페이지](/connectors/aci/) 또는 컨테이너 그룹 [yaml 참조](../container-instances/container-instances-reference-yaml.md)를 검토 합니다.

## <a name="next-steps"></a>다음 단계

* 전자 메일 또는 Twitter 텍스트의 감정를 분석 하기 위해 Azure Container Instances 컨테이너를 실행 하는 [샘플 논리 앱](https://github.com/Azure-Samples/aci-logicapps-integration) 을 참조 하세요.

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.