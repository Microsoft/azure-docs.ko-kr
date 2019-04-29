---
title: 웹 서비스 엔드포인트 만들기
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio에서 웹 서비스 엔드포인트를 만듭니다. 웹 서비스의 각 엔드포인트는 독립적으로 처리, 제한 및 관리됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: ac434a696f6e77e5ce61b430232166e7727eda38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751181"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>배포된 Azure Machine Learning Studio 웹 서비스에 대한 엔드포인트 만들기

> [!NOTE]
> 이 항목에서는 **클래식** Machine Learning 웹 서비스에 적용되는 기술을 설명합니다.

웹 서비스가 배포된 후 해당 서비스에 대한 기본 엔드포인트가 만들어집니다. 기본 엔드포인트는 API 키를 사용하여 호출할 수 있습니다. 웹 서비스 포털에서 고유 키를 사용하여 엔드포인트를 더 추가할 수 있습니다.
웹 서비스의 각 엔드포인트는 독립적으로 처리, 제한 및 관리됩니다. 각 엔드포인트에는 고객에게 배포할 수 있는 권한 부여 키가 있는 고유한 URL이 있습니다.

## <a name="add-endpoints-to-a-web-service"></a>웹 서비스에 엔드포인트 추가

Azure Machine Learning 웹 서비스 포털을 사용하여 웹 서비스에 엔드포인트를 추가할 수 있습니다. 엔드포인트가 만들어지면 동기 API, 일괄 처리 API 및 Excel 워크시트를 통해 엔드포인트를 사용할 수 있습니다.

> [!NOTE]
> 웹 서비스에 엔드포인트를 더 추가한 경우 기본 엔드포인트는 삭제할 수 없습니다.

1. Machine Learning Studio의 왼쪽 탐색 열에서 Web Services를 클릭합니다.
2. 웹 서비스 대시보드 아래쪽에서 **엔드포인트 관리**를 클릭합니다. Azure Machine Learning 웹 서비스 포털에 웹 서비스 엔드포인트 페이지가 열립니다.
3. **새로 만들기**를 클릭합니다.
4. 새 엔드포인트에 대한 이름 및 설명을 입력합니다. 엔드포인트 이름은 길이가 24자 이하이고 알파벳 소문자 또는 숫자로 구성되어야 합니다. 로깅 수준 및 예제 데이터 사용 여부를 선택합니다. 자세한 내용은 [Machine Learning 웹 서비스에 대해 로깅 사용](web-services-logging.md)을 참조하세요.

## <a id="scaling"></a> 엔드포인트를 더 추가하여 웹 서비스 확장

기본적으로 게시된 각각의 웹 서비스는 20개의 동시 요청을 지원하고 최대 200개의 동시 요청을 지원할 수 있도록 구성됩니다. Azure Machine Learning Studio는 웹 서비스에 대한 최상의 성능을 제공하기 위해 설정을 자동으로 최적화하고 포털 값은 무시됩니다.

최대 동시 호출 값인 200에서 지원하는 것보다 많은 부하를 사용하여 API를 호출하려는 경우 동일한 웹 서비스에서 여러 엔드포인트를 만들어야 합니다. 그런 다음 모든 끝점에 부하를 무작위로 분산해야 합니다.

웹 서비스의 크기를 조정하는 것은 일반적인 작업입니다. 크기를 조정하는 몇 가지 이유는 200개 이상의 동시 요청을 지원하거나, 여러 엔드포인트를 통해 가용성을 높이거나, 웹 서비스에 대한 별도의 엔드포인트를 제공하기 위해서 입니다. [Azure Machine Learning 웹 서비스](https://services.azureml.net/) 포털을 통해 동일한 웹 서비스에 대한 추가 엔드포인트를 추가하여 규모를 증가시킬 수 있습니다.

해당하는 높은 속도로 API를 호출하지 않을 경우 매우 높은 동시성 개수를 사용하는 것은 바람직하지 않습니다. 높은 부하로 구성된 API에 상대적으로 낮은 부하를 배치할 경우 가끔 시간 초과 및/또는 대기 시간 급증이 나타날 수 있습니다.

동기 API는 일반적으로 낮은 대기 시간을 원하는 경우에 사용됩니다. 여기서 대기 시간은 API가 하나의 요청을 완료하는 데 걸리는 시간을 의미하며 네트워크 지연을 고려하지 않습니다. 대기 시간이 50ms인 API가 있다고 가정합시다. 높음 제한 수준 및 최대 동시 호출 = 20으로 사용 가능한 용량을 완전히 사용하려면 이 API를 초당 20 * 1000 / 50 = 400회 호출해야 합니다. 더욱 확장하여 최대 동시 호출 수를 200으로 설정하면 대기 시간이 50ms일 경우 API를 초당 4000회 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md).
