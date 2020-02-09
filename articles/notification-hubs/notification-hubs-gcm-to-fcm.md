---
title: Azure Notification Hubs 및 Google FCM (Firebase Cloud Messaging) 마이그레이션
description: Azure Notification Hubs에서 Google GCM을 FCM로 해결 하는 방법을 설명 합니다.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 80eae09240bde61870995468485338db5f0b9c2d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212310"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure Notification Hubs 및 Google FCM (Firebase Cloud Messaging) 마이그레이션

## <a name="current-state"></a>현재 상태

Google에서 Google Cloud Messaging (GCM)에서 FCM (Firebase Cloud Messaging)로의 마이그레이션을 발표 했을 때와 같은 푸시 서비스는 변경을 수용 하기 위해 Android 장치에 알림을 전송 하는 방법을 조정 해야 했습니다.

서비스 백 엔드를 업데이트 한 후 필요에 따라 API 및 Sdk에 대 한 업데이트를 게시 했습니다. 이 구현에서는 고객의 영향을 최소화 하기 위해 기존 GCM 알림 스키마와의 호환성을 유지 하는 방법을 결정 했습니다. 이는 현재 FCM 레거시 모드에서 FCM를 사용 하 여 Android 장치로 알림을 보내는 것을 의미 합니다. 궁극적으로 새로운 기능 및 페이로드 형식을 포함 하 여 FCM에 대 한 진정한 지원을 추가 하려고 합니다. 이는 장기적인 변화 이며 현재 마이그레이션은 기존 응용 프로그램 및 Sdk와의 호환성을 유지 하는 데 중점을 두었습니다. 앱에서 GCM 또는 FCM Sdk (SDK 포함)를 사용할 수 있으며 알림이 올바르게 전송 되는지 확인 합니다.

일부 고객은 최근에 알림에 대해 GCM 엔드포인트을 사용 하 여 앱에 대 한 Google 경고에서 전자 메일을 받았습니다. 이것은 경고 일 뿐 이며, 아무 것도 중단 되지 않습니다. 앱의 Android 알림은 처리를 위해 Google에 전송 되 고 Google은 계속 처리 합니다. 서비스 구성에서 GCM 엔드포인트을 명시적으로 지정한 일부 고객은 여전히 사용 되지 않는 엔드포인트을 사용 하 고 있습니다. 이 격차를 이미 파악 했 고 Google에서 전자 메일을 보낼 때 문제를 해결 하기 위해 노력 했습니다.

사용 되지 않는 엔드포인트 및 수정 프로그램을 배포 했습니다.

## <a name="going-forward"></a>앞으로 이동

Google의 FCM FAQ는 아무것도 수행할 필요가 없다는 것을 의미 합니다. [FCM FAQ](https://developers.google.com/cloud-messaging/faq)에서 Google은 "클라이언트 SDK 및 GCM 토큰은 계속 무기한으로 작동 합니다. 그러나 FCM로 마이그레이션해야 하는 경우를 제외 하 고 Android 앱에서 최신 버전의 Google Play 서비스를 대상으로 지정할 수 없습니다. "

앱에서 GCM 라이브러리를 사용 하는 경우 계속 해 서 Google 지침에 따라 앱의 FCM 라이브러리로 업그레이드 합니다. 이 SDK는와 호환 되므로 앱에서 앱의 모든 항목을 업데이트할 필요가 없습니다 (SDK 버전을 최신 상태로 유지 하는 경우).

## <a name="questions-and-answers"></a>질문과 대답

다음은 고객 으로부터 몇 가지 일반적인 질문에 대 한 답변입니다.

**Q:** 구분 날짜의 호환성을 위해 수행 해야 하는 작업 (Google의 현재 구분 날짜는 29 일 수 있으며 변경 될 수 있음)

**A:** 아무 일도 일어나지 않습니다. 기존 GCM 알림 스키마와의 호환성을 유지 합니다. GCM 키는 응용 프로그램에서 사용 하는 GCM Sdk 및 라이브러리와 마찬가지로 정상적으로 계속 작동 합니다.

새 기능을 활용 하기 위해 FCM Sdk 및 라이브러리로 업그레이드 하기로 결정 한 경우 GCM 키가 계속 작동 합니다. 원하는 경우 FCM 키를 사용 하도록 전환할 수 있지만 새 Firebase 프로젝트를 만들 때 기존 GCM 프로젝트에 Firebase를 추가 해야 합니다. 이렇게 하면 아직 GCM Sdk 및 라이브러리를 사용 하는 이전 버전의 앱을 실행 중인 고객과의 호환성이 보장 됩니다.

새 FCM 프로젝트를 만드는 중이 고 기존 GCM 프로젝트에 연결 되지 않은 경우 새 FCM 암호를 사용 하 여 Notification Hubs를 업데이트 하면 새 FCM 키에 이전 GCM에 대 한 링크가 없으므로 현재 앱 설치에 알림을 푸시할 수 있는 기능이 손실 됩니다. 프로젝트가.

**Q:** 이전 GCM 엔드포인트에 대 한이 전자 메일을 받는 이유는 무엇 인가요? 무엇을 해야 하나요?

**A:** 아무 일도 일어나지 않습니다. 새 엔드포인트으로 마이그레이션 되었으며 곧 완료 될 예정 이므로 변경이 필요 하지 않습니다. 아무것도 중단 되지 않으며, 한 번의 누락 된 엔드포인트은 Google에서 경고 메시지를 발생 시켰습니다.

**Q:** 기존 사용자를 중단 하지 않고 새로운 FCM Sdk 및 라이브러리로 전환 하려면 어떻게 해야 하나요?

A: 언제 든 지 업그레이드 합니다. Google은 아직 기존 GCM Sdk 및 라이브러리의 사용 중단을 발표 하지 않았습니다. 기존 사용자에 게 푸시 알림이 중단 되지 않도록 하려면 기존 GCM 프로젝트와 연결 하는 새 Firebase 프로젝트를 만들어야 합니다. 이렇게 하면 새 Firebase 비밀이 GCM Sdk 및 라이브러리를 사용 하 여 앱의 이전 버전을 실행 하는 사용자와 FCM Sdk 및 라이브러리를 사용 하는 앱의 새로운 사용자에 게 적용 됩니다.

**Q:** 내 알림에 대해 새로운 FCM 기능 및 스키마를 언제 사용할 수 있나요?

**A:** API 및 Sdk에 대 한 업데이트를 게시 한 후에는 향후 몇 개월 내에 사용자에 게 필요한 항목이 있을 것입니다.
