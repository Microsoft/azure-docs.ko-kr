---
title: 앱을 최신 스키마로 마이그레이션
description: 논리 앱 워크플로 JSON 정의를 최신 워크플로 정의 언어 스키마 버전으로 마이그레이션하는 방법을 설명합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 114b8b32d4abb1fd9b7e641625cd1b132470bafd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87281449"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>논리 앱을 최신 스키마 버전으로 마이그레이션

기존 논리 앱을 최신 스키마로 이동하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

2. 논리 앱의 메뉴에서 **개요** 를 선택합니다. 도구 모음에서 **스키마 업데이트** 를 선택합니다.

   > [!NOTE]
   > **스키마 업데이트** 를 선택하면 Azure Logic Apps가 자동으로 마이그레이션 단계를 실행하고 코드 출력을 제공합니다. 이 출력을 사용하여 논리 앱 정의를 업데이트할 수 있습니다. 그러나 다음 **모범 사례** 섹션에 설명된 대로 모범 사례를 따라야 합니다.

   ![스키마 업데이트](./media/connectors-schema-migration/update-schema.png)

   스키마 업데이트 페이지가 나타나고 새 스키마의 향상된 기능을 설명하는 문서 링크를 보여줍니다.

## <a name="best-practices"></a>모범 사례

다음은 논리 앱을 최신 스키마 버전으로 마이그레이션하는 모범 사례입니다.

* 마이그레이션된 스크립트를 새 논리 앱으로 복사합니다. 테스트를 완료하고 마이그레이션된 앱이 예상대로 작동하는 것을 확인하기 전에는 이전 버전을 덮어쓰지 마세요.

* 프로덕션에 배치하기 **전에** 논리 앱을 테스트합니다.

* 마이그레이션이 완료되면 가능한 경우 [관리 API](../connectors/apis-list.md)를 사용하도록 논리 앱 업데이트를 시작합니다. 예를 들어 Dropbox v1을 사용하는 모든 곳에서 DropBox v2를 사용하기 시작합니다.

## <a name="next-steps"></a>다음 단계

* [논리 앱을 수동으로 마이그레이션](../logic-apps/logic-apps-schema-2016-04-01.md)하는 방법을 알아봅니다.

