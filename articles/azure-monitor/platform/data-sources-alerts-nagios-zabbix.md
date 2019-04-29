---
title: Azure Monitor에서 Nagios 및 Zabbix 경고 수집 | Microsoft Docs
description: Nagios 및 Zabbix는 오픈 소스 모니터링 도구입니다. 다른 원본의 경고와 함께 분석하기 위해 이러한 도구에서 Azure Monitor로 경고를 수집할 수 있습니다.  이 문서에서는 이러한 시스템에서 경고를 수집하도록 Linux용 Log Analytics 에이전트를 구성하는 방법을 설명합니다.
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
ms.openlocfilehash: 0ed6747573edf4c059eb29d28107a22706c52856
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759982"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>Linux용 Log Analytics 에이전트에서 Azure Monitor에 Nagios 및 Zabbix의 경고 수집 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

[Nagios](https://www.nagios.org/) 및 [Zabbix](https://www.zabbix.com/)는 오픈 소스 모니터링 도구입니다. 다른 원본의 로그 데이터와 함께 분석하기 위해 이러한 도구에서 Azure Monitor로 경고를 수집할 수 있습니다.  이 문서에서는 이러한 시스템에서 경고를 수집하도록 Linux용 Log Analytics 에이전트를 구성하는 방법을 설명합니다.


> [!NOTE]
> [Azure Monitor에서 만든 경고](alerts-overview.md)는 로그 데이터와 별도로 저장되며 로그 쿼리에서 액세스할 수 없습니다.

 
## <a name="prerequisites"></a>필수 조건
Linux용 Log Analytics 에이전트는 최대 버전 4.2.x의 Nagios, 최대 버전 2.x의 Zabbix에서 경고를 수집하도록 지원합니다.

## <a name="configure-alert-collection"></a>경고 수집 구성

### <a name="configuring-nagios-alert-collection"></a>Nagios 경고 수집 구성
경고를 수집하려면 Nagios 서버에서 다음 단계를 수행합니다.

1. **omsagent** 사용자에게 Nagios 로그 파일 `/var/log/nagios/nagios.log`에 대한 읽기 액세스 권한을 부여합니다. nagios.log 파일이 `nagios` 그룹의 소유라는 가정 하에, **omsagent** 사용자를 **nagios** 그룹에 추가합니다. 

    sudo usermod -a -G nagios omsagent

2.  `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`에서 구성 파일을 수정합니다. 다음 항목이 포함되고 주석 처리되지 않도록 합니다.  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. omsagent 디먼 다시 시작

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Zabbix 경고 수집 구성
Zabbix 서버에서 경고를 수집하려면 *일반 텍스트*에서 사용자 및 암호를 지정해야 합니다.  이상적이지는 않지만 읽기 전용 권한을 가진 Zabbix 사용자를 만들어 관련 경고를 catch하는 것이 좋습니다.

Nagios 서버에서 경고를 수집하려면 다음 단계를 수행합니다.

1. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`에서 구성 파일을 수정합니다. 다음 항목이 포함되고 주석 처리되지 않도록 합니다.  Zabbix 환경에 대한 값으로 사용자 이름 및 암호를 변경합니다.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. omsagent 디먼 다시 시작

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>경고 레코드
Azure Monitor의 [로그 검색](../log-query/log-query-overview.md)을 사용하여 Nagios 및 Zabbix에서 경고 레코드를 검색할 수 있습니다.

### <a name="nagios-alert-records"></a>Nagios 경고 레코드

Nagios에서 수집된 경고는 **경고**의 **형식** 및 **Nagios**의 **SourceSystem**을 가집니다.  이들은 다음 표의 속성을 가집니다.

| 자산 | 설명 |
|:--- |:--- |
| `Type` |*경고* |
| `SourceSystem` |*Nagios* |
| `AlertName` |경고의 이름입니다. |
| `AlertDescription` | 경고에 대한 설명입니다. |
| `AlertState` | 서비스 또는 호스트의 상태입니다.<br><br>확인<br>경고<br>위로<br>아래로 |
| `HostName` | 경고를 생성한 호스트의 이름입니다. |
| `PriorityNumber` | 경고의 우선 순위 수준입니다. |
| `StateType` | 경고 상태의 형식입니다.<br><br>소프트 - 다시 확인되지 않은 문제입니다.<br>하드 - 특정 횟수를 다시 확인한 문제입니다.  |
| `TimeGenerated` |경고가 만들어진 날짜 및 시간입니다. |


### <a name="zabbix-alert-records"></a>Zabbix 경고 레코드
Zabbix에서 수집된 경고는 **경고**의 **형식** 및 **Zabbix**의 **SourceSystem**을 가집니다.  이들은 다음 표의 속성을 가집니다.

| 자산 | 설명 |
|:--- |:--- |
| `Type` |*경고* |
| `SourceSystem` |*Zabbix* |
| `AlertName` | 경고의 이름입니다. |
| `AlertPriority` | 경고의 심각도입니다.<br><br>분류되지 않음<br>정보<br>Warning<br>average<br>높음<br>심각  |
| `AlertState` | 경고의 상태입니다.<br><br>0 - 최신 상태입니다.<br>1 - 상태를 알 수 없습니다.  |
| `AlertTypeNumber` | 경고가 여러 문제 이벤트를 생성할 수 있는지 여부를 지정합니다.<br><br>0 - 최신 상태입니다.<br>1 - 상태를 알 수 없습니다.    |
| `Comments` | 경고에 대한 추가 설명입니다. |
| `HostName` | 경고를 생성한 호스트의 이름입니다. |
| `PriorityNumber` | 경고의 심각도를 나타내는 값입니다.<br><br>0 - 분류되지 않음<br>1 - 정보<br>2 - 경고<br>3 - 평균<br>4 - 높음<br>5 - 심각 |
| `TimeGenerated` |경고가 만들어진 날짜 및 시간입니다. |
| `TimeLastModified` |경고의 상태가 마지막 변경된 날짜 및 시간입니다. |


## <a name="next-steps"></a>다음 단계
* Azure Monitor의 [경고](alerts-overview.md)에 대해 알아봅니다.
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다. 
