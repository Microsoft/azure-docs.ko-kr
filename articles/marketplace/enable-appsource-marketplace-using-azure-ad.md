---
title: Azure Active Directory를 사용하여 Microsoft AppSource와 Azure Marketplace 목록을 사용하도록 설정 | Azure
description: 앱 및 서비스 게시자의 Azure Marketplace와 AppSource에서 Azure Active Directory를 사용하여 열거 형식을 사용하도록 설정합니다.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: 247a45a38d732ace0455c6ca2ebbd5c44c384004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734255"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Azure Active Directory를 사용하여 AppSource와 Marketplace 목록을 사용하도록 설정

 Azure AD(Azure Active Directory)는 Microsoft 계정으로 인증할 수 있도록 하는 클라우드 ID 서비스입니다. Azure AD는 산업 표준 프레임워크를 사용합니다. [Azure Active Directory에 대해 자세히 알아보세요](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Azure AD 혜택

Microsoft AppSource 및 Azure Marketplace 고객은 제품 내 환경을 사용하여 목록 카탈로그를 검색합니다. 이 작업을 수행하려면 고객이 제품에 로그인해야 합니다. Azure AD 통합은 다음과 같은 이점을 제공합니다.

- 더 빠른 참여 및 최적화된 사용자 환경
- 수백만 명의 엔터프라이즈 사용자를 위한 SSO(Single Sign-On)
- 여러 파트너가 게시한 다양한 애플리케이션에 걸쳐 일관된 로그인 환경
- 모바일 및 클라우드 앱의 확장성 있는 플랫폼 간 인증

## <a name="offers-that-require-azure-ad"></a>Azure AD가 필요한 제공 사항

AppSource 및 Azure Marketplace에 대한 다양한 [목록 옵션 및 제품 유형](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)에는 Azure AD 구현을 위한 다양한 요구 사항이 있습니다. 자세한 내용은 다음 표를 참조하세요.

| **제품 유형**    | **Azure AD SSO 필수 여부**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | 연락처 | 평가판 | 시험 사용 | 거래 |
| Virtual Machine | N/A | 아닙니다. | 아니요 | 아닙니다. |
| Azure 앱(솔루션 템플릿)  | N/A | 해당 사항 없음 | 해당 사항 없음 | N/A |
| 관리되는 앱  | N/A | 해당 사항 없음 | N/A | 아닙니다. |
| SaaS  | 아닙니다. | 예 | 예 | 예 |
| 컨테이너  | N/A | 해당 사항 없음 | N/A | 아닙니다. |
| 컨설팅 서비스  | 아닙니다. | N/A | 해당 사항 없음 | N/A |

SaaS 기술 요구 사항에 대한 자세한 내용은 [SaaS 애플리케이션 제품 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)를 참조하세요.

## <a name="azure-ad-integration"></a>Azure AD 통합

- Azure AD를 목록에 통합하여 Single Sign-On을 사용하도록 설정하는 방법에 대한 자세한 내용은 [개발자용 Azure Active Directory]( https://aka.ms/aaddev)를 참조하세요.
- Azure AD Single Sign-On에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="enable-a-trial-listing"></a>평가판 목록 사용

고객 설정을 자동화하면 변환 가능성을 높일 수 있습니다. 고객이 평가판 목록을 선택하고 평가판 환경으로 경로 재지정되면, 추가 로그인 단계를 수행할 필요가 없도록 직접 고객을 설정할 수 있습니다.

인증 중에 Azure AD는 앱 또는 제품에 토큰을 보냅니다. 토큰에서 제공하는 사용자 정보를 통해 앱 또는 제품에서 사용자 계정을 생성할 수 있습니다. 자세한 내용은 [샘플 토큰](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)을 참조하세요.

Azure AD를 사용하여 앱 또는 평가판에 대한 원클릭 인증이 가능해지면 다음을 수행할 수 있습니다.

- 사용자 환경을 Marketplace에서 평가판으로 간단히 전환합니다.
- 사용자가 Marketplace에서 도메인 또는 체험 환경으로 리디렉션되더라도 제품 내 환경을 사용한다는 느낌을 유지합니다.
- 추가 로그인 단계가 없으므로 리디렉션 중에 중단될 가능성이 줄어듭니다.
- 대규모 Azure AD 사용자에 대한 배포 장애물이 줄어듭니다.

## <a name="verify-azure-ad-integration"></a>Azure AD 통합 확인

### <a name="multitenant-solutions"></a>다중 테넌트 솔루션

Azure AD를 사용하여 다음 작업을 지원합니다.

- Marketplace 상점 중 하나에 앱을 등록합니다. 자세한 내용은 [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) 또는 [AppSource 인증](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)을 참조하세요.
- Azure AD의 다중 테넌트 지원 기능으로 원클릭 체험 환경을 사용할 수 있습니다.

Azure AD 페더레이션 Single Sign-On을 처음 사용하는 경우 다음 단계를 완료하세요.

1. Marketplace에서 앱을 등록합니다.
1. [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) 또는 [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)를 사용하여 Azure AD를 통해 SSO를 개발합니다.
1. Azure AD의 다중 테넌트 지원 기능으로 원클릭 체험 환경을 제공할 수 있습니다.

### <a name="single-tenant-solutions"></a>단일 테넌트 솔루션

Azure AD를 사용하여 다음 작업 중 하나를 지원합니다.

- [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)를 사용하여 디렉터리에 게스트 사용자를 추가합니다.
- **연락처** 게시 옵션을 사용하여 고객을 위한 평가판을 수동으로 설정합니다.
- 고객별로 시험 사용을 개발합니다.
- SSO를 사용하는 다중 테넌트 샘플 데모 앱을 빌드합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Marketplace에 등록](https://azuremarketplace.microsoft.com/sell)했는지 확인합니다.
- 제품을 만들거나 완성할 수 있도록 [Cloud 파트너 포털에 로그인](https://cloudpartner.azure.com/)합니다.
