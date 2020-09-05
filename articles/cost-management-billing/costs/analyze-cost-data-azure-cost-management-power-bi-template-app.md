---
title: Power BI 앱을 사용하여 Azure 비용 분석
description: 이 문서에서는 Azure Cost Management Power BI 앱을 설치하고 사용하는 방법에 대해 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 2a66ef2f91715180ae84036fc7eead759e7ee00f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685054"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Azure Cost Management Power BI 앱을 사용하여 EA(기업계약) 비용 분석

이 문서에서는 Azure Cost Management Power BI 앱을 설치하고 사용하는 방법에 대해 설명합니다. 이 앱은 Power BI에서 Azure 비용을 분석하고 관리하는 데 도움이 됩니다. 이 앱을 사용하여 비용, 사용 추세를 모니터링하고 비용 최적화 옵션을 식별하여 비용을 줄일 수 있습니다.

앱을 있는 그대로 사용하거나 기본 필터, 보기 및 시각화를 확장하여 요구 사항에 맞게 사용자 지정할 수 있습니다. 그런 다음, 추가 데이터를 조인하고 사용자 지정 보고서를 만들어 전반적인 비즈니스 비용을 전체적으로 파악할 수 있습니다.

Azure Cost Management Power BI 앱은 현재 [기업계약](https://azure.microsoft.com/pricing/enterprise-agreement/)을 체결한 고객만 지원합니다.

> [!NOTE]
> Power BI 템플릿 앱은 PBIX 파일 다운로드를 지원하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 앱을 설치하고 사용하기 위한 [Power BI Pro 라이선스](/power-bi/service-self-service-signup-for-power-bi)
- 데이터에 연결하려면 [엔터프라이즈 관리자](../manage/understand-ea-roles.md) 계정을 사용해야 합니다.

## <a name="installation-steps"></a>설치 단계

앱을 설치하려면 다음을 수행합니다.

1. [Azure Cost Management Power BI 앱](https://aka.ms/costmgmt/ACMApp)을 엽니다.
2. Power BI AppSource 페이지에서 **지금 가져오기**를 선택합니다.
3. **계속**을 선택하여 사용 약관 및 개인정보처리방침에 동의합니다.
4. **이 Power BI 앱을 설치하시겠습니까?** 상자에서 **설치**를 선택합니다.
5. 필요한 경우 작업 영역을 만들고 **계속**을 선택합니다.
6. 설치가 완료되면 새 앱이 준비되었다는 알림이 표시됩니다.
7. **앱으로 이동**을 선택합니다.
8. **새 앱 시작**의 **데이터 연결** 아래에서 **연결**을 선택합니다.  
  ![새 앱 시작 - 연결](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. 표시되는 대화 상자의 **BillingProfileIdOrEnrollmentNumber**에서 EA 등록 번호를 입력합니다. 가져올 데이터의 개월 수를 지정합니다. 기본 **범위** 값의 **등록 번호**를 그대로 유지하고, **다음**을 선택합니다.  
  ![EA 등록 정보 입력](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. 다음 대화 상자에서 Azure에 연결하고 예약 인스턴스 추천 사항에 필요한 데이터를 가져옵니다. *기본값을 구성된 대로 그대로 유지하고*, **로그인**을 선택합니다.  
  ![Azure에 연결](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. 최종 설치 단계가 EA 등록에 연결되며, [엔터프라이즈 관리자](../manage/understand-ea-roles.md) 계정을 요구합니다. **로그인**을 선택하여 EA 등록으로 인증합니다. 이 단계에서는 Power BI에서 데이터 새로 고침 작업도 시작합니다.  
  ![EA 등록에 연결](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > 데이터 새로 고침 프로세스를 완료하는 데 시간이 오래 걸릴 수 있습니다. 소요 시간은 지정된 개월 수 및 동기화하는 데 필요한 데이터 양에 따라 달라집니다.
12. 데이터 새로 고침 상태를 확인하려면 작업 영역에서 **데이터 세트** 탭을 선택합니다. 새로 고친 타임스탬프 옆에 표시됩니다. 여전히 업데이트 중인 경우 새로 고침이 진행되고 있음을 나타내는 표시기가 표시됩니다.  
  ![데이터 새로 고침](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

데이터 새로 고침이 완료되면 Azure Cost Management 앱을 선택하여 미리 만든 보고서를 표시합니다.

## <a name="reports-available-with-the-app"></a>앱에서 사용할 수 있는 보고서

앱에서 사용할 수 있는 보고서는 다음과 같습니다.

**시작** - 설명서에 대한 유용한 링크와 피드백을 제공하기 위한 링크를 제공합니다.

**계정 개요** - 보고서에서 월별로 요약되는 정보는 다음과 같습니다.

- 크레딧 대비 요금
- 새 구매
- Azure Marketplace 요금
- 초과분 요금 및 총 요금

**구독 및 리소스 그룹별 사용량** - 구독 및 리소스 그룹별로 비용을 보여 주는 시간 경과별 비용 보기 및 차트를 제공합니다.

**서비스별 사용량** - MeterCategory의 사용 시간에 대한 보기를 제공합니다. 사용량 데이터를 추적하고 이상 항목을 분석하여 사용량 급증 또는 급감을 파악할 수 있습니다.

**상위 5개 사용량 영향 요소** - 보고서에서 상위 5개의 MeterCategory 및 해당 MeterName을 기준으로 필터링된 비용 요약을 표시합니다.

**Windows Server AHB 사용량** - 보고서에서 Azure 하이브리드 혜택을 사용하도록 설정된 가상 머신의 수를 표시합니다. 가상 머신에서 사용하는 코어/vCPU 수도 표시합니다.

![Azure 하이브리드 혜택 전체 보고서](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

이 보고서에서도 하이브리드 혜택을 **사용하도록 설정되지만** vCPU가 8개 _미만_인 Windows VM을 식별합니다. 또한 8개 _이상_의 vCPU가 포함된 하이브리드 혜택을 **사용하도록 설정되지 않은** 위치도 보여 줍니다. 이 정보는 하이브리드 혜택을 완벽하게 사용하는 데 도움이 됩니다. 이 혜택을 가장 저렴한 가상 머신에 적용하여 잠재적인 절감 효과를 극대화할 수 있습니다.

![Azure 하이브리드 혜택 - vCPU가 8개 미만이고 이를 사용하도록 설정되지 않음](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

**RI 지불 거절** - 이 보고서는 지역, 구독, 리소스 그룹 또는 리소스별로 적용되는 RI(예약 인스턴스) 혜택의 위치와 양을 파악하는 데 도움이 됩니다. 이 보고서는 분할 상환 사용 데이터를 사용하여 보기를 표시합니다.

필터를 _chargetyype_에 적용하여 RI 미달 사용 데이터를 확인할 수 있습니다.

분할 상환 데이터에 대한 자세한 내용은 [기업계약 예약 비용 및 사용량 가져오기](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)를 참조하세요.

**RI 절약** - 보고서에서 구독, 리소스 그룹 및 리소스 수준 예약 기준으로 계산된 절감액을 표시합니다. 표시되는 항목은 다음과 같습니다.

- 예약 비용
- 예약이 사용량에 적용되지 않은 경우 예상 주문형 비용
- 예약 기준으로 계산된 비용 절감액

 이 보고서는 총 절감액에서 미달 사용 예약 폐기 비용을 뺍니다. 예약이 없으면 폐기 비용이 발생하지 않습니다.

분할 상환 사용 데이터를 사용하여 데이터를 만들 수 있습니다.

<a name="shared-recommendation"></a>
**VM RI 적용 범위(공유 추천 사항)** - 보고서는 선택한 기간 동안의 주문형 VM 사용량과 RI VM 사용량으로 분할되어 있습니다. 공유 범위의 VM RI 구매에 대한 추천 사항을 제공합니다.

보고서를 사용하려면 드릴다운 필터를 선택합니다.

![VM RI 적용 범위 보고서 – 드릴다운 선택](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

분석하려는 지역을 선택합니다. 그런 다음, 인스턴스 크기 유연성 그룹 등을 선택합니다.

각 드릴다운 수준에 대해 보고서에 적용되는 필터는 다음과 같습니다.

- 오른쪽의 적용 범위 데이터는 주문형 요율로 청구되는 사용량과 예약에 적용된 양을 보여 주는 필터입니다.
- 추천 사항도 필터링됩니다.

추천 사항 테이블에는 사용된 VM 크기에 따라 예약 구매에 대한 추천 사항이 나와 있습니다.

_정규화된 크기_ 및 _추천되는 정규화된 수량_ 값은 인스턴스 크기 유연성 그룹의 구매를 최소 크기로 정규화하는 데 도움이 됩니다. 이 정보는 인스턴스 크기 유연성 그룹의 모든 크기에 대해 하나의 예약만 구매하려는 경우에 유용합니다.

![RI 추천 사항](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

**VM RI 적용 범위(단일 추천 사항)** - 보고서는 선택한 기간 동안의 주문형 VM 사용량과 RI VM 사용량으로 분할되어 있습니다. 구독 범위의 VM RI 구매에 대한 추천 사항을 제공합니다.

보고서를 사용하는 방법에 대한 자세한 내용은 [VM RI 적용 범위(공유 추천 사항)](#shared-recommendation) 섹션을 참조하세요.

**RI 구매** - 보고서에서 지정된 기간 동안의 RI 구매를 표시합니다.

**가격표** - 보고서에서 청구 계정 또는 EA 등록과 관련된 자세한 가격 목록을 표시합니다.

## <a name="troubleshoot-problems"></a>문제 해결

Power BI 앱에 문제가 있는 경우 다음과 같은 문제 해결 정보가 도움이 될 수 있습니다.

### <a name="error-processing-the-data-in-the-dataset"></a>데이터 세트에서 데이터를 처리하는 동안 오류 발생

다음을 나타내는 오류가 발생할 수 있습니다.

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

`<TableName>` 대신 테이블 이름이 표시됩니다.

#### <a name="cause"></a>원인

Cost Management 연결에서 `Enrollment Number`의 기본 **범위** 값이 변경되었습니다.

#### <a name="solution"></a>해결 방법

Cost Management에 다시 연결하고 **범위** 값을 `Enrollment Number`로 설정합니다. 조직의 등록 번호를 입력하지 말고 다음 이미지에 표시된 대로 정확하게 `Enrollment Number`를 입력합니다.

![EA 등록 정보 입력](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  

### <a name="budgetamount-error"></a>BudgetAmount 오류

다음을 나타내는 오류가 발생할 수 있습니다.

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>원인

이 오류는 기본 메타데이터를 사용하는 버그로 인해 발생합니다. 이 문제는 Azure Portal의 **Cost Management > 예산**에서 사용할 수 있는 예산이 없기 때문에 발생합니다. 버그 수정은 Power BI Desktop 및 Power BI 서비스에 배포되는 과정에 있습니다. 

#### <a name="solution"></a>해결 방법

- 버그가 해결될 때까지 청구 계정/EA 등록 수준에서 Azure Portal에서 테스트 예산을 추가하여 문제를 해결할 수 있습니다. 테스트 예산이 Power BI와의 연결을 차단 해제합니다. 예산 만들기에 대한 자세한 내용은 [자습서: Azure 예산 만들기 및 관리](tutorial-acm-create-budgets.md)를 참조하세요.


### <a name="invalid-credentials-for-azureblob-error"></a>AzureBlob 오류에 대한 잘못된 자격 증명

다음을 나타내는 오류가 발생할 수 있습니다.

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>원인

AutoFitComboMeter Blob 연결에 대한 인증 방법을 변경하는 경우 이 오류가 발생합니다.

#### <a name="solution"></a>해결 방법

1. 데이터에 연결합니다.
1. EA 등록 및 개월 수를 입력한 후 인증 방법에 대해 **익명**, 개인 정보 수준 설정에 대해 **없음**의 기본값을 그대로 유지합니다.  
  ![Azure에 연결](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png)  
1. 다음 페이지에서 인증 방법에 대해 **OAuth2**를 개인 정보 수준에 대해 **없음** 세트를 설정합니다. 그런 다음, 로그인하여 등록으로 인증합니다. 이 단계에서는 Power BI 데이터 새로 고침을 시작합니다.


## <a name="data-reference"></a>데이터 참조

앱을 통해 사용할 수 있는 데이터를 요약한 정보는 다음과 같습니다. 데이터 필드 및 값에 대한 자세한 정보를 제공하기 위한 API 링크도 있습니다.

| **테이블 참조** | **설명** |
| --- | --- |
| **AutoFitComboMeter** | RI 추천 사항 및 사용량을 인스턴스 패밀리 그룹에서 가장 작은 크기로 정규화하기 위해 앱에 포함된 데이터입니다. |
| [**잔액 요약**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | 기업계약의 잔액에 대한 요약입니다. |
| [**예산**](/rest/api/consumption/budgets/get#definitions) | 기존 예산 목표와 비교하여 실제 비용 또는 사용량을 확인할 수 있는 예산 세부 정보입니다. |
| [**가격표**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | 제공된 청구 프로필 또는 EA 등록에 적용되는 측정기 요율입니다. |
| [**RI 요금**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | 지난 24개월 동안의 예약 인스턴스와 관련된 요금입니다. |
| [**RI 추천 사항(공유)** ](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | 지난 7, 30일 또는 60일 동안의 모든 구독 사용 추세에 기반한 예약 인스턴스 구매 추천 사항입니다. |
| [**RI 추천 사항(단일)** ](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | 지난 7, 30일 또는 60일 동안의 단일 구독 사용 추세에 기반한 예약 인스턴스 구매 추천 사항입니다. |
| [**RI 사용량 세부 정보**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | 지난 한 달 동안의 기존 예약 인스턴스에 대한 사용량 세부 정보입니다. |
| [**RI 사용 요약**](/rest/api/consumption/reservationssummaries/list) | 일일 Azure 예약 사용률입니다. |
| [**사용량 세부 정보**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | EA 등록에서 지정된 청구 프로필의 사용량과 예상 요금에 대한 분석입니다. |
| [**분할 상환 사용량 세부 정보**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | EA 등록에서 지정된 청구 프로필의 사용량과 분할 상환 요금에 대한 분석입니다. |

## <a name="next-steps"></a>다음 단계

데이터 구성, 새로 고침, 보고서 공유 및 추가 보고서 사용자 지정에 대한 자세한 내용은 다음 문서를 참조하세요.

- [예약된 새로 고침 구성](/power-bi/refresh-scheduled-refresh)
- [동료 및 다른 사용자와 Power BI 대시보드 및 보고서 공유](/power-bi/service-share-dashboards)
- [Power BI 서비스의 보고서 및 대시보드에 자신과 다른 사용자 구독](/power-bi/service-report-subscribe)
- [Power BI 서비스에서 Power BI Desktop으로 보고서 다운로드](/power-bi/service-export-to-pbix)
- [Power BI 서비스 및 Power BI 데스크톱에서 보고서 저장](/power-bi/service-report-save)
- [데이터 세트를 가져와 Power BI 서비스에서 보고서 만들기](/power-bi/service-report-create-new)
