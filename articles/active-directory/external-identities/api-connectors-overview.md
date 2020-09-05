---
title: 셀프 서비스 등록 흐름의 API 커넥터 정보-Azure AD
description: Azure AD (Azure Active Directory) API 커넥터를 사용 하 여 web Api를 사용 하 여 셀프 서비스 등록 사용자 흐름을 사용자 지정 하 고 확장할 수 있습니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb6e85b6d81de3d4b88ba315ddd35bd5b37ae7a
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165212"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>API 커넥터를 사용 하 여 셀프 서비스 등록 사용자 지정 및 확장 

## <a name="overview"></a>개요 
개발자 또는 IT 관리자는 API 커넥터를 사용 하 여 web Api를 활용 하 여 [셀프 서비스 등록 사용자 흐름](self-service-sign-up-overview.md) 을 외부 시스템과 통합할 수 있습니다. 예를 들어 API 커넥터를 사용 하 여 다음을 수행할 수 있습니다.

- [**사용자 지정 승인 워크플로와 통합**](self-service-sign-up-add-approvals.md)합니다. 계정 만들기를 관리 하기 위해 사용자 지정 승인 시스템에 연결 합니다.
- [**Id 확인을 수행**](code-samples-self-service-sign-up.md#identity-verification)합니다. Id 확인 서비스를 사용 하 여 계정 생성 결정에 보안 수준을 추가 합니다.
- **사용자 입력 데이터 유효성 검사**. 형식이 잘못 되었거나 잘못 된 사용자 데이터에 대 한 유효성을 검사 합니다. 예를 들어 외부 데이터 저장소의 기존 데이터 또는 허용 되는 값 목록에 대해 사용자가 제공한 데이터의 유효성을 검사할 수 있습니다. 잘못 된 경우 사용자에 게 유효한 데이터를 제공 하거나 사용자가 등록 흐름을 계속할 수 없도록 차단할 수 있습니다.
- **사용자 특성을 덮어씁니다**. 사용자 로부터 수집 된 특성에 값을 다시 포맷 하거나 할당 합니다. 예를 들어 사용자가 이름을 모두 소문자 또는 대문자로 입력한 경우 이름의 첫 번째 문자만을 대문자로 시작하도록 서식을 지정할 수 있습니다. 
<!-- - **Enrich user data**. Integrate with your external cloud systems that store user information to integrate them with the sign-up flow. For example, your API can receive the user's email address, query a CRM system, and return the user's loyalty number. Returned claims can be used to pre-fill form fields or return additional data in the application token.  -->
- **사용자 지정 비즈니스 논리 실행**. 클라우드 시스템에서 다운스트림 이벤트를 트리거하여 푸시 알림을 보내고, 회사 데이터베이스를 업데이트 하 고, 사용 권한을 관리 하 고, 데이터베이스를 감사 하 고, 다른 사용자 지정 작업을 수행할 수 있습니다.

API 커넥터는 HTTP 끝점 URL 및 인증을 정의 하 여 API 끝점을 호출 하는 데 필요한 정보를 Azure Active Directory 제공 합니다. API 커넥터를 구성한 후에는 사용자 흐름에서 특정 단계에 대해이 커넥터를 사용 하도록 설정할 수 있습니다. 사용자가 등록 흐름에서 해당 단계에 도달 하면 API 커넥터가 호출 되어 API에 대 한 HTTP POST 요청으로 구체화 되어 사용자 정보 ("클레임")를 JSON 본문의 키-값 쌍으로 보냅니다. API 응답은 사용자 흐름의 실행에 영향을 줄 수 있습니다. 예를 들어 API 응답은 사용자가 등록 하지 못하도록 차단할 수 있으며 사용자에 게 정보를 다시 입력 하도록 요청 하거나 사용자 특성을 덮어쓰고 추가 합니다.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용 하도록 설정할 수 있는 위치

사용자 흐름에는 API 커넥터를 사용 하도록 설정할 수 있는 두 가지 위치가 있습니다.

- Id 공급자를 사용 하 여 로그인 한 후
- 사용자를 만들기 전에

> [!IMPORTANT]
> 이러한 두 경우 모두, 로그인이 아닌 사용자 **등록**중에 API 커넥터가 호출 됩니다.

### <a name="after-signing-in-with-an-identity-provider"></a>Id 공급자를 사용 하 여 로그인 한 후

사용자가 id 공급자 (Google, Facebook, Azure AD)를 사용 하 여 인증 하는 즉시 등록 프로세스의이 단계에서 API 커넥터가 호출 됩니다. 이 단계는 사용자 특성을 수집 하기 위해 사용자에 게 표시 되는 형식인 ***특성 컬렉션 페이지***앞에 나옵니다. 다음은이 단계에서 사용 하도록 설정할 수 있는 API 커넥터 시나리오의 예입니다.

- 사용자가 제공한 전자 메일 또는 페더레이션 id를 사용 하 여 기존 시스템에서 클레임을 조회 합니다. 기존 시스템에서 이러한 클레임을 반환 하 고 특성 컬렉션 페이지를 미리 채운 다음 토큰에서 반환할 수 있도록 설정 합니다.
- 사용자가 허용 또는 거부 목록에 포함 되어 있는지 확인 하 고 등록 흐름을 계속 진행할 수 있는지 여부를 제어 합니다.

### <a name="before-creating-the-user"></a>사용자를 만들기 전에

등록 프로세스의이 단계에서 API 커넥터는 포함 된 경우 특성 컬렉션 페이지 다음에 호출 됩니다. 이 단계는 항상 Azure AD에서 사용자 계정을 만들기 전에 호출 됩니다. 다음은 등록 중에이 시점에서 사용 하도록 설정할 수 있는 시나리오의 예입니다.

- 사용자 입력 데이터의 유효성을 검사 하 고 데이터를 다시 전송 하도록 사용자에 게 요청 합니다.
- 사용자가 입력 한 데이터를 기반으로 사용자 등록을 차단 합니다.
- Id 확인을 수행 합니다.
- 외부 시스템에서 사용자에 대 한 기존 데이터를 쿼리하여 응용 프로그램 토큰에서 반환 하거나 Azure AD에 저장 합니다.

<!-- > [!IMPORTANT]
> If an invalid response is returned or another error occurs (for example, a network error), the user will be redirected to the app with the error re -->

## <a name="next-steps"></a>다음 단계
- [사용자 흐름에 API 커넥터를 추가](self-service-sign-up-add-api-connector.md) 하는 방법 알아보기
- [셀프 서비스 등록에 사용자 지정 승인 시스템을 추가](self-service-sign-up-add-approvals.md) 하는 방법 알아보기