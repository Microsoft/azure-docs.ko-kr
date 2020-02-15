---
title: 프로덕션을 위한 모바일 앱 호출 웹 Api 준비 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법을 알아봅니다. 프로덕션을 위해 앱을 준비 합니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1ea19b8b76f4eb4a2c984f0e39eb0fd373c8b83c
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132380"
---
# <a name="prepare-mobile-apps-for-production"></a>프로덕션을 위해 모바일 앱 준비

이 문서에서는 모바일 앱을 프로덕션으로 이동 하기 전에 모바일 앱의 품질 및 안정성을 개선 하는 방법에 대 한 세부 정보를 제공 합니다.

## <a name="handle-errors"></a>오류 처리

프로덕션을 위해 모바일 앱을 준비할 때 몇 가지 오류 조건이 발생할 수 있습니다. 처리할 주요 사례는 자동 오류 이며 상호 작용에 대 한 대체입니다. 네트워크 상황, 서비스 중단, 관리자 동의에 대 한 요구 사항, 기타 시나리오 관련 사례 등을 고려해 야 합니다.

각 MSAL (Microsoft 인증 라이브러리) 형식에 대해 오류 조건을 처리 하는 방법을 설명 하는 샘플 코드와 wiki 콘텐츠를 찾을 수 있습니다.

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>문제 완화 및 조사

앱에서 문제를 더 잘 진단 하려면 데이터를 수집 합니다. 수집할 수 있는 데이터의 종류에 대 한 자세한 내용은 [MSAL 응용 프로그램의 로깅](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)을 참조 하세요.

다음은 데이터 수집에 대 한 몇 가지 제안 사항입니다.

- 사용자는 문제가 발생할 때 도움을 요청할 수 있습니다. 로그를 캡처하고 일시적으로 저장 하는 것이 가장 좋습니다. 사용자가 로그를 업로드할 수 있는 위치를 제공 합니다. MSAL은 인증에 대 한 자세한 정보를 캡처하기 위해 로깅 확장을 제공 합니다.

- 원격 분석을 사용할 수 있는 경우 MSAL을 통해 사용 하도록 설정 하 여 사용자가 앱에 로그인 하는 방법에 대 한 데이터를 수집 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

추가 샘플을 보려면 [데스크톱 및 모바일 공용 클라이언트 앱](sample-v2-code.md#desktop-and-mobile-public-client-apps)을 참조 하세요.
