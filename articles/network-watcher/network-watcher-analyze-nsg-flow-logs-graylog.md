---
title: Azure 네트워크 보안 그룹 흐름 로그 분석 - Graylog | Microsoft Docs
description: Network Watcher 및 Graylog를 사용하여 Azure에서 네트워크 보안 그룹 흐름 로그를 관리하고 분석하는 방법을 알아봅니다.
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
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: a5fadcfce154740a79a8764f44f08b21ad18f4d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625286"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Network Watcher 및 Graylog를 사용하여 Azure에서 네트워크 보안 그룹 흐름 로그 관리 및 분석

[네트워크 보안 그룹 흐름 로그](network-watcher-nsg-flow-logging-overview.md)는 Azure 네트워크 인터페이스의 송/수신 IP 트래픽을 파악하는 데 사용할 수 있는 정보를 제공합니다. 흐름 로그는 네트워크 보안 그룹 규칙 기반의 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 네트워크 인터페이스, 흐름에 대한 5계층 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜) 및 트래픽이 허용 또는 거부되었는지 여부를 보여줍니다.

흐름 로깅을 사용하도록 설정된 네트워크 보안 그룹이 네트워크에 많이 있을 수 있습니다. 이러한 네트워크 보안 그룹 중 일부는 구문 분석하고 로그에서 정보를 얻기가 느리고 복잡할 수 있습니다. 이 문서에서는 Graylog(오픈 소스 로그 관리 및 분석 도구)와 Logstash(오픈 소스 서버 쪽 데이터 처리 파이프라인)를 사용하여 이러한 네트워크 보안 그룹 흐름 로그를 중앙에서 관리하는 솔루션을 제공합니다.

> [!Warning]
> 다음 단계는 흐름 로그 버전 1을 사용하여 작동합니다. 자세한 내용은 [네트워크 보안 그룹에 대한 흐름 로깅 소개](network-watcher-nsg-flow-logging-overview.md)를 참조하세요. 다음 지침은 수정 없이 로그 파일의 버전 2를 사용하여 작동하지 않습니다.

## <a name="scenario"></a>시나리오

네트워크 보안 그룹 흐름 로그는 Network Watcher를 사용하여 활성화됩니다. 흐름 로그는 Azure Blob Storage로 전달됩니다. Logstash 플러그 인은 Blob Storage에서 흐름 로그를 연결하고 처리하여 Graylog에 보내는 데 사용됩니다. 흐름 로그가 Graylog에 저장되면 분석되어 사용자 지정된 대시보드에 시각화될 수 있습니다.

![Graylog 워크플로](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>설치 단계

### <a name="enable-network-security-group-flow-logging"></a>네트워크 보안 그룹 흐름 로그 사용

이 시나리오에서는 계정에 있는 하나 이상의 네트워크 보안 그룹에서 네트워크 보안 그룹 흐름 로깅을 사용하도록 설정해야 합니다. 네트워크 보안 그룹 흐름 로그를 사용하도록 설정하는 방법에 대한 지침은  [네트워크 보안 그룹에 대한 흐름 로깅 소개](network-watcher-nsg-flow-logging-overview.md) 문서를 참조하세요.

### <a name="setting-up-graylog"></a>Graylog 설정

이 예에서 Graylog와 Logstash는 모두 Azure에 배포된 Ubuntu 14.04 서버에서 구성됩니다.

- Ubuntu에 설치하는 방법에 대한 단계별 지침은 Graylog의 [설명서](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html)를 참조하세요.
- [설명서](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif)에 따라 Graylog 웹 인터페이스도 구성해야 합니다.

이 예에서는 최소 Graylog 설정(즉, Graylog 단일 인스턴스)을 사용하지만, Graylog는 시스템 및 프로덕션 환경 요구 사항에 따라 리소스 전반에 걸쳐 크기 조정하도록 설계될 수 있습니다. 아키텍처 고려 사항 또는 심층적인 아키텍처 가이드에 대한 자세한 내용은 Graylog의 [설명서](https://docs.graylog.org/en/2.2/pages/architecture.html) 및 [아키텍처 가이드](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)를 참조하세요.

Graylog는 플랫폼 및 기본 설정에 따라 다양한 방법으로 설치할 수 있습니다. 설치 가능한 방법에 대한 전체 목록은 Graylog의 공식 [설명서](https://docs.graylog.org/en/2.2/pages/installation.html)를 참조하세요. Graylog 서버 애플리케이션은 Linux 배포판에서 실행되며, 다음과 같은 필수 구성 요소를 포함합니다.

-  Java SE 8 이상 - [Azul Azure JDK 설명서](https://aka.ms/azure-jdks)
-  Elastic Search 2.x(2.1.0 이상) - [Elasticsearch 설치 설명서](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 이상 - [MongoDB 설치 설명서](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Logstash 설치

Logstash는 JSON 형식의 흐름 로그를 흐름 튜플 수준으로 평면화하는 데 사용됩니다. 흐름 로그를 평면화하면 Graylog에서 로그를 더 쉽게 구성하고 검색할 수 있습니다.

1. Logstash를 설치하려면 다음 명령을 실행합니다.

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. 흐름 로그를 구문 분석하고 Graylog로 보내도록 Logstash를 구성합니다. Logstash.conf 파일을 만듭니다.

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. 파일에 다음 내용을 추가합니다. 강조 표시된 값을 변경하여 저장소 계정 세부 정보를 반영합니다.

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   제공되는 Logstash 구성 파일은 input, filter 및 output의 세 부분으로 구성됩니다. input 섹션은 Logstash에서 처리할 로그의 입력 원본을 지정합니다. 이 경우 Blob Storage에 저장된 네트워크 보안 그룹 흐름 로그 JSON 파일에 액세스할 수 있게 하는 Azure 블로그 입력 플러그 인(다음 단계에서 설치됨)을 사용합니다.

그런 후 필터 섹션은 각 개별 흐름 튜플 및 연결된 속성이 별도의 Logstash 이벤트가 되도록 각 흐름 로그 파일을 평면화합니다.

마지막으로 출력 섹션은 Graylog 서버에 각 Logstash 이벤트를 전달합니다. 필요에 따라 특정 요구에 맞게 Logstash 구성 파일을 수정합니다.

   > [!NOTE]
   > 이전 구성 파일에서는 Graylog 서버가 로컬 호스트 루프백 IP 주소 127.0.0.1에 구성된 것으로 가정합니다. 그렇지 않은 경우 출력 섹션의 호스트 매개 변수를 올바른 IP 주소로 변경해야 합니다.

Logstash 설치에 대한 자세한 지침은 Logstash [설명서](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)를 참조하세요.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Azure Blob Storage용 Logstash 입력 플러그 인 설치

Logstash 플러그 인을 사용하면 지정된 Blob Storage 계정에서 흐름 로그에 직접 액세스할 수 있습니다. 플러그 인을 설치하려면 기본 Logstash 설치 디렉터리(이 경우 /usr/share/logstash/bin)에서 다음 명령을 실행합니다.

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

이 플러그 인에 대한 자세한 내용은 [설명서](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)를 참조하세요.

### <a name="set-up-connection-from-logstash-to-graylog"></a>Logstash에서 Graylog로의 연결 설정

이제 Logstash를 사용하여 흐름 로그에 대한 연결을 설정하고 Graylog 서버를 설정했으므로 들어오는 로그 파일을 수락하도록 Graylog를 구성해야 합니다.

1. 구성된 URL을 사용하여 Graylog 서버 웹 인터페이스로 이동합니다. 브라우저를 `http://<graylog-server-ip>:9000/`에 연결하여 인터페이스에 액세스할 수 있습니다.

2. 구성 페이지로 이동하려면 위쪽 탐색 모음의 오른쪽에 있는 **시스템** 드롭다운 메뉴를 선택한 다음 **입력**을 클릭합니다.
   또는 `http://<graylog-server-ip>:9000/system/inputs`로 이동합니다.

   ![시작](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. 새 입력을 시작하려면 **입력 선택** 드롭다운에서 *GELF UDP*를 선택한 다음 양식을 작성합니다. GELF는 Graylog Extended Log Format의 약자입니다. GELF 형식은 Graylog에서 개발되었습니다. 이점에 대한 자세한 내용은 Graylog [설명서](https://docs.graylog.org/en/2.2/pages/gelf.html)를 참조하세요.

   Graylog 서버를 구성한 IP에 입력을 바인딩해야 합니다. IP 주소는 Logstash 구성 파일에 지정된 UDP 출력의 **host** 필드와 일치해야 합니다. 기본 포트는 *12201*이어야 합니다. 포트가 Logstash 구성 파일에 지정된 UDP 출력의 **port** 필드와 일치하는지 확인합니다.

   ![입력](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   입력을 시작하면 다음 그림과 같이 **로컬 입력** 섹션 아래에 해당 입력이 표시됩니다.

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Graylog 메시지 입력에 대한 자세한 내용은 [설명서](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs)를 참조하세요.

4. 이러한 구성이 완료되면 Logstash를 시작하여 `sudo systemctl start logstash.service` 명령으로 흐름 로그에서 읽기를 시작할 수 있습니다.

### <a name="search-through-graylog-messages"></a>Graylog 메시지를 통해 검색

Graylog 서버에서 메시지를 수집하도록 허용된 약간의 시간이 지나면 메시지를 검색할 수 있습니다. Graylog 서버로 보내는 메시지를 확인하려면 **입력** 구성 페이지에서 만든 GELF UDP 입력의 "**Show received messages(수신 메시지 표시)**" 단추를 클릭합니다. 다음 그림과 비슷한 화면으로 이동하게 됩니다. 

![히스토그램](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

파란색 "%{Message}" 링크를 클릭하면 다음 그림과 같이 각 메시지를 확장하여 각 흐름 튜플의 매개 변수를 표시합니다.

![메시지](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

검색할 특정 메시지 필드를 선택하지 않으면 기본적으로 모든 메시지 필드가 검색에 포함됩니다. 특정 메시지(예: 특정 원본 IP의 흐름 튜플)를 검색하려면 Graylog 검색 쿼리 언어를 [문서화](https://docs.graylog.org/en/2.2/pages/queries.html)된 대로 사용할 수 있습니다.

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Graylog를 사용하여 네트워크 보안 그룹 흐름 로그 분석

Graylog 실행을 설정했으므로 기능 중 일부를 사용하여 흐름 로그 데이터를 더 잘 파악할 수 있습니다. 이러한 방법 중 하나는 대시보드를 사용하여 데이터의 특정 보기를 만드는 것입니다.

### <a name="create-a-dashboard"></a>대시보드 만들기

1. 위쪽 탐색 모음에서 **대시보드**를 선택하거나 `http://<graylog-server-ip>:9000/dashboards/`로 이동합니다.

2. 여기서 녹색 **대시보드 만들기** 단추를 클릭하고 대시보드의 제목과 설명이 포함된 간단한 양식을 작성합니다. **저장** 단추를 눌러 새 대시보드를 만듭니다. 다음 그림과 비슷한 대시보드가 표시됩니다.

    ![대시보드](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>위젯 추가

대시보드의 제목을 클릭하여 볼 수 있지만, 위젯을 추가하지 않았으므로 현재 비어 있습니다. 대시보드에 추가할 수 있는 쉽고 유용한 형식 위젯은 **빠른 값** 차트이며, 여기에서는 선택한 필드 및 해당 배포의 값 목록이 표시됩니다.

1. 위쪽 탐색 모음에서 **검색**을 선택하여 흐름 로그를 받는 UDP 입력의 검색 결과로 다시 이동합니다.

2. 화면 왼쪽의 **검색 결과** 패널 아래에서 **필드** 탭을 찾습니다. 여기에서는 들어오는 흐름 튜플 메시지 각각의 다양한 필드가 나열됩니다.

3. 시각화하려는 매개 변수를 모두 선택합니다(이 예제에서는 IP 원본이 선택됨). 가능한 위젯 목록을 표시하려면 필드의 왼쪽에 있는 파란색 드롭다운 화살표를 클릭한 다음 **빠른 값**을 선택하여 위젯을 생성합니다. 다음 그림과 비슷하게 표시됩니다.

   ![원본 IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. 여기서 위젯의 오른쪽 위 모서리에 있는 **대시보드에 추가** 단추를 선택하고, 추가할 해당 대시보드를 선택할 수 있습니다.

5. 대시보드로 다시 이동하여 방금 추가한 위젯을 확인합니다.

   대시보드에 다양한 다른 위젯(예: 히스토그램 및 개수)을 추가하여 다음 그림처럼 샘플 대시보드와 같은 중요한 메트릭을 추적할 수 있습니다.

   ![흐름 로그 대시보드](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    대시보드 및 다른 형식의 위젯에 대한 자세한 설명은 Graylog [설명서](https://docs.graylog.org/en/2.2/pages/dashboards.html)를 참조하세요.

Network Watcher와 Graylog를 통합함으로써 이제는 네트워크 보안 그룹 흐름 로그를 중앙에서 편리하게 관리하고 시각화할 수 있습니다. Graylog에는 흐름 로그를 자세히 관리하고 네트워크 트래픽을 더 잘 파악하는 데 사용할 수 있는 스트림 및 경고와 같은 강력한 기능이 다양하게 포함되어 있습니다. 이제 Graylog를 설정하고 Azure에 연결했으므로 제공되는 다른 기능들도 자유롭게 계속 탐색해 보세요.

## <a name="next-steps"></a>다음 단계

 [Power BI를 사용하여 네트워크 보안 그룹 흐름 로그 시각화](network-watcher-visualize-nsg-flow-logs-power-bi.md)에서 Power BI를 사용하여 네트워크 보안 그룹 흐름 로그를 시각화하는 방법을 알아봅니다.
