---
title: Azure Time Series Insights의 고객 데이터 요청 기능 | Microsoft Docs
description: Azure Time Series Insights의 고객 데이터 요청 기능에 대한 요약입니다.
author: ashannon7
ms.author: anshan
manager: cshankar
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 30f6b1fd953f89170a18d56bf0353c643853074e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880711"
---
# <a name="summary-of-customer-data-request-features"></a>고객 데이터 요청 기능 요약

Azure Time Series Insights는 수십억 개의 이벤트를 동시에 쉽게 수집, 저장, 탐색, 분석할 수 있는 저장소, 분석 및 시각화 구성 요소를 갖춘 관리되는 클라우드 서비스입니다.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

데이터 주체 요청이 적용될 수 있는 개인 데이터를 보고, 내보내고, 삭제하려면 Azure Time Series Insights 테넌트 관리자는 Azure Portal 또는 REST API를 사용하면 됩니다. Azure Portal을 사용하여 데이터 주체 요청을 처리하는 것은 이러한 작업을 수행하는 보다 간단한 방법이므로 대부분의 사용자가 선호합니다.

## <a name="identifying-customer-data"></a>고객 데이터 식별

Azure Time Series Insights에서는 개인 데이터를 Time Series Insights의 관리자 및 사용자와 연결된 데이터로 간주합니다. Time Series Insights는 환경에 대한 액세스 권한이 있는 사용자의 Azure Active Directory 개체 ID를 저장합니다. Azure Portal에 사용자 전자 메일 주소가 표시되지만 이러한 전자 메일 주소는 Time Series Insights 내에 저장되지 않으며, Azure Active Directory에서 Azure Active Directory 개체 ID를 사용하여 동적으로 조회됩니다.

## <a name="deleting-customer-data"></a>고객 데이터 삭제

테넌트 관리자는 Azure Portal을 사용하여 고객 데이터를 삭제할 수 있습니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

그러나 포털을 통해 고객 데이터를 삭제하려면 먼저 Azure Portal 내의 Time Series Insights 환경에서 사용자의 액세스 정책을 제거해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 Time Series Insights 환경에 대한 데이터 액세스 권한 부여](time-series-insights-data-access.md)를 참조하세요.

또한 REST API를 사용하여 액세스 정책 삭제 작업을 수행할 수 있습니다. 자세한 내용은 [액세스 정책 - 삭제](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete)를 참조하세요.

Time Series Insights는 Azure Portal에서 정책 블레이드와 통합됩니다. Time Series Insights와 정책 블레이드 모두에서 서비스 내에 저장된 사용자 데이터를 보고, 내보내고, 삭제할 수 있습니다. Azure Portal의 정책 블레이드 내에서 삭제 작업을 수행하면 Time Series Insights 내에서 사용자 데이터가 삭제됩니다. 예를 들어, 사용자에게 저장된 개인 쿼리가 있는 경우 이 쿼리는 Time Series Insights 탐색기에서 영구적으로 삭제됩니다. 반면, 사용자에게 저장된 공유 쿼리가 있는 경우 쿼리는 유지되지만 사용자 정보는 영구적으로 삭제됩니다. 다음 참고 사항에는 이러한 작업을 수행하는 방법에 대한 지침이 포함되어 있습니다.

## <a name="exporting-customer-data"></a>고객 데이터 내보내기

데이터를 삭제하는 것과 마찬가지로, 테넌트 관리자는 Azure Portal의 정책 블레이드에서 Time Series Insights에 저장된 데이터를 보고 내보낼 수 있습니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

테넌트 관리자인 경우 Azure Portal의 Time Series Insights 환경 내에서 데이터 액세스 정책을 볼 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 Time Series Insights 환경에 대한 데이터 액세스 권한 부여](time-series-insights-data-access.md)를 참조하세요.

또한 제공된 REST API에서 "환경별 목록" 작업을 사용하여 액세스 정책에 대한 내보내기 작업을 수행할 수 있습니다. 자세한 내용은 [액세스 정책 - 환경별 목록](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment)을 참조하세요.

## <a name="to-delete-data-stored-within-time-series-insights"></a>Time Series Insights 내에 저장된 데이터를 삭제하려면

개인 데이터는 사용자 및 관리자 데이터와 다르게 Time Series Insights 저장소로 이동할 수도 있습니다. Time Series Insights에 저장된 데이터를 개인 데이터로 간주하는 경우 다음 단계를 사용하여 이 데이터를 내보내고 삭제할 수 있습니다.

**데이터 보기 및 내보내기**

Time Series Insights 내에 저장된 데이터를 보고 내보내려면 해당 데이터를 검색해야 합니다. Time Series Insights 탐색기 또는 Time Series Insights 쿼리 API를 사용하여 데이터를 보고 내보낼 수 있습니다. Time Series Insights 탐색기를 사용하여 데이터를 보고 내보내려면 먼저 검색을 수행하여 해당 사용자 데이터를 찾습니다. 검색 후 차트를 마우스 오른쪽 단추로 클릭하고 **이벤트 탐색**을 선택합니다. 이벤트 눈금이 표시되고 데이터를 CSV 및 JSON으로 내보낼 수 있는 옵션이 제공됩니다.

자세한 내용은 [Azure Time Series Insights 탐색기](time-series-insights-explorer.md)를 참조하세요.

**데이터 삭제**

현재 Time Series Insights는 세분화된 데이터 삭제를 지원하지 않습니다. 그러나 보존 정책을 구성하여 Time Series Insights 내에 저장된 고객 데이터를 제거하는 기능을 제공합니다. 삭제 요구 사항에 적합한 날짜 수로 전체 Time Series Insights 환경의 보존 기간을 조정할 수 있습니다.

자세한 내용은 [Time Series Insights에서 보존 구성](time-series-insights-how-to-configure-retention.md)을 참조하세요.