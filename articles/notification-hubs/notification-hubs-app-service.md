---
title: App Service Mobile Apps와 Notification Hubs 통합
description: Azure Notification Hubs가 Azure App Service Mobile Apps와 어떻게 작동하는지 알아봅니다.
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88004051"
---
# <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps와 통합

Azure 서비스 전반에서 원활하고 일관적인 사용 환경을 조성하기 위해 Azure Notification Hubs를 사용한 알림이 [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)에 기본적으로 지원됩니다. [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)는 엔터프라이즈 개발자 및 시스템 통합자를 위해 확장성이 크고 전 세계에서 사용 가능한 모바일 애플리케이션 개발 플랫폼을 제공합니다. 이 플랫폼은 모바일 개발자에게 풍부한 기능 세트를 제공합니다.

Mobile Apps 개발자는 다음 워크플로에서 Notification Hubs를 사용할 수 있습니다.

1. 디바이스 PNS 핸들을 검색합니다.
2. Mobile Apps 클라이언트 SDK 등록 API를 사용하여 Notification Hubs에 디바이스를 등록합니다.

    > [!NOTE]
    > Mobile Apps는 등록 시 보안을 목적으로 모든 태그를 제거합니다. 백 엔드에서 Notification Hubs와 직접 작업하여 디바이스와 태그를 연결합니다.

3. Notification Hubs를 통해 앱 백 엔드에서 알림을 보냅니다.

이 통합이 제공하는 몇 가지 이점은 다음과 같습니다.

- **Mobile Apps 클라이언트 SDK**: 이러한 다중 플랫폼 SDK는 등록을 위한 API를 제공하고 모바일 앱과 연결된 알림 허브와 통신합니다. Notification Hubs 자격 증명이 필요하지 않거나 추가 서비스를 사용하지 않아도 됩니다.
  - *사용자에게 푸시*: SDK는 "사용자에게 푸시" 시나리오를 활성화하기 위해 Mobile Apps 인증 사용자 ID로 지정된 디바이스에 자동으로 태그를 지정합니다.
  - *디바이스에 푸시*: SDK는 자동으로 Mobile Apps 설치 ID를 GUID로 사용하여 Notification Hubs에 등록하므로 여러 서비스 GUID를 유지 관리할 필요가 없습니다.
- **설치 모델**: Mobile Apps는 Notification Hubs의 최신 푸시 모델과 함께 사용되어 푸시 알림 서비스에 맞게 조정되고 사용이 편리한 JSON 설치의 디바이스와 연결된 모든 푸시 속성을 나타냅니다.
- **유연성**: 개발자는 통합되어 있더라도 Notification Hubs와 직접 작동하도록 언제든지 선택할 수 있습니다.
- **[Azure Portal](https://portal.azure.com)의 통합된 환경**: 푸시는 하나의 기능으로 Mobile Apps에 시각적으로 표현되며 개발자는 Mobile Apps를 통해 연결된 알림 허브와 쉽게 작업할 수 있습니다.
