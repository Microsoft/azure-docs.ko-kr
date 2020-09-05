---
title: Azure Table 스토리지를 사용한 잠재 고객 관리 - Microsoft 상업용 마켓플레이스
description: Azure Table 스토리지를 사용하여 Microsoft AppSource 및 Azure Marketplace에 대해 잠재 고객을 구성하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/25/2020
ms.openlocfilehash: db2bae9d9e1c9658937e725a04d919743ff9999e
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855742"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Azure Table 스토리지를 사용하여 상업용 Marketplace 잠재 고객 관리

CRM(고객 관계 관리) 시스템이 Microsoft AppSource 및 Azure Marketplace 잠재 고객을 받을 수 있도록 파트너 센터에서 명시적으로 지원되지 않는 경우 Azure Table 스토리지를 사용하여 잠재 고객을 처리할 수 있습니다. 그런 다음 데이터를 내보내고 CRM 시스템으로 가져올 수 있습니다. 이 문서에서는 Azure Storage 계정 및 해당 계정 아래에 테이블을 만드는 방법을 설명합니다. 또한 Power Automate를 사용하여 제품이 잠재 고객을 받은 경우 메일 알림을 보내도록 새 흐름을 만들 수 있습니다.

## <a name="configure-an-azure-storage-account"></a>Azure Storage 계정 구성

1. Azure 계정이 없으면 [평가판 계정을 생성](https://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.
1. Azure 계정이 활성화되면 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal에서 다음 절차에 따라 스토리지 계정을 만듭니다.

    1. 왼쪽 메뉴 모음에서 **+리소스 만들기**를 선택합니다. 오른쪽에 **새로 만들기** 창이 나타납니다.
    1. **새로 만들기** 창에서 **스토리지**를 선택합니다. 오른쪽에 **추천** 목록이 표시됩니다.
    1. **스토리지 계정**을 선택하여 계정 만들기를 시작합니다. [스토리지 계정 만들기](../../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)의 지침을 따릅니다.

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="Azure storage 계정을 만드는 단계입니다.":::

        스토리지 계정에 대한 자세한 내용은 [빠른 시작 자습서](../../storage/index.yml)를 참조하세요. 스토리지 가격에 대한 자세한 내용은 [스토리지 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

1. 스토리지 계정이 프로비전될 때까지 기다립니다. 이 프로세스에는 일반적으로 몇 분 정도 걸립니다.

## <a name="create-a-table-in-your-storage-account"></a>스토리지 계정에서 테이블 만들기

1. Azure Portal의 **홈** 페이지에서 저장소 계정에 액세스 하기 위한 **모든 리소스 보기** 를 선택 합니다. Azure Portal의 왼쪽 표시줄에서 **모든 리소스**를 선택할 수도 있습니다.

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="Azure storage 계정에 액세스 합니다.":::

1. 스토리지 계정 창에서 **액세스 키**를 선택하고 키의 **연결 문자열** 값을 복사합니다. 이 값은 Azure Marketplace 제품에 대한 잠재 고객을 받기 위해 게시 포털에서 입력해야 하는 **스토리지 계정 연결 문자열**이므로 저장해 두세요.

    다음은 연결 문자열의 예입니다.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Azure 저장소 키입니다.":::


1. 스토리지 계정 창에서 **테이블**을 선택하고 **+ 테이블**을 선택하여 테이블을 만듭니다. 테이블의 이름을 입력하고 **확인**을 선택합니다. 이 값은 잠재 고객이 수신되었을 때 메일 알림을 받는 흐름을 구성하려는 경우 필요하므로 저장해 두세요.

    ![Azure 테이블](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    [Azure Storage Explorer](https://archive.codeplex.com/?p=azurestorageexplorer) 또는 다른 도구를 사용하여 스토리지 테이블의 데이터를 확인할 수 있습니다. Azure 테이블의 데이터를 내보낼 수도 있습니다.

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>(선택 사항) Power Automate를 사용하여 잠재 고객 알림 받기

[Power Automate](https://docs.microsoft.com/flow/)를 사용하여 잠재 고객이 Azure Storage 테이블에 추가될 때마다 알림이 발송되도록 자동화할 수 있습니다. 계정이 없으면 [체험 계정에 가입](https://flow.microsoft.com/)할 수 있습니다.

### <a name="lead-notification-example"></a>잠재 고객 알림 예제

이 예제에서는 Azure Table 스토리지에 새 잠재 고객이 추가되면 메일 알림을 자동으로 전송하는 흐름을 만듭니다. 이 예제에서는 테이블 스토리지가 업데이트되면 매시간 잠재 고객 정보를 되풀이해서 보내도록 설정합니다.

1. Power Automate 계정에 로그인합니다.
1. 왼쪽 표시줄에서 **내 흐름**을 선택합니다.
1. 위쪽 표시줄에서 **+ 새로 만들기**를 선택합니다.
1. 드롭다운 목록에서 **+ 예약됨--새로 시작**을 선택합니다.

   ![내 흐름 + 예약됨--새로 시작](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. **예약된 흐름 빌드** 창에서 **반복 기간**의 간격으로 **1**을 선택하고 빈도로 **시간**을 선택합니다. 원하는 경우 흐름에 이름을 지정합니다. **만들기**를 선택합니다.

   >[!NOTE]
   >이 예제에서는 1시간 간격을 사용하지만 비즈니스 요구에 가장 적합한 간격 및 빈도를 선택할 수 있습니다.

   ![예약된 흐름 빌드](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. **+ 새 단계**를 선택합니다.
1. **작업 선택** 창에서 **과거 시간 가져오기**를 검색합니다. 그런 다음 **작업** 아래에서 **과거 시간 가져오기**를 선택합니다.

   ![작업 선택](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. **과거 시간 가져오기** 창에서 **간격**을 **1**로 설정합니다. **시간 단위** 드롭다운 목록에서 **시간**을 선택합니다.

    >[!IMPORTANT]
    >8단계에서 설정하는 간격 및 시간 단위는 5단계에서 되풀이에 대해 구성한 간격 및 빈도와 일치해야 합니다.

    ![과거 시간 가져오기 간격 설정](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >흐름을 검사하여 각 단계가 올바르게 구성됐는지 확인할 수 있습니다. 흐름을 검사하려면 **흐름** 메뉴 모음에서 **흐름 검사**를 선택합니다.

   이어지는 단계에서는 테이블에 연결한 후 새 잠재 고객을 처리하는 처리 논리를 설정합니다.

1. **+ 새 단계**를 선택합니다. **작업 선택** 창에서 **엔터티 가져오기**를 검색합니다.
1. **작업** 아래에서 **엔터티 가져오기(Azure Table Storage)** 를 선택합니다.
1. **Azure Table Storage** 창에서 다음 상자에 정보를 지정하고 **만들기**를 선택합니다.

    * **연결 이름**: 이 흐름과 테이블 사이에 설정하는 연결에 의미 있는 이름을 지정합니다.
    * **스토리지 계정 이름**: 테이블에 대한 스토리지 계정의 이름을 지정합니다. 이 이름은 스토리지 계정의 **액세스 키** 페이지에서 찾을 수 있습니다.
    * **공유 스토리지 키**: 테이블에 대한 스토리지 계정의 키 값을 지정합니다. 이 값은 스토리지 계정의 **액세스 키** 페이지에서 찾을 수 있습니다.

      ![Azure Table Storage 창](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   **만들기**를 선택하면 **엔터티 가져오기** 창이 표시됩니다. 여기에서 **고급 옵션 표시**를 선택하고 다음 상자에 정보를 지정합니다.

   * **테이블**: 테이블의 이름([테이블 만들기](#create-a-table-in-your-storage-account)에서 지정한 이름)을 선택합니다. 다음 그림에서는 `marketplaceleads` 이 예제에 대해 테이블을 선택 하는 경우의 프롬프트를 보여 줍니다.

     ![엔터티 가져오기 창](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **필터 쿼리**: 이 상자를 선택하고 상자에 `Timestamp gt datetime'@{body('Get_past_time')}'` 함수를 붙여넣습니다.

     ![엔터티 가져오기, 필터 쿼리 상자](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Azure 테이블에 대한 연결 설정을 마쳤으니 이번에는 **새 단계**를 선택하고 조건을 추가하여 Azure 테이블에서 새 잠재 고객을 검색합니다.

1. **작업 선택** 창에서 **작업**을 선택합니다. 그런 다음 **조건 제어**를 선택합니다.

    ![작업 창 선택](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. **조건** 창에서 **값 선택**을 선택합니다. 그런 다음 팝업 창에서 **식**을 선택합니다.

1. **fx** 상자에 `length(body('Get_entities')?['value'])`를 붙여넣습니다. **확인**을 선택하여 이 함수를 추가합니다.

1. 조건 설정을 마치려면
    1. 드롭다운 목록에서 **보다 큼**을 선택합니다.
    2. 값으로 **0**을 입력합니다.

        ![조건 창](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   이어지는 단계에서는 조건의 결과에 따라 수행할 작업을 설정합니다.

   * 조건이 **아니요인 경우**로 확인되면 아무것도 수행하지 않습니다.
   * 조건이 **예인 경우**를 확인하면 Office 365 계정에 연결하는 작업을 트리거하여 이메일을 전송합니다.

1. **예인 경우** 아래에서 **작업 추가**를 선택합니다.

    ![조건 창, 예인 경우, 작업 추가](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. **메일 보내기(Office 365 Outlook)** 를 선택합니다.

    ![조건 창, 예인 경우, 메일 보내기](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >다른 메일 공급자를 사용하려면 작업으로 **메일 알림 보내기(메일)** 를 검색하여 선택합니다. 이 지침에서는 Office 365 Outlook을 사용하여 구성하는 방법을 보여 주고 있지만 다른 메일 공급자의 경우에도 비슷한 방법을 사용하면 됩니다.

1. Office 365 Outlook 창에서 다음 상자에 정보를 지정합니다.

    1. **받는 사람**: 이 알림을 받을 모든 사람의 메일 주소를 입력합니다.
    1. **주체**: 메일의 제목을 입력합니다. 예: **새로운 잠재 고객!**
    1. **본문**: 각 메일에 추가할 텍스트를 추가한 다음 `body('Get_entities')?['value']`에 붙여넣습니다.

    >[!NOTE]
    >이 이메일의 본문에 정적 또는 동적 추가 데이터 요소를 삽입할 수 있습니다.

    ![조건 창, 예인 경우, Office 365 Outlook 창](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. **저장**을 선택하여 흐름을 저장합니다. Power Automate에서 자동으로 테스트를 진행하여 흐름에 오류가 있는지 검사합니다. 오류가 없으면 흐름이 저장된 후 실행을 시작합니다.

다음 이미지에서는 최종 흐름이 어떻게 표시되는지 보여 줍니다.

![최종 흐름의 예](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>흐름 관리

실행된 후 흐름을 관리하기는 쉽습니다. 흐름에 대해 완전히 제어할 수 있습니다. 예를 들어 흐름을 중지하고 편집하고 실행 기록을 확인하고 분석을 얻을 수 있습니다. 다음 이미지에서는 흐름 관리에 사용할 수 있는 옵션을 보여 줍니다.

 ![흐름 관리](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

흐름은 **흐름 해제** 옵션을 사용하여 중지될 때까지 계속 실행 상태를 유지합니다.

잠재 고객 메일 알림이 수신되지 않는다면 Azure 테이블에 새로운 잠재 고객이 추가되지 않았음을 의미합니다. 흐름 오류가 있는 경우 다음과 같은 메일을 받게 됩니다.

 ![흐름 실패 이메일 알림](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Azure 테이블로 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대해 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 수행합니다.

1. 제품의 **제품 설정** 페이지로 이동합니다.

1. **잠재 고객** 섹션에서 **연결**을 선택합니다.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="잠재 고객":::

1. **연결 세부 정보** 팝업 창에서 **잠재 고객 대상**으로 **Azure Table**을 선택합니다. 
     ![잠재 고객 관리, 연결 세부 정보](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. 앞 단계에서 Azure Storage 계정을 만들 때 생성된 연결 문자열을 **스토리지 계정 연결 문자열** 상자에 붙여넣습니다.
     ![잠재 고객 관리, 연결 세부 정보 스토리지 계정](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **연락처 메일**: 새 잠재 고객이 수신되었을 때 메일 알림을 받을 회사 사용자의 메일을 입력합니다. 세미콜론으로 구분하여 여러 메일을 입력할 수 있습니다.

1. **확인**을 선택합니다.

잠재 고객 대상에 성공적으로 연결되었는지 확인하려면 **유효성 검사** 단추를 선택합니다. 성공하면 잠재 고객 대상에 테스트 잠재 고객이 생깁니다.

>[!NOTE]
>제품에 대한 잠재 고객을 받으려면 먼저 제품의 나머지 부분에 대해 구성을 끝낸 후에 게시해야 합니다.

잠재 고객이 생성되면 Microsoft에서 Azure 테이블로 잠재 고객을 보냅니다. 흐름을 구성할 경우 지정한 메일 주소로 메일도 발송됩니다.
