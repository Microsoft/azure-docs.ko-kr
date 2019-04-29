---
title: AS2 메시지 설정-Azure Logic Apps
description: AS2에 대 한 참조 가이드를 보내고 Azure Logic Apps 및 엔터프라이즈 통합 팩의에서 수신 설정
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769453"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩을 사용 하 여 Azure Logic Apps에서 AS2 메시지 설정에 대 한 참조

이 참조에는 AS2 규약에서 거래 파트너 간에 보내고 받은 메시지를 처리 하는 방법을 지정 하는 데 설정할 수 있는 속성을 설명 합니다. 파트너와 메시지를 교환에 규약에 따라 이러한 속성을 설정 합니다.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 수신 설정

!["수신 설정" 선택](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| 자산 | 필수 | 설명 |
|----------|----------|-------------|
| **메시지 속성 재정의** | 아닙니다. | 속성 설정을 사용 하 여 들어오는 메시지의 속성을 재정의합니다. |
| **메시지 서명 필요** | 아닙니다. | 모든 들어오는 메시지에 디지털 서명 해야 하는지 여부를 지정 합니다. 서명, 필요한 경우에서 **인증서** 목록 메시지 서명의 유효성을 검사 하는 것에 대 한 기존 게스트 파트너 공용 인증서를 선택 합니다. 인증서가 없는 경우에 대해 자세히 알아보세요 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)합니다. |
| **메시지 암호화** | 아닙니다. | 모든 들어오는 메시지를 암호화 해야 하는지 여부를 지정 합니다. 암호화되지 않은 메시지는 거부됩니다. , 암호화가 필요 합니다 **인증서** 목록에서 들어오는 메시지 암호 해독에 대 한 기존 호스트 파트너 개인 인증서를 선택 합니다. 인증서가 없는 경우에 대해 자세히 알아보세요 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)합니다. |
| **메시지 압축 필요** | 아닙니다. | 모든 들어오는 메시지를 압축 해야 하는지 여부를 지정 합니다. 압축되지 않은 메시지는 거부됩니다. |
| **메시지 ID 중복 허용 안 함** | 아닙니다. | 중복 된 Id 사용 하 여 메시지를 허용할지 여부를 지정 합니다. 중복 된 Id를 허용 하지 않을 경우에 검사 사이의 일 수가 선택 합니다. 중복 항목을 일시 중단할지 여부를 선택할 수도 있습니다. |
| **MDN 텍스트** | 아닙니다. | 기본 MDN 메시지 처리 알림 ()를 메시지 보낸 사람에 게 지정 합니다. |
| **Send MDN** | 아닙니다. | 받은 메시지에 대 한 동기 Mdn을 보낼 것인지 지정 합니다.  |
| **서명 된 MDN 보내기** | 아닙니다. | 받은 메시지에 대 한 서명 된 Mdn을 보낼 것인지 지정 합니다. 서명, 필요한 경우에서 **MIC 알고리즘** 목록에서 메시지 서명에 사용할 알고리즘을 선택 합니다. |
| **비동기 MDN 보내기** | 아닙니다. | 비동기적으로 Mdn을 보낼 것인지 지정 합니다. 비동기 Mdn을 선택 합니다 **URL** 상자 Mdn을 보낼 위치에 대 한 URL을 지정 합니다. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 송신 설정

!["설정 보내기" 선택](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| 자산 | 필수 | 설명 |
|----------|----------|-------------|
| **메시지 서명 사용** | 아닙니다. | 나가는 모든 메시지 디지털 서명 해야 하는지 여부를 지정 합니다. 서명, 필요한 경우 이러한 값을 선택 합니다. <p>**서명 알고리즘** 목록에서 메시지 서명에 사용할 알고리즘을 선택 합니다. <br>**인증서** 목록 메시지에 서명 하는 것에 대 한 기존 호스트 파트너 개인 인증서를 선택 합니다. 인증서가 없는 경우에 대해 자세히 알아보세요 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)합니다. |
| **메시지 암호화 사용** | 아닙니다. | 나가는 모든 메시지를 암호화 해야 하는지 여부를 지정 합니다. 암호화를 요구 하는 경우 이러한 값을 선택 합니다. <p>**암호화 알고리즘** 목록에서 메시지를 암호화 하는 데 사용할 게스트 파트너 공용 인증서 알고리즘을 선택 합니다. <br>**인증서** 목록에서 보내는 메시지 암호화에 대 한 기존 게스트 파트너 개인 인증서를 선택 합니다. 인증서가 없는 경우에 대해 자세히 알아보세요 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)합니다. |
| **메시지 압축 사용** | 아닙니다. | 나가는 모든 메시지를 압축 해야 하는지 여부를 지정 합니다. |
| **HTTP 헤더 펼침** | 아닙니다. | HTTP 배치 `content-type` 단일 줄으로 헤더입니다. |
| **MIME 헤더로 파일 이름 전송** | 아닙니다. | MIME 헤더로 파일 이름 포함 여부를 지정 합니다. |
| **MDN 요청** | 아닙니다. | 나가는 모든 메시지에 대 한 메시지 처리 알림 (Mdn)을 받을 것인지를 지정 합니다. |
| **서명 된 MDN 요청** | 아닙니다. | 나가는 모든 메시지에 대 한 서명 된 Mdn을 받을 것인지를 지정 합니다. 서명, 필요한 경우에서 **MIC 알고리즘** 목록에서 메시지 서명에 사용할 알고리즘을 선택 합니다. |
| **비동기 MDN 요청** | 아닙니다. | 비동기적으로 Mdn을 받으려면 것인지 지정 합니다. 비동기 Mdn을 선택 합니다 **URL** 상자 Mdn을 보낼 위치에 대 한 URL을 지정 합니다. |
| **Nrr 사용** | 아닙니다. | 비 거부 수신 (NRR) 할지 여부를 지정 합니다. 이 통신 특성 증명 정보를 제공 합니다. 데이터는 해결 되었으며 받은 합니다. |
| **SHA2 알고리즘 형식** | 아닙니다. | 헤더를 나가는 AS2 메시지 또는 MDN의 서명에 사용할 MIC 알고리즘 형식을 지정 합니다. |
||||

## <a name="next-steps"></a>다음 단계

[AS2 메시지 교환](../logic-apps/logic-apps-enterprise-integration-as2.md)
