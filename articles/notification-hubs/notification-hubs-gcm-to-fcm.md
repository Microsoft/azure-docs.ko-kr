---
title: Azure Notification Hubs 및 Google Firebase Cloud Messaging (FCM) 마이그레이션
description: Azure Notification Hubs FCM 마이그레이션 Google GCM을 해결 하는 방법을 설명 합니다.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458300"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure Notification Hubs 및 Google Firebase Cloud Messaging (FCM) 마이그레이션

## <a name="current-state"></a>현재 상태

때 Google에서 메시징 GCM (Google Cloud)에 FCM Firebase Cloud Messaging (), 푸시 서비스 변경 내용을 수용 하기 위해 Android 장치에 보냈습니다 알림 조정 해야 하는 우리와 같은 해당 마이그레이션을 발표 했습니다.

서비스 백 엔드를 업데이트 하 고 API 및 Sdk 필요에 따라 업데이트를 게시 합니다. 이 구현 고객 영향을 최소화 하기 위해 기존 GCM 알림 스키마를 사용 하 여 호환성을 유지 하기로 결정을 했습니다. 즉, FCM 레거시 모드로 FCM을 사용 하 여 Android 장치로 알림을 현재 보냅니다. 궁극적으로 페이로드 형식을 확인 하 고 새로운 기능을 포함 하 여 FCM에 대 한 true 지원을 추가 하려고 합니다. 장기적인 변경 이며 현재 마이그레이션이 기존 응용 프로그램 및 Sdk 호환성을 유지 관리에 포커스가 있습니다. (SDK)와 함께 앱에서 GCM 또는 FCM Sdk를 사용할 수 있습니다 및 알림이 올바르게 전송 되 고 있는지 확인 합니다.

최근에 일부 고객은 Google 경고 알림에 대 한 GCM 끝점을 사용 하 여 앱에 대 한 전자 메일을 받았습니다. 이 단지 경고 아무 것도 손상 – Android 앱의 알림 처리를 위해 Google에 보낸는 여전히 및 Google에서 계속 처리 합니다. 서비스 구성에서 GCM 끝점을 명시적으로 지정 하는 일부 고객이 사용 되지 않는 끝점을 사용도 했습니다. 이미이 간격 확인 하 고 Google 전자 메일을 보낼 때 문제를 해결 하기 위해 작업.

사용 되지 않는 끝점 교체 하 고 수정 배포 됩니다.

## <a name="going-forward"></a>앞으로

Google의 FCM FAQ 라는 작업을 수행할 필요가 없습니다. 에 [FCM FAQ](https://developers.google.com/cloud-messaging/faq), Google 있다고 "클라이언트 Sdk 및 GCM 토큰 계속 무기한으로 작동 합니다. 그러나 없습니다 수 FCM로 마이그레이션하지 않으면 Android 앱에서 Google Play Services의 최신 버전을 대상으로 지정할 수 있습니다. "

앱이 GCM 라이브러리를 사용 하는 경우 계속 해 서 앱의 FCM 라이브러리를 업그레이드 하려면 Google의 지침을 따릅니다. SDK와도 호환 이므로 (으로 여러분이 SDK 버전을 사용 하 여 최신 상태로) 우리쪽에서 앱에 아무 것도 업데이트할 필요가 없습니다.

## <a name="questions-and-answers"></a>질문 및 답변

다음은 몇 가지 일반적인 질문과 대답을 고객 으로부터 들은 것입니다.

**Q:** 마감 날짜에서 호환 되도록 하기 위해 하나요 (Google의 현재 구분 날짜 5 월 29 일 이며 변경 될 수 있습니다)?

**A:** 아무 일도 일어나지 않습니다. 우리는 기존 GCM 알림 스키마를 사용 하 여 호환성을 유지 합니다. GCM 키로 모든 GCM Sdk 및 응용 프로그램에서 사용 되는 라이브러리 정상적으로 작동 계속 됩니다.

경우에 새로운 기능을 활용 하려면 FCM Sdk 및 라이브러리를 업그레이드 하려면, GCM 키 계속 작동 합니다. 원하는 해도 추가할 Firebase 기존 GCM 프로젝트에 새 Firebase 프로젝트를 만들 때 확인 하는 경우는 FCM 키를 사용 하 여 전환할 수 있습니다. 이 여전히 GCM Sdk 및 라이브러리를 사용 하는 앱의 이전 버전을 실행 하는 고객을 사용 하 여 이전 버전과 호환성을 보증 합니다.

새 FCM 프로젝트를 만들고 새 FCM 암호를 사용 하 여 Notification Hubs를 업데이트 하면 기존 GCM 프로젝트에 연결 하지 없어집니다 기능 푸시 알림을 현재 응용 프로그램 설치를 새 FCM 키에 이전 GCM에 연결 되지 않고 있으므로 프로젝트입니다.

**Q:** 이 전자 메일을 사용 하 고 이전 GCM 끝점에 대 한 이유는 무엇입니까? 수행 합니까?

**A:** 아무 일도 일어나지 않습니다. 새 끝점을 마이그레이션 하 고는 완료 될 예정 이므로 변하지가 필요 합니다. 아무 것도 손상, 하나의 누락 된 끝점 Google에서 경고 메시지를 간단히 발생 합니다.

**Q:** 기존 사용자를 중단 하지 않고 새 FCM Sdk 및 라이브러리를 전환 하는 방법을 해야 수 있습니까?

A: 언제 든 지 업그레이드 합니다. Google 기존 GCM Sdk 및 라이브러리의 모든 사용 중단 아직 발표 되지 했습니다. 기존 사용자에 게 푸시 알림을 중단 하지 않습니다, 경우 했는지 확인 하려면 기존 GCM 프로젝트와 연결 하는 새 Firebase 프로젝트를 만들어야 합니다. 이렇게 하면 새 Firebase 비밀 FCM Sdk 및 라이브러리를 사용 하 여 앱의 새 사용자 뿐만 아니라 GCM Sdk 및 라이브러리를 사용 하 여 앱의 이전 버전을 실행 하는 사용자에 대 한 작동 합니다.

**Q:** 내 알림에 대 한 새로운 FCM 기능과 스키마 사용할 수는 경우

**A:** API 및 Sdk를 기대해 – 업데이트가 게시 되 면 몇 달 안에 게 될 예정입니다.
