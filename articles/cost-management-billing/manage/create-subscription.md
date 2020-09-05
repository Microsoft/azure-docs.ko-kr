---
title: 추가 Azure 구독 만들기
description: Azure Portal에서 새 Azure 구독을 추가하는 방법을 알아봅니다. 청구 계정 양식에 대한 정보를 확인하고 사용 가능한 추가 리소스를 살펴봅니다.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: aa8cf0d2a48c75b71895eb75db362c4ec4e291c5
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88925048"
---
# <a name="create-an-additional-azure-subscription"></a>추가 Azure 구독 만들기

Azure Portal에서 [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft 고객 계약](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) 또는 [Microsoft 파트너 계약](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) 청구 계정에 대한 추가 구독을 만들 수 있습니다. 구독 제한을 피하거나, 보안을 위한 별도의 환경을 만들거나, 규정 준수를 위한 데이터 격리 목적으로 추가 구독이 필요할 수 있습니다.

MOSP(Microsoft 온라인 서비스 프로그램) 청구 계정이 있는 경우 [Azure 가입 포털](https://account.azure.com/signup?offer=ms-azr-0003p)에서 추가 구독을 만들 수 있습니다.

청구 계정에 대해 자세히 알아보고 청구 계정 유형을 확인하려면 [Azure Portal에서 청구 계정 보기](view-all-accounts.md)를 참조하세요.

## <a name="permission-required-to-create-azure-subscriptions"></a>Azure 구독을 만드는 데 필요한 권한

구독을 만들려면 다음 권한이 필요합니다.

|청구 계정  |사용 권한  |
|---------|---------|
|EA(기업 계약) |  기업계약 등록에 대한 계정 소유자 역할 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](understand-ea-roles.md)를 참조하세요.    |
|MCA(Microsoft 고객 계약) |  청구서 섹션, 청구 프로필 또는 청구 계정에 대한 소유자 또는 기여자 역할 또는 청구서 섹션의 Azure 구독 작성자 역할  자세한 내용은 [구독 청구 역할 및 작업](understand-mca-roles.md#subscription-billing-roles-and-tasks)을 참조하세요.    |
|MPA(Microsoft 파트너 계약) |   CSP 파트너 조직의 글로벌 관리자 및 관리 에이전트 역할. 자세히 알아보려면 [파트너 센터 - 사용자 역할 및 사용 권한 할당](https://docs.microsoft.com/partner-center/permissions-overview)을 참조하세요.  Azure 구독을 만들려면 사용자가 파트너 테넌트에 서명해야 합니다.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Azure Portal에 구독 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **구독**을 검색합니다.

   ![포털의 구독 검색을 보여 주는 스크린샷](./media/create-subscription/billing-search-subscription-portal.png)

1. **추가**를 선택합니다.

   ![구독 보기의 추가 단추를 보여 주는 스크린샷](./media/create-subscription/subscription-add.png)

1. 여러 청구 계정에 액세스할 수 있는 경우 구독을 만들려는 청구 계정을 선택합니다.

1. 양식을 채우고 **만들기**를 클릭합니다. 아래 표에는 각 청구 계정 유형에 대한 양식의 필드가 나열되어 있습니다.

**기업 계약**

|필드  |정의  |
|---------|---------|
|Name     | Azure Portal에서 구독을 쉽게 식별하는 데 도움이 되는 표시 이름입니다.  |
|제안     | 개발 또는 테스트 작업에 이 구독을 사용하려는 경우 EA 개발/테스트를 선택하고 그렇지 않은 경우 Microsoft Azure 엔터프라이즈를 사용합니다. DevTest 제품은 EA 개발/테스트 구독을 만들기 위해 등록 계정에 대해 활성화되어야 합니다.|

**Microsoft 고객 계약**

|필드  |정의  |
|---------|---------|
|청구 프로필     | 구독에 대한 요금은 선택한 청구 프로필로 청구됩니다. 하나의 청구 프로필에만 액세스할 수 있는 경우 선택 영역이 회색으로 표시됩니다.     |
|청구서 섹션     | 구독에 대한 요금은 청구 프로필 청구서의 이 섹션에 표시됩니다. 하나의 청구서 섹션에만 액세스할 수 있는 경우 선택 영역이 회색으로 표시됩니다.  |
|계획     | 개발 또는 테스트 작업에 이 구독을 사용하려는 경우 DevTest용 Microsoft Azure 플랜을 선택하고 그렇지 않은 경우 Microsoft Azure 플랜을 사용합니다. 청구 프로필에 대해 하나의 계획만 활성화되는 경우 선택 영역이 회색으로 표시됩니다.  |
|Name     | Azure Portal에서 구독을 쉽게 식별하는 데 도움이 되는 표시 이름입니다.  |

**Microsoft 파트너 계약**

|필드  |정의  |
|---------|---------|
|Customer    | 선택한 고객에 대한 구독이 생성됩니다. 고객이 한 명만 있는 경우 선택 영역이 회색으로 표시됩니다.  |
|Reseller    | 고객에게 서비스를 제공하는 재판매인입니다. 이 필드는 CSP 2계층 모델에서 간접 공급자에만 적용되는 선택적 필드입니다. |
|Name     | Azure Portal에서 구독을 쉽게 식별하는 데 도움이 되는 표시 이름입니다.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>프로그래밍 방식으로 추가 Azure 구독 만들기

프로그래밍 방식으로 추가 구독을 만들 수도 있습니다. 자세한 내용은 [프로그래밍 방식으로 Azure 구독 만들기](programmatically-create-subscription.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 구독 관리자 추가 또는 변경](add-change-subscription-administrator.md)
- [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [리소스 조직 및 관리를 위한 관리 그룹 만들기](../../governance/management-groups/create.md)
- [Azure 구독 취소](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
