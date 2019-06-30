---
title: Dynamics 365 Customer Engagement에 대 한 필수 구성 요소를 제공 합니다. | Azure Marketplace
description: Azure Marketplace에서 Azure 애플리케이션 제품을 게시하기 위한 필수 구성 요소입니다.
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 9cfec65843012e781c8a8dd06cbc4d1225b0dd36
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338904"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Dynamics 365 for Customer Engagement 필수 구성 요소

이 문서에서는 AppSource Marketplace에서 Dynamics 365 for Customer Engagement 애플리케이션 제안을 게시하기 위한 기술 및 비즈니스 필수 구성 요소에 대해 설명합니다.  아직 수행 하는 경우 검토 합니다 [Office 365, Dynamics 365, PowerApps 및 Power BI 제품 게시 가이드](../../appsource-offer-publishing-guide.md)합니다.


## <a name="technical-requirements"></a>기술적인 요구 사항

Dynamics 365 for Customer Engagement 애플리케이션에서 다음 요구 사항을 포함한 [Microsoft AppSource 앱 검토 지침](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf)을 준수해야 합니다.


|              요구 사항             |        설명           |
|            ---------------           |      ---------------         |
| Azure Active Directory 통합   | 앱은 동의가 이루어질 경우 Azure Active Directory 페더레이션 Single Sign-On(AAD 페더레이션 SSO)을 허용해야 합니다. 자세한 내용은 [Azure Active Directory에 대해 인증된 AppSource를 가져오는 방법](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified)을 참조하세요. |
| Microsoft 클라우드 서비스와의 통합(선택 사항) | 이 기능이 필요한 경우 앱은 Microsoft Power BI, Microsoft Flow 또는 Microsoft Azure 서비스(예: 기계 학습 또는 인지 서비스)와 같은 다른 Microsoft 클라우드 서비스와 통합되어야 합니다. |
| 기간 업무 중심            |  앱은 잘 정의된 비즈니스 프로세스 또는 문제에 집중해야 하고, 주로 비즈니스 고객을 대상으로 하며, 사용자가 자신의 회사 자격 증명(사용자 이름 및 암호)을 사용하여 로그인할 수 있도록 해야 합니다.  |
| 평가판 기간 및 체험 환경 |  고객은 제한된 시간 동안 평가판 앱, 즉 평가판 앱 "지금 설치하기", 특정 기간 동안의 "평가판 앱", "시험 사용" 시연자 또는 "나에게 연락" 요청 옵션을 사용할 수 있어야 합니다.  |
| 구성 없음/낮음                 | 앱은 쉽고 빠르게 구성하고 설정해야 합니다(개발 또는 사용자 지정이 필요하지 않음).  |
| 고객 지원                     | 앱 지원에는 고객이 도움을 받을 수 있는 지원 링크가 있어야 합니다.  |
| 가용성/가동 시간                  | 앱의 가동 시간은 99.9% 이상이어야 합니다. |
|  |  |


## <a name="business-requirements"></a>비즈니스 요구 사항

비즈니스 요구 사항에는 다음과 같은 절차, 계약 및 법적 의무가 포함됩니다.

* [MPN(Microsoft 파트너 네트워크)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx)에 등록되거나 등록된 클라우드 Marketplace 게시자여야 합니다. 등록되지 않은 경우 [클라우드 Marketplace 게시자 되기](https://docs.microsoft.com/azure/marketplace/become-publisher)의 단계를 수행합니다.  (사용 하 여 연결할 수 있습니다 합니다 [Dynamics 365 Customer Engagement 온 보 딩 팀](https://experience.dynamics.com/isvengage/)).

    >[!NOTE]
    >동일한 Microsoft 개발자 센터 등록 계정을 사용하여 Cloud 파트너 포털에 로그온해야 합니다. Azure Marketplace 제품에 대해 Microsoft 계정이 하나만 있으면 됩니다. 이 계정은 개별 서비스 또는 제품에 한정되지 않아야 합니다.

* AppSource에서 거래 지원 게시 옵션을 제공하지 않으므로 추가로 투자하거나 변경하지 않고 이 현재 주문 및 청구 인프라를 사용해야 합니다.
* 상업적이고 합리적인 방식으로 고객이 이용할 수 있는 기술 지원을 담당합니다. 이러한 지원은 체험, 유료 또는 커뮤니티 접근 방식을 통해 제공될 수 있습니다.
* 소프트웨어 및 타사 소프트웨어 종속성에 대해 사용 허가를 받을 책임이 있습니다.
* 공식적인 앱 이름, 설명(H HTML 형식), PNG 형식의 로고 이미지(40x40, 90x90, 115x115 및 255x115 픽셀), 사용 약관 및 개인정보처리방침과 같은 관련 마케팅 참고 자료를 만들었어야 합니다.  


## <a name="next-steps"></a>다음 단계

이러한 요구 사항이 충족되면 [Dynamics 365 Customer Engagement 제안을 만들 수 있습니다](./cpp-create-offer.md). 
