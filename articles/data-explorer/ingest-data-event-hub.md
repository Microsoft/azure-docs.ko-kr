---
title: Event Hub에서 Azure Data Explorer로 데이터 수집
description: 이 문서에서는 이벤트 허브에서 Azure 데이터 탐색기로 데이터를 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: bb9357ca4388bd1fb7ae3e3704cf4112d07c1105
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188197"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Event Hub에서 Azure Data Explorer로 데이터 수집

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-hub-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure 데이터 탐색기에서는 빅 데이스트리터 밍 플랫폼이자 이벤트 수집 서비스인 이벤트 허브에서 데이터를 수집(로드)하는 기능을 제공합니다. [Event Hubs](/azure/event-hubs/event-hubs-about)에서는 초당 수백만 개의 이벤트를 거의 실시간으로 처리할 수 있습니다. 이 문서에서는 이벤트 허브를 만들고, Azure 데이터 탐색기에서 연결 하 고, 시스템을 통해 데이터 흐름을 확인 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md).
* 데이터를 생성하고 이벤트 허브로 보내는 [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)이 있어야 합니다. 샘플 앱을 시스템에 다운로드하세요.
* 샘플 앱을 실행하기 위한 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/).

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

이 문서에서는 샘플 데이터를 생성 하 고 이벤트 허브로 보냅니다. 첫 단계에서는 이벤트 허브를 만듭니다. 이렇게 하려면 Azure Portal에서 Azure Resource Manager 템플릿을 사용합니다.

1. 이벤트 허브를 만들려면 다음 단추를 사용하여 배포를 시작합니다. 마우스 오른쪽 단추로 클릭하고 **새 창에서 열기**를 선택하면 이 문서의 나머지 단계를 수행할 수 있습니다.

    [![Azure에 배포](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    **Azure에 배포** 단추를 선택하면 Azure Portal에서 배포 양식을 작성할 수 있는 페이지로 이동하게 됩니다.

    ![Deploy to Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. 이벤트 허브를 만들려는 구독을 선택하고 이름이 *test-hub-rg*인 리소스 그룹을 만듭니다.

    ![리소스 그룹 만들기](media/ingest-data-event-hub/create-resource-group.png)

1. 다음 정보로 양식을 작성합니다.

    ![배포 양식](media/ingest-data-event-hub/deployment-form.png)

    다음 표에 나와 있지 않은 모든 설정에는 기본값을 사용하세요.

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 이벤트 허브에 사용할 Azure 구독을 선택합니다.|
    | Resource group | *test-hub-rg* | 새 리소스 그룹을 만듭니다. |
    | 위치 | *미국 서부* | 이 문서에 대 한 *미국 서 부* 를 선택 합니다. 프로덕션 시스템의 경우 요구에 가장 적합한 지역을 선택합니다. 최상의 성능을 위해 Kusto 클러스터와 동일한 위치에 Event Hub 네임스페이스를 만듭니다(처리량이 높은 Event Hub 네임스페이스에 가장 중요).
    | 네임스페이스 이름 | 고유한 네임스페이스 이름 | 네임스페이스를 식별하는 고유한 이름을 선택합니다. 예를 들어 *mytestnamespace*를 선택합니다. 입력한 이름에 도메인 이름 *servicebus.windows.net*이 추가됩니다. 이 이름에는 문자, 숫자 및 하이픈만 포함할 수 있습니다. 이름은 문자로 시작하고 문자나 숫자로 끝나야 합니다. 값의 길이는 6자에서 50자 사이여야 합니다.
    | 이벤트 허브 이름 | *test-hub* | 이벤트 허브는 고유한 범위 지정 컨테이너 역할을 하는 네임스페이스 아래에 배치됩니다. 이벤트 허브 이름은 네임스페이스 내에서 고유해야 합니다. |
    | 소비자 그룹 이름 | *test-group* | 소비자 그룹을 사용하면 각기 별도의 이벤트 스트림 보기가 표시되는 여러 애플리케이션을 사용할 수 있습니다. |
    | | |

1. **구매**를 선택하면 구독에서 리소스 만들기를 승인하게 됩니다.

1. 프로비전 프로세스를 모니터링하려면 도구 모음에서 **알림**을 선택합니다. 배포가 정상적으로 완료되려면 몇 분 정도 걸릴 수 있지만 이제 다음 단계를 진행해도 됩니다.

    ![공지](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 대상 테이블 만들기

이제 Azure 데이터 탐색기에서 테이블을 만듭니다. 이벤트 허브는 이 테이블로 데이터를 보냅니다. **필수 구성 요소**에서 프로비전했던 클러스터와 데이터베이스에서 테이블을 만듭니다.

1. Azure Portal에서 클러스터로 이동한 후 **쿼리**를 선택합니다.

    ![쿼리 애플리케이션 링크](media/ingest-data-event-hub/query-explorer-link.png)

1. 다음 명령을 창에 복사하고, **실행**을 선택하여 수집된 데이터를 받을 테이블(TestTable)을 만듭니다.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![쿼리 만들기 실행](media/ingest-data-event-hub/run-create-query.png)

1. 다음 명령을 창에 복사하고, **실행**을 선택하여 들어오는 JSON 데이터를 테이블(TestTable)의 열 이름과 데이터 형식에 매핑합니다.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

## <a name="connect-to-the-event-hub"></a>이벤트 허브에 연결

이제 Azure Data Explorer에서 이벤트 허브에 연결합니다. 이 연결이 설정되면 이벤트 허브로 들어오는 데이터가 이 문서의 앞 부분에서 만든 테스트 테이블로 스트리밍됩니다.

1. 도구 모음에서 **알림**을 선택하여 이벤트 허브 배포가 정상적으로 완료되었는지 확인합니다.

1. 앞에서 만든 클러스터 아래에서 **데이터베이스**, **TestDatabase**를 차례로 선택합니다.

    ![테스트 데이터베이스 선택](media/ingest-data-event-hub/select-test-database.png)

1. **데이터 수집** 및 **데이터 연결 추가**를 선택합니다. 그런 다음, 다음 정보로 양식을 작성합니다. 작업을 마쳤으면 **만들기**를 선택합니다.

    ![이벤트 허브 연결](media/ingest-data-event-hub/event-hub-connection.png)

    **데이터 원본:**

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 데이터 연결 이름 | *test-hub-connection* | Azure 데이터 탐색기에서 만들 연결의 이름입니다.|
    | 이벤트 허브 네임스페이스 | 고유한 네임스페이스 이름 | 앞에서 선택한 네임스페이스를 식별하는 이름입니다. |
    | 이벤트 허브 | *test-hub* | 앞에서 만든 이벤트 허브입니다. |
    | 소비자 그룹 | *test-group* | 앞에서 만든 이벤트 허브에 정의된 소비자 그룹입니다. |
    | 이벤트 시스템 속성 | 관련 속성 선택 | [이벤트 허브 시스템 속성](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations)입니다. 이벤트 메시지 마다 여러 레코드가 있는 경우 시스템 속성이 첫 번째 속성에 추가 됩니다. 시스템 속성을 추가 하는 경우 선택한 속성을 포함 하도록 테이블 스키마 및 [매핑을](/azure/kusto/management/mappings) [만들거나](/azure/kusto/management/create-table-command) [업데이트](/azure/kusto/management/alter-table-command) 합니다. |
    | 압축 | *없음* | 이벤트 허브 메시지 페이로드의 압축 유형입니다. 지원 되는 압축 유형: *None, GZip*.|
    | | |

    **대상 테이블:**

    수집된 데이터를 라우팅하기 위한 옵션으로는 *고정* 라우팅과 *동적* 라우팅이라는 두 가지 옵션이 있습니다. 
    이 문서에서는 고정 라우팅을 사용합니다. 이 경우 테이블 이름, 데이터 형식 및 매핑을 직접 지정합니다. 따라서 **내 데이터에 라우팅 정보 포함**을 선택 취소한 상태로 둡니다.

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 테이블 | *TestTable* | **TestDatabase**에 만든 테이블입니다. |
    | 데이터 형식 | *JSON* | 지원 되는 형식은 Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE, TXT, ORC 및 PARQUET입니다. |
    | 열 매핑 | *TestMapping* | **Testdatabase**에서 만든 [매핑으로](/azure/kusto/management/mappings) , 들어오는 JSON 데이터를 **testdatabase**의 열 이름 및 데이터 형식에 매핑합니다. JSON 또는 여러 줄 JSON에 필요 하 고 다른 형식의 경우 선택 사항입니다.|
    | | |

    > [!NOTE]
    > * 동적 라우팅을 사용하려면 **라우팅 정보를 포함하는 내 데이터**를 선택합니다. [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) 주석에서 볼 수 있듯이 데이터에는 필수 라우팅 정보가 포함됩니다. 정적 및 동적 속성을 모두 설정하는 경우 동적 속성은 정적 속성을 재정의합니다. 
    > * 데이터 연결을 만든 후에 큐에 넣은 이벤트만 수집 됩니다.
    > * [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)에 표시 된 것 처럼 동적 속성을 통해 압축 유형을 설정할 수도 있습니다.
    > * Avro, ORC 및 PARQUET 형식 및 이벤트 시스템 속성은 GZip 압축 페이로드에 지원 되지 않습니다.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="copy-the-connection-string"></a>연결 문자열 복사

필수 구성 요소에 나열된 [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)을 실행할 때는 이벤트 허브 네임스페이스의 연결 문자열이 필요합니다.

1. 앞에서 만든 이벤트 허브 네임스페이스 아래에서 **공유 액세스 정책**, **RootManageSharedAccessKey**를 차례로 선택합니다.

    ![공유 액세스 정책](media/ingest-data-event-hub/shared-access-policies.png)

1. **연결 문자열 - 기본 키**를 복사합니다. 다음 섹션에 붙여넣습니다.

    ![연결 문자열](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>샘플 데이터 생성

다운로드한 [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)을 사용하여 데이터를 생성합니다.

1. Visual Studio에서 샘플 앱 솔루션을 엽니다.

1. *program.cs* 파일에서 `connectionString` 상수를 이벤트 허브 네임스페이스에서 복사한 연결 문자열로 업데이트합니다.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. 앱을 빌드 및 실행합니다. 앱이 이벤트 허브로 메시지를 전송하고 10초마다 상태를 출력합니다.

1. 앱이 메시지를 몇 개 전송하고 나면 다음 단계(이벤트 허브와 테스트 테이블로의 데이터 흐름 검토)를 진행합니다.

## <a name="review-the-data-flow"></a>데이터 흐름 검토

이제 앱 생성 데이터를 사용하여 이벤트 허브에서 클러스터의 테이블로의 데이터 흐름을 볼 수 있습니다.

1. 앱이 실행되는 동안에는 Azure Portal의 이벤트 허브 아래에 활동량이 급증하는 것으로 표시됩니다.

    ![이벤트 허브 그래프](media/ingest-data-event-hub/event-hub-graph.png)

1. 현재까지 데이터베이스로 전송된 메시지의 수를 확인하려면 테스트 데이터베이스에서 다음 쿼리를 실행합니다.

    ```Kusto
    TestTable
    | count
    ```

1. 메시지 내용을 확인하려면 다음 쿼리를 실행합니다.

    ```Kusto
    TestTable
    ```

    결과 세트는 다음과 같이 표시됩니다.

    ![메시지 결과 집합](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * Azure Data Explorer에는 데이터 수집을 위한 집계(일괄 처리) 정책이 있으며, 이는 수집 프로세스를 최적화하도록 설계되었습니다. 이 정책은 기본적으로 5 분 또는 500 MB의 데이터로 구성 되므로 대기 시간이 길어질 수 있습니다. 집계 옵션에 대 한 [일괄 처리 정책](/azure/kusto/concepts/batchingpolicy) 을 참조 하세요. 
    > * 이벤트 허브 수집에는 10 초 또는 1mb의 이벤트 허브 응답 시간이 포함 됩니다. 
    > * 스트리밍을 지원 하도록 테이블을 구성 하 고 응답 시간에서 지연 시간을 제거 합니다. [스트리밍 정책](/azure/kusto/concepts/streamingingestionpolicy)을 참조 하세요. 

## <a name="clean-up-resources"></a>리소스 정리

이벤트 허브를 다시 사용하지 않으려는 경우비용이 발생하지 않도록 **test-hub-rg**를 정리합니다.

1. Azure Portal에서 맨 왼쪽에 있는 **리소스 그룹**을 선택한 다음, 만든 리소스 그룹을 선택합니다.  

    왼쪽 메뉴가 접혀 있으면 ![[확장] 단추를](media/ingest-data-event-hub/expand.png) 클릭하여 펼칩니다.

   ![삭제할 리소스 그룹 선택](media/ingest-data-event-hub/delete-resources-select.png)

1. **test-resource-group** 아래에서 **리소스 그룹 삭제**를 선택합니다.

1. 새 창에서 삭제할 리소스 그룹의 이름(*test-hub-rg*)을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기에서 데이터 쿼리](web-query-data.md)
