---
title: Azure HDInsight 클러스터 자동 크기 조정
description: Azure HDInsight 자동 크기 조정 기능을 사용 하 여 자동으로 클러스터 크기 조정 Apache Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/11/2020
ms.openlocfilehash: 172753f6bbcc47ed8ae9061b71ca3291e95b7a33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162857"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터 자동 크기 조정

> [!Important]
> 자동 크기 조정 기능은 2019 년 5 월 8 일에 생성 된 Apache Spark, ApacheHive, LLAP 및 Apache HBase 클러스터에 대해서만 작동 합니다. LLAP 및 HBase에 대 한 자동 크기 조정은 미리 보기 상태입니다.

Azure HDInsight의 클러스터 자동 크기 조정 기능은 클러스터의 작업자 노드 수를 자동으로 확장 및 축소 합니다. 클러스터의 다른 노드 유형은 현재 확장할 수 없습니다.  새 HDInsight 클러스터를 만드는 동안 작업자 노드의 최소 및 최대 수를 설정할 수 있습니다. 자동 크기 조정은 분석 부하의 리소스 요구 사항을 모니터링 하 고 작업자 노드 수를 확장 하거나 축소 합니다. 이 기능에 대 한 추가 비용은 없습니다.

## <a name="cluster-compatibility"></a>클러스터 호환성

다음 표에서는 자동 크기 조정 기능과 호환 되는 클러스터 유형 및 버전을 설명 합니다.

| 버전 | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 (ESP 제외) | yes | yes | yes | 예* | 예 | 예 | 예 |
| HDInsight 4.0 (ESP 제외) | yes | yes | yes | 예* | 예 | 예 | 예 |
| HDInsight 3.6 및 ESP | yes | yes | yes | 예* | 예 | 예 | 예 |
| HDInsight 4.0 및 ESP | yes | yes | yes | 예* | 예 | 예 | 예 |

\* HBase 클러스터는 부하를 기반으로 하는 것이 아니라 일정 기반 크기 조정에 대해서만 구성할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

HDInsight 클러스터에 대 한 부하 기반 크기 조정 또는 일정 기반 크기 조정을 선택할 수 있습니다. 부하 기반 크기 조정 기능을 사용 하면 CPU 사용률을 최적화 하 고 실행 비용을 최소화할 수 있도록 설정 하는 범위 내에서 클러스터의 노드 수가 변경 됩니다.

일정 기반 크기 조정에서는 특정 시간에 적용 되는 조건에 따라 클러스터의 노드 수를 변경 합니다. 이러한 조건은 클러스터를 원하는 수의 노드로 확장 합니다.

### <a name="metrics-monitoring"></a>메트릭 모니터링

자동 크기 조정은 지속적으로 클러스터를 모니터링하면서 다음 메트릭을 수집합니다.

|메트릭|Description|
|---|---|
|총 보류 중인 CPU|보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 코어 수입니다.|
|총 보류 중인 메모리|보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 메모리(MB)입니다.|
|총 사용 가능한 CPU|활성 작업자 노드에서 사용되지 않는 모든 코어의 합계입니다.|
|사용 가능한 총 메모리|활성 작업자 노드에서 사용되지 않는 메모리(MB)의 합계입니다.|
|노드당 사용 되는 메모리|작업자 노드의 부하입니다. 메모리 사용량이 10GB인 작업자 노드는 메모리 사용량이 2GB인 작업자보다 부하가 많은 것으로 간주됩니다.|
|노드당 응용 프로그램 마스터 수|작업자 노드에서 실행되는 AM(애플리케이션 마스터) 수입니다. 오전 2 시 컨테이너를 호스트 하는 작업자 노드는 제로 AM 컨테이너를 호스트 하는 작업자 노드 보다 더 중요 한 것으로 간주 됩니다.|

위의 메트릭은 60초마다 확인됩니다. 자동 크기 조정은 이러한 메트릭에 따라 확장 및 축소 결정을 내립니다.

### <a name="load-based-cluster-scale-up"></a>부하 기반 클러스터 확장

다음 조건이 검색 되 면 자동 크기 조정에서 강화 요청을 실행 합니다.

* 총 보류 중인 CPU가 3 분 넘게 총 사용 가능한 CPU 보다 큽니다.
* 총 보류 중인 메모리가 3 분 넘게 사용 가능한 총 메모리 보다 큽니다.

HDInsight 서비스는 현재 CPU 및 메모리 요구 사항을 충족 하는 데 필요한 새 작업자 노드 수를 계산한 다음 필요한 노드 수를 추가 하기 위한 확장 요청을 실행 합니다.

### <a name="load-based-cluster-scale-down"></a>부하 기반 클러스터 확장

다음 조건이 검색 되 면 자동 크기 조정에서 수평 확장 요청을 실행 합니다.

* 보류 중인 총 CPU가 사용 가능한 총 CPU보다 작은 시간이 10분을 초과합니다.
* 보류 중인 총 메모리가 사용 가능한 총 메모리보다 작은 시간이 10분을 초과합니다.

노드당 AM 컨테이너 수와 현재 CPU 및 메모리 요구 사항에 따라 자동 크기 조정에서 특정 수의 노드를 제거 하는 요청을 실행 합니다. 또한 서비스는 현재 작업 실행을 기반으로 제거할 노드를 검색 합니다. Scale down 작업은 먼저 노드를 add-on 다음 클러스터에서 제거 합니다.

## <a name="get-started"></a>시작하기

### <a name="create-a-cluster-with-load-based-autoscaling"></a>부하 기반 자동 크기 조정을 사용 하 여 클러스터 만들기

클러스터에서 자동 크기 조정을 사용 하려면 클러스터를 만들 때 **자동 크기 조정 사용** 옵션을 사용 하도록 설정 해야 합니다. 부하 기반 크기 조정으로 자동 크기 조정 기능을 사용 하도록 설정 하려면 일반 클러스터 만들기 프로세스의 일부로 다음 단계를 완료 합니다.

1. **구성 + 가격 책정** 탭에서 **자동 크기 조정 사용** 확인란을 선택 합니다.
1. **자동 크기 조정 유형**아래에서 **부하 기반** 을 선택 합니다.
1. 다음 속성에 대해 원하는 값을 입력 합니다.  

    * **작업자 노드에**대 한 초기 **노드 수** 입니다.
    * **최소** 작업자 노드 수입니다.
    * **최대** 작업자 노드 수입니다.

    ![작업자 노드 부하 기반 자동 크기 조정 사용](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

초기 작업자 노드 수는 최소 작업자 노드 수~최대 작업자 노드 수 사이여야 합니다(최댓값 및 최솟값 포함). 이 값은 생성 될 때 클러스터의 초기 크기를 정의 합니다. 최소 작업자 노드 수는 3 개 이상으로 설정 해야 합니다. 클러스터를 3 개 미만의 노드로 확장 하면 파일 복제가 충분 하지 않기 때문에 안전 모드에서 중단 될 수 있습니다.  자세한 내용은 [안전 모드에서 중단 하기](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)를 참조 하세요.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>일정 기반 자동 크기 조정을 사용 하 여 클러스터 만들기

일정 기반 크기 조정으로 자동 크기 조정 기능을 사용 하도록 설정 하려면 일반 클러스터 만들기 프로세스의 일부로 다음 단계를 완료 합니다.

1. **구성 + 가격 책정** 탭에서 **자동 크기 조정 사용** 확인란을 선택 합니다.
1. **작업자 노드에**대 한 **노드 수** 를 입력 합니다 .이 노드는 클러스터의 확장에 대 한 제한을 제어 합니다.
1. **자동 크기 조정 유형**아래에서 **일정 기반** 옵션을 선택 합니다.
1. **구성** 을 선택 하 여 **자동 크기 조정 구성** 창을 엽니다.
1. 표준 시간대를 선택 하 고 **+ 조건 추가** 를 클릭 합니다.
1. 새 조건이 적용 되는 요일을 선택 합니다.
1. 조건이 적용 되는 시간 및 클러스터의 크기를 조정 해야 하는 노드 수를 편집 합니다.
1. 필요한 경우 조건을 더 추가 합니다.

    ![작업자 노드 일정 기반 생성 사용](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

노드 수는 3에서 조건을 추가 하기 전에 입력 한 최대 작업자 노드 수와 같아야 합니다.

### <a name="final-creation-steps"></a>최종 생성 단계

부하 기반 및 일정 기반 크기 조정 모두에 대해 **노드 크기**아래의 드롭다운 목록에서 vm을 선택 하 여 작업자 노드의 vm 유형을 선택 합니다. 각 노드 유형에 대 한 VM 유형을 선택한 후 전체 클러스터의 예상 비용 범위를 확인할 수 있습니다. VM 유형을 예산에 맞게 조정 합니다.

![작업자 노드 일정 기반 자동 크기 조정 노드 크기 사용](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

구독에는 각 Azure 지역에 대한 용량 할당량이 있습니다. 헤드 노드의 코어 수와 최대 작업자 노드 수를 더한 값이 용량 할당량을 초과하면 안 됩니다. 그러나 이 할당량은 소프트 제한이며, 언제든지 지원 티켓을 만들어서 간편하게 할당량을 늘릴 수 있습니다.

> [!Note]  
> 총 코어 할당량 한도를 초과 하면 ' 최대 노드가이 지역에서 사용 가능한 코어 수를 초과 했습니다. 다른 지역을 선택 하거나, 지원에 문의 하 여 할당량을 늘리세요. ' 라는 오류 메시지가 표시 됩니다.

Azure Portal을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 클러스터 만들기

#### <a name="load-based-autoscaling"></a>부하 기반 자동 크기 조정

아래 json 코드 조각에 표시 된 것 처럼 `computeProfile` > `workernode` 섹션에 `autoscale` 노드를 추가 하 여 Azure Resource Manager 템플릿을 사용 하 여 HDInsight 클러스터를 만들 수 있습니다.`minInstanceCount``maxInstanceCount`

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

Resource Manager 템플릿을 사용하여 클러스터를 만드는 방법에 대한 자세한 내용은 [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.  

#### <a name="schedule-based-autoscaling"></a>일정 기반 자동 크기 조정

`computeProfile` > `workernode` 섹션에 `autoscale` 노드를 추가 하 여 Azure Resource Manager 템플릿에 대 한 일정 기반 자동 크기 조정을 사용 하 여 HDInsight 클러스터를 만들 수 있습니다. `autoscale` 노드에는 `timezone` 및 변경이 수행 되는 시점을 설명 하는 `schedule` 있는 `recurrence` 포함 되어 있습니다.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>실행 중인 클러스터에 자동 크기 조정 사용 및 사용 안 함

#### <a name="using-the-azure-portal"></a>Azure Portal 사용

실행 중인 클러스터에서 자동 크기 조정을 사용 하도록 설정 하려면 [ **설정**] 아래에서 **클러스터 크기** 를 선택 합니다. 그런 다음 **자동 크기 조정 사용**을 선택 합니다. 원하는 자동 크기 조정 유형을 선택 하 고 부하 기반 또는 일정 기반 크기 조정 옵션을 입력 합니다. 마지막으로 **저장**을 선택합니다.

![실행 중인 클러스터의 작업자 노드 일정 기반 자동 크기 조정 사용](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>REST API 사용

REST API를 사용 하 여 실행 중인 클러스터에서 자동 크기 조정을 사용 하거나 사용 하지 않도록 설정 하려면 아래 코드 조각에 표시 된 것 처럼 자동 크기 조정 끝점에 대 한 POST 요청을 수행 합니다.

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

요청 페이로드에서 적절 한 매개 변수를 사용 합니다. 아래 json 페이로드를 사용 하 여 자동 크기 조정을 사용 하도록 설정할 수 있습니다. 자동 크기 조정을 사용 하지 않도록 설정 하려면 페이로드 `{autoscale: null}`를 사용 합니다.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

모든 페이로드 매개 변수에 대 한 전체 설명은 [부하 기반 자동 크기 조정 사용](#load-based-autoscaling) 에 대 한 이전 섹션을 참조 하세요.

## <a name="best-practices"></a>모범 사례

### <a name="choosing-load-based-or-schedule-based-scaling"></a>부하 기반 또는 일정 기반 크기 조정 선택

선택할 모드를 결정 하기 전에 다음 요인을 고려 하십시오.

* 클러스터를 만드는 동안 자동 크기 조정을 사용 하도록 설정 합니다.
* 최소 노드 수는 세 개 이상 이어야 합니다.
* 부하 분산: 클러스터의 로드가 특정 날짜에 특정 시간에 일관 된 패턴을 따르도록 합니다. 그렇지 않은 경우 부하 기반 일정은 더 나은 옵션입니다.
* SLA 요구 사항: 자동 크기 조정 크기 조정은 예측 대신 대응식입니다. 부하가 증가 하기 시작 하는 시간과 클러스터가 대상 크기에 있어야 하는 시간 사이에 충분 한 지연이 있나요? 엄격한 SLA 요구 사항이 있고 부하가 알려진 알려진 패턴 인 경우 ' 일정 기반 '이 더 나은 옵션입니다.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>규모 확장 또는 축소 작업의 대기 시간 고려

크기 조정 작업을 완료 하는 데 10 ~ 20 분 정도 걸릴 수 있습니다. 사용자 지정 일정을 설정 하는 경우이 지연 시간을 계획 합니다. 예를 들어 9:00 오전에 클러스터 크기를 20으로 설정 해야 하는 경우 크기 조정 작업이 9:00 AM에서 완료 되도록 일정 트리거를 8:30 AM과 같은 이전 시간으로 설정 합니다.

### <a name="preparation-for-scaling-down"></a>축소 준비

클러스터 규모를 축소 하는 동안 자동 크기 조정 기능은 대상 크기에 맞게 노드를 서비스 해제 합니다. 해당 노드에 실행 중인 태스크가 있는 경우 자동 크기 조정 기능은 작업이 완료 될 때까지 대기 합니다. 또한 각 작업자 노드는 HDFS에서 역할을 수행 하므로 임시 데이터가 나머지 노드로 이동 됩니다. 따라서 나머지 노드에 모든 임시 데이터를 호스트할 수 있는 충분 한 공간이 있는지 확인 해야 합니다.

실행 중인 작업은 계속 실행 되 고 완료 됩니다. 보류 중인 작업은 사용 가능한 작업자 노드가 적을수록 정상적으로 예약 될 때까지 대기 합니다.

### <a name="minimum-cluster-size"></a>최소 클러스터 크기

클러스터를 3 개 미만의 노드로 확장 하지 마십시오. 클러스터를 3 개 미만의 노드로 확장 하면 파일 복제가 충분 하지 않기 때문에 안전 모드에서 중단 될 수 있습니다.  자세한 내용은 [안전 모드에서 중단 하기](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)를 참조 하세요.

## <a name="monitoring"></a>모니터링

### <a name="cluster-status"></a>클러스터 상태

Azure Portal에 나열 된 클러스터 상태를 통해 자동 크기 조정 작업을 모니터링할 수 있습니다.

![작업자 노드 부하 기반 자동 크기 조정 클러스터 상태를 사용 하도록 설정](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

표시 될 수 있는 모든 클러스터 상태 메시지는 아래 목록에 설명 되어 있습니다.

| 클러스터 상태 | Description |
|---|---|
| 실행 중 | 클러스터가 정상적으로 작동 하 고 있습니다. 모든 이전 자동 크기 조정 작업이 성공적으로 완료 되었습니다. |
| 업데이트  | 클러스터 자동 크기 조정 구성을 업데이트 하 고 있습니다.  |
| HDInsight 구성  | 클러스터 확장 또는 축소 작업이 진행 중입니다.  |
| 업데이트 오류  | HDInsight에서 자동 크기 조정 구성 업데이트 중에 문제가 발생 했습니다. 고객은 업데이트를 다시 시도 하거나 자동 크기 조정을 사용 하지 않도록 선택할 수 있습니다.  |
| Error  | 클러스터에 문제가 있어 사용할 수 없습니다. 이 클러스터를 삭제 하 고 새 클러스터를 만듭니다.  |

클러스터의 현재 노드 수를 보려면 클러스터의 **개요** 페이지에서 **클러스터 크기** 차트로 이동 하거나 **설정**아래에서 **클러스터 크기** 를 선택 합니다.

### <a name="operation-history"></a>작업 기록

클러스터 메트릭의 일부로 클러스터 확장 및 축소 기록을 볼 수 있습니다. 지난 일, 주 또는 기타 기간 동안 모든 크기 조정 작업을 나열할 수도 있습니다.

**모니터링**아래에서 **메트릭** 을 선택 합니다. 그런 다음 **메트릭** 드롭다운 상자에서 **메트릭 추가** 및 **활성 작업자 수** 를 선택 합니다. 오른쪽 위에 있는 단추를 선택 하 여 시간 범위를 변경 합니다.

![작업자 노드 일정 기반 자동 크기 조정 메트릭 사용](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>다음 단계

[크기 조정 모범 사례](hdinsight-scaling-best-practices.md)에서 클러스터 크기를 수동으로 조정하는 모범 사례에 대해 알아보세요.
