---
title: Azure Marketplace SaaS 애플리케이션 기술 게시 가이드
description: Azure Marketplace에 SaaS 애플리케이션을 게시하는 단계별 가이드 및 게시 검사 목록
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: keithcharlie
manager: nunoc
editor: keithcharlie
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: keithcharlie
ms.openlocfilehash: b653b0276cedea1e3b45adf7a9dc390b24f0d03f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765400"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS 애플리케이션 제품 게시 가이드

SaaS 애플리케이션은 “연락처”, “지금 사용해 보기” 및 “지금 가져오기” 작업에 대한 세 개의 다른 호출을 사용하여 Marketplace에 게시할 수 있습니다. 이 가이드에서는 각각에 대한 요구 사항을 비롯한 세 가지 옵션을 설명합니다. 

## <a name="offer-overview"></a>제품 개요  

SaaS 애플리케이션은 모두 Azure 상점에서 사용할 수 있습니다. 다음 표에서는 현재 사용할 수 있는 옵션을 설명합니다.

| 상점 옵션 | 나열 | 평가판/거래 |  
| --- | --- | --- |  
| AppSource | 예(연락처) | 예(PowerBI/Dynamics) |
| Azure 마켓플레이스 | 아닙니다. | 예(SaaS 앱) |   

**나열:**  나열 게시 옵션은 연락처 제품 형식으로 이루어지고 평가판 또는 트랜잭션 수준의 참여가 불가능할 때 사용됩니다. 이 방식은 마켓 내 솔루션의 게시자가 비즈니스 수익을 창출하는 거래를 할 수 있는 잠재 고객을 바로 받을 수 있다는 장점이 있습니다.  
**평가판/트랜잭션:**  고객은 솔루션에 대한 평가판을 직접 구입하거나 요청할 수 있습니다. 평가판 환경을 제공하면 고객에게 제공되는 참여 수준이 높아지고 고객이 솔루션을 구입하기 전에 탐색할 수 있습니다. 평가판 환경을 사용할 경우 상점에서 홍보할 기회를 갖고 고객 참여를 통해 보다 풍부하고 많은 잠재 고객을 창출할 수 있습니다. 체험의 경우 적어도 체험 기간 동안 무료 지원이 포함되어야 합니다.  

| SaaS 앱 제품 | 비즈니스 요구 사항 | 기술적인 요구 사항 |  
| --- | --- | --- |  
| **문의처** | 예 | 아닙니다. |  
| **PowerBI/Dynamics** | 예 | 예(Azure AD 통합) |  
| **SaaS 앱**| 예 | 예(Azure AD 통합) |     

## <a name="saas-list"></a>SaaS 목록

평가판이 없고 청구 기능이 없는 SaaS 목록의 작업에 대한 호출은 "연락처"입니다. 

SaaS 애플리케이션을 나열하도록 Azure Active Directory를 구성할 필요가 없습니다. 

|요구 사항  |세부 정보  |
|---------|---------|
|앱이 SaaS 제품입니다.  |   사용자의 솔루션은 SaaS 제품이며 다중 테넌트 SaaS 제품을 제공합니다.      |


## <a name="saas-trial"></a>SaaS 평가판

평가판, SaaS(Software-as-a-Service) 기반 평가판을 사용하여 솔루션을 제공합니다. 평가판은 제한된 사용 또는 제한된 기간 평가판 계정으로 표시될 수 있습니다. 


|요구 사항  |세부 정보  |
|---------|---------|
|앱이 SaaS 제품입니다.  |   사용자의 솔루션은 SaaS 제품이며 다중 테넌트 SaaS 제품을 제공합니다.      |
|앱이 AAD를 사용하도록 설정됩니다.     |   고객이 도메인으로 리디렉션될 수 있고 고객과 직접 트랜잭션합니다.       |


## <a name="saas-trial-technical-requirements"></a>SaaS 평가판 기술 요구 사항

SaaS 애플리케이션에 대한 기술 요구 사항은 간단합니다. 게시할 Azure AD(Azure Active Directory)와 게시자가 통합되기만 하면 됩니다. Azure AD와 애플리케이션의 통합은 문서화가 잘 되어있으며 이를 위해 Microsoft는 다수의 SDK와 리소스가 제공됩니다.  

시작하려면 다른 이니셔티브와 작업을 격리할 수 있도록 Azure Marketplace 게시 전용 구독을 마련하는 것이 좋습니다. 이 작업이 완료되면 이 구독에서 SaaS 애플리케이션을 배포하기 시작하여 개발 작업을 시작할 수 있습니다.  

최상의 Azure Active Directory 설명서, 샘플 및 지침은 다음 사이트에 위치합니다. 

* [Azure Active Directory 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Azure Active Directory와 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Azure Active Directory와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure 로드맵 - 보안 및 ID](https://azure.microsoft.com/roadmap/?category=security-identity)

비디오 자습서는 다음 정보를 검토합니다.

* [Vittorio Bertocci가 소개하는 Azure Active Directory 인증](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory ID 기술 브리핑 - 1/2부](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory ID 기술 브리핑 - 2/2부](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Microsoft Azure Active Directory로 앱 구축](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Active Directory에 중점을 둔 Microsoft Azure 비디오](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

무료 Azure Active Directory 교육은 다음에서 지원됩니다.  
* [IT 전문가용 Microsoft Azure 콘텐츠 시리즈: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

또한 Azure Active Directory에서는 서비스 업데이트를 확인하는 사이트를 제공합니다.   
* [Azure AD 서비스 업데이트](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Azure Active Directory를 사용하여 체험 지원  

Microsoft에서는 Azure AD를 사용하여 모든 Marketplace를 인증하므로, 인증된 사용자가 Marketplace에서 평가판 항목을 클릭하고 평가판 환경으로 리디렉션되면, 추가 로그인 단계를 요구하지 않으면서 사용자를 평가판 단계로 직접 프로비전할 수 있습니다. 인증 중에 Azure AD에서 앱이 수신하는 토큰에는 앱에서 사용자 계정을 만드는 데 사용할 수 있는 귀중한 사용자 정보가 포함되어 있으므로, 프로비전 환경을 자동화하고 변환 가능성을 높일 수 있습니다. 토큰에 대한 자세한 내용은 [샘플 토큰](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)을 참조하세요.

Azure AD를 사용하면 다음과 같이 앱 또는 평가판에 대한 원클릭 인증을 사용할 수 있습니다.  
* 고객이 Marketplace의 환경에서 평가판으로 간단히 전환할 수 있습니다.  
* 사용자가 Marketplace에서 도메인 또는 평가판 환경으로 리디렉션되더라도 '제품 내 환경'을 사용한다는 느낌을 유지할 수 있습니다.  
* 추가 로그인 단계가 없으므로 리디렉션 중에 중단될 가능성이 줄어듭니다.  
* 대규모 Azure AD 사용자에 대한 배포 장애물이 줄어듭니다.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Marketplace에 대한 Azure AD 통합 인증  

애플리케이션이 단일 테넌트인지 아니면 다중 테넌트인지, Azure AD 페더레이션된 SSO(Single Sign-On)을 처음 사용하는지 아니면 이미 지원하는지에 따라 몇 가지 다른 방법으로 Azure AD 통합을 인증할 수 있습니다.  

**다중 테넌트 애플리케이션의 경우:**  

이미 Azure AD를 지원하는 경우 다음을 수행합니다.
1.  Azure Portal에서 애플리케이션을 등록합니다.
2.  Azure AD의 다중 테넌트 지원 기능으로 원클릭 평가판 환경을 사용할 수 있습니다. [여기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)에서 자세한 내용을 찾을 수 있습니다.  

Azure AD 페더레이션 SSO를 처음 사용하는 경우 다음을 수행합니다. 
1.  Azure Portal에서 애플리케이션을 등록합니다.
2.  [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 또는 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)을 사용하여 Azure AD에서 SSO를 개발합니다.
3.  AAD의 다중 테넌트 지원 기능으로 '원클릭'만으로 평가판 환경을 사용할 수 있습니다. [여기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)에서 자세한 내용을 찾을 수 있습니다.  

**단일 테넌트 애플리케이션의 경우 다음 옵션 중 하나를 사용합니다.**  
* [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)를 사용하여 게스트 사용자로 디렉터리에 사용자 추가
* '연락처'를 사용하여 고객에게 평가판을 수동으로 프로비전
* 고객별로 '시험 사용' 개발
* SSO를 통해 다중 테넌트 샘플 데모 앱을 빌드합니다.

## <a name="saas-subscriptions"></a>SaaS 구독

SaaS 앱 제품 유형을 사용하면 고객이 SaaS 기반, 기술 솔루션을 구독으로 구매할 수 있습니다. SaaS 앱에 대해 다음 요구 사항이 충족되어야 합니다.
- 매월 정액으로 서비스 가격을 책정하고 요금을 청구합니다.
- 언제든지 서비스를 업그레이드하거나 취소할 방법을 제공합니다.
Microsoft는 상거래를 주도합니다. Microsoft는 사용자를 대신하여 고객에게 요금을 청구합니다. 구독으로서 SaaS 앱의 요금을 청구하려면 자신의 구독 관리 서비스 API를 사용하도록 설정해야 합니다. 구독 관리 서비스 API는 Azure Resource Manager API와 직접 통신해야 합니다. 구독 관리 서비스 API는 서비스 프로비저닝, 업그레이드 및 취소를 지원해야 합니다.

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
|청구 및 계량 | 제품은 월정액으로 가격이 책정됩니다. 사용량 기반 가격 책정 및 사용량 기반 "현실화" 기능은 이번에 지원되지 않습니다. |  
|취소 | 제품은 언제든지 고객에 의해 취소될 수 있습니다. |  
|트랜잭션 방문 페이지 | 사용자가 SaaS 서비스 계정을 만들고 관리할 수 있는 Azure 공동 브랜드 트랜잭션 방문 페이지를 호스트합니다. |   
| 구독 API | SaaS 구독과 상호 작용할 수 있는 서비스를 노출하여 사용자 계정 및 서비스 계획을 생성하고, 업데이트하고, 삭제합니다. 24시간 내에 중요한 API 변경 내용이 지원되어야 합니다. 중요하지 않은 API 변경 내용은 정기적으로 릴리스됩니다. |  

>[!Note]
>클라우드 솔루션 공급자 (CSP) 파트너 채널 옵트인를 출시 되었습니다.  참조 하세요 [클라우드 솔루션 공급자](./cloud-solution-providers.md) Microsoft CSP를 통해 제품을 마케팅 하는 방법은 채널 파트너입니다.

## <a name="next-steps"></a>다음 단계
아직 수행하지 않았다면

- Marketplace에 [등록](https://azuremarketplace.microsoft.com/sell).

새 제품을 등록하고 만들거나 기존에서 작업하는 경우

- 제품을 만들거나 완료하기 위해 [Cloud 파트너 포털에 로그인](https://cloudpartner.azure.com).
- 자세한 내용은 [Azure SaaS 애플리케이션 제품](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-offer)을 참조하세요.
