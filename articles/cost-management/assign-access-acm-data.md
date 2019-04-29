---
title: Azure Cost Management 데이터에 대한 액세스 할당 | Microsoft Docs
description: 이 문서에서는 다양한 액세스 범위에 맞게 Azure Cost Management 데이터에 대한 권한을 할당하는 방법을 안내합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: 24a77561d08cc3db5356dd0e931f62bf2d16406d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577110"
---
# <a name="assign-access-to-cost-management-data"></a>Cost Management 데이터에 대한 액세스 할당

Azure 엔터프라이즈 계약을 맞은 사용자는 Azure Portal과 EA(기업계약) 포털에 부여된 권한 조합에 따라 Azure Cost Management 데이터에 대한 사용자의 액세스 수준이 정의됩니다. 다른 Azure 계정 유형을 사용하는 사용자의 액세스 수준은 Cost Management 데이터입니다. 이 문서에서는 Cost Management 데이터에 대한 액세스 권한을 할당하는 방법을 안내합니다. 권한 조합이 할당되면 사용자는 액세스 권한이 있는 범위와 Azure Portal에서 선택한 범위에 따라 Cost Management에서 데이터를 봅니다.

사용자가 선택한 범위는 Cost Management 전체에서 데이터 통합을 제공하고 비용 정보에 대한 액세스를 제어하는 데 사용됩니다. 범위를 사용하는 경우 사용자는 여러 개의 범위를 선택하지 않습니다. 대신 자식 범위가 롤업하는 더 큰 범위를 선택한 다음, 보려는 대상으로 필터링합니다. 일부 사용자는 자식 범위가 롤업하는 부모 범위에 액세스할 수 없으므로 데이터 통합을 이해해야 합니다.

## <a name="cost-management-scopes"></a>Cost Management 범위

Cost Management는 다양한 유형의 Azure 계정을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](understand-cost-mgt-data.md)를 참조하세요. 계정 유형에 따라 사용할 수 있는 범위가 결정됩니다.

### <a name="azure-ea-subscription-scopes"></a>Azure EA 구독 범위

Azure EA 구독의 비용 데이터를 보려면 사용자에게 적어도 다음 범위 중 하나 이상에 대한 읽기 액세스 권한이 있어야 합니다.

| **범위** | **정의되는 위치** | **데이터를 보는 데 필요한 액세스 권한** | **필수 구성 요소 EA 설정** | **데이터 통합 위치** |
| --- | --- | --- | --- | --- |
| 청구 계정<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | 엔터프라이즈 관리자 | 없음 | 기업계약의 모든 구독 |
| department | [https://ea.azure.com](https://ea.azure.com/) | 부서 관리자 | **DA 요금 보기** 사용 | 부서에 연결된 등록 계정에 속하는 모든 구독 |
| 등록 계정<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | 계정 소유자 | **AO 요금 보기** 사용 | 등록 계정의 모든 구독 |
| 관리 그룹 | [https://portal.azure.com](https://portal.azure.com/) | Cost Management 읽기 권한자(또는 읽기 권한자) | **AO 요금 보기** 사용 | 관리 그룹 아래의 모든 구독 |
| 구독 | [https://portal.azure.com](https://portal.azure.com/) | Cost Management 읽기 권한자(또는 읽기 권한자) | **AO 요금 보기** 사용 | 구독의 모든 리소스/리소스 그룹 |
| 리소스 그룹 | [https://portal.azure.com](https://portal.azure.com/) | Cost Management 읽기 권한자(또는 읽기 권한자) | **AO 요금 보기** 사용 | 리소스 그룹의 모든 리소스 |

<sup>1</sup> 청구 계정은 기업 계약 또는 등록이라고도 합니다.

<sup>2</sup> 등록 계정은 계정 소유자라고도 합니다.

다음 다이어그램에서는 역할 및 EA 포털 설정을 사용하여 Cost Management 범위 간의 관계를 보여줍니다.

![역할 및 EA 포털 설정을 사용하여 Cost Management 범위 간의 관계를 보여주는 다이어그램](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

EA 포털에서 **DA 요금 보기**가 비활성화되면 부서 및 계정의 요금을 보려고 할 때 *조직에서 비용을 사용할 수 없도록 설정됨*이라는 메시지가 표시됩니다.

마찬가지로, EA 포털에서 **AO 요금 보기**가 비활성화되면 등록 계정, 관리 그룹, 구독 및 리소스 그룹의 비용을 보려고 할 때 *조직에서 비용을 사용할 수 없도록 설정됨*이라는 메시지가 표시됩니다.

## <a name="other-azure-account-scopes"></a>다른 Azure 계정 범위

다른 Azure 구독의 비용 데이터를 보려면 사용자에게 적어도 다음 범위 중 하나 이상에 대한 읽기 액세스 권한이 있어야 합니다.

- Azure 계정
- 관리 그룹
- 리소스 그룹

## <a name="enable-access-to-costs-in-the-ea-portal"></a>EA 포털에서 비용에 대한 액세스를 사용하도록 설정

부서 범위에는 EA 포털에서 **사용**으로 설정된 **DA 요금 보기** 옵션이 필요합니다. 다른 모든 범위에는 EA 포털에서 **사용**으로 설정된 **AO 요금 보기** 옵션이 필요합니다.

옵션을 사용하도록 설정하려면 다음을 수행합니다.

1. [https://ea.azure.com](https://ea.azure.com)의 EA 포털에 엔터프라이즈 관리자 계정으로 로그인합니다.
2. 왼쪽 창에서 **관리**를 선택합니다.
3. 액세스를 제공하려는 Cost Management 범위의 경우 요금 옵션으로 **DA 요금 보기** 및/또는 **AO 요금 보기**를 사용하도록 설정합니다.  
    ![DA 및 AO 보기 요금 옵션이 표시된 등록 탭](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

요금 보기 옵션을 사용하도록 설정하면 대부분의 범위에 Azure Portal의 RBAC(역할 기반 액세스 제어) 권한 구성도 필요합니다.

## <a name="enterprise-administrator-role"></a>엔터프라이즈 관리자 역할

기본적으로 엔터프라이즈 관리자는 청구 계정(기업 계약/등록) 및 자식 범위인 다른 모든 범위에 액세스할 수 있습니다. 엔터프라이즈 관리자는 다른 사용자의 범위에 대한 액세스 권한을 할당합니다. 비즈니스 연속성을 위한 모범 사례로, 엔터프라이즈 관리자 액세스 권한이 있는 두 명의 사용자가 있어야 합니다. 다음 섹션은 다른 사용자의 범위에 대한 액세스 권한을 할당하는 엔터프라이즈 관리자의 연습 예제입니다.

## <a name="assign-billing-account-scope-access"></a>청구 계정 범위 액세스 할당

청구 계정 범위에 액세스하려면 EA 포털에서 엔터프라이즈 관리자 권한이 필요합니다. 엔터프라이즈 관리자는 전체 EA 등록 또는 여러 등록에서 비용을 볼 수 있습니다. Azure Portal에서는 청구 계정 범위에 대한 어떠한 작업도 필요하지 않습니다.

1. [https://ea.azure.com](https://ea.azure.com)의 EA 포털에 엔터프라이즈 관리자 계정으로 로그인합니다.
2. 왼쪽 창에서 **관리**를 선택합니다.
3. **등록** 탭에서 관리하려는 등록을 선택합니다.  
    ![EA 포털에서 등록 선택](./media/assign-access-acm-data/ea-portal.png)
4. **+ 관리자 추가**를 클릭합니다.
5. [관리자 추가] 상자에서 인증 유형을 선택하고 사용자의 이메일 주소를 입력합니다.
6. 사용자에게 비용 및 사용량 데이터에 대한 읽기 전용 액세스 권한이 있어야 하는 경우 **읽기 전용** 아래에서 **예**를 선택합니다.  그렇지 않은 경우 **아니요**를 선택합니다.
7. **추가**를 클릭하여 계정을 만듭니다.  
    ![관리자 추가 상자에 표시된 예제 정보](./media/assign-access-acm-data/add-admin.png)

새 사용자가 Cost Management의 데이터에 액세스하는 데 최대 30분이 걸릴 수 있습니다.

### <a name="assign-department-scope-access"></a>부서 범위 액세스 할당

부서 범위에 액세스하려면 EA 포털에서 부서 관리자(DA 요금 보기) 액세스 권한이 필요합니다. 부서 관리자는 한 부서 또는 여러 부서와 연결된 비용 및 사용량 데이터를 볼 수 있습니다. 부서에 대한 데이터에는 부서에 연결된 등록 계정에 속한 모든 구독이 포함됩니다. Azure Portal에서는 어떠한 작업도 필요하지 않습니다.

1. [https://ea.azure.com](https://ea.azure.com)의 EA 포털에 엔터프라이즈 관리자 계정으로 로그인합니다.
2. 왼쪽 창에서 **관리**를 선택합니다.
3. **등록** 탭에서 관리하려는 등록을 선택합니다.
4. **부서** 탭을 클릭한 다음, **관리자 추가**를 클릭합니다.
5. [부서 관리자 추가] 상자에서 인증 유형을 선택한 다음, 사용자의 이메일 주소를 입력합니다.
6. 사용자에게 비용 및 사용량 데이터에 대한 읽기 전용 액세스 권한이 있어야 하는 경우 **읽기 전용** 아래에서 **예**를 선택합니다.  그렇지 않은 경우 **아니요**를 선택합니다.
7. 부서 관리자 권한을 부여하려는 부서를 선택합니다.
8. **추가**를 클릭하여 계정을 만듭니다.  
    ![부서 관리자 추가 상자에 필요한 정보 입력](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>등록 계정 범위 액세스 할당

등록 계정 범위에 액세스하려면 EA 포털에서 계정 소유자(AO 요금 보기) 액세스 권한이 있어야 합니다. 계정 소유자는 해당 등록 계정에서 생성된 구독과 관련된 비용 및 사용량 데이터를 볼 수 있습니다. Azure Portal에서는 어떠한 작업도 필요하지 않습니다.

1. [https://ea.azure.com](https://ea.azure.com)의 EA 포털에 엔터프라이즈 관리자 계정으로 로그인합니다.
2. 왼쪽 창에서 **관리**를 선택합니다.
3. **등록** 탭에서 관리하려는 등록을 선택합니다.
4. **계정** 탭을 클릭한 다음, **계정 추가**를 클릭합니다.
5. [계정 추가] 상자에서 계정을 연결하려는 **부서**를 선택하거나 할당되지 않은 상태로 그대로 둡니다.
6. 인증 유형을 선택하고, 계정 이름을 입력합니다.
7. 사용자의 이메일 주소를 입력한 다음, 선택적으로 비용 센터를 입력합니다.
8. **추가**를 클릭하여 계정을 만듭니다.  
    ![등록 계정에 대한 계정 추가 상자에 필요한 정보 입력](./media/assign-access-acm-data/add-account.png)

위의 단계를 완료하면 사용자 계정이 Enterprise Portal의 등록 계정이 되고 구독을 만들 수 있습니다. 사용자는 자신이 만든 구독에 대한 비용 및 사용량 데이터에 액세스할 수 있습니다.

## <a name="assign-management-group-scope-access"></a>관리 그룹 범위 액세스 할당

관리 그룹 범위에 액세스하려면 Cost Management 읽기 권한자(또는 읽기 권한자) 이상의 권한이 필요합니다. Azure Portal에서 관리 그룹에 대한 권한을 구성할 수 있습니다. 다른 사람들의 액세스가 가능하도록 설정하려면 관리 그룹에 대해 사용자 액세스 관리자(또는 소유자) 이상의 권한이 있어야 합니다. 그리고 Azure EA 계정의 경우 EA 포털에서 **AO 요금 보기** 설정을 사용하도록 설정해야 합니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. 사이드바에서 **모든 서비스**를 선택하고, _관리 그룹_을 검색한 다음,  **관리 그룹**을 선택합니다.
3. 계층 구조에서 관리 그룹을 선택합니다.
4. 관리 그룹 이름 옆에 있는 **세부 정보**를 클릭합니다.
5. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.
6. **추가**를 클릭합니다.
7. **역할** 아래에서 **Cost Management 읽기 권한자**를 선택합니다.
8. **다음에 대한 액세스 할당** 아래에서 **Azure AD, 사용자, 그룹 또는 애플리케이션**을 선택합니다.
9. 액세스 권한을 할당하려면 사용자를 검색하여 선택합니다.
10. **저장**을 클릭합니다.  
    ![관리 그룹에 대한 사용 권한 추가 상자의 예제 정보](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>구독 범위 액세스 할당

구독에 액세스하려면 Cost Management 읽기 권한자(또는 읽기 권한자) 이상의 권한이 필요합니다. Azure Portal에서 구독에 대한 권한을 구성할 수 있습니다. 다른 사람들의 액세스가 가능하도록 설정하려면 구독에 대해 사용자 액세스 관리자(또는 소유자) 이상의 권한이 있어야 합니다. 그리고 Azure EA 계정의 경우 EA 포털에서 **AO 요금 보기** 설정을 사용하도록 설정해야 합니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. 사이드바에서 **모든 서비스**를 선택하고, _구독_을 검색한 다음, **구독**을 선택합니다.
3. 구독을 선택합니다.
4. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.
5. **추가**를 클릭합니다.
6. **역할** 아래에서 **Cost Management 읽기 권한자**를 선택합니다.
7. **다음에 대한 액세스 할당** 아래에서 **Azure AD, 사용자, 그룹 또는 애플리케이션**을 선택합니다.
8. 액세스 권한을 할당하려면 사용자를 검색하여 선택합니다.
9. **저장**을 클릭합니다.

## <a name="assign-resource-group-scope-access"></a>리소스 그룹 범위 액세스 할당

리소스 그룹에 액세스하려면 Cost Management 읽기 권한자(또는 읽기 권한자) 이상의 권한이 필요합니다. Azure Portal에서 리소스 그룹에 대한 권한을 구성할 수 있습니다. 다른 사람들의 액세스가 가능하도록 설정하려면 리소스 그룹에 대해 사용자 액세스 관리자(또는 소유자) 이상의 권한이 있어야 합니다. 그리고 Azure EA 계정의 경우 EA 포털에서 **AO 요금 보기** 설정을 사용하도록 설정해야 합니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. 사이드바에서 **모든 서비스** 를 선택하고, _리소스 그룹_을 검색한 다음, **리소스 그룹**을 선택합니다.
3. 리소스 그룹을 선택합니다.
4. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.
5. **추가**를 클릭합니다.
6. **역할** 아래에서 **Cost Management 읽기 권한자**를 선택합니다.
7. **다음에 대한 액세스 할당** 아래에서 **Azure AD, 사용자, 그룹 또는 애플리케이션**을 선택합니다.
8. 액세스 권한을 할당하려면 사용자를 검색하여 선택합니다.
9. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](quick-acm-cost-analysis.md)을 참조하세요.
