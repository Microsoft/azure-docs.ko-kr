---
title: Notification Hubs 보안
description: 이 항목에서는 Azure 알림 허브 보안에 대해 설명합니다.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bd9df12cbe941b868c769daccd02c1d81b39f7bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776493"
---
# <a name="security-model-of-azure-notification-hubs"></a>Azure Notification Hubs의 보안 모델

## <a name="overview"></a>개요

이 항목에서는 Azure Notification Hubs의 보안 모델에 대해 설명합니다. Notification Hubs는 Service Bus 엔터티이므로 Service Bus와 동일한 보안 모델을 구현합니다. 자세한 내용은 [Service Bus 인증](https://msdn.microsoft.com/library/azure/dn155925.aspx) 항목을 참조하세요.

## <a name="shared-access-signature-security-sas"></a>SAS(공유 액세스 서명) 보안

Notification Hubs는 SAS(공유 액세스 서명)라는 엔터티 수준 보안 체계를 구현합니다. 이 체계를 통해 메시징 엔터티가 해당 엔터티에 대한 권한을 부여하는 설명에서 최대 12개의 권한 부여 규칙을 선언할 수 있습니다.

"보안 클레임" 섹션에 설명된 대로 각 규칙에는 이름, 키 값(공유 암호) 및 권한 집합이 포함됩니다. 알림 허브를 만들 때 두 개의 규칙이 자동으로 만들어집니다. 즉, 클라이언트 앱에서 사용하는 수신 대기 권한이 있는 규칙과 앱 백 엔드에서 사용하는 모든 권한이 있는 규칙입니다.

클라이언트 앱에서 등록 관리를 수행할 때 날씨 업데이트와 같이 알림을 통해 보낸 정보가 중요하지 않으면 알림 허브에 액세스하는 일반적인 방법은 규칙 수신 대기 전용 액세스의 키 값을 클라이언트 앱에 부여하는 것과 규칙 모든 권한의 키 값을 앱 백 엔드에 부여하는 것입니다.

Windows 스토어 클라이언트 앱에 키 값을 포함하지 않는 것이 좋습니다. 키 값을 포함하지 않는 방법은 클라이언트 앱이 시작할 때 앱 백 엔드에서 키 값을 검색하도록 하는 것입니다.

수신 대기 액세스가 있는 키를 통해 클라이언트 앱이 모든 태그를 등록할 수 있음을 이해하는 것이 중요합니다. 앱이 특정 클라이언트에 대한 특정 태그로 등록을 제한해야 하는 경우(예: 태그가 사용자 ID를 나타내는 경우) 앱 백 엔드가 등록을 수행해야 합니다. 자세한 내용은 등록 관리를 참조하세요. 이러한 방식으로 클라이언트 앱이 Notification Hubs에 직접 액세스할 수 없습니다.

## <a name="security-claims"></a>보안 클레임

다른 엔터티와 마찬가지로 알림 허브 작업에 허용된 3가지 보안 클레임은 수신 대기, 전송 및 관리입니다.

| 클레임   | 설명                                          | 허용되는 연산 |
| ------- | ---------------------------------------------------- | ------------------ |
| 수신 대기  | 단일 등록 만들기/업데이트, 읽기 및 삭제 | 등록 만들기/업데이트<br><br>등록 읽기<br><br>핸들에 대한 모든 등록 읽기<br><br>등록 삭제 |
| 보내기    | 알림 허브에 메시지 보내기                | 메시지 보내기 |
| 관리  | Notification Hubs의 CRUD(PNS 자격 증명 및 보안 키 업데이트 포함) 및 태그 기준 등록 읽기 |알림 허브 만들기/업데이트/읽기/삭제<br><br>태그별 등록 읽기 |

Notification Hubs는 Microsoft Azure Access Control 토큰 및 Notification Hubs에서 직접 구성하는 공유 키로 생성된 서명 토큰에서 부여한 클레임을 허용합니다.
