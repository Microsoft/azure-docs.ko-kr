---
title: HDInsight에서 WebHCat 오류 이해 및 해결 - Azure
description: HDInsight의 WebHCat에서 반환된 일반 오류 및 문제를 해결하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 683580ba65ad775ccec105c78cc1af66fbb63c37
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761120"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>HDInsight WebHCat에서 받은 오류 이해 및 해결

HDInsight에서 WebHCat을 사용할 때 수신되는 오류 및 해결 방법을 알아봅니다. WebHCat은 Azure PowerShell 및 Data Lake Tools for Visual Studio와 같은 클라이언트 쪽 도구에서 내부적으로 사용됩니다.

## <a name="what-is-webhcat"></a>WebHCat이란?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)은 [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog)용 REST API, Apache Hadoop용 테이블 및 스토리지 관리 계층입니다. WebHCat은 HDInsight 클러스터에서 기본적으로 활성화되어있으며 다양한 도구에서 사용되어 클러스터에 로그인하지 않고 작업을 제출하고, 작업 상태 등을 가져옵니다.

## <a name="modifying-configuration"></a>구성 수정

> [!IMPORTANT]  
> 구성된 최대값을 초과하기 때문에 이 문서에 나열된 몇몇 오류가 발생합니다. 해결 단계에서 값을 변경할 수 있음을 언급하면, 다음 중 하나를 사용하여 변경해야 합니다.

* **Windows** 클러스터: 클러스터를 만드는 동안 값을 구성하려면 스크립트 동작을 사용합니다. 자세한 내용은 [스크립트 동작 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요.

* **Linux** 클러스터: Apache Ambari(Web 또는 REST API)를 사용하여 값을 수정합니다. 자세한 내용은 [Apache Ambari를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

### <a name="default-configuration"></a>기본 구성

다음과 같은 기본값이 초과되면 WebHCat 성능이 저하되거나 오류가 발생할 수 있습니다.

| 설정 | 기능 | 기본값 |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |동시에 활성화될 수 있는 최대 작업 수(보류 또는 실행 중인) |10000 |
| [templeton.exec.max-procs][max-procs] |동시에 제공 될 수 있는 최대 요청 수 |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |작업 기록이 보존되는 기간(일) |7 일 |

## <a name="too-many-requests"></a>너무 많은 요청

**HTTP 상태 코드**: 429

| 원인 | 해결 방법 |
| --- | --- |
| 분당 WebHCat으로 제공되는 최대 동시 요청 수를 초과했습니다(기본값 20). |작업을 줄여 동시 요청의 최대 수 이상으로 제출하지 않거나 `templeton.exec.max-procs`를 수정하여 동시 요청 제한을 늘립니다. 자세한 내용은 [구성 수정](#modifying-configuration)을 참조하세요. |

## <a name="server-unavailable"></a>서버 사용할 수 없음

**HTTP 상태 코드**: 503

| 원인 | 해결 방법 |
| --- | --- |
| 이 상태 코드는 클러스터에 대한 기본 및 보조 헤드 노드 간 장애 조치 중에 발생합니다. |2분을 기다린 후 작업을 다시 시도합니다. |

## <a name="bad-request-content-could-not-find-job"></a>잘못된 요청 콘텐츠: 작업을 찾을 수 없음

**HTTP 상태 코드**: 400

| 원인 | 해결 방법 |
| --- | --- |
| 작업 세부 정보는 작업 기록 클리너에서  정리됩니다. |작업 기록에 대한 기본 보존 기간은 7일입니다. 기본 보존 기간은 `mapreduce.jobhistory.max-age-ms`를 수정하여 변경할 수 있습니다. 자세한 내용은 [구성 수정](#modifying-configuration)을 참조하세요. |
| 장애 조치때문에 작업이 중단되었습니다. |최대 2분 동안 작업 제출을 다시 시도하세요. |
| 잘못 된 작업 ID는 사용한 |작업 ID가 올바른 경우 확인 |

## <a name="bad-gateway"></a>나쁜 게이트웨이

**HTTP 상태 코드**: 502

| 원인 | 해결 방법 |
| --- | --- |
| WebHCat 프로세스 내에서 내부 가비지 컬렉션이 발생합니다. |가비지 컬렉션이 WebHCat 서비스 다시 시작 또는 완료를 대기합니다. |
| ResourceManager 서비스에서 응답을 기다리는 동안 시간이 초과되었습니다. 이 오류는 활성 애플리케이션 수가 구성된 최대값(기본값 10,000)에 도달할 때 발생할 수 있습니다. |현재 실행 중인 작업을 완료하거나 `yarn.scheduler.capacity.maximum-applications`을 수정하여 동시 작업 제한을 늘릴 때까지 대기합니다. 자세한 내용은 [구성 수정](#modifying-configuration) 섹션을 참조하세요. |
| `Fields`가 `*`로 설정되어 있는 동안 [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) 호출을 통해 모든 작업을 검색하려고 시도합니다. |*모든* 작업 세부 정보를 검색하지는 마세요. 대신 사용 하 여 `jobid` 만 특정 작업 ID 보다 큰 작업에 대 한 세부 정보를 검색 하려면 또는 `Fields`를 사용하지 마십시오. |
| 헤드 노드의 장애 조치 중 WebHCat 서비스가 종료됩니다. |2분을 기다린 후 작업을 다시 시도합니다. |
| WebHCat을 통해 전송되는 500개 이상의 보류 중인 작업이 있습니다. |더 많은 작업을 제출하기 전에 현재 보류 중인 작업이 완료될 때까지 대기합니다. |

[maximum-applications]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
