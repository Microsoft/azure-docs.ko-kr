---
title: Azure AD .NET 프로토콜 개요 | Microsoft Docs
description: Azure AD를 사용하여 테넌트에서 웹 애플리케이션 및 Web API에 대한 액세스 권한을 부여하기 위해 HTTP 메시지를 사용하는 방법입니다.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523336"
---
## <a name="register-your-application-with-your-ad-tenant"></a>AD 테넌트에 애플리케이션 등록
먼저 Azure Active Directory (Azure AD) 테넌트에 응용 프로그램을 등록 합니다. 그러면 애플리케이션에 대한 애플리케이션 ID가 제공되며 토큰을 수신하는 데 사용할 수 있습니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
   
1. 페이지의 오른쪽 위 모서리에서 계정을 선택 하 고 **디렉터리 탐색 스위치** 를 선택한 다음 적절 한 테넌트를 선택 하 여 Azure AD 테넌트를 선택 합니다. 
   - 계정에 Azure AD 테넌트가 하나만 있거나 적절 한 Azure AD 테넌트를 이미 선택한 경우이 단계를 건너뜁니다.
   
1. Azure Portal에서 **Azure Active Directory**를 검색 하 고 선택 합니다.
   
1. **Azure Active Directory** 왼쪽 메뉴에서 **앱 등록**을 선택 하 고 **새 등록**을 선택 합니다.
   
1. 프롬프트에 따라 새 애플리케이션을 만듭니다. 이 자습서에서는 웹 응용 프로그램이 든 공용 클라이언트 (모바일 & 데스크톱) 응용 프로그램 인지는 중요 하지 않지만, 웹 응용 프로그램 또는 공용 클라이언트 응용 프로그램에 대한 특정 예제를 원하는 경우 빠른 [시작을 확인 하세요.](../articles/active-directory/develop/v1-overview.md)
   
   - **이름**은 애플리케이션 이름이고 최종 사용자에게 애플리케이션을 설명합니다.
   - **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
   - **리디렉션 URI**를 제공 합니다. 웹 응용 프로그램의 경우 사용자가 로그인 할 수 있는 앱의 기본 URL입니다.  예: `http://localhost:12345` 공용 클라이언트 (모바일 & 데스크톱)의 경우 Azure AD에서 토큰 응답을 반환 하는 데 사용 합니다. 애플리케이션에 특정한 값을 입력합니다.  예: `http://MyFirstAADApp`
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. 등록이 완료 되 면 Azure AD는 응용 프로그램에 고유한 클라이언트 식별자 ( **응용 프로그램 ID**)를 할당 합니다. 이 값은 다음 섹션에서 필요하므로 애플리케이션 페이지에서 이 값을 복사해 둡니다.
   
1. Azure Portal에서 응용 프로그램을 찾으려면 **앱 등록**를 선택한 다음 **모든 응용 프로그램 보기**를 선택 합니다.
