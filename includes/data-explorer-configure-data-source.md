---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: f8b3e541dfd55bbd613af2791329a08402cf8670
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581902"
---
## <a name="configure-the-data-source"></a>데이터 원본 구성

다음 단계를 수행 하 여 Azure 데이터 탐색기를 대시보드 도구의 데이터 원본으로 구성 합니다. 이 섹션에서 이 단계를 더 자세히 다룹니다.

1. Azure Active Directory(Azure AD) 서비스 주체를 만듭니다. 서비스 주체는 대시보드 도구에서 Azure 데이터 탐색기 서비스에 액세스 하는 데 사용 됩니다.

1. Azure AD 서비스 주체를 Azure Data Explorer 데이터베이스에서 *뷰어* 역할에 추가합니다.

1. Azure AD 서비스 주체의 정보에 따라 대시보드 도구 연결 속성을 지정 하 고 연결을 테스트 합니다.

### <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI](#azure-portal) 명령줄 환경을 사용하여 또는 [Azure Portal](#azure-cli)에 서비스 주체를 만들 수 있습니다. 어떤 방법을 사용하든 서비스 주체를 만든 후 이후 단계에서 사용하는 네 가지 연결 속성에 대한 값을 가져옵니다.

#### <a name="azure-portal"></a>Azure portal

1. 서비스 주체를 만들려면 [Azure Portal 설명서](/azure/active-directory/develop/howto-create-service-principal-portal)의 지침을 따릅니다.

    1. [역할에 애플리케이션 할당](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) 섹션에서 **Reader** 역할 유형을 Azure Data Explorer 클러스터에 할당합니다.

    1. 로그인에 [사용할 값 가져오기](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 섹션에서 **디렉터리 ID** (테넌트 Id), **응용 프로그램 id**및 **암호**단계에서 설명 하는 세 가지 속성 값을 복사 합니다.

1. Azure Portal에서 **구독**을 선택한 다음, 서비스 주체를 만든 구독에 대한 ID를 복사합니다.

    ![구독 ID - Portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. 서비스 주체를 만듭니다. `reader`의 해당 범위 및 역할 유형을 설정합니다.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    자세한 내용은 [Azure CLI을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

1. 명령은 다음과 같이 결과 집합을 반환합니다. 세 가지 속성 값 **appID**, **암호** 및 **테넌트**를 복사합니다.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. 구독 목록을 가져옵니다.

    ```azurecli
    az account list --output table
    ```

    해당 구독 ID를 복사합니다.

    ![구독 ID - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>뷰어 역할에 서비스 주체 추가

서비스 주체를 만들었으므로, 이제 Azure Data Explorer 데이터베이스에서 *뷰어* 역할에 추가합니다. 관리 명령을 사용하여 Azure Portal의 **사용 권한** 또는 **쿼리**에서 이 작업을 수행할 수 있습니다.

#### <a name="azure-portal---permissions"></a>Azure Portal - 사용 권한

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다.

1. **개요** 섹션에서 StormEvents 샘플 데이터를 사용하여 데이터베이스를 선택합니다.

    ![데이터베이스 선택](media/data-explorer-configure-data-source/select-database.png)

1. **권한**을 선택한 다음, **추가**를 선택합니다.

    ![데이터베이스 권한](media/data-explorer-configure-data-source/database-permissions.png)

1. **데이터베이스 권한 추가**에서 **뷰어** 역할을 선택한 다음, **주체 선택**을 선택합니다.

    ![데이터베이스 권한 추가](media/data-explorer-configure-data-source/add-permission.png)

1. 만든 서비스 사용자를 검색 합니다. 주체를 선택한 다음, **선택**을 클릭합니다.

    ![Azure Portal에서 권한 관리](media/data-explorer-configure-data-source/new-principals.png)

1. **저장**을 선택합니다.

    ![Azure Portal에서 권한 관리](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>관리 명령 - 쿼리

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동한 다음, **쿼리**를 선택합니다.

    ![쿼리](media/data-explorer-configure-data-source/query.png)

1. 쿼리 창에서 다음 명령을 실행합니다. Azure Portal 또는 CLI에서 애플리케이션 ID 및 테넌트 ID를 사용합니다.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    명령은 다음과 같이 결과 집합을 반환합니다. 이 예제에서 첫 번째 행은 데이터베이스에서 기존 사용자를 사용되며 두 번째 행은 방금 추가된 서비스 주체를 위해 사용됩니다.

    ![결과 집합](media/data-explorer-configure-data-source/result-set.png)
