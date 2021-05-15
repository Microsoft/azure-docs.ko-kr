---
title: Azure Notification Hubs에서 Apple Push Notification Service 구성 | Microsoft Docs
description: APNS(Apple Push Notification Service) 설정을 사용하여 Azure 알림 허브를 구성하는 방법을 알아봅니다.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85255401"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure Portal에서 알림 허브에 대한 Apple Push Notification Service 설정 구성

이 문서에서는 Azure Portal을 사용하여 Azure 알림 허브에 대한 APNS(Apple Push Notification Service) 설정을 구성하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요.

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification Service 구성

다음 절차에서는 알림 허브에 대한 APNS(Apple Push Notification Service) 설정을 구성하는 단계를 설명합니다.

1. Azure Portal의 **알림 허브** 페이지 왼쪽 메뉴에서 **Apple(APNS)** 을 선택합니다.

1. **인증 모드** 의 경우 **인증서** 또는 **토큰** 을 선택합니다.

   - **인증서** 를 선택한 경우:
      - 파일 아이콘을 선택한 다음, 업로드할 *.p12* 파일을 선택합니다.
      - 암호를 입력합니다.
      - **샌드박스** 모드를 선택합니다. 또는 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려면 **프로덕션** 모드를 선택합니다.

     ![Azure Portal의 APNS 인증서 구성 스크린샷](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - **토큰** 을 선택한 경우:
      - **키 ID**, **번들 ID**, **팀 ID** 및 **토큰** 에 대한 값을 입력합니다.
      - **샌드박스** 모드를 선택합니다. 또는 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려면 **프로덕션** 모드를 선택합니다.

     ![Azure Portal의 APNS 토큰 구성 스크린샷](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>다음 단계

iOS 디바이스로 알림을 보내는 방법에 대한 단계별 지침이 포함된 자습서는 [Azure Notification Hubs를 사용하여 iOS 앱에 푸시 알림 보내기](ios-sdk-get-started.md) 문서를 참조하세요.
