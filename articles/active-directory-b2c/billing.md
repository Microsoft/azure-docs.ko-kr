---
title: Azure Active Directory B2C에 대한 청구 모델
description: Azure AD MAU (월간 활성 사용자) 청구 모델 및 특정 Azure 구독에 대한 대금 청구를 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4db758ce0710f5b8b122feaadaa8b97a90dcb7cb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846862"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대한 청구 모델

Azure Active Directory B2C (Azure AD B2C) 사용은 연결 된 Azure 구독에 청구 되 고 MAU (월간 활성 사용자) 청구 모델을 사용 합니다. 다음 섹션에서 구독에 Azure AD B2C 리소스를 연결 하는 방법 및 MAU 청구 모델의 작동 방법에 대해 알아봅니다.

> [!IMPORTANT]
> 이 문서에는 가격 정보가 포함 되어 있지 않습니다. 사용량 청구 및 가격 책정에 대한 최신 정보는 [Azure Active Directory B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)을 참조 하세요.

## <a name="monthly-active-users-mau-billing"></a>MAU (월간 활성 사용자) 청구

Azure AD B2C 청구는 MAU (월간 활성 사용자) 청구 라고 하는 일정 월 내에 인증 활동이 있는 고유한 사용자 수를 기준으로 측정 됩니다.

**2019 년 11 월**1 일부 터 새로 만든 Azure AD B2C 테넌트는 모두 mau (월별 활성 사용자)로 청구 됩니다. 2019 년 11 월 01 일 이후 [구독에 연결](#link-an-azure-ad-b2c-tenant-to-a-subscription) 된 기존 테넌트는 mau (월별 활성 사용자)로 청구 됩니다.

01 년 11 월 2019 이전에 구독에 연결 된 기존 Azure AD B2C 테넌트가 있는 경우 다음 중 하나를 수행 하도록 선택할 수 있습니다.

* MAU (월간 활성 사용자) 청구 모델로 업그레이드 하거나
* 인증 당 청구 모델을 유지 합니다.

### <a name="upgrade-to-monthly-active-users-billing-model"></a>월간 활성 사용자 청구 모델로 업그레이드

Azure AD B2C 리소스에 대한 관리 권한이 있는 Azure 구독 소유자는 MAU 청구 모델로 전환할 수 있습니다. 청구 옵션은 Azure AD B2C 리소스에서 구성 됩니다.

MAU (월간 활성 사용자) 청구는 **취소할**수 없습니다. Azure AD B2C 리소스를 MAU 기반 청구 모델로 변환한 후에는 해당 리소스를 인증 당 청구 모델로 되돌릴 수 없습니다.

기존 Azure AD B2C 리소스에 대해 MAU 청구로 전환 하는 방법은 다음과 같습니다.

1. 구독 소유자로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 mau 청구로 업그레이드 하려는 Azure AD B2C 디렉터리를 선택 합니다.<br/>
    Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png) ![디렉터리 및 구독 필터
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. Azure AD B2C 테넌트의 **개요** 페이지에 있는 **리소스 이름**에서 링크를 선택 합니다. Azure AD 테넌트에서 Azure AD B2C 리소스로 리디렉션됩니다.<br/>
    Azure Portal에 강조 표시 된 Azure AD B2C 리소스 링크 ![](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Azure AD B2C 리소스의 **개요** 페이지에 있는 **청구 단위**에서 **인증 당 (mau로 변경)** 링크를 선택 합니다.<br/>
    Azure Portal에 강조 표시 된 MAU 링크로 변경 ![](./media/billing/portal-mau-03-change-to-mau-link.png)
1. **확인** 을 선택 하 여 mau 청구로 업그레이드를 완료 합니다.<br/>
    ![MAU 기반 청구 확인 대화 상자 Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>인증 별 청구에서 MAU 청구로 전환 하는 경우에 발생할 수 있는 사항

MAU 기반 계량은 구독/리소스 소유자가 변경 내용을 확인 하는 즉시 사용 하도록 설정 됩니다. 월별 청구서는 변경 될 때까지 청구 되는 인증 단위와 변경으로 시작 하는 MAU의 새 단위를 반영 합니다.

사용자는 전환 월에 두 번 계산 되지 않습니다. 변경 전에 인증 하는 고유 활성 사용자에 게는 달력 월의 인증 당 요금이 부과 됩니다. 이러한 동일한 사용자는 구독 청구 주기의 나머지 부분에 대한 MAU 계산에 포함 되지 않습니다. 예:

* Contoso B2C 테넌트에는 1000 명의 사용자가 있습니다. 250 사용자는 지정 된 달에 활성화 됩니다. 구독 관리자는 해당 월 10 일에 인증 단위를 MAU (월간 활성 사용자)로 변경 합니다.
* 1-10에 대한 청구는 인증 당 모델을 사용 하 여 청구 됩니다.
  * 이 기간 동안 100 사용자가 로그인 하면 (1-10) 해당 사용자는 해당 *월에 유료*으로 태그가 지정 됩니다.
* 10의 요금 청구 (유효한 전환 시간)는 MAU 율로 청구 됩니다.
  * 이 기간 (10-30) 동안 추가 150 사용자가 로그인 한 경우에는 추가 150 청구 됩니다.
  * 처음 100 사용자의 지속적인 활동은 해당 월의 나머지 기간에 대한 청구에 영향을 주지 않습니다.

전환 기간 동안 구독 소유자는 Azure 구독 청구 명세서에 표시 되는 두 가지 방법 (인증 당 및 MAU)에 대한 항목을 볼 수 있습니다.

* 인증 당 적용 되는 변경 날짜/시간 까지의 사용에 대한 항목입니다.
* MAU (월간 활성 사용자)를 반영 하는 변경 후의 사용에 대한 항목입니다.

Azure AD B2C 사용 요금 청구 및 가격 책정에 대한 최신 정보는 [Azure Active Directory B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)을 참조 하세요.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>구독에 Azure AD B2C 테넌트 연결

Azure Active Directory B2C (Azure AD B2C)에 대한 사용 요금은 Azure 구독에 청구 됩니다. Azure AD B2C 테넌트가 만들어지면 테넌트 관리자는 Azure AD B2C 테넌트를 Azure 구독에 명시적으로 연결해야 합니다.

구독 링크는 대상 Azure 구독 내에서 Azure AD B2C *리소스* 를 만들어 구현 합니다. 여러 Azure AD B2C 리소스는 가상 머신, 저장소 계정, Logic Apps 등의 다른 Azure 리소스와 함께 단일 Azure 구독에서 만들 수 있습니다. 구독이 연결 된 Azure Active Directory (Azure AD) 테넌트로 이동 하 여 구독 내의 모든 리소스를 볼 수 있습니다.

Azure AD B2C 테넌트에 연결 된 구독은 추가 Azure AD B2C 리소스를 포함 하 여 Azure AD B2C 사용 또는 기타 Azure 리소스의 요금 청구에 사용 될 수 있습니다. Azure AD B2C 테넌트 내의 다른 Azure 라이선스 기반 서비스 또는 Office 365 라이선스를 추가하는 데는 구독을 사용할 수 없습니다.

### <a name="prerequisites"></a>필수 조건

* [Azure 구독](https://azure.microsoft.com/free/)
* 구독에 연결 하려는 [Azure AD B2C 테넌트](tutorial-create-tenant.md)
  * 테넌트 관리자 여야 합니다.
  * 테넌트가 구독에 이미 연결 되어 있지 않아야 합니다.

### <a name="create-the-link"></a>링크 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 사용 하려는 Azure 구독을 포함 하는 디렉터리 (Azure AD B2C 테넌트를 포함 하는 디렉터리*아님* )를 선택 합니다.
1. **리소스 만들기**를 선택 하 고 **Marketplace 검색** 필드에 `Active Directory B2C`를 입력 한 다음 **Azure Active Directory B2C**를 선택 합니다.
1. **만들기**
1. **내 Azure 구독에 기존 Azure AD B2C 테넌트 연결을**선택 합니다.
1. 드롭다운에서 **Azure AD B2C 테넌트** 를 선택 합니다. 사용자가 전역 관리자이 고 구독에 아직 연결 되지 않은 테넌트만 표시 됩니다. **Azure AD B2C 리소스 이름** 필드는 선택한 Azure AD B2C 테넌트의 도메인 이름으로 채워집니다.
1. 관리자 인 활성 Azure **구독** 을 선택 합니다.
1. **리소스 그룹**에서 **새로 만들기**를 선택 하 고 **리소스 그룹 위치**를 지정 합니다. 여기에 리소스 그룹 설정은 Azure AD B2C 테넌트 위치, 성능 또는 청구 상태에 영향을 주지 않습니다.
1. **만들기**를 선택합니다.
    Azure Portal에서 Azure AD B2C 리소스 생성 페이지를 ![](./media/billing/portal-01-create-b2c-resource-page.png)

Azure AD B2C 테넌트에 대해 이러한 단계를 완료 한 후 azure 구독에는 Azure 직접 또는 기업계약 세부 정보 (해당 하는 경우)에 따라 요금이 청구 됩니다.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Azure AD B2C 테넌트 리소스 관리

Azure 구독에서 Azure AD B2C 리소스를 만든 후에는 다른 Azure 리소스와 함께 "B2C 테넌트" 유형의 새 리소스가 표시 되어야 합니다.

이 리소스를 사용하여 다음을 수행할 수 있습니다.

* 구독으로 이동 하 여 청구 정보를 검토 합니다.
* GUID 형식으로 Azure AD B2C 테넌트의 테넌트 ID 가져오기
* Azure AD B2C 테넌트로 이동합니다.
* 지원 요청 제출
* Azure AD B2C 테넌트 리소스를 다른 Azure 구독 또는 리소스 그룹으로 이동

![Azure Portal의 B2C 리소스 설정 페이지](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>지역 제한

구독에서 Azure 리소스 만들기에 대한 지역별 제한을 설정한 경우 해당 제한으로 인해 Azure AD B2C 리소스를 만들지 못할 수 있습니다.

이 문제를 완화 하려면 지역 제한을 완화 하세요.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure CSP (클라우드 솔루션 공급자) 구독

Azure CSP(Cloud Solution Providers) 구독은 Azure AD B2C에서 지원됩니다. 이 기능은 Azure AD B2C 또는 모든 Azure 리소스에 대한 API 또는 Azure Portal을 통해 사용할 수 있습니다. CSP 구독 관리자는 다른 Azure 리소스와의 관계를 Azure AD B2C 연결 하 고, 이동 하 고, 삭제할 수 있습니다.

역할 기반 액세스 제어를 사용한 Azure AD B2C 관리는 Azure AD B2C 테넌트와 Azure CSP 구독 간의 연결에 의해 영향을 받지 않습니다. 역할 기반 액세스 제어는 구독 기반 역할이 아닌 테넌트 기반 역할을 사용 하 여 구현 됩니다.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Azure AD B2C 테넌트 청구 구독 변경

원본 및 대상 구독이 동일한 Azure Active Directory 테넌트 내에 있는 경우 Azure AD B2C 테넌트를 다른 구독으로 이동할 수 있습니다.

Azure AD B2C 테넌트와 같은 Azure 리소스를 다른 구독으로 이동 하는 방법에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조 하세요.

이동을 시작 하기 전에 전체 문서를 읽고 이러한 이동에 대한 제한 사항 및 요구 사항을 완전히 이해 해야 합니다. 리소스 이동에 대한 지침 외에도 이동 전 검사 목록 및 이동 작업의 유효성을 검사 하는 방법과 같은 중요 한 정보를 포함 합니다.

## <a name="next-steps"></a>다음 단계

선택한 Azure 구독 내에서 사용량 및 청구 정보를 검토 하는 것 외에도 [사용 보고 API](view-usage-reports.md)를 사용 하 여 자세한 일일 사용 현황 보고서를 검토할 수 있습니다.
