---
title: Azure Monitor에서 CollectD의 데이터 수집 | Microsoft Docs
description: CollectD는 주기적으로 애플리케이션의 데이터 및 시스템 수준 정보를 수집하는 오픈 소스 Linux 디먼입니다.  이 문서에서는 Azure Monitor에서 CollectD의 데이터를 수집하는 방법에 대한 정보를 제공합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 2118f137f2c0d32f891a170c3509bceee7ba13ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764963"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Azure Monitor에서 Linux 에이전트의 CollectD에서 데이터 수집
[CollectD](https://collectd.org/)는 주기적으로 애플리케이션의 성능 메트릭 및 시스템 수준 정보를 수집하는 오픈 소스 Linux 디먼입니다. 예제 애플리케이션은 JVM(Java Virtual Machine), MySQL 서버 및 Nginx를 포함합니다. 이 문서에서는 Azure Monitor에서 CollectD의 성능 데이터를 수집하는 방법에 대한 정보를 제공합니다.

사용 가능한 플러그 인의 전체 목록은 [플러그 인의 테이블](https://collectd.org/wiki/index.php/Table_of_Plugins)에서 찾을 수 있습니다.

![CollectD 개요](media/data-sources-collectd/overview.png)

다음 CollectD 구성은 CollectD 데이터를 Linux용 Log Analytics 에이전트로 라우팅하도록 Linux용 Log Analytics 에이전트에 포함되어 있습니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

또한 5.5 이전의 collectD 버전을 사용하는 경우 다음 구성을 대신 사용합니다.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

CollectD 구성은 기본값`write_http` 플러그 인을 사용하여 26000 포트를 통해 성능 메트릭 데이터를 Linux용 Log Analytics 에이전트에 보냅니다. 

> [!NOTE]
> 필요한 경우 이 포트는 사용자 지정 정의된 포트로 구성될 수 있습니다.

또한 Linux용 Log Analytics 에이전트는 CollectD 메트릭에 대해 26000 포트에서 수신한 다음, Azure Monitor 스키마 메트릭으로 변환합니다. 다음은 Linux용 Log Analytics 에이전트 구성 `collectd.conf`입니다.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>지원되는 버전
- Azure Monitor는 현재 CollectD 버전 4.8 이상을 지원합니다.
- CollectD 메트릭 수집에 Linux용 Log Analytics 에이전트 v1.1.0-217 이상이 필요합니다.


## <a name="configuration"></a>구성
Azure Monitor에서 CollectD 데이터의 컬렉션을 구성하는 기본 단계는 다음과 같습니다.

1. write_http 플러그 인을 사용하여 Linux용 Log Analytics 에이전트에 데이터를 보내도록 CollectD를 구성합니다.  
2. 적절한 포트에서 CollectD 데이터에 대해 수신 대기하도록 Linux용 Log Analytics 에이전트를 구성합니다.
3. CollectD 및 Linux용 Log Analytics 에이전트를 다시 시작합니다.

### <a name="configure-collectd-to-forward-data"></a>데이터를 전달하도록 CollectD 구성 

1. CollectD 데이터를 Linux용 Log Analytics 에이전트로 라우팅하려면 `oms.conf`를 CollectD의 구성 디렉터리에 추가해야 합니다. 이 파일의 대상은 컴퓨터의 Linux 배포판에 따라 달라집니다.

    CollectD config 디렉터리가 /etc/collectd.d/에 있는 경우:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    CollectD config 디렉터리가 /etc/collectd/collectd.conf.d/에 있는 경우:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >5.5 이전의 CollectD 버전의 경우 위와 같이 `oms.conf`에서 태그를 수정해야 합니다.
    >

2. collectd.conf를 원하는 작업 영역의 omsagent 구성 디렉터리에 복사합니다.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. 다음 명령을 사용하여 CollectD 및 Linux용 Log Analytics 에이전트를 다시 시작합니다.

    sudo service collectd restart  sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>CollectD 메트릭을 Azure Monitor 스키마로 변환
Linux용 Log Analytics 에이전트에서 이미 수집된 인프라 메트릭과 CollectD에서 수집된 새 메트릭 간에 친숙한 모델을 유지하기 위해 다음 스키마 매핑이 사용됩니다.

| CollectD 메트릭 필드 | Azure Monitor 필드 |
|:--|:--|
| `host` | Computer |
| `plugin` | 없음 |
| `plugin_instance` | 인스턴스 이름<br>**plugin_instance**가 *null*인 경우 InstanceName="*_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>**type_instance**가 *null*인 경우 CounterName=**비어 있음** |
| `dsnames[]` | CounterName |
| `dstypes` | 없음 |
| `values[]` | CounterValue |

## <a name="next-steps"></a>다음 단계
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다. 
* [사용자 지정 필드](custom-fields.md)를 사용하여 syslog 레코드의 데이터를 개별 필드로 구문 분석합니다.
