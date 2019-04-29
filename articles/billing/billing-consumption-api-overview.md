---
title: Azure Consumption API 개요 | Microsoft Docs
description: Azure Consumption API를 사용하여 Azure 리소스의 비용 및 사용 현황 데이터에 프로그래밍 방식으로 액세스하는 방법에 대해 알아봅니다.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: 16e0bdfa0fc70d5239cb4127e61891a013bf54a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615889"
---
# <a name="azure-consumption-api-overview"></a>Azure Consumption API 개요 

Azure Consumption API는 Azure 리소스의 비용 및 사용 현황 데이터에 프로그래밍 방식으로 액세스하는 방법을 제공합니다. 이러한 API는 현재 몇 가지 예외를 제외하고 엔터프라이즈 등록 및 웹 직접 구독만 지원합니다. API는 다른 형식의 Azure 구독을 지원하도록 지속적으로 업데이트됩니다.

Azure Consumption API는 다음에 대한 액세스를 제공합니다.
- 기업 및 웹 직접 고객 
    - 사용량 세부 정보 
    - Marketplace 요금 
    - 예약 권장 사항 
    - 예약 세부 정보 
    - 예약 요약 
- 기업 고객만 
    - 가격표 
    - 예산 
    - 잔액 

## <a name="usage-details-api"></a>사용량 세부 정보 API

사용량 세부 정보 API를 사용하여 모든 Azure 자사 리소스에 대한 요금 및 사용 현황 데이터를 가져옵니다. 정보는 하루에 리소스 및 미터당 한 번씩 보내지는 사용량 세부 정보 레코드 형식으로 제공됩니다. 정보는 모든 리소스에 걸쳐 비용을 합산하거나 특정 리소스에 대한 비용/사용량을 조사하는 데 사용할 수 있습니다. 

API에는 다음이 포함됩니다.

-   **미터 수준 소비량 데이터** - 사용 비용, 요금이 발생한 미터 및 요금이 어떤 Azure 리소스에 대한 것인지 등의 데이터를 확인합니다. 모든 사용량 세부 정보 레코드는 일별 버킷에 매핑됩니다.
-   **Azure 역할 기반 액세스 제어** - [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 또는 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 애플리케이션이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다. 
-   **필터링** - 다음 필터를 사용하여 API 결과 집합을 더 작은 사용량 세부 정보 레코드 집합으로 자릅니다.
    - 사용 종료/사용 시작
    - 리소스 그룹
    - 리소스 이름
-   **데이터 집계** - OData로 식을 적용하여 태그 또는 필터 속성별로 사용량 세부 정보를 집계합니다.
-   **다양한 제품 유형에 대한 사용량** - 사용량 세부 정보는 현재 기업 및 웹 직접 고객에게 제공됩니다.

자세한 내용은 [사용량 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/usagedetails)의 기술 사양을 참조하세요.

## <a name="marketplace-charges-api"></a>Marketplace 요금 API

Marketplace 요금 API를 사용하여 모든 Marketplace 리소스(Azure 타사 제품)에 대한 요금 및 사용 현황 데이터를 가져옵니다. 이 데이터는 모든 Marketplace 리소스에 걸쳐 비용을 합산하거나 특정 리소스에 대한 비용/사용량을 조사하는 데 사용할 수 있습니다. 

API에는 다음이 포함됩니다.

-   **미터 수준 소비량 데이터** - 마켓플레이스 사용 비용, 요금이 발생한 미터 및 요금이 어떤 리소스에 대한 것인지 등의 데이터를 확인합니다. 모든 사용량 세부 정보 레코드는 일별 버킷에 매핑됩니다.
-   **Azure 역할 기반 액세스 제어** - [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 또는 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 애플리케이션이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다. 
-   **필터링** - 다음 필터를 사용하여 API 결과 집합을 더 작은 마켓플레이스 레코드 집합으로 자릅니다.
    - 사용 시작/사용 종료
    - 리소스 그룹
    - 리소스 이름
-   **다양한 제품 유형에 대한 사용량** - Marketplace 정보는 현재 기업 및 웹 직접 고객에게 제공됩니다.

자세한 내용은 [Marketplace 요금 API](https://docs.microsoft.com/rest/api/consumption/marketplaces)의 기술 사양을 참조하세요.

## <a name="balances-api"></a>잔액 API

기업 고객은 잔액 API를 사용하여 잔액, 신규 구매, Azure Marketplace 서비스 요금, 조정 및 초과 요금에 대한 월별 정보 요약을 얻을 수 있습니다. 현재 청구 기간 또는 과거의 특정 기간에 대해 이 정보를 얻을 수 있습니다. 기업은 이 데이터를 사용하여 수동으로 계산한 요약 요금과 비교할 수 있습니다. 이 API는 리소스 관련 정보 및 비용에 대한 집계 보기는 제공하지 않습니다. 

API에는 다음이 포함됩니다.

-   **Azure 역할 기반 액세스 제어** - [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 또는 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 애플리케이션이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다. 
-   **기업 고객만** - 이 API는 EA 고객에게만 제공됩니다. 
    - 고객이 이 API를 호출하려면 엔터프라이즈 관리자 권한이 있어야 합니다. 

자세한 내용은 [잔액 API](https://docs.microsoft.com/rest/api/consumption/balances)의 기술 사양을 참조하세요.

## <a name="budgets-api"></a>예산 API

기업 고객은 이 API를 사용하여 리소스, 리소스 그룹 또는 청구 측정 단위에 대한 비용 또는 사용 예산을 만들 수 있습니다. 이 정보가 결정되면 사용자 정의 예산 임계값이 초과되면 이를 알리도록 경고를 구성할 수 있습니다. 

API에는 다음이 포함됩니다.

-   **Azure 역할 기반 액세스 제어** - [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 또는 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 애플리케이션이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다. 
-   **기업 고객만** - 이 API는 EA 고객에게만 제공됩니다.
-   **구성 가능한 알림** - 예산이 초과될 때 알림을 받을 사용자를 지정합니다.
-   **사용량 또는 비용 기반 예산** - 시나리오에 의한 필요에 따라 소비량 및 비용 기반 예산을 만듭니다.
-   **필터링** - 다음 구성 가능한 필터를 사용하여 예산을 더 작은 리소스 하위 집합으로 필터링합니다.
    - 리소스 그룹
    - 리소스 이름
    - 미터
-   **구성 가능한 예산 기간** - 예산 재설정 빈도와 예산 유효 기간을 지정합니다.

자세한 내용은 [예산 API](https://docs.microsoft.com/rest/api/consumption/budgets)의 기술 사양을 참조하세요.

## <a name="reservation-recommendations-api"></a>예약 권장 사항 API

이 API를 사용하여 Reserved VM Instances 구매에 대한 권장 사항을 가져옵니다. 권장 사항은 고객이 예상 비용 절감 및 구매 금액을 분석할 수 있도록 설계되었습니다. 

API에는 다음이 포함됩니다.

-   **Azure 역할 기반 액세스 제어** - [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 또는 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 애플리케이션이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다. 
-   **필터링** - 다음 필터를 사용하여 권장 사항 결과를 조정합니다.
    - 범위
    - 되돌아보기 기간
-   **다양한 제품 유형에 대한 예약 정보** - 예약 정보는 현재 기업 및 웹 직접 고객에게 제공됩니다.

자세한 내용은 [예약 권장 사항 API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations)의 기술 사양을 참조하세요.

## <a name="reservation-details-api"></a>예약 세부 정보 API

예약 세부 정보 API를 사용하면 예약된 소비량과 실제로 사용된 양과 같은 이전에 구매한 VM 예약 정보를 볼 수 있습니다. VM 수준별로 데이터를 자세히 볼 수 있습니다. 

API에는 다음이 포함됩니다.

-   **Azure 역할 기반 액세스 제어** - [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 또는 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 애플리케이션이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다. 
-   **필터링** - 다음 필터를 사용하여 API 결과 집합을 더 작은 예약 집합으로 자릅니다.
    - 날짜 범위
-   **다양한 제품 유형에 대한 예약 정보** - 예약 정보는 현재 기업 및 웹 직접 고객에게 제공됩니다.

자세한 내용은 [예약 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails)의 기술 사양을 참조하세요.

## <a name="reservation-summaries-api"></a>예약 요약 API

이 API를 사용하면 예약된 소비량과 실제로 사용된 집계량과 같은 이전에 구매한 VM 예약에 대한 집계 정보를 볼 수 있습니다. 

API에는 다음이 포함됩니다.

-   **Azure 역할 기반 액세스 제어** - [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 또는 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 애플리케이션이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다. 
-   **필터링** - 다음 필터를 사용하여 일별 단위를 사용할 때 결과를 조정합니다.
    - 사용 날짜
-   **다양한 제품 유형에 대한 예약 정보** - 예약 정보는 현재 기업 및 웹 직접 고객에게 제공됩니다.
-   **일별 또는 월별 집계** – 호출자는 예약 요약 데이터를 일별 또는 월별 단위로 원하는지를 지정할 수 있습니다.

자세한 내용은 [예약 요약 API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries)의 기술 사양을 참조하세요.

## <a name="price-sheet-api"></a>가격표 API
기업 고객은 이 API를 사용하여 모든 미터에 대한 사용자 지정 가격을 검색할 수 있습니다. 기업은 사용량 세부 정보 및 마켓플레이스 사용 정보를 함께 사용하여 사용량 및 마켓플레이스 데이터를 사용한 비용 계산을 수행할 수 있습니다. 

API에는 다음이 포함됩니다.

-   **Azure 역할 기반 액세스 제어** - [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 또는 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 애플리케이션이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다. 
-   **기업 고객만** - 이 API는 EA 고객에게만 제공됩니다. 웹 직접 고객은 가격을 얻으려면 RateCard API를 사용해야 합니다. 

자세한 내용은 [가격표 API](https://docs.microsoft.com/rest/api/consumption/pricesheet)의 기술 사양을 참조하세요.

## <a name="scenarios"></a>시나리오

다음은 소비용 API를 통해 구성 가능한 몇 가지 시나리오입니다.

-   **청구서 조정** - Microsoft가 적절한 금액을 청구했나요?  내 청구서는 무엇이며 내가 직접 계산할 수 있나요?
-   **교차 청구** - 청구 금액은 알았는데, 조직에서 누가 지불해야 하나요?
-   **비용 최적화** - 청구 금액이 얼마인지 알고 있습니다... 어떻게 하면 Azure에서 지출하는 것보다 더 많은 돈을 벌 수 있나요?
-   **비용 추적** - Azure를 사용하여 내가 얼마나 지출하고 있는지를 시간별로 알고 싶습니다. 추세란 무엇인가요? 어떻게 더 개선할 수 있나요?
-   **해당월 Azure 소비** - 이번 달 현재까지 지출액은 얼마인가요? Azure의 지출 및/또는 사용량을 조정해야 하나요? 해당 월 중에 언제 나는 Azure를 가장 많이 소비했나요?
-   **경고 설정** - 예산에 따라 리소스 기반 소비 또는 통화 기반 알림을 설정하고 싶습니다.

## <a name="next-steps"></a>다음 단계

- Azure 청구 API를 사용하여 Azure 사용량에 대한 정보를 프로그래밍 방식으로 얻는 방법에 대한 자세한 내용은 [Azure 청구 API 개요](billing-usage-rate-card-overview.md)를 참조하세요.



