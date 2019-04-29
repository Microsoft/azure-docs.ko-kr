---
title: Azure Network Watcher 및 오픈 소스 도구를 사용하여 네트워크 침입 검색 수행 | Microsoft Docs
description: 이 문서에서는 Azure Network Watcher 및 오픈 소스 도구를 사용하여 네트워크 침입 검색을 수행하는 방법에 대해 설명합니다.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 41b039c6598334d49b9ddb0608d7ad2ae045b223
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115501"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Network Watcher 및 오픈 소스 도구를 사용하여 네트워크 침입 검색 수행

패킷 캡처는 네트워크 IDS(침입 검색 시스템)를 구현하고 NSM(네트워크 보안 모니터링)을 수행하기 위한 핵심 구성 요소입니다. 패킷 캡처를 처리하고 가능한 네트워크 침입 및 악의적인 활동의 서명을 찾기 위한 여러 가지 오픈 소스 IDS 도구가 있습니다. Network Watcher에서 제공하는 패킷 캡처를 사용하여 나쁜 영향을 주는 침입 또는 취약성에 대해 네트워크를 분석할 수 있습니다.

이러한 오픈 소스 도구로 Suricata가 있으며, 의심스러운 이벤트가 발생할 때마다 경고를 트리거하고 네트워크 트래픽을 모니터링하는 규칙 집합을 사용하는 IDS 엔진입니다. Suricata는 멀티스레드 엔진을 제공하므로 빨라진 속도 및 효율성으로 네트워크 트래픽 분석을 수행할 수 있습니다. Suricata 및 해당 기능에 대한 자세한 내용은 https://suricata-ids.org/에서 웹 사이트를 방문하세요.

## <a name="scenario"></a>시나리오

이 문서에서는 Network Watcher, Suricata 및 탄력적 스택을 사용하여 네트워크 침입 검색을 수행하도록 사용자 환경을 설정하는 방법을 설명합니다. Network Watcher는 네트워크 침입 검색을 수행하는 데 사용되는 패킷 캡처를 제공합니다. Suricata는 패킷 캡처를 처리하고 지정된 위협의 규칙 집합과 일치하는 패킷에 따라 경고를 트리거합니다. 이러한 경고는 로컬 컴퓨터의 로그 파일에 저장됩니다. 탄력적 스택을 사용하여 Suricata에서 생성한 로그를 인덱싱하고 Kibana 대시보드를 만드는 데 사용하여 로그의 시각적 표현을 제공하고 잠재적인 네트워크 취약성을 신속하게 파악할 수 있습니다.  

![간단한 웹 애플리케이션 시나리오][1]

두 오픈 소스 도구 모두 Azure VM에서 설정하여 사용자의 Azure 네트워크 환경 내에서 분석을 수행할 수 있습니다.

## <a name="steps"></a>단계

### <a name="install-suricata"></a>Suricata 설치

설치의 다른 모든 메서드는 https://suricata.readthedocs.io/en/latest/install.html을 방문하세요.

1. VM의 명령줄 터미널에서 다음 명령을 실행합니다.

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. 설치를 확인하려면 `suricata -h` 명령을 실행하여 전체 명령 목록을 확인합니다.

### <a name="download-the-emerging-threats-ruleset"></a>Emerging Threats 규칙 집합 다운로드

이 단계에서는 Suricata에 대해 실행할 규칙이 없습니다. 네트워크에 검색할 구체적인 위협이 있는 경우 사용자 고유의 규칙을 만들거나 여러 공급자로부터 개발된 규칙 집합(예: Emerging Threats, Snort의 VRT 규칙)을 사용할 수 있습니다. Emerging Threats 규칙 집합은 여기에서 자유롭게 액세스할 수 있습니다.

규칙 집합을 다운로드하고 디렉터리에 복사합니다.

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Suricata로 패킷 캡처 처리

Suricata를 사용하여 패킷 캡처를 처리하려면 다음 명령을 실행합니다.

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
결과 경고를 확인하려면 fast.log 파일을 읽습니다.
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>탄력적 스택 설정

Suricata에서 생성하는 로그에는 네트워크에서 발생하는 작업에 대한 유용한 정보가 포함되지만 이러한 로그 파일은 읽고 이해하기 어렵습니다. Suricata를 탄력적 스택과 연결하여 로그에서 정보를 검색하고, 그래프화하며 분석하고 정보를 끌어낼 수 있는 Kibana 대시보드를 만들 수 있습니다.

#### <a name="install-elasticsearch"></a>Elasticsearch 설치

1. 이번 5.0 이상의 탄력적 스택에는 Java 8이 필요합니다. `java -version` 명령을 실행하여 버전을 확인합니다. java가 설치되지 않은 경우 [Azure 지원 JDK](https://aka.ms/azure-jdks)에 대한 설명서를 참조하세요.

1. 시스템에 맞는 이진 패키지를 다운로드합니다.

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    다른 설치 방법은 [Elasticsearch 설치](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)에서 확인할 수 있습니다.

1. 명령으로 Elasticsearch가 실행 중인지 확인합니다.

    ```
    curl http://127.0.0.1:9200
    ```

    다음과 유사한 응답이 표시됩니다.

    ```
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

탄력적 검색 설치에 대한 추가 정보는 [설치](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html) 페이지를 참조하세요.

### <a name="install-logstash"></a>Logstash 설치

1. Logstash를 설치하려면 다음 명령을 실행합니다.

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. 다음으로 eve.json 파일의 출력에서 읽어오도록 Logstash를 구성해야 합니다. 다음을 사용하여 logstash.conf 파일을 만듭니다.

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. 다음 내용을 파일에 추가합니다(eve.json 파일의 경로가 올바른지 확인).

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Logstash가 파일을 수집할 수 있도록 하려면 eve.json 파일에 대해 적절한 권한을 부여해야 합니다.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Logstash를 시작하려면 명령을 실행합니다.

    ```
    sudo /etc/init.d/logstash start
    ```

Logstash 설치에 대한 추가 정보는 [공식적인 설명서](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)를 참조하세요.

### <a name="install-kibana"></a>Kibana 설치

1. 다음 명령을 실행하여 Kibana를 설치합니다.

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Kibana를 실행하려면 다음 명령을 사용합니다.

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Kibana 웹 인터페이스를 보려면 `http://localhost:5601`로 이동합니다.
1. 이 시나리오에서 Suricata 로그에 대해 사용된 인덱스 패턴은 "logstash-*"입니다.

1. Kibana 대시보드를 원격으로 보려면 **포트 5601**에 대해 액세스를 허용하는 인바운드 NSG 규칙을 만듭니다.

### <a name="create-a-kibana-dashboard"></a>Kibana 대시보드 만들기

이 문서에서는 경고에 대한 추세 및 세부 정보를 볼 수 있는 샘플 대시보드를 제공했습니다.

1. 대시보드 파일은 [여기](https://aka.ms/networkwatchersuricatadashboard)에서, 시각화 파일은 [여기](https://aka.ms/networkwatchersuricatavisualization)에서, 저장된 검색 파일은 [여기](https://aka.ms/networkwatchersuricatasavedsearch)에서 다운로드합니다.

1. Kibana의 **관리** 탭 아래에서 **저장된 개체**로 이동하고 세 개 파일을 모두 가져옵니다. 그런 다음 **대시보드** 탭에서 샘플 대시보드를 열고 로드할 수 있습니다.

사용자가 관심 있는 메트릭에 맞는 시각화 및 대시보드를 만들 수 있습니다. Kibana의 [공식적인 설명서](https://www.elastic.co/guide/en/kibana/current/visualize.html)에서 Kibana 시각화 만들기에 대해 자세히 알아보세요.

![Kibana 대시보드][2]

### <a name="visualize-ids-alert-logs"></a>IDS 경고 로그 시각화

샘플 대시보드는 Suricata 경고 로그에 대한 다양한 시각화를 제공합니다.

1. GeoIP별 경고 – 지리적 위치에 따라 출처 국가별로 경고의 분포를 보여 주는 맵(IP로 확인)입니다.

    ![지역 ip][3]

1. 상위 10개 경고 - 가장 자주 트리거된 상위 10개 경고 및 이에 대한 설명을 요약하여 보여 줍니다. 개별 경고를 클릭하면 특정 경고와 관련된 정보로 대시보드가 필터링됩니다.

    ![이미지 4][4]

1. 경고 수 - 규칙 집합에 의해 트리거된 총 경고 수입니다.

    ![이미지 5][5]

1. 상위 20개 원본/대상 IP/포트 - 경고를 트리거한 상위 20개 IP 및 포트를 보여 주는 원형 차트입니다. 트리거된 경고 수 및 경고 종류를 표시하도록 특정 IP/포트로 필터링할 수 있습니다.

    ![이미지 6][6]

1. 경고 요약 – 각 개별 경고의 구체적인 세부 정보를 요약하여 보여주는 표입니다. 각 경고에 대해 관심이 있는 다른 매개 변수를 보여 주도록 이 표를 사용자 지정할 수 있습니다.

    ![이미지 7][7]

사용자 지정 시각화 및 대시보드 만들기에 대한 자세한 내용은 [Kibana의 공식적인 설명서](https://www.elastic.co/guide/en/kibana/current/introduction.html)를 참조하세요.

## <a name="conclusion"></a>결론

Network Watcher에서 제공하는 패킷 캡처와 Suricata와 같은 오픈 소스 IDS 도구를 결합하여 광범위한 위협에 대해 네트워크 침입 검색을 수행할 수 있습니다. 이러한 대시보드를 통해 네트워크 내에서 추세와 이상 현상을 신속하게 파악하고 데이터를 자세히 살펴, 악의적인 사용자 에이전트 또는 취약한 포트 등 경고의 근본 원인을 파악할 수 있습니다. 이렇게 추출된 데이터로 나쁜 영향을 주는 침입 시도로부터 네트워크를 보호 및 대응하는 방법을 합리적으로 결정하고 향후 네트워크에 대한 침입을 방지하는 규칙을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

[패킷 캡처를 사용하여 Azure Functions로 자동 관리 네트워크 모니터링 수행](network-watcher-alert-triggered-packet-capture.md)에서 경고를 기반으로 패킷 캡처를 트리거하는 방법에 대해 알아보세요.

[PowerBI에서 NSG 흐름 로그 시각화](network-watcher-visualize-nsg-flow-logs-power-bi.md)에서 Power BI로 NSG 흐름 로그를 시각화하는 방법에 대해 알아보세요.



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
