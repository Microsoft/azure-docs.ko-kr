---
title: CSP 파트너 정보를 사용하여 Azure의 Cloudyn에 등록
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
ms.openlocfilehash: 518d4d0f672ae0e83748dbf7dc0a87097ca7bbf7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688167"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>CSP 파트너 프로그램에 등록 및 데이터 비용 보기

CSP 파트너로서 Cloudyn에 등록할 수 있습니다. 등록하면 Cloudyn 포털에 액세스할 수 있습니다. 이 빠른 시작 정보에서는 Cloudyn 평가판 구독을 만들고 Cloudyn 포털에 로그인하는 데 필요한 등록 프로세스를 상세히 설명합니다. 또한 비용 데이터를 즉시 보기 시작하는 방법을 보여줍니다.


[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

> [!NOTE]
>
> CSP 직접 파트너와 CSP 간접 공급자는 Cloudyn 등록을 완료할 수 있습니다.
>
> 파트너 센터 API의 구성은 인증과 데이터 액세스에 필요합니다. API 액세스를 프로비전하려면 파트너 센터 전역 관리자 계정이 필요합니다.
> 자세한 내용은 [파트너 센터 API에 연결](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx)을 참조하세요.
>
> Cloudyn 액세스는 CSP 간접 공급자가 Cloudyn에 등록한 후 CSP 간접 대리점에 제공될 수 있습니다. 그러면 CSP 간접 대리점이 Azure 고객 및 구독에 Cloudyn 액세스를 제공할 수 있습니다.
>
>Cloudy는 Microsoft 보안 애플리케이션 모델을 준수합니다. 자세한 내용은 [보안 애플리케이션 모델 프레임워크 사용](/partner-center/develop/enable-secure-app-model)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

- [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="register-with-cloudyn"></a>Cloudyn에 등록

1. Azure Portal의 서비스 목록에서 **Cost Management + 청구**를 클릭합니다.
2. **개요**에서 **Cloudyn**을 클릭합니다.  
    ![Azure Portal에 표시된 Cloudyn 페이지](./media/quick-register-csp/cost-mgt-billing-service.png)
3. **Cloudyn** 페이지에서 **Cloudyn으로 이동**을 클릭하여 새 창에 Cloudyn 등록 페이지를 엽니다.
4. Cloudyn 포털 평가판 등록 페이지에서 회사 이름을 입력하고 **Microsoft CSP 파트너 프로그램 관리자**를 선택하고 **다음**을 클릭합니다.  
5. **애플리케이션 ID**, **상거래 ID**, **애플리케이션 비밀 키**를 입력하고 **기본 가격 책정 계획**을 선택합니다. 정보가 없는 경우 기본 관리자 계정을 사용하여 [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com)에서 파트너 센터 포털에 로그인하고 다음 단계를 수행합니다.
   1. **대시보드**로 이동하고, **설정** 기호를 클릭하고, **파트너 설정**을 클릭하고, **앱 관리**를 클릭합니다.
   2. 이전에 Web App을 만든 경우 이 단계를 건너뜁니다. 그렇지 않으면 **Web App** 섹션에서 **새 웹앱 추가**를 클릭합니다.
   3. 웹 애플리케이션에서 **앱 ID** GUID를 복사합니다.
   4. 웹 애플리케이션에서 **상거래 ID** GUID를 복사합니다.
   5. 필요에 따라 키 유효 기간을 한 해 또는 두 해로 선택합니다. **키 추가**를 선택하고 비밀 키 값을 복사하고 저장합니다.  
    ![자격 증명 정보를 복사하는 파트너 대시보드](./media/quick-register-csp/csp-partner-center.png)
   6. Cloudyn 등록 페이지로 돌아가서 정보를 붙여넣습니다.  
      ![Cloudyn 등록 페이지에 자격 증명 정보 붙여넣기](./media/quick-register-csp/csp-reg.png)
6. 사용 약관에 동의한 다음 정보의 유효성을 검사합니다. **다음**을 클릭하여 Cloudyn에서 Azure 리소스 데이터를 수집할 권한을 부여합니다. 수집되는 데이터에는 구독의 사용량, 성능, 청구 및 태그 데이터가 포함됩니다.  
7. **기타 관련자 초대**에서 이메일 주소를 입력하여 사용자를 추가할 수 있습니다. 완료되면 **다음**을 클릭합니다. 모든 청구 데이터가 Cloudyn에 추가되려면 2시간 정도 소요됩니다.
8. **Cloudyn으로 이동**을 클릭하여 Cloudyn 포털을 연 다음 **클라우드 계정 관리** 페이지에서 등록된 CSP 계정 정보를 확인할 수 있습니다.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Cloudyn에서 간접 CSP 액세스 구성

기본적으로 파트너 센터 API는 직접 CSP에만 액세스할 수 있습니다. 그러나 직접 CSP 공급자는 Cloudyn에서 엔터티 그룹을 사용하여 간접 CSP 고객 또는 파트너에 대한 액세스 권한을 구성할 수 있습니다.

간접 CSP 고객이나 파트너에 대한 액세스를 사용하도록 설정하려면 [Cloudyn에 등록](#register-with-cloudyn)의 단계에 따라 평가판 등록을 설정합니다. 다음에는 Cloudyn 엔터티 그룹으로 다음 단계를 완료하여 간접 CSP 데이터를 분할합니다. 그런 다음 엔터티 그룹에 적절한 사용자 권한을 할당합니다.

1. [엔터티 만들기](tutorial-user-access.md#create-and-manage-entities)의 정보를 사용하여 엔터티 그룹을 만듭니다.
2. [비용 엔터티에 구독 할당](https://www.youtube.com/watch?v=d9uTWSdoQYo)의 단계를 따릅니다. 간접 CSP 고객의 계정 및 Azure 구독을 이전에 만든 엔터티에 연결합니다.
3. [관리자 액세스 권한이 있는 사용자 만들기](tutorial-user-access.md#create-a-user-with-admin-access)의 단계에 따라 관리자 액세스 권한이 있는 사용자 계정을 만듭니다. 그런 다음 사용자 계정이 간접 계정에 대해 이전에 만든 특정 엔터티에 대한 관리자 액세스 권한을 갖는지 확인합니다.

간접 CSP 파트너는 만들어진 계정을 사용하여 Cloudyn 포털에 로그인합니다.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 CSP 정보를 사용하여 Cloudyn에 등록했습니다. 또한 Cloudyn 포털에 로그인하여 비용 데이터 보기를 시작했습니다. Cloudyn에 대해 자세히 알아보려면 Cloudyn용 자습서를 계속 확인하세요.

> [!div class="nextstepaction"]
> [사용량 및 비용 검토](tutorial-review-usage.md)
