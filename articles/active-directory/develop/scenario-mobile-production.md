---
title: 프로덕션을 위한 모바일 앱 호출 웹 Api 준비 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다. 프로덕션을 위해 앱을 준비 합니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675960"
---
# <a name="prepare-mobile-apps-for-production"></a>프로덕션을 위해 모바일 앱 준비

이 문서에서는 모바일 앱을 프로덕션으로 이동 하기 전에 모바일 앱의 품질 및 안정성을 개선 하는 방법에 대 한 세부 정보를 제공 합니다.

## <a name="handle-errors"></a>오류 처리

프로덕션을 위해 모바일 앱을 준비할 때 몇 가지 오류 조건이 발생할 수 있습니다. 처리할 주요 사례는 자동 오류 이며 상호 작용에 대 한 대체입니다. 네트워크 상황, 서비스 중단, 관리자 동의에 대 한 요구 사항, 기타 시나리오 관련 사례 등을 고려해 야 합니다.

각 MSAL (Microsoft 인증 라이브러리) 형식에 대해 오류 조건을 처리 하는 방법을 설명 하는 샘플 코드와 wiki 콘텐츠를 찾을 수 있습니다.

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

추가 샘플을 보려면 [데스크톱 및 모바일 공용 클라이언트 앱](sample-v2-code.md#desktop-and-mobile-public-client-apps)을 참조 하세요.