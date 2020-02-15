---
title: 개발자 용 Azure Active Directory (v1.0) 개요
description: 이 문서에서는 Azure Active Directory v1.0 엔드포인트 및 플랫폼을 사용하여 Microsoft 회사 및 학교 계정에 로그인하는 방법의 개요를 제공합니다.
services: active-directory
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: azuread-dev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 638c265fda3c8b331415d54047180b3c0ee2174a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197547"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>개발자 용 Azure Active Directory (v1.0) 개요

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure AD(Azure Active Directory)는 개발자가 Microsoft 회사 또는 학교 계정으로 사용자를 안전하게 로그인하는 앱을 빌드할 수 있는 클라우드 ID 서비스입니다. Azure AD는 단일 테넌트 및 기간 업무 앱을 모두 빌드하는 개발자와 다중 테넌트 앱을 개발하는 개발자를 지원합니다. 기본 로그인 외에도, Azure AD는 앱에서 [Microsoft Graph](https://docs.microsoft.com/graph/overview) 같은 Microsoft API와 Azure AD 플랫폼에서 빌드되는 사용자 지정 API를 호출하는 것을 허용합니다. 이 설명서에서는 OAuth2.0 및 OpenID Connect 같은 산업 표준 프로토콜을 사용하여 애플리케이션에 Azure AD 지원을 추가하는 방법을 보여줍니다.

> [!NOTE]
> 이 페이지의 콘텐츠는 대부분 Microsoft 회사 또는 학교 계정만 지원하는 v1.0 엔드포인트 및 플랫폼을 중심으로 합니다. 소비자 또는 개인 Microsoft 계정에 로그인하려면 [v2.0 엔드포인트 및 플랫폼](../develop/v2-overview.md)에 대한 정보를 참조하세요. v2.0 엔드포인트는 모든 Microsoft ID를 로그인하려는 앱에 대한 통합 개발자 환경을 제공합니다.

| | |
| --- | --- |
|[인증 기본 사항](v1-authentication-scenarios.md) | Azure AD를 사용하는 인증에 대한 소개입니다. |
|[애플리케이션 유형](app-types.md) | Azure AD에서 지원하는 인증 시나리오에 대한 개요입니다. |
| | |

## <a name="get-started"></a>시작하기

v1.0 빠른 시작 및 자습서에서는 ADAL(Azure AD 인증 라이브러리) SDK를 사용하여 선호하는 플랫폼에서 앱을 빌드하는 과정을 안내합니다. 시작하려면 **Microsoft ID 플랫폼(개발자용 Azure Active Directory)** 에서 **v1.0 빠른 시작** 및 [v1.0 자습서](index.yml)를 참조하세요.

## <a name="how-to-guides"></a>방법 가이드

Azure AD의 가장 일반적인 작업에 대한 자세한 내용 및 연습은 **v1.0 방법 가이드**를 참조하세요.

## <a name="reference-topics"></a>참조 항목

다음 문서는 API, 프로토콜 메시지 및 Azure AD에서 사용되는 용어에 대한 자세한 정보를 제공합니다.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [인증 라이브러리(ADAL)](active-directory-authentication-libraries.md)   | Azure AD에서 제공하는 라이브러리 및 SDK에 대한 개요입니다. |
| [코드 샘플](sample-v1-code.md)                                  | 모든 Azure AD 코드 샘플의 목록입니다. |
| [용어 설명](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)                                      | 이 설명서에 사용된 용어 및 단어에 대한 정의입니다. |
|  |  |

## <a name="videos"></a>동영상

새 Microsoft id 플랫폼으로 마이그레이션하는 데 도움이 필요한 경우 [개발자 플랫폼 비디오 Azure Active Directory](videos.md) 를 참조 하세요.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
