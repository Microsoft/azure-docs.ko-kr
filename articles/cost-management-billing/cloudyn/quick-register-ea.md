---
title: Cloudyn에 Azure Enterprise Agreement 등록
description: 이 빠른 시작 정보에서는 Cloudyn 평가판 구독을 만들고 Cloudyn 포털에 로그인하는 데 필요한 등록 프로세스를 상세히 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: af8eb5c06d427981c7154750bc069d802152f8c5
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688116"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Azure Enterprise Agreement 등록 및 비용 데이터 보기

Azure 기업계약을 통해 Cloudyn에 등록할 수 있습니다. 등록하면 Cloudyn 포털에 액세스할 수 있습니다. 이 빠른 시작 정보에서는 Cloudyn 평가판 구독을 만들고 Cloudyn 포털에 로그인하는 데 필요한 등록 프로세스를 상세히 설명합니다. 또한 비용 데이터를 즉시 보기 시작하는 방법을 보여줍니다.

Azure Cost Management는 Cloudyn과 비슷한 기능을 제공합니다. Azure Cost Management는 기본 Azure 비용 관리 솔루션입니다. 이 솔루션을 사용하면 비용을 분석하고, 예산을 작성 및 관리하고, 데이터를 내보내고, 비용 절약을 위한 최적화 권장 사항을 검토하고 필요한 조치를 취할 수 있습니다. 자세한 내용은 [Azure Cost Management](../cost-management-billing-overview.md)를 참조하세요.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

- [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="register-with-cloudyn"></a>Cloudyn에 등록

1. Azure Portal의 서비스 목록에서 **Cost Management + 청구**를 클릭합니다.
2. **개요**에서 **Cloudyn**을 클릭합니다.  
    ![Azure Portal에 표시된 Cloudyn 페이지](./media/quick-register-ea/cost-mgt-billing-service.png)
3. **Cloudyn** 페이지에서 **으로 이동**하여 새 창에 Cloudyn 등록 페이지를 엽니다.
4. Cloudyn 포털 평가판 등록 페이지에서 회사 이름을 입력하고 **Azure Enterprise 등록 관리자**를 선택합니다.  
5. 엔터프라이즈 포털 등록 API 키를 입력합니다. 키가 없는 경우 [Enterprise Portal](https://ea.azure.com) 링크를 클릭하고 다음 단계를 수행합니다.  
    ![요금 청구 탭에 API 키 붙여넣기](./media/quick-register-ea/trial-reg.png)
   1. Azure Enterprise 웹 사이트에 로그인하고 **보고서**, **API 액세스 키**를 차례로 클릭한 다음 기본 키를 복사합니다.  
    ![EA 포털에 있는 EA API 키의 예](./media/quick-register-ea/ea-key.png)
   3. 등록 페이지로 돌아가 API 키에 붙여 넣습니다.
6. 사용 약관에 동의한 다음 키의 유효성을 검사합니다. **다음**을 클릭하여 Cloudyn에서 Azure 리소스 데이터를 수집할 권한을 부여합니다. 수집되는 데이터에는 구독의 사용량, 성능, 청구 및 태그 데이터가 포함됩니다.  
    ![성공한 EA API 키 유효성 검사의 예](./media/quick-register-ea/ea-key-validated.png)
7. **기타 관련자 초대**에서 이메일 주소를 입력하여 사용자를 추가할 수 있습니다. 완료되면 **다음**을 클릭합니다. Azure 등록의 규모에 따라 모든 청구 데이터가 Cloudyn에 추가되는 데 최대 24 시간이 걸릴 수 있습니다.
8. **Cloudyn으로 이동**을 클릭하여 Cloudyn 포털을 연 다음 **클라우드 계정 관리** 페이지에서 등록된 EA 계정 정보를 확인할 수 있습니다.

기업계약을 등록하는 방법에 대한 자습서 동영상을 시청하려면 [에서 사용할 EA 등록 ID 및 API 키를 찾는 방법](https://youtu.be/u_phLs_udig)을 참조하세요.

[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 기업계약 정보를 사용하여 Cloudyn에 등록했습니다. 또한 Cloudyn 포털에 로그인하여 비용 데이터 보기를 시작했습니다. Cloudyn에 대해 자세히 알아보려면 Cloudyn용 자습서를 계속 확인하세요.

> [!div class="nextstepaction"]
> [사용량 및 비용 검토](tutorial-review-usage.md)
