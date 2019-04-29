---
title: Azure API Management의 용어 | Microsoft Docs
description: 이 문서에서는 API Management와 관련된 용어에 대한 정의를 제공합니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: b99ca444532799d21850058eae0e3f40ed871135
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074057"
---
# <a name="terminology"></a>용어

이 문서에서는 APIM(API Management)과 관련된 용어에 대한 정의를 제공합니다.

## <a name="term-definitions"></a>용어 정의

* **백 엔드 API** - API 및 해당 작업을 구현하는 HTTP 서비스입니다. 
* **프런트 엔드 API**/**APIM API** - APIM API는 API를 호스트하지 않으며, 백 엔드 API를 사용하지 않고 사용자의 필요에 따라 외관을 사용자 지정하기 위해 API용 외관을 만듭니다. 자세한 내용은 [API 가져오기 및 게시](import-and-publish.md)를 참조하세요.
* **APIM 제품** - 제품에는 하나 이상의 API뿐만 아니라 사용 할당량 및 사용 약관이 포함됩니다. 다양한 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. 자세한 내용은 [제품 만들기 및 게시](api-management-howto-add-products.md)를 참조하세요.
* **APIM API 작업** - 각 APIM API는 개발자가 사용할 수 있는 작업 집합을 나타냅니다. 각 APIM API에는 API를 구현하는 백 엔드 서비스에 대한 참조가 포함되어 있으며, 해당 작업은 백 엔드 서비스에 의해 구현되는 작업에 매핑됩니다. 자세한 내용은 [모의 API 응답 만들기](mock-api-responses.md)를 참조하세요.
* **버전** - 특정 사용자를 대상으로 새로운 API 기능이나 다른 API 기능을 게시하려고 하는데, 현재 잘 사용하고 있는 API를 그대로 사용하려는 사용자도 있을 수 있습니다. 자세한 내용은 [여러 버전의 API 게시](api-management-get-started-publish-versions.md)를 참조하세요.
* **수정 버전** - API가 준비되어 개발자들이 사용하기 시작하면 대개 API 호출자의 작업을 중단하지 않으면서 해당 API를 변경해야 합니다. 이렇게 하면 개발자에게 변경 내용을 알릴 때도 유용합니다. 자세한 내용은 [수정 버전 사용](api-management-get-started-revise-api.md)을 참조하세요.
* **개발자 포털** - 고객(개발자)은 개발자 포털을 사용하여 API에 액세스해야 합니다. 개발자 포털은 사용자 지정할 수 있습니다. 자세한 내용은 [개발자 포털 사용자 지정](api-management-customize-styles.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인스턴스 만들기](get-started-create-service-instance.md)

