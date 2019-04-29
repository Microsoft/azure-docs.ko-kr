---
title: 열거 유형별 요구 사항 | Azure
description: 이 문서에서는 Azure Marketplace의 앱을 게시하는 방법을 이해하려는 파트터의 자격 기준 및 게시 요구 사항에 대해 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: ebe344d9f596f862fe5ffbfef083725e6527d0d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599324"
---
# <a name="requirements-by-listing-type"></a>등록 형식별 요구 사항  
마케팅 및 기술 콘텐츠 요구 사항은 상점, 제품 유형 및 열거 유형에 따라 달라집니다. 준수 여부를 확인하기 위해 다음 사양을 검토합니다.  
1. 상점 요구 사항:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. 열거 유형 및 제품 유형 요구 사항:  
    *   열거 유형 및 제품 유형에 대한 자세한 내용은 [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md)에 있는 솔루션에 대한 열거 유형 결정 페이지를 참조하세요.  

## <a name="storefront-requirements-appsource"></a>상점 요구 사항: AppSource  
다음 표에서는 AppSource에 게시하기 위한 필수 구성 요소 요구 사항을 설명합니다.  

| 요구 사항 | 세부 정보 | 필수 또는 권장 |  
|:--- |:--- |:--- |  
| ***Azure AD(Azure Active Directory)*** | 앱은 동의가 이루어질 경우 Azure Active Directory 페더레이션 Single Sign-On(Azure AD 페더레이션 SSO)을 허용해야 합니다.<ul> <li>Azure AD 페더레이션 SSO 사용에 대한 자세한 내용은 [docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)에 있는 Azure Active Directory 애플리케이션 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성 페이지를 참조하세요.</li> </ul> | 필수 |   
| ***Microsoft Cloud Services와의 통합*** | 앱은 Microsoft Power BI와 같은 Microsoft 클라우드 서비스, Cortana Intelligence 또는 Microsoft Azure 서비스와 통합되어야 합니다.<ul> <li>Microsoft 클라우드 서비스의 예로는 사물 인터넷이 있습니다.</li> </ul> | 권장 |  
| ***대상*** | 앱은 기간 업무 사용자 및 비즈니스 소유자에 대한 것이어야 합니다. | 필수 | 
| ***비즈니스용 SaaS(Software as a Service) 앱*** | 앱은 다음 요구 사항을 충족해야 합니다.<ul> <li>기간 업무 SaaS 앱</li> <li>비즈니스 프로세스 중점</li> <li>비즈니스 고객 대상</li> <li>사용자가 사용자 이름 및 암호와 같은 회사 자격 증명을 사용하여 로그인 가능</li> </ul> | 필수 |  
| ***무료 평가 기간 및 체험 환경*** | 고객이 제한된 시간 동안 앱을 체험할 수 있도록 앱에 다음 옵션을 포함해야 합니다.<ul> <li>`try` 메서드를 제공하면 고객이 AppSource 내에서 앱 체험을 시작할 수 있습니다.</li> <li>AppSource에 `request trial` 옵션을 제공하면 고객이 앱 평가판을 요청할 수 있습니다.</li> </ul>제공하는 평가판에는 고객이 추가 비용 부담 없이 앱을 사용해 볼 수 있는 기간이 미리 설정되어야 합니다. | 필수 |  
| ***쉽게 구성 가능하고 즉시 사용할 수 있는 솔루션*** | 앱은 사용자 지정 없이도 구성 및 설정이 빠르고 쉬워야 합니다. | 필수 |  
| ***잠재 고객 관리*** | 상점의 잠재 고객을 받기 전에 CRM에서 잠재 고객 데이터를 허용하도록 설정합니다.<ul> <li>CRM의 예로 Marketo, Microsoft Dynamics 또는 Salesforce가 있습니다.</li> </ul> | 필수 |  
| ***개인 정보 취급 방침 및 사용 약관*** | 앱은 공용 URL을 사용하여 개인정보처리방침 방침 페이지의 링크를 제공해야 합니다. 텍스트로 게시하는 동안 사용 약관을 제공해야 합니다. | 필수 |  
| ***지원*** | 앱은 공용 URL을 사용하여 고객 지원 페이지의 링크를 제공해야 합니다. 앱이 평가판인 경우, 평가 기간 중에는 추가 비용 없이 지원해야 합니다. | 필수 |  

## <a name="storefront-requirements-azure-marketplace"></a>상점 요구 사항: Azure Marketplace  
다음은 Azure Marketplace의 열거 유형에 대한 필수 구성 요소 요구 사항입니다.  

| 요구 사항 | 세부 정보 | 열거 유형 |  
|:--- |:--- |:--- |  
| ***참가 정책*** | 앱은 Azure Marketplace 참가 정책을 따라야 합니다.<ul> <li>참가 정책에 대한 자세한 내용은 [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies)에 있는 Azure Marketplace 참가 정책 페이지를 참조하세요.</li></ul> | list<br />거래<br />체험 |  
| ***Microsoft와의 통합*** | 제품은 계산, 네트워킹 또는 스토리지 등 Microsoft Azure 서비스 유형을 사용하거나 확장해야 합니다. 또한 데이터베이스, 보안 및 네트워킹 등 기존 Azure Marketplace 범주에 맞추어야 합니다.<ul> <li>Marketplace 제품에 대한 자세한 내용은 [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps)에 있는 Marketplace 앱 페이지를 참조하세요.</li> </ul> | list<br />거래<br />체험 |  
| ***대상*** | 제품은 IT 전문가, 클라우드 개발자 또는 기타 고객 기술 역할에 대한 것이어야 합니다. | list<br />거래<br />체험 |  
| ***잠재 고객 관리*** | 상점의 잠재 고객을 받기 전에 CRM(Marketo, Microsoft Dynamics 또는 Salesforce)에서 잠재 고객 데이터를 허용하도록 설정합니다. | list<br />거래<br />체험 |  
| ***개인 정보 취급 방침 및 사용 약관*** | 앱은 공용 URL을 사용하여 개인정보처리방침 방침 페이지의 링크를 제공해야 합니다. 텍스트로 게시하는 동안 사용 약관을 제공해야 합니다. | list<br />거래<br />체험 |  
| ***지원*** | 제품은 공용 URL을 사용하여 고객 지원 페이지의 링크를 제공해야 합니다. 제품이 평가판인 경우 평가 기간 중에는 추가 비용 없이 지원해야 합니다. | 거래<br />체험 |    

## <a name="non-transact-listings"></a>비거래 열거  
이 섹션에서는 거래 열거 유형을 사용하지 않는 모든 제품 유형에 대해 설명합니다. 

### <a name="list"></a>나열  
나열 열거 유형은 상점의 다음 제품 유형을 마켓플레이스에 포함합니다.  

| 제품 유형 | 상점 | 세부 정보 |  
|:---        |:---        |:---     |  
| 컨설팅 서비스 | AppSource | Requirements: AppSource: 목록: 컨설팅 서비스 |  
| 컨설팅 서비스 | Azure Marketplace | Requirements: Azure Marketplace: 목록: 컨설팅 서비스 |  
| 연락처 | AppSource | [](#) |  
| 연락처 | Azure Marketplace | Requirements: AppSource: 목록: 연락처 |  

#### <a name="requirements-appsource-list-consulting-service"></a>Requirements: AppSource: 목록: 컨설팅 서비스  

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
| 서비스 제안 특성 | 컨설팅 서비스는 다음 기준을 충족해야 합니다.<ul> <li>고정 범위, 고정 기간, 고정 가격(또는 무료) 약정을 제공합니다.</li> <li>주로 판매 전 지원을 지향합니다.</li> <li>단일 고객으로 제한합니다.</li> <li>사이트에서 수행합니다.</li> </ul> |  
| 컨설팅 서비스에 대한 파트너 요구 사항 | 서비스에 대한 관련 영역의 기준을 충족합니다.<table><tr><th>솔루션 영역</th><th>조건</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>실버 또는 골드 클라우드 고객 관계 관리 역량을 갖습니다.</td></tr><tr><td>Dynamics 365 for Operations, Enterprise Edition</td><td>실버 또는 골드 엔터프라이즈 리소스 플랜 역량을 갖고, 최근 12개월 동안 클라우드 운영에서 최소 25,000 USD 수익을 달성합니다.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Business Edition</td><td>한 명 이상의 고객에 대해 CSP(클라우드 서비스 공급자) 또는 DPOR(디지털 공식 파트너) 역할을 수행합니다.</td></tr><tr><td>Power BI</td><td>솔루션 파트너 기준을 충족합니다.</td></tr><tr><td>PowerApps</td><td>파트너 쇼케이스 솔루션이 있습니다.</td></tr></table><ul> <li>고객 관계 관리에 대한 자세한 내용은 [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency)에 있는 클라우드 고객 관계 관리 페이지를 참조하세요.</li> <li>리소스 플랜에 대한 자세한 내용은 [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency)에 있는 엔터프라이즈 리소스 플랜 페이지를 참조하세요.</li> <li>CSP에 대한 자세한 내용은 [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider)에 있는 클라우드 서비스 공급자 페이지를 참조하세요.</li> <li>DPOR에 대한 자세한 내용은 [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record)에 있는 디지털 공식 파트너 및 파트너 연결 페이지를 참조하세요.</li> <li>솔루션 파트너 기준에 대한 자세한 내용은 [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf)에 있는 솔루션 파트너 개요 및 인센티브 문서를 참조하세요.</li> <li>파트너 쇼케이스에 대한 자세한 내용은 [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase)에 있는 파트너 쇼케이스 페이지를 참조하세요.</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Requirements: Azure Marketplace: 목록: 컨설팅 서비스  

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
| 서비스 제안 특성 | 컨설팅 서비스는 다음 기준을 충족해야 합니다.<ul> <li>고정 범위, 고정 기간, 고정 가격(또는 무료) 약정을 제공합니다.</li> <li>주로 판매 전 지원을 지향합니다.</li> <li>단일 고객으로 제한합니다.</li> <li>사이트에서 수행합니다.</li> </ul> |  
| 컨설팅 서비스에 대한 파트너 요구 사항 | 서비스의 관련 영역에서 다음 역량 중 하나에 실버 또는 골드가 있어야 합니다. <table><tr><th>솔루션 영역</th><th>역량</th></tr><td>클라우드 플랫폼 및 인프라</td><td>클라우드 플랫폼<br />데이터 센터</td><tr><td>애플리케이션 개발 및 ISV</td><td>애플리케이션 개발<br />애플리케이션 통합<br />DevOps</td></tr><tr><td>데이터 관리 및 분석</td><td>데이터 분석<br />데이터 플랫폼</td></tr></table><ul> <li>역량에 대한 자세한 내용은 [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies)에 있는 Microsoft 파트너 네트워크를 통한 역량 페이지를 참조하세요.</li> <li>열거에 대한 자세한 내용은 [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services)에 있는 Azure Marketplace 컨설팅 서비스 페이지를 참조하세요.</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>평가판  

| 제품 유형 | 상점 | 세부 정보 |  
|:---        |:---        |:---     |  
| 무료/SaaS 평가판 | AppSource | 열거 유형 요구 사항: 평가판 |  
| 무료/SaaS 평가판 | Azure Marketplace | Requirements: Azure Marketplace: 평가판: 평가판/SaaS 평가판 |  
| 대화형 데모 | AppSource | 열거 유형 요구 사항: 평가판 |  
| 대화형 데모 | Azure Marketplace | [요구 사항: Azure Marketplace: 평가판: 대화형 데모](#requirements-azure-marketplace-trial-interactive-demo) |  
| 시험 사용 | AppSource | 열거 유형 요구 사항: 평가판 |  
| 시험 사용 | Azure Marketplace | [요구 사항: Azure Marketplace: 평가판: 시험 사용](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Requirements: Azure Marketplace: 평가판  

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
| 평가판 기간 및 체험 환경 | 고객은 제한된 기간 동안 앱을 체험해 볼 수 있습니다.<br /><br />고객은 제품이나 앱에 대한 라이선스 또는 구독 요금을 지급할 필요가 없습니다. 고객은 기본 Microsoft 자사 제품 또는 서비스에 대한 요금을 지급할 필요가 없습니다. 모든 평가판 옵션은 Azure 구독에 배포됩니다. 비용 최적화 및 관리 평가판을 단독으로 제어할 수 있습니다.<br /><br />평가판, 대화형 데모 또는 시험 사용 중에서 선택할 수 있습니다. 어떤 방식을 선택하든, 평가판에는 고객이 추가 비용 부담 없이 앱을 사용해 볼 수 있는 기간이 미리 설정되어야 합니다.<ul> <li>시험 사용 생성 프로세스를 시작하려면 [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)에 메일을 보내세요.</li> </ul>참고: Azure Marketplace SaaS 평가판 환경에서는 고객이 회사 자격 증명으로 로그인하도록 허용해야 합니다.<ul> <li>자세한 내용은 [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences)에 있는 AppSource 체험 환경 섹션을 참조하세요.</li> </ul> |  
| 쉽게 구성 가능하고 즉시 사용할 수 있는 솔루션 | 앱은 구성 및 설정이 빠르고 쉬워야 합니다. |  
| 가용성/작동 시간 | SaaS 또는 플랫폼의 가동 시간은 최소 99.9%여야 합니다. |  
| Azure Active Directory | 제품은 동의가 이루어질 경우 Azure AD(Azure Active Directory) 페더레이션 SSO(Single Sign-On)(Azure AD 페더레이션 SSO)를 허용해야 합니다. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Requirements: Azure Marketplace: 평가판: 평가판/SaaS 평가판  

| 혜택 | 요구 사항 |  
|:--- |:--- |  
| 고객이 유료 사용으로 자동 전환 방법을 사용하여 제품을 구매하기 전에 사용해 보도록 합니다. 또한 고객에 대한 개념 증명 및 Microsoft 판매 팀과의 공동 계약을 사용할 수 있습니다. | 사용자의 솔루션은 가상 머신 또는 솔루션 템플릿입니다.<br /><br />사용자의 솔루션은 SaaS 제품이며 다중 테넌트 SaaS 제품을 제공합니다.<br /><br />사용자는 고객이 빨리 정상 작동 상태에 이르도록 하는 처음 실행 환경을 가지고 있습니다.<br /><br />사용자는 단일 테넌트를 가지고 있지만 고객을 게스트 사용자로 추가합니다. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Requirements: Azure Marketplace: 평가판: 대화형 데모  

| 혜택 | 요구 사항 |  
|:--- |:--- |  
| 고객이 복잡한 설치 없이 작동되는 제품을 볼 수 있도록 합니다. | 솔루션이 평가 기간에 구현하기 어려운 복잡한 설정을 요구합니다. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Requirements: Azure Marketplace: 평가판: 시험 사용  

| 혜택 | 요구 사항 |  
|:--- |:--- |  
| 고객이 사용자의 제품을 구입하기 전에 시험 사용할 수 있도록 합니다.<br /><br />미리 구성된 설정을 사용하여 솔루션 안내 방식 환경을 제공합니다.<br /><br />시험 사용을 사용할 경우 다음과 같은 추가적인 혜택이 있습니다.<ul> <li>마켓플레이스에서 진행되는 사용자 검색의 27%는 시험 사용해본 제품만 보려는 사용자에 해당합니다.</li> <li>시험 사용을 제공하는 제품은 시험 사용을 제공하지 않는 제품보다 38% 더 많은 잠재 고객을 창출합니다.</li> <li>마켓플레이스에서 새 고객이 제품을 구입하는 경우 중 36%가 시험 사용해본 고객에 해당합니다.</li> <li>시험 사용은 Microsoft 현장 판매자들이 공동 판매를 위해 제품을 보다 잘 이해할 수 있도록 합니다.</li> </ul> | 사용자의 솔루션은 단일 테넌트가 있는 가상 머신, 솔루션 템플릿 또는 SaaS 앱이거나, 프로비전하기 복잡합니다. <br /><br />평가판을 유료 제품으로 전환할 방법이 없습니다. |  

---

## <a name="transact-specific-listings"></a>거래별 열거

### <a name="transact"></a>거래  

| 제품 유형 | 상점 | 세부 정보 |   
|:---        |:---        | :--- |  
| Azure 앱: 관리형 앱 | Azure Marketplace | Requirements: Azure Marketplace: 거래: Azure 앱: 관리되는 앱 |  
| Azure 앱: 솔루션 템플릿 | Azure Marketplace | Requirements: Azure Marketplace: 거래: Azure 앱: 솔루션 템플릿 |  
| 컨테이너 | Azure Marketplace | [요구 사항: Azure Marketplace: 거래: 컨테이너](#requirements-azure-marketplace-transact-container) |  
| SaaS 앱  | Azure Marketplace | [요구 사항: Azure Marketplace: 거래: SaaS 앱](#requirements-azure-marketplace-transact-saas-app) |  
| 가상 머신 | Azure Marketplace | [요구 사항: Azure Marketplace: 거래: 가상 머신](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Requirements: Azure Marketplace: 거래: 컨테이너  

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
| 청구 및 계량 | 무료 또는 BYOL 청구 모델을 지원합니다. |  
| Docker 기반 이미지 | 컨테이너 이미지는 Docker 이미지 형식을 기반으로 해야 하며 Azure Container Registries에서 풀해야 합니다. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Requirements: Azure Marketplace: 거래: SaaS 앱  

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
| 청구 및 계량 | 제품은 월정액으로 가격이 책정됩니다. 사용량 기반 가격 책정 및 사용량 기반 *트루업* 옵션은 이번에 지원되지 않습니다. |  
| 취소 | 제품은 언제든지 고객에 의해 취소될 수 있습니다. |  
| 트랜잭션 방문 페이지 | Azure 공동 브랜드 트랜잭션 방문 페이지를 호스트합니다. 방문 페이지를 사용하면 고객이 SaaS 서비스 계정을 만들고 관리할 수 있습니다. |  
| SaaS 구독 API | SaaS 구독과 상호 작용하는 서비스를 제공하여 사용자 계정 및 서비스 계획을 생성하고, 업데이트하고, 삭제합니다. 24시간 내에 모든 중요한 API 변경 내용이 지원되어야 합니다. 중요하지 않은 API 변경 내용은 정기적으로 업데이트됩니다. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Requirements: Azure Marketplace: 거래: 가상 머신  

| 요구 사항 | 세부 정보 |  
|:--- |:--- | 
| 청구 및 계량 | VM은 BYOL 또는 종량제 월간 청구를 지원해야 합니다. |  
| Azure 호환 VHD(가상 하드 디스크) | VM은 Windows 또는 Linux에서 빌드해야 합니다.<ul> <li>Linux VHD를 만드는 방법에 대한 자세한 내용은 [Azure 보증 Linux 배포판](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)을 참조하세요.</li> <li>Windows VHD를 만드는 방법에 대한 자세한 내용은 [Azure 호환 VHD 만들기](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)를 참조하세요.</li> </ul> |  

## <a name="next-steps"></a>다음 단계
*   [Azure Marketplace 및 AppSource 게시자 가이드](./marketplace-publishers-guide.md) 페이지를 방문합니다.  

