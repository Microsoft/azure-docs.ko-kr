---
title: Microsoft 상업용 Marketplace 제품과 Azure Active Directory 통합
description: Azure Active Directory를 사용하여 Microsoft AppSource 및 Azure Marketplace 제품을 인증합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: a6e304e5ffeab8f0a44cbdfe1566465f2b9bf34a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607413"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>상업용 Marketplace 목록과 Azure Active Directory 통합

 이 문서에서는 Azure AD(Azure Active Directory)를 사용하여 상업용 Marketplace 목록 또는 제품을 통합하기 위한 요구 사항을 설명합니다. Azure AD는 업계 표준 프레임워크를 사용하여 Microsoft 계정으로 인증할 수 있도록 하는 클라우드 ID 서비스입니다. [Azure Active Directory에 대해 자세히 알아보세요](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Azure AD 혜택

Microsoft AppSource 및 Azure Marketplace 고객은 제품 내 환경을 사용 하 여 온라인 상점 목록 카탈로그를 검색 합니다. 이 작업을 수행하려면 고객이 제품에 로그인해야 합니다. Azure AD 통합은 다음과 같은 이점을 제공합니다.

- 더 빠른 참여 및 최적화된 사용자 환경
- 수백만 명의 엔터프라이즈 사용자를 위한 SSO(Single Sign-On)
- 여러 파트너가 게시한 다양한 애플리케이션에 걸쳐 일관된 로그인 환경
- 모바일 및 클라우드 앱의 확장성 있는 플랫폼 간 인증

## <a name="offers-that-require-azure-ad"></a>Azure AD가 필요한 제공 사항

다양한 상업용 Marketplace [목록 옵션 및 제품 유형](determine-your-listing-type.md)에는 Azure AD 구현을 위한 다양한 요구 사항이 있습니다. 자세한 내용은 다음 표를 참조하세요.

| 제품 유형    | 연락 하기 위해 Azure AD SSO가 필요 한가요?  | 평가판에 Azure AD SSO가 필요 한가요? | 테스트 드라이브에는 Azure AD SSO가 필요 한가요?  | Transact-sql에 필요한 Azure AD SSO |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Virtual Machine | 해당 없음 | 예 | 예 | 예 |
| Azure 앱(솔루션 템플릿)  | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 관리되는 앱  | 해당 없음 | 해당 없음 | 해당 없음 | 예 |
| SaaS  | 예 | 예 | 예 | 예 |
| 컨테이너  | 해당 없음 | 해당 없음 | 해당 없음 | 예 |
| 컨설팅 서비스  | 예 | 해당 없음 | 해당 없음 | 해당 없음 |

SaaS 기술 요구 사항에 대 한 자세한 내용은 [상용 marketplace의 AZURE AD 및 불가능 SaaS 제품](./azure-ad-saas.md)을 참조 하세요.

## <a name="azure-ad-integration"></a>Azure AD 통합

- 불가능 SaaS (software as a service) 제품에 Azure AD를 통합 하는 방법에 대 한 자세한 내용은 [상용 marketplace의 AZURE ad 및 불가능 SaaS 제품](./azure-ad-saas.md)을 참조 하세요.
- Azure AD를 목록에 통합하여 Single Sign-On을 사용하도록 설정하는 방법에 대한 자세한 내용은 [개발자용 Azure Active Directory](../active-directory/develop/index.yml)를 참조하세요.
- Azure AD Single Sign-On에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../active-directory/manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="enable-a-trial-listing"></a>평가판 목록 사용

고객 설정을 자동화하면 변환 가능성을 높일 수 있습니다. 고객이 평가판 목록을 선택하고 평가판 환경으로 경로 재지정되면, 추가 로그인 단계를 수행할 필요가 없도록 직접 고객을 설정할 수 있습니다.

인증 중에 Azure AD는 앱 또는 제품에 토큰을 보냅니다. 토큰에서 제공하는 사용자 정보를 통해 앱 또는 제품에서 사용자 계정을 생성할 수 있습니다. 자세한 내용은 [샘플 토큰](../active-directory/develop/id-tokens.md)을 참조하세요.

Azure AD를 사용하여 앱 또는 평가판에 대한 원클릭 인증이 가능해지면 다음을 수행할 수 있습니다.

- 상용 marketplace에서 평가판 목록으로 사용자 환경을 간소화 합니다.
- 사용자가 상업적 marketplace에서 도메인 또는 체험 환경으로 리디렉션되는 경우에도 제품 내 환경의 느낌을 유지 합니다.
- 추가 로그인 단계가 없으므로 리디렉션 중에 중단될 가능성이 줄어듭니다.
- 대규모 Azure AD 사용자에 대한 배포 장애물이 줄어듭니다.

## <a name="verify-azure-ad-integration"></a>Azure AD 통합 확인

### <a name="multitenant-solutions"></a>다중 테넌트 솔루션

Azure AD를 사용하여 다음 작업을 지원합니다.

- 상용 marketplace 온라인 상점 중 하나에 앱을 등록 합니다. 자세한 내용은 [앱 등록](../active-directory/develop/quickstart-register-app.md) 또는 [AppSource 인증](../active-directory/azuread-dev/howto-get-appsource-certified.md)을 참조하세요.
- Azure AD의 다중 테넌트 지원 기능으로 원클릭 체험 환경을 사용할 수 있습니다.

Azure AD 페더레이션 Single Sign-On을 처음 사용하는 경우 다음 단계를 완료하세요.

1. 상용 marketplace에 앱을 등록 합니다.
1. [OAuth 2.0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) 또는 [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md)를 사용하여 Azure AD를 통해 SSO를 개발합니다.
1. Azure AD의 다중 테넌트 지원 기능으로 원클릭 체험 환경을 제공할 수 있습니다.

### <a name="single-tenant-solutions"></a>단일 테넌트 솔루션

Azure AD를 사용하여 다음 작업 중 하나를 지원합니다.

- [Azure AD B2B](../active-directory/b2b/what-is-b2b.md)를 사용하여 디렉터리에 게스트 사용자를 추가합니다.
- **연락처** 게시 옵션을 사용하여 고객을 위한 평가판을 수동으로 설정합니다.
- 고객별로 시험 사용을 개발합니다.
- SSO를 사용하는 다중 테넌트 샘플 데모 앱을 빌드합니다.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않았다면 

- 상업적 marketplace에 [대해 알아보세요](https://azuremarketplace.microsoft.com/sell) .

파트너 센터에 등록하거나 새 제품을 만들거나 기존 제품에 대해 작업하려면

- 제품을 만들거나 완성할 수 있도록 [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)합니다.
