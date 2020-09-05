---
title: '빠른 시작: Azure Data Catalog 만들기'
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Data Catalog를 만드는 방법을 설명합니다.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: e08527ae530b7b5ed5e5293a3af9e2d8dd3f795a
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118793"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>빠른 시작: Azure Data Catalog 만들기

Azure Data Catalog는 기업 데이터 자산의 등록 시스템 및 검색 시스템 역할을 하는 완전히 관리되는 클라우드 서비스입니다. 자세한 개요는 [Azure Data Catalog란](overview.md)을 참조하세요.

이 빠른 시작은 Azure Data Catalog 만들기를 시작하는 데 도움이 됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

> [!Note]
> Azure 보안 요구 사항으로 인해 Azure Data Catalog는 TLS(전송 계층 보안) 1.2를 강화했습니다. TLS 1.0 및 TLS 1.1이 비활성화되었습니다. 머신이 TLS 1.2용으로 업데이트되지 않은 경우 등록 도구를 실행하는 동안 오류가 발생할 수 있습니다. TLS 1.2에 대해 머신을 업데이트하려면 [전송 계층 보안(1.2) 사용](https://docs.microsoft.com/mem/configmgr/core/plan-design/security/enable-tls-1-2)을 참조하세요.

시작하려면 다음이 필요합니다.

* [Microsoft Azure](https://azure.microsoft.com/) 구독
* 고유한 [Azure Active Directory 테넌트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)가 있어야 합니다.

데이터 카탈로그를 설정하려면 Azure 구독의 소유자 또는 공동 소유자여야 합니다.

## <a name="create-a-data-catalog"></a>데이터 카탈로그 만들기

조직(Azure Active Directory 도메인)당 하나의 데이터 카탈로그만 제공할 수 있습니다. 따라서 이 Azure Active Directory 도메인에 속한 Azure 구독의 소유자 또는 공동 소유자가 이미 카탈로그를 만든 경우 사용자는 여러 Azure 구독이 있어도 카탈로그를 다시 만들 수 없습니다. Azure Active Directory 도메인에서 사용자가 데이터 카탈로그를 만들었는지 여부를 테스트하려면 [Azure Data Catalog 홈 페이지](http://azuredatacatalog.com) 로 이동하여 카탈로그가 표시되는지 확인합니다. 카탈로그가 이미 생성된 경우 다음 절차를 건너뛰고 다음 섹션으로 이동합니다.

1. [Azure Portal](https://portal.azure.com) > **리소스 만들기**로 이동하고 **Data Catalog**를 선택합니다.

    ![Azure Data Catalog 만들기 단추](media/data-catalog-get-started/data-catalog-create.png)

2. 데이터 카탈로그의 **이름**, 사용하려는 **구독** 및 카탈로그의 **위치**, **가격 책정 계층**을 지정합니다. 그런 다음 **만들기**를 선택합니다.

3. [Azure Data Catalog 홈 페이지](http://azuredatacatalog.com) 로 이동하고 **데이터 게시**를 클릭합니다.

   ![Azure Data Catalog - 데이터 게시 단추](media/data-catalog-get-started/data-catalog-publish-data.png)

   또한 **시작**을 선택하여 [Data Catalog 서비스 페이지](https://azure.microsoft.com/services/data-catalog)에서 Data Catalog 홈페이지로 이동할 수 있습니다.

   ![Azure Data Catalog - 마케팅 방문 페이지](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. **설정** 페이지로 이동합니다.

    ![Azure Data Catalog - 데이터 카탈로그 프로비전](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. **가격 책정**을 확장하고 Azure Data Catalog **버전**(무료 또는 Standard)을 확인합니다.

    ![Azure Data Catalog--버전 선택](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. *Standard* 버전을 가격 책정 계층으로 선택할 경우 **보안 그룹**을 확장하고, Active Directory 보안 그룹의 Data Catalog 액세스 권한 부여를 사용하고 청구 단위 자동 조정을 사용할 수 있습니다.

    ![Azure Data Catalog 보안 그룹](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. **카탈로그 사용자**를 확장하고 **추가**를 클릭하여 데이터 카탈로그의 사용자를 추가합니다. 그러면 자동으로 이 그룹에 추가됩니다.

    ![Azure Data Catalog--사용자](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. *Standard* 버전을 가격 책정 계층으로 선택할 경우 **용어집 관리자**를 확장하고 **추가**를 클릭하여 용어집 관리자 사용자를 추가할 수 있습니다. 그러면 자동으로 이 그룹에 추가됩니다.

    ![Azure Data Catalog 용어집 관리자](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. **카탈로그 관리자**를 확장하고 **추가**를 클릭하여 데이터 카탈로그에 추가 관리자를 추가합니다. 그러면 자동으로 이 그룹에 추가됩니다.

    ![Azure Data Catalog--관리자](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. **포털 제목**을 확장하고 포털 제목에 표시될 텍스트를 추가합니다.

    ![Azure Data Catalog-포털 제목](media/data-catalog-get-started/data-catalog-portal-title.png)

11. **설정** 페이지를 완성했으면 **게시** 페이지로 이동합니다.

    ![Azure Data Catalog--생성됨](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Azure 포털에서 데이터 카탈로그 찾기

1. 웹 브라우저의 별도 탭 또는 별도 웹 브라우저 창에서 [Azure Portal](https://portal.azure.com) 로 이동하고 이전 단계에서 데이터 카탈로그를 만드는 데 사용한 동일한 계정으로 로그인합니다.

2. **모든 서비스**를 선택한 다음, **Data Catalog**를 클릭합니다.

    ![Azure Data Catalog - Azure 찾아보기](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    만든 데이터 카탈로그가 표시됩니다.

    ![Azure Data Catalog - 목록에서 카탈로그 보기](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. 만든 카탈로그를 클릭합니다. 포털에서 **Data Catalog** 블레이드가 표시됩니다.

   ![Azure Data Catalog - 포털의 블레이드](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. 데이터 카탈로그의 속성을 보고 업데이트할 수 있습니다. 예를 들어 **가격 책정 계층** 을 클릭하고 버전을 변경합니다.

    ![Azure Data Catalog - 가격 책정 계층](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 조직을 위한 Azure Data Catalog를 만드는 방법을 알아보았습니다. 이제 데이터 카탈로그에서 데이터 원본을 등록할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Data Catalog에서 데이터 원본 등록](data-catalog-how-to-register.md)
