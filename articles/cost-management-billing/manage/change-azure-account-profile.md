---
title: Azure 계정의 연락처 정보 변경
description: Azure 관리 계정의 연락처 정보를 변경하는 방법 설명
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/03/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6045afba230fa204dd5f93adc11b67ff0e3e209f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684901"
---
# <a name="change-the-contact-information-for-your-azure-account"></a>Azure 계정의 연락처 정보 변경

이 문서는 Azure Portal에서 계정의 연락처 정보를 업데이트하는 데 도움이 됩니다. 연락처 정보를 업데이트하는 지침은 청구 계정 유형에 따라 다릅니다. 청구 계정에 대해 자세히 알아보고 청구 계정 유형을 확인하려면 [Azure Portal에서 청구 계정 보기](view-all-accounts.md)를 참조하세요.

*구매자 주소* - 구매자 주소는 청구 계정을 관리하는 조직 또는 개인의 주소와 연락처 정보입니다. 청구 계정에 대해 생성되는 모든 청구서에 표시됩니다.

*청구 주소* - 청구 주소는 청구 계정에 대해 생성되는 청구서를 받을 조직 또는 개인의 주소와 연락처 정보입니다. MOSP(Microsoft 온라인 구독 프로그램)에 대한 청구 계정의 경우 청구 주소가 하나 있으며, 계정에 대해 생성되는 모든 청구서에 표시됩니다. MCA(Microsoft 고객 계약)에 대한 청구 계정의 경우 청구 프로필마다 청구 주소가 하나씩 있으며 청구 프로필에 대해 생성되는 청구서에 표시됩니다.

*서비스 및 마케팅 이메일의 연락처 이메일 주소* - 로그인에 사용하는 이메일 주소와 다른 이메일 주소를 지정하여 중요한 대금 청구, 서비스 및 Azure 계정에 대한 권장 사항 관련 알림을 받을 수 있습니다. 긴급 보안 문제, 가격 변경 또는 계정에서 사용하는 서비스의 주요 변경 내용 등의 서비스 알림 이메일은 항상 로그인 주소로 전송됩니다.

## <a name="update-an-mosp-billing-account-address"></a>MOSP 청구 계정 주소 업데이트

1. 계정에 대한 계정 관리자 권한이 있는 이메일 주소를 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.  
    ![비용 관리 + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/change-azure-account-profile/search-cmb.png)
1. 왼쪽에서 **속성**을 선택합니다.  
    ![주소 업데이트 페이지를 보여주는 스크린샷](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. **청구 주소 업데이트**를 선택하여 구매자 주소 및 청구 주소를 업데이트합니다. 새 주소를 입력한 다음, **저장**을 선택합니다.  
    ![주소 업데이트 페이지를 보여주는 스크린샷](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>MCA 청구 계정 구매자 주소 업데이트

1. Microsoft 고객 계약의 청구 계정에 대한 소유자 또는 기여자 역할이 있는 이메일 주소를 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.  
    ![비용 관리 + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/change-azure-account-profile/search-cmb.png)
1. 왼쪽에서 **속성**을 선택한 다음, **구매자 업데이트**를 선택합니다.  
    ![구매자 업데이트 선택을 보여주는 스크린샷](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. 새 주소를 입력하고 **저장**을 선택합니다.  
    ![주소 업데이트를 보여주는 스크린샷](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > 일부 계정은 구매자 주소를 업데이트하기 전에 추가 확인이 필요합니다. 계정에서 수동 승인이 필요한 경우 Azure 지원에 문의하라는 메시지가 표시됩니다.

## <a name="update-an-mca-billing-account-address"></a>MCA 청구 계정 주소 업데이트

1. MCA의 청구 계정에 대한 소유자 또는 기여자 역할이 있는 이메일 주소를 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.  
    ![비용 관리 + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/change-azure-account-profile/search-cmb.png)
1. 왼쪽에서 **청구 프로필**을 선택합니다.
1. 청구 프로필을 선택하여 청구 주소를 업데이트합니다.  
    ![Cost Management + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. 왼쪽에서 **속성**을 선택합니다.
1. **주소 업데이트**를 선택합니다.  
    ![비용 관리 + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. 새 주소를 입력한 다음, **저장**을 선택합니다.  
    ![주소 업데이트를 보여주는 스크린샷](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>서비스 및 마케팅 이메일

[Azure Portal](https://portal.azure.com)에서는 90일마다 이메일 주소를 확인하거나 업데이트하라는 메시지가 표시됩니다. Microsoft는 다음에 대한 Azure 계정 관련 정보를 사용하여 이 이메일 주소로 이메일을 보냅니다.

- 서비스 알림
- 보안 경고
- 청구 목적
- 지원
- 마케팅 커뮤니케이션
- Azure 사용량에 기반한 모범 사례 권장 사항

계정에 대한 통신을 받을 이메일 주소를 입력합니다. 이메일 주소를 입력하면 Microsoft의 통신을 받기로 옵트인하는 것입니다.

![연락처 정보를 업데이트하라는 메시지의 예](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>연락처 이메일 주소 변경

다음 방법 중 하나를 사용하여 연락처 이메일 주소를 변경할 수 있습니다. 연락처 이메일 주소를 업데이트해도 로그인에 사용하는 이메일 주소는 업데이트되지 않습니다.

* MOSP 계정의 계정 관리자인 경우 [MOSP 청구 계정 주소 업데이트](#update-an-mosp-billing-account-address)의 지침에 따르고 마지막 단계에서 **연락처 정보 업데이트**를 선택합니다. 그리고 새 이메일 주소를 입력합니다.

* Azure Portal의 [연락처 정보](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) 영역으로 이동하여 새 이메일 주소를 입력합니다. 

* [Azure Portal](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade)에서 이니셜이나 그림이 있는 아이콘을 선택합니다. 그런 다음, 상황에 맞는 메뉴( **...** )를 선택합니다. 다음으로, 메뉴에서 **내 연락처 정보**를 선택하고 새 이메일 주소를 입력합니다.

![Azure에서 이메일 주소를 업데이트 하는 예](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>마케팅 이메일 옵트아웃

마케팅 이메일 받기를 옵트아웃하려면 다음을 수행합니다.

1. [요청 양식](https://account.microsoft.com/profile/permissions-link-request)으로 이동하고 프로필 이메일 주소를 사용하여 요청을 제출합니다. 이메일을 통해 기본 설정을 업데이트하는 링크를 받게 됩니다.
1. 링크를 선택하여 **통신 권한 관리** 페이지를 엽니다. 이 페이지에는 이메일 주소가 옵트인되어 있는 마케팅 통신의 유형이 표시됩니다. 옵트아웃할 항목을 선택 취소한 다음, **저장**을 선택합니다.  
    ![통신 권한을 관리하는 페이지 예제](./media/change-azure-account-profile/manage-communication-permissions.png)

마케팅 통신을 옵트아웃해도 계정을 기반으로 서비스 알림이 전송됩니다.

## <a name="update-the-email-address-that-you-sign-in-with"></a>로그인에 사용하는 이메일 주소 업데이트

계정에 액세스하는 데 사용하는 이메일 주소는 업데이트할 수 없습니다. 그러나 MOSP에 대한 청구 계정이 있는 경우 새 이메일 주소를 사용하여 다른 계정에 가입한 후 구독 소유권을 다음 계정으로 양도할 수 있습니다. MCA 청구 계정의 경우 [새 이메일 주소에 계정에 대한 권한을 부여할 수 있습니다.](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

## <a name="update-your-credit-card"></a>신용 카드 업데이트

신용 카드를 업데이트하는 방법은 [Azure 구독 대금 결제에 사용되는 신용 카드 변경](change-credit-card.md)을 참조하세요.

## <a name="update-your-country-or-region"></a>국가 또는 지역 업데이트

기존 계정의 국가 또는 지역을 변경할 수 없습니다. 그러나 다른 국가 또는 지역에서 새 계정을 만든 다음, Azure 지원에 문의하여 구독을 새 계정으로 이전할 수 있습니다.

## <a name="change-the-subscription-name"></a>구독 이름 변경

1. [Azure Portal](https://portal.azure.com)에 로그인하고 왼쪽 창에서 **구독**을 선택한 다음, 이름을 바꿀 구독을 선택합니다.
1. **개요**를 선택한 다음, 명령 모음에서 **이름 바꾸기**를 선택합니다.  
    ![Azure 구독의 이름을 바꾸는 예](./media/change-azure-account-profile/rename-sub.png)
1. 이름을 바꾼 다음, **저장**을 선택합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [청구 계정 보기](view-all-accounts.md)
