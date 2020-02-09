---
title: Logstash에서 Azure Data Explorer로 데이터 수집
description: 이 문서에서는 Logstash에서 Azure 데이터 탐색기를 (부하) 데이터를 수집 하는 방법을 알아봅니다.
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 86f6732cbf2409d3c79a3d7709100e8af24988a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494536"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>Logstash에서 Azure Data Explorer로 데이터 수집

[Logstash](https://www.elastic.co/products/logstash)는 동시에 여러 원본의 데이터를 수집하고, 데이터를 변환한 다음, 데이터를 즐겨찾는 “스태시”에 전송하는 오픈 소스인 서버 쪽 데이터 처리 파이프라인입니다. 이 문서에서는 보내게 데이터는 Azure 데이터 탐색기로 로그 및 원격 분석 데이터에 대한 빠르고 확장성이 높은 데이터 탐색 서비스인. 처음에 테이블 및 데이터 매핑을 테스트 클러스터에 만든 다음, 테이블에 데이터를 보내고 결과를 확인하도록 Logstash로 보냅니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없으면 시작하기 전에 [체험 Azure 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure Data Explorer [테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md).
* 버전 6 이상 Logstash [설치 지침](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>테이블 만들기

클러스터 및 데이터베이스를 만든 후에는 테이블을 만들 차례입니다.

1. 테이블을 만들려면 데이터베이스 쿼리 창에서 다음 명령을 실행합니다.

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. 다음 명령을 실행하여 새 테이블 `logs`가 생성되었고 비어 있는지 확인합니다.
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>매핑 만들기

매핑은 Azure Data Explorer에서 대상 테이블 스키마에 들어오는 데이터를 변환하는 데 사용됩니다. 다음 명령은 `path`에서 설명한 것처럼 들어오는 json에서 속성을 추출하고 `column`에 출력하는 `basicmsg`라는 새 매핑을 만듭니다.

쿼리 창에서 다음 명령을 실행합니다.

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Logstash 출력 플러그인 설치

Logstash 출력 플러그인은 Azure Data Explorer와 통신하며 서비스에 데이터를 보냅니다.
플러그인을 설치하려면 Logstash 루트 디렉터리 내에서 다음 명령을 실행합니다.

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>샘플 데이터 세트를 생성하는 Logstash 구성

Logstash는 엔드투엔드 파이프라인을 테스트하는 데 사용할 수 있는 샘플 이벤트를 생성할 수 있습니다.
Logstash를 이미 사용 중이며 자체 이벤트 스트림에 액세스할 수 있는 사용자는 다음 섹션으로 건너뜁니다. 

> [!NOTE]
> 사용자 고유의 데이터를 사용하는 경우 이전 단계에서 정의된 테이블 및 매핑 개체를 변경합니다.

1. 필요한 파이프라인 설정을 포함할 새 텍스트 파일을 편집합니다(vi 사용).

    ```sh
    vi test.conf
    ```

1. Logstash에게 1000개 테스트 이벤트를 생성하도록 지시하는 다음 설정을 붙여넣습니다.

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

이 구성에는 사용자가 더 많은 메시지를 직접 쓸 수 있는 `stdin` 입력 플러그인도 포함됩니다(*Enter*를 사용하여 파이프라인에 제출하는지 확인).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Azure Data Explorer로 데이터를 보내도록 Logstash 구성

이전 단계에서 사용된 동일한 구성 파일에 다음 설정을 붙여넣습니다. 모든 자리 표시자를 설치 프로그램에 대한 관련 값으로 바꿉니다. 자세한 내용은 [AAD 애플리케이션 만들기](/azure/kusto/management/access-control/how-to-provision-aad-app)를 참조하세요. 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| 매개 변수 이름 | 설명 |
| --- | --- |
| **path** | Logstash 플러그인은 이벤트를 Azure Data Explorer로 보내기 전에 임시 파일에 기록합니다. 이 매개 변수에는 파일을 작성해야 하는 경로와 Azure Data Explorer 서비스에 대한 업로드를 트리거하기 위한 파일 순환의 시간 표현식이 포함됩니다.|
| **ingest_url** | 수집 관련 통신을 위한 Kusto 엔드포인트입니다.|
| **app_id**, **app_key** 및 **app_tenant**| Azure Data Explorer에 연결하는 데 필요한 자격 증명입니다. 수집 권한이 있는 애플리케이션을 사용해야 합니다. |
| **database**| 이벤트를 적용할 데이터베이스 이름입니다. |
| **테이블** | 이벤트를 적용할 대상 테이블 이름입니다. |
| **mapping** | 매핑은 들어오는 이벤트 json 문자열을 올바른 행 형식으로 매핑하는 데 사용됩니다(어떤 속성이 열에 들어가는지 정의). |

## <a name="run-logstash"></a>Logstash 실행

이제 Logstash를 실행하고 설정을 테스트할 준비가 되었습니다.

1. Logstash 루트 디렉터리에서 다음 명령을 실행합니다.

    ```sh
    bin/logstash -f test.conf
    ```

    화면에 출력된 정보가 표시된 다음, 샘플 구성에서 생성된 1000개의 메시지가 표시되어야 합니다. 이 시점에서 더 많은 메시지를 수동으로 입력할 수도 있습니다.

1. 몇 분 후 다음 Data Explorer 쿼리를 실행하여 정의한 테이블에서 메시지를 확인합니다.

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Logstash를 종료하려면 Ctrl + C를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

데이터베이스에서 다음 명령을 실행하여 `logs` 테이블을 정리합니다.

```Kusto
.drop table logs
```

## <a name="next-steps"></a>다음 단계

* [쿼리 작성](write-queries.md)