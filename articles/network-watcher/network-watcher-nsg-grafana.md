---
title: Network Watcher 및 Grafana를 사용하여 네트워크 보안 그룹 흐름 로그 관리 | Microsoft Docs
description: Network Watcher 및 Grafana를 사용하여 Azure에서 네트워크 보안 그룹 흐름 로그를 관리하고 분석합니다.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: mareat
ms.openlocfilehash: 73173c144f979d4a10b90a16aec783fe51a3f90e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116256"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Network Watcher 및 Grafana를 사용하여 네트워크 보안 그룹 흐름 로그 관리 및 분석

[NSG(네트워크 보안 그룹) 흐름 로그](network-watcher-nsg-flow-logging-overview.md)는 네트워크 인터페이스의 송/수신 IP 트래픽을 이해하는 데 사용할 수 있는 정보를 제공합니다. 이러한 흐름 로그는 트래픽이 허용되거나 거부된 경우 각 NSG 규칙을 기준으로 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC, 흐름에 대한 5개의 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜)를 보여줍니다.

> [!Warning]  
> 다음 단계는 흐름 로그 버전 1을 사용하여 작동합니다. 자세한 내용은 [네트워크 보안 그룹에 대한 흐름 로깅 소개](network-watcher-nsg-flow-logging-overview.md)를 참조하세요. 다음 지침은 수정 없이 로그 파일의 버전 2를 사용하여 작동하지 않습니다.

네트워크에는 흐름 로깅을 사용하도록 설정한 많은 NSG가 있을 수 있습니다. 이러한 양의 로깅 데이터는 구문 분석하고 심층적으로 이해하기 어려울 수 있습니다. 이 문서에서는 Grafana, 오픈 소스 그래프 도구, ElasticSearch, 분산 검색 및 분석 엔진, Logstash(오픈 소스 서버 쪽 데이터 처리 파이프라인)를 사용하여 일한 NSG 흐름 로그를 중앙에서 관리하기 위한 솔루션을 제공합니다.  

## <a name="scenario"></a>시나리오

NSG 흐름 로그는 Network Watcher를 사용하여 사용할 수 있으며 Azure Blob Storage에 저장됩니다. Logstash 플러그 인은 Blob Storage에 연결하여 흐름 로그를 처리하고 ElasticSearch에 전송하는 데 사용됩니다.  흐름 로그가 ElasticSearch에 저장되면 Grafana의 사용자 지정된 대시보드를 사용하여 분석하고 시각화할 수 있습니다.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>설치 단계

### <a name="enable-network-security-group-flow-logging"></a>네트워크 보안 그룹 흐름 로그 사용

이 시나리오에서는 계정에 있는 하나 이상의 네트워크 보안 그룹에서 네트워크 보안 그룹 흐름 로깅을 사용하도록 설정해야 합니다. 네트워크 보안 흐름 로그를 사용하도록 설정하는 방법에 대한 지침은 [네트워크 보안 그룹에 대한 흐름 로깅 소개](network-watcher-nsg-flow-logging-overview.md) 문서를 참조하세요.

### <a name="setup-considerations"></a>설치 고려 사항

이 예제에서 Grafana, ElasticSearch 및 Logstash는 Azure에 배포된 Ubuntu 16.04 LTS 서버에서 구성됩니다. 이 최소 설치는 세 가지 구성 요소를 모두 실행하는 데 사용되며 모든 구성 요소는 동일한 VM에서 실행됩니다. 이 설치는 테스트 및 중요하지 않은 워크로드에만 사용해야 합니다. Logstash, Elasticsearch 및 Grafana는 여러 인스턴스 간에 독립적으로 확장되도록 설계할 수 있습니다. 자세한 내용은 이러한 각 구성 요소에 대한 설명서를 참조하세요.

### <a name="install-logstash"></a>Logstash 설치

Logstash를 사용하여 JSON 형식 흐름 로그를 흐름 튜플 수준으로 평면화합니다.

1. Logstash를 설치하려면 다음 명령을 실행합니다.

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. 흐름 로그를 구문 분석한 후 ElasticSearch로 전송하도록 Logstash를 구성합니다. 다음을 사용하여 Logstash.conf 파일을 만듭니다.

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. 파일에 다음 내용을 추가합니다. 저장소 계정 세부 정보를 반영하도록 저장소 계정 이름 및 액세스 키를 변경합니다.

   ```bash
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

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
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

제공되는 Logstash 구성 파일은 입력, 필터 및 출력의 세 부분으로 구성됩니다.
입력 섹션은 Logstash가 처리하는 로그의 입력 원본을 지정합니다. 이 경우는 Blob Storage에 저장된 NSG 흐름 로그 JSON 파일에 액세스할 수 있도록 하는 "azureblob" 입력 플러그 인(다음 단계에서 설치)을 사용할 것입니다. 

그런 후 필터 섹션은 각 개별 흐름 튜플 및 연결된 속성이 별도의 Logstash 이벤트가 되도록 각 흐름 로그 파일을 평면화합니다.

마지막으로 출력 섹션은 ElasticSearch 서버에 각 Logstash 이벤트를 전달합니다. 특정 요구에 맞게 Logstash 구성 파일을 자유롭게 수정합니다.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Azure Blob Storage에 대한 Logstash 입력 플러그 인 설치

이 Logstash 플러그 인을 사용하면 지정된 Blob Storage 계정에서 흐름 로그에 직접 액세스할 수 있습니다. 이 플러그 인을 설치하려면 기본 Logstash 설치 디렉터리(이 경우 /usr/share/logstash/bin)에서 다음 명령을 실행합니다.

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

이 플러그 인에 대한 자세한 내용은 [Azure Storage Blob에 대한 Logstash 입력 플러그 인](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)을 참조하세요.

### <a name="install-elasticsearch"></a>ElasticSearch 설치

다음 스크립트를 사용하여 ElasticSearch를 설치할 수 있습니다. ElasticSearch를 설치하는 방법에 대한 자세한 내용은 [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html)을 참조하세요.

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Grafana 설치

Grafana를 설치하려면 다음 명령을 실행합니다.

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

추가 설치 정보에 대해서는 [Debian/Ubuntu에 설치](https://docs.grafana.org/installation/debian/)를 참조하세요.

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>ElasticSearch 서버를 데이터 원본으로 추가

다음으로 흐름 로그를 데이터 원본으로 포함하는 ElasticSearch 인덱스를 추가해야 합니다. **데이터 원본 추가**를 선택하고 양식에 관련 정보를 채운 후 데이터 원본을 추가할 수 있습니다. 이 구성의 샘플은 다음 스크린샷에서 찾을 수 있습니다.

![데이터 원본 추가](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>대시보드 만들기

NSG 흐름 로그가 포함된 ElasticSearch 인덱스에서 읽을 수 있게 Grafana를 성공적으로 구성했으므로 이제 대시보드를 만들고 개인별로 설정할 수 있습니다. 새 대시보드를 만들려면 **첫 번째 대시보드 만들기**를 선택합니다. 다음 샘플 그래프 구성은 NSG 규칙별로 분할된 흐름을 보여 줍니다.

![대시보드 그래프](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

다음 스크린샷에는 상위 흐름 및 빈도를 보여 주는 그래프와 차트가 나와 있습니다. 흐름을 NSG 규칙 및 결정을 기준으로 표시할 수도 있습니다. Grafana는 사용자 지정 기능이 뛰어나므로 특정 모니터링 요구에 맞게 대시보드를 만들 수 있습니다. 다음 예제에서는 일반적인 대시보드를 보여 줍니다.

![대시보드 그래프](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>결론

Network Watcher를 ElasticSearch 및 Grafana와 통합하여, 이제 중앙에서 편리하게 NSG 흐름 로그와 기타 데이터를 관리하고 시각화할 수 있습니다. Grafana에는 흐름 로그를 보다 잘 관리하고 네트워크 트래픽을 쉽게 이해하는 데 사용할 수 있는 기타 강력한 그래프 기능도 다양하게 포함되어 있습니다. Grafana 인스턴스가 설치되고 Azure에 연결되었으므로 제공되는 다른 기능들도 자유롭게 탐색하세요.

## <a name="next-steps"></a>다음 단계

- [Network Watcher](network-watcher-monitoring-overview.md) 사용에 대해 자세히 알아보기

