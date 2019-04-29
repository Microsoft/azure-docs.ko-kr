---
title: Network Watcher 및 Elastic Stack을 사용하여 네트워크 보안 그룹 흐름 로그 관리 및 분석 | Microsoft Docs
description: Network Watcher 및 Elastic Stack을 사용하여 Azure에서 네트워크 보안 그룹 흐름 로그를 관리하고 분석합니다.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 7361eff0f76271564fd5a0e9b8a18221ec4138e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860119"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>오픈 소스 도구를 사용하여 Azure Network Watcher NSG 흐름 로그 시각화

네트워크 보안 그룹 흐름 로그는 네트워크 보안 그룹의 송/수신 IP 트래픽을 이해하는 데 사용할 수 있는 정보를 제공합니다. 이러한 흐름 로그는 트래픽이 허용되거나 거부된 경우 각 규칙을 기준으로 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC, 흐름에 대한 5개의 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜)를 보여줍니다.

이러한 흐름 로그는 수동으로 구문 분석하고 인사이트를 얻기 어려울 수 있습니다. 그러나 이 데이터를 시각화하는 데 도움이 되는 오픈 소스 도구가 몇 가지 있습니다. 이 문서에서는 Kibana 대시보드에서 흐름 로그를 신속하게 인덱싱하고 시각화할 수 있는 탄력적인 스택을 사용하여 이러한 로그를 시각화하는 솔루션을 제공합니다.

> [!Warning]  
> 다음 단계는 흐름 로그 버전 1을 사용하여 작동합니다. 자세한 내용은 [네트워크 보안 그룹에 대한 흐름 로깅 소개](network-watcher-nsg-flow-logging-overview.md)를 참조하세요. 다음 지침은 수정 없이 로그 파일의 버전 2를 사용하여 작동하지 않습니다.

## <a name="scenario"></a>시나리오

이 문서에서는 탄력적인 스택을 사용하여 네트워크 보안 그룹 흐름 로그를 시각화할 수 있는 솔루션을 설정합니다.  Logstash 입력 플러그 인은 흐름 로그를 포함하기 위해 구성된 Blob Storage에서 직접 흐름 로그를 가져옵니다. 그런 다음, 탄력적인 스택을 사용하여 흐름 로그가 인덱싱되고, 흐름 로그로 Kibana 대시보드를 만들어 정보를 시각화합니다.

![시나리오][scenario]

## <a name="steps"></a>단계

### <a name="enable-network-security-group-flow-logging"></a>네트워크 보안 그룹 흐름 로그 사용
이 시나리오에서는 계정에 있는 하나 이상의 네트워크 보안 그룹에서 네트워크 보안 그룹 흐름 로깅을 사용하도록 설정해야 합니다. 네트워크 보안 흐름 로그를 사용하도록 설정하는 방법에 대한 지침은 [네트워크 보안 그룹에 대한 흐름 로깅 소개](network-watcher-nsg-flow-logging-overview.md) 문서를 참조하세요.

### <a name="set-up-the-elastic-stack"></a>탄력적 스택 설정
NSG 흐름 로그를 탄력적 스택과 연결하여 로그에서 정보를 검색하고, 그래프화하며 분석하고 정보를 끌어낼 수 있는 Kibana 대시보드를 만들 수 있습니다.

#### <a name="install-elasticsearch"></a>Elasticsearch 설치

1. 이번 5.0 이상의 탄력적 스택에는 Java 8이 필요합니다. `java -version` 명령을 실행하여 버전을 확인합니다. java가 설치되지 않은 경우 [Azure 지원 JDK](https://aka.ms/azure-jdks)에 대한 설명서를 참조하세요.
2. 시스템에 맞는 이진 패키지를 다운로드합니다.

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   다른 설치 방법은 [Elasticsearch 설치](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)에서 확인할 수 있습니다.

3. 명령으로 Elasticsearch가 실행 중인지 확인합니다.

    ```bash
    curl http://127.0.0.1:9200
    ```

    다음과 유사한 응답이 표시됩니다.

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

탄력적 검색 설치에 대한 추가 정보는 [설치 지침](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)을 참조하세요.

### <a name="install-logstash"></a>Logstash 설치

1. Logstash를 설치하려면 다음 명령을 실행합니다.

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. 다음으로, 흐름 로그를 액세스하고 구문 분석하도록 Logstash를 구성해야 합니다. 다음을 사용하여 logstash.conf 파일을 만듭니다.

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. 파일에 다음 내용을 추가합니다.

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

Logstash 설치에 대한 추가 정보는 [공식 설명서](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)를 참조하세요.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Azure Blob Storage를 위한 Logstash 입력 플러그 인 설치

이 Logstash 플러그 인을 사용하면 지정된 스토리지 계정에서 흐름 로그에 직접 액세스할 수 있습니다. 이 플러그 인을 설치하려면 기본 Logstash 설치 디렉터리(이 경우 /usr/share/logstash/bin)에서 다음 명령을 실행합니다.

```bash
logstash-plugin install logstash-input-azureblob
```

Logstash를 시작하려면 다음 명령을 실행합니다.

```bash
sudo /etc/init.d/logstash start
```

이 플러그 인에 대한 자세한 내용은 [설명서](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)를 참조하세요.

### <a name="install-kibana"></a>Kibana 설치

1. 다음 명령을 실행하여 Kibana를 설치합니다.

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Kibana를 실행하려면 다음 명령을 사용합니다.

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Kibana 웹 인터페이스를 보려면 `http://localhost:5601`로 이동합니다.
4. 이 시나리오에서 흐름 로그에 사용된 인덱스 패턴은 "nsg-flow-logs"입니다. logstash.conf 파일의 "output" 섹션에서 인덱스 패턴을 변경할 수 있습니다.
5. Kibana 대시보드를 원격으로 보려면 **포트 5601**에 대해 액세스를 허용하는 인바운드 NSG 규칙을 만듭니다.

### <a name="create-a-kibana-dashboard"></a>Kibana 대시보드 만들기

샘플 대시보드가 다음 그림에 표시되어 경고에서 추세 및 세부 정보를 보여줍니다.

![그림 1][1]

[대시보드 파일](https://aka.ms/networkwatchernsgflowlogdashboard), [시각화 파일](https://aka.ms/networkwatchernsgflowlogvisualizations) 및 [저장된 검색 파일](https://aka.ms/networkwatchernsgflowlogsearch)을 다운로드합니다.

Kibana의 **관리** 탭 아래에서 **저장된 개체**로 이동하고 세 개 파일을 모두 가져옵니다. 그런 다음 **대시보드** 탭에서 샘플 대시보드를 열고 로드할 수 있습니다.

사용자가 관심 있는 메트릭에 맞는 시각화 및 대시보드를 만들 수 있습니다. Kibana의 [공식적인 설명서](https://www.elastic.co/guide/en/kibana/current/visualize.html)에서 Kibana 시각화 만들기에 대해 자세히 알아보세요.

### <a name="visualize-nsg-flow-logs"></a>NSG 흐름 로그 시각화

샘플 대시보드는 흐름 로그에 대한 다양한 시각화를 제공합니다.

1. Flows by Decision/Direction Over Time - 기간별 흐름 수를 보여주는 시계열 그래프입니다. 이러한 시각화 요소의 시간 단위와 범위를 편집할 수 있습니다. Flows by Decision은 허용 또는 거부 결정의 비율을 보여주고, Flows by Direction은 인바운드 및 아웃바운드 트래픽의 비율을 보여줍니다. 이러한 시각 효과를 사용하면 시간에 따른 트래픽 추세를 검토할 수 있고 스파이크 또는 비정상 패턴을 찾을 수 있습니다.

   ![figure2][2]

2. Flows by Destination/Source Port - 각 해당 포트에 대한 흐름의 분석 결과를 보여 주는 원형 차트입니다. 이 보기를 사용하면 가장 자주 사용되는 포트를 볼 수 있습니다. 원형 차트 내에서 특정 포트를 클릭하면 대시보드의 나머지 부분이 해당 포트의 흐름으로 필터링됩니다.

   ![figure3][3]

3. Number of Flows 및 Earliest Log Time – 기록된 흐름 수 및 가장 빨리 캡처된 로그의 날짜를 보여주는 메트릭입니다.

   ![figure4][4]

4. Flows by NSG and Rule – 각 NSG 내 흐름 분포와 각 NSG 내 규칙 분포를 보여주는 막대 그래프입니다. 여기에서 어떤 NSG와 규칙이 가장 많은 트래픽을 생성했는지 볼 수 있습니다.

   ![figure5][5]

5. Top 10 Source/Destination IPs - 상위 10개의 원본 및 대상 IP를 보여주는 막대형 차트입니다. 이러한 차트를 조정하여 표시되는 상위 IP를 늘리거나 줄일 수 있습니다. 여기에서 가장 자주 발생하는 IP와 각 IP에 대해 수행되는 트래픽 의사 결정(허용 또는 거부)을 볼 수 있습니다.

   ![figure6][6]

6. Flow Tuples - 이 표에서는 각 흐름 튜플 내에 포함된 정보와 해당하는 NGS 및 규칙을 보여줍니다.

   ![figure7][7]

대시보드 맨 위에 있는 쿼리 표시줄을 사용하여 구독 ID, 리소스 그룹, 규칙 또는 원하는 다른 변수 같은 흐름의 모든 매개 변수를 기준으로 대시보드를 필터링할 수 있습니다. Kibana의 쿼리 및 필터에 대한 자세한 내용을 보려면 [공식 설명서](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)를 참조하세요.

## <a name="conclusion"></a>결론

탄력적인 스택과 네트워크 보안 그룹 흐름 로그를 결합하여 네트워크 트래픽을 가시화하는 강력하고 사용자 지정 가능한 방법을 개발했습니다. 이러한 대시보드를 사용하면 네트워크 트래픽에 대한 통찰력을 신속하게 얻고 공유할 뿐만 모든 잠재적인 이상 요소를 필터링하고 조사할 수 있습니다. Kibana를 사용하면 이러한 대시보드를 조정하고 모든 보안, 감사 및 규정 준수 요구 사항에 맞는 특정 시각화 요소를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

[PowerBI에서 NSG 흐름 로그 시각화](network-watcher-visualize-nsg-flow-logs-power-bi.md)에서 Power BI로 NSG 흐름 로그를 시각화하는 방법에 대해 알아보세요.

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
