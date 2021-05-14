---
title: Azure Notification Hubs에서 Windows 푸시 알림 서비스 구성 | Microsoft Docs
description: Azure 알림 허브에 대해 Windows 푸시 알림 서비스 설정을 구성하는 방법을 알아봅니다.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 62d73a23fff3bd15d873a579d451b2f91509b7fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87758733"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Azure Portal에서 Windows 푸시 알림 서비스 설정 구성

이 문서에서는 Azure Portal을 사용하여 Azure 알림 허브에 대해 WNS(Windows 알림 서비스) 설정을 구성하는 방법을 보여 줍니다.  

## <a name="prerequisites"></a>필수 구성 요소

알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요.

## <a name="configure-windows-push-notification-service-wns"></a>WNS(Windows 푸시 알림 서비스) 구성

다음 절차에서는 알림 허브에 대해 WNS(Windows 푸시 알림 서비스) 설정을 구성하는 단계를 설명합니다.

1. Azure Portal의 **알림 허브** 페이지에서 왼쪽 메뉴에 있는 **Windows(WNS)** 를 선택합니다.
2. **패키지 SID** 및 **보안 키** 에 사용할 값을 입력합니다.
3. **저장** 을 선택합니다.

   ![패키지 SID 및 보안 키 상자를 보여주는 스크린샷](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>다음 단계

Azure Notification Hubs 및 WNS(Windows 푸시 알림 서비스)를 사용하여 유니버설 Windows 플랫폼 애플리케이션에 푸시 알림을 보내는 방법에 대한 단계별 지침이 포함된 자습서는 [Azure Notification Hubs를 사용하여 UWP 앱에 알림 보내기](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)를 참조하세요.
