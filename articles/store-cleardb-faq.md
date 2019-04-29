---
title: Azure App Service를 사용하는 ClearDB MySQLl 데이터베이스에 대한 FAQ
description: Azure App Service를 사용하는 ClearDB MySQL 데이터베이스를 사용하는 방법에 대한 일반적인 질문에 대한 답변입니다.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0887f58ca455dfec0474c8d6a1acba584224f0d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929453"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Azure App Service를 사용하는 ClearDB MySQLl 데이터베이스에 대한 FAQ
이 FAQ는 Azure Web Apps용 ClearDB MySQL 데이터베이스의 사용 및 구매에 대한 일반적인 질문에 답변합니다.

> [!IMPORTANT]
> 2018년 6월 13일을 기준으로 ClearDB는 Microsoft에서 요금이 청구되는 Azure 기반 고객을 ClearDB를 사용하는 직접 청구 모델로 전환했습니다. 이 문서의 정보는 이전 내용입니다. 더 이상 Azure에서 만든 ClearDB 데이터베이스를 만들거나 업그레이드할 수 없습니다.
>
> 자세한 내용 및 다음 단계를 보려면 [ClearDB 서비스 계획으로 변경](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/)을 참조하세요.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Azure에서 MySQL을 사용하려면 어떤 옵션이 필요한가요?
해당 서비스에 대한 최신 정보는 [ClearDB](https://w2.cleardb.net/)를 참조하세요. ClearDB는 MySQL 호스팅 서비스이며 MySQL 인프라를 관리합니다. 

Azure에서 MySQL 호스팅에 대한 몇 가지 다른 옵션이 있습니다.
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [Azure VM에서 실행되는 MySQL 클러스터](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Azure VM에서 실행되는 MySQL의 단일 인스턴스](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Azure Marketplace의 웹앱 + MySQL 템플릿에 신용 카드가 필요한가요?
사용하고 있는 구독 유형에 따라 다릅니다. 다음은 일반적으로 사용되는 구독 유형입니다.

* [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/): 신용 카드가 필요하며 유료 MySQL 데이터베이스를 구매하는 경우 신용 카드로 요금이 청구됩니다.
* [평가판](https://azure.microsoft.com/pricing/free-trial/): Microsoft Azure 서비스에서 사용할 크레딧이 포함되어 있지만 타사 리소스의 구매에는 사용할 수 없습니다. 타사 서비스 또는 유료 MySQL 데이터베이스를 구매하려면 신용 카드가 설정된 구독을 사용해야 합니다. Web Apps의 경우 무료 ClearDB MySQL 데이터베이스를 만들 수 있습니다.
* [MSDN 구독](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) 및 **MSDN 개발 테스트 종량제**: 평가판과 유사한 MSDN 구독을 사용하려면 신용 카드로 ClearDB의 유료 MySQL 솔루션을 구매해야 합니다.
* [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA 고객은 Azure Marketplace(타사)에서 구매한 모든 제품에 대해 EA 분기별로 별도의 통합 청구서로 청구됩니다. 모든 마켓플레이스에서 구매한 제품은 현금 약정 금액과 별도로 청구됩니다. 현재 아제르바이잔, 크로아티아, 노르웨이 및 푸에르토리코에서 등록한 고객은 Azure 저장소를 사용할 수 없습니다. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>왜 Azure Marketplace에서 웹앱+ MySQL에 대해 3.50달러가 청구되었나요?
기본 데이터베이스 옵션은 3.50달러인 Titan입니다. 데이터베이스를 만드는 동안 비용이 표시되지 않으므로 의도하지 않은 데이터베이스를 실수로 구매할 수도 있습니다. 환경을 개선할 방법을 찾으려고 노력하고 있습니다. 그 전까지는 **만들기**를 클릭하고 리소스를 배포하기 전에 웹앱 및 데이터베이스에 대해 선택한 가격 책정 계층을 모두 확인해야 합니다.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Azure 가상 머신에서 MySQL을 실행하고 있습니다. Azure 웹앱을 내 데이터베이스에 연결할 수 있나요?
예. Azure VM에서 웹앱으로 원격 액세스할 수 있으면 웹앱을 데이터베이스에 연결할 수 있습니다. 자세한 내용은 [가상 컴퓨터에 MySQL 설치](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>ClearDB 프리미엄 MySQL 클러스터가 지원되는 국가는 어디인가요?
ClearDB 프리미엄 MySQL 클러스터는 인도, 오스트레일리아, 브라질 남부 및 중국을 제외한 전 세계 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>ClearDB 프리미엄 클러스터 솔루션을 사용하여 데이터베이스를 만들기 전에 새 클러스터를 만들 수 있나요?
아니요, 빈 ClearDB 클러스터 만들기는 지원되지 않습니다. Azure Portal을 사용하면 클러스터에서 데이터베이스를 만들 수 있으며 동시에 새 클러스터를 만들 수 있습니다.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>내 애플리케이션 중 하나에서 사용 중인 ClearDB MySQL 데이터베이스를 삭제하려고 하면 경고가 표시되나요?
아니요, 애플리케이션이 사용하고 있는 마켓플레이스 구매 제품을 삭제할 경우 Azure는 경고를 표시하지 않습니다.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>어떤 지역에서 ClearDB 데이터베이스를 만들 수 있나요?
아제르바이잔, 크로아티아, 노르웨이 또는 푸에르토리코에서 등록한 고객은 Azure Marketplace를 사용할 수 없습니다. 이러한 지역에서는 ClearDB를 사용할 수 없습니다.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>프로덕션 웹앱 및 데이터베이스에 대해 어떤 가격 책정 계층을 선택해야 하나요?
Web Apps의 경우 기본이나 더 높은 가격 책정 계층을 사용합니다. ClearDB의 경우 Saturn 또는 Jupiter 플랜을 사용하는 것이 좋습니다. [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) 및 [ClearDB MySQL 데이터베이스](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/)에 대한 각 가격 책정 계층의 기능 및 제한 사항을 검토하여 요구에 맞는 계층을 선택합니다.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>다른 플랜으로 ClearDB 데이터베이스를 업그레이드하려면 어떻게 하나요?
[Azure Portal](https://portal.azure.com)의 경우 ClearDB 공유 호스팅 데이터베이스를 강화할 수 있습니다. 자세한 내용은 이 [문서](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/)를 참조하세요. 현재는 Azure Portal에서 ClearDB 프리미엄 클러스터에 대한 업그레이드를 지원하지 않습니다.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Azure Portal에서 내 ClearDB 데이터베이스를 볼 수 없나요?
클래식에서 ClearDB 데이터베이스를 만든 경우 [Azure Portal](https://portal.azure.com)에서 데이터베이스를 볼 수 없습니다. 이 시나리오의 경우 해결 방법이 없습니다.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>데이터베이스가 다운되었을 때 지원을 받으려면 누구에게 문의해야 하나요?
데이터베이스 관련 문제에 대해서는 [ClearDB 지원](https://www.cleardb.com/developers/help/support) 에 문의하세요. Azure 구독 정보를 제공해야 하므로 준비합니다.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>ClearDB MySQL 데이터베이스 클러스터 솔루션에 대한 추가 사용자를 만들 수 있나요?
아니요. 추가 사용자를 만들 수는 없지만 ClearDB 데이터베이스 클러스터에서 추가 데이터베이스를 만들 수 있습니다.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Basic/Pro 시리즈 데이터베이스를 오늘날 Planetary 계획처럼 ClearDB 포털에서 전체 업그레이드할 수 있나요?
예, Basic 시리즈 데이터베이스는 전체 업그레이드(Basic 60 ~ Basic 500)할 수 있습니다. Pro 시리즈는 Pro 60을 제외하고 전체 업그레이드(Pro 125 ~ Pro 1000)할 수 있습니다. 현재 Pro 60 데이터베이스 업그레이드는 지원되지 않습니다. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>리소스를 하나의 구독에서 다른 구독으로 마이그레이션할 때 ClearDB MySQL 데이터베이스도 마이그레이션되나요?
구독 간에 리소스 마이그레이션을 수행할 때 일부 [제한 사항](azure-resource-manager/resource-group-move-resources.md#app-service-limitations) 이 적용됩니다. ClearDB MySQL 데이터베이스는 타사 서비스이므로 Azure 구독 마이그레이션 중에 마이그레이션되지 않습니다. Azure 리소스를 마이그레이션하기 전에 MySQL 데이터베이스의 마이그레이션을 관리하지 않으면 ClearDB MySQL 데이터베이스가 사용할 수 없도록 설정될 수 있습니다. 먼저 수동으로 데이터베이스를 마이그레이션한 다음 웹앱에 대한 Azure 구독 마이그레이션을 수행합니다. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>내 구독에서 지출 한도에 도달했습니다. 한도를 삭제했고 App Service는 온라인 상태지만 데이터베이스에 액세스할 수 없습니다. 어떻게 ClearDB 데이터베이스를 다시 사용하나요?
[ClearDB 지원](https://www.cleardb.com/developers/help/support)에 연락하여 데이터베이스를 다시 사용합니다. Azure 구독 정보와 데이터베이스 이름을 제공합니다.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>신용 카드 구독에서 EA 구독으로 ClearDB 데이터베이스를 전송할 수 있나요?
기존 ClearDB 데이터베이스에서 기존 구독과 연결된 신용 카드를 사용합니다. EA 구독을 사용하려면 데이터를 새 데이터베이스로 마이그레이션해야 합니다.

* EA 구독으로 새 ClearDB 데이터베이스를 구매합니다.
* 새 데이터베이스로 데이터를 마이그레이션합니다.
* 새 데이터베이스를 사용할 애플리케이션을 업데이트합니다.
* 이전 ClearDB 데이터베이스를 삭제합니다.

MySQL(ClearDB)을 사용하여 새 웹앱을 만들거나 MySQL 데이터베이스(ClearDB)를 만드는 경우 선택한 구독이 서비스에 대한 지불 방법을 결정합니다. EA 구독을 사용하면 Azure Portal의 ClearDB와 같은 타사 서비스의 조달이 차단되지 않습니다. EA 구독은 현금 약정 금액과 별도로 청구되며 분기별로 체납 청구됩니다. EA 고객은 모든 타사 마켓플레이스 서비스에 대해 지불하는 데 사용할 결제 방법(예: 신용 카드)을 설정해야 합니다.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>EA 구독의 ClearDB 리소스에 대한 요금은 어디에서 볼 수 있나요?
Direct EA 고객의 경우 Azure Marketplace 요금은 엔터프라이즈 포털에 표시됩니다. 마켓플레이스에서 구매한 모든 제품 및 사용량 과금은 현금 약정 금액과 별도로 청구되며 분기별로 체납 청구됩니다. EA 고객은 타사 서비스 공급자에게 직접 요금을 지불해야 하며 EA 계정으로 신용 카드 등의 결제 방법을 사용하여 지불할 수 있습니다.

Indirect EA 고객은 엔터프라이즈 포털의 **구독 관리** 페이지에서 Azure Marketplace 구독을 찾을 수 있지만 가격은 표시되지 않습니다. 고객은 마켓플레이스 요금에 대한 내용을 LSP에 문의해야 합니다.

EA Azure 등록 관리자가 관리할 수 있는 타사 서비스용 Azure Marketplace에 액세스합니다. 엔터프라이즈 포털의 계정 및 구독 관리에 있는 계정 섹션에서 스토어에서 구매한 타사 제품에 대한 액세스를 사용하지 않도록 설정하거나 다시 사용하도록 설정할 수 있습니다.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>EA 구독의 ClearDB 서비스의 요금에 대한 질문이 있는 경우 누구에게 문의해야 하나요?
EA 등록에서 요금 청구와 관련된 사항은 [엔터프라이즈 고객 지원](https://aka.ms/AzureEntSupport) 에 문의하세요. EA 포털 지원 팀이 질문에 대답하거나 문제를 해결하는데 도움을 줄 것입니다.

## <a name="more-information"></a>자세한 정보
[Azure Marketplace FAQ](https://azure.microsoft.com/marketplace/faq/)

