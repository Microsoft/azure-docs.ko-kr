---
title: Linux 기반 HDInsight 클러스터의 OS 패치 일정 구성 - Azure
description: Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법에 대해 알아봅니다.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 3fad8869a31688e9e2413abb350eccf1f871f7dd
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330715"
---
# <a name="os-patching-for-hdinsight"></a>HDInsight의 OS 패치 

> [!IMPORTANT]
> Ubuntu 이미지 게시할 3 개월 내 새 HDInsight 클러스터를 만들기 위해 사용할 수 있습니다. 2019년 1월부터 실행 중인 클러스터는 자동 패치되지 **않습니다**. 고객이 스크립트 동작이나 기타 메커니즘을 사용하여 실행 중인 클러스터를 패치해야 합니다. 항상 새로 만든 클러스터에는 최신 보안 패치를 포함한 사용 가능한 최신 업데이트가 있습니다.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법
중요한 보안 패치를 설치할 수 있도록 간혹 HDInsight 클러스터에서 가상 머신을 다시 부팅해야 합니다. 

이 문서에 설명 된 스크립트 동작 사용의 OS 패치 일정을 다음과 같이 수정할 수 있습니다.
1. 모든 업데이트를 설치 하거나 설치 커널 + 보안 업데이트만 또는 커널 업데이트만 설치 합니다.
2. 즉시 다시 부팅 하거나 VM에서 재부팅을 예약 합니다.

> [!NOTE]  
> 2016 년 8 월 1 일 이후에 만든 Linux 기반 HDInsight 클러스터를 사용 하 여 이러한 스크립트 작업 에서만 작동 합니다. VM이 다시 부팅되는 경우에만 패치가 적용됩니다. 이러한 스크립트는 모든 향후 업데이트 주기에 대 한 업데이트를 자동으로 적용 되지 않습니다. 각 시간 새 업데이트에서 업데이트를 설치 하 고 VM을 다시 부팅 하기 위해 적용 해야 하는 스크립트를 실행 합니다.

## <a name="how-to-use-the-script"></a>스크립트를 사용하는 방법 

이 스크립트를 사용 하 여 다음 정보가 필요 합니다.
1. 설치 업데이트 일정 재부팅 스크립트 위치: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 합니다.
    
   HDInsight는 이 URI를 사용하여 클러스터의 모든 가상 머신에서 스크립트를 찾아 실행합니다. 이 스크립트는 업데이트를 설치 하 고 VM을 다시 부팅 하는 옵션을 제공 합니다.
  
2. 일정-다시 부팅 스크립트 위치: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 합니다.
    
   HDInsight는 이 URI를 사용하여 클러스터의 모든 가상 머신에서 스크립트를 찾아 실행합니다. 이 스크립트는 VM을 재부팅합니다.
  
3. 스크립트를 적용하는 클러스터 노드 유형: 헤드 노드, workernode, zookeeper 이 스크립트는 클러스터의 모든 노드 유형에 적용되어야 합니다. 노드 형식에는 적용 되지 않으면, 한 다음 해당 노드 유형인 가상 컴퓨터는 업데이트 되거나 다시 부팅 합니다.

4. 매개 변수: 업데이트 일정 재부팅 설치 스크립트는 두 개의 숫자 매개 변수를 허용합니다.

    | 매개 변수 | 정의 |
    | --- | --- |
    | 모든 업데이트/설치 커널 + 보안 설치 / 커널 업데이트만 설치만 업데이트 |0 또는 1 또는 2입니다. 값이 0 모든 업데이트 및 2 개의 설치 커널 + 보안 정보만 업데이트 1 설치 하는 동안 커널 업데이트만 설치 합니다. 없는 매개 변수를 제공 하는 경우 기본값은 0입니다. |
    | 다시 부팅 사용/사용 일정 다시 부팅 사용/즉시 다시 부팅 안 함 |0 또는 1 또는 2입니다. 값이 0 1 일정 다시 부팅을 사용 하면 2를 사용 하면 즉시 다시 부팅 해야 하는 동안 다시 부팅을 해제 합니다. 없는 매개 변수를 제공 하는 경우 기본값은 0입니다. 사용자는 매개 변수 입력 매개 변수 2에 1을 입력 해야 합니다. |
   
 5. 매개 변수: 일정-다시 부팅 한 후 스크립트는 하나의 숫자 매개 변수를 허용합니다.

    | 매개 변수 | 정의 |
    | --- | --- |
    | 일정 다시 부팅 사용/즉시 다시 부팅을 사용 하도록 설정 |1 또는 2입니다. 값이 1 일정 다시 부팅을 사용 하도록 설정 (다시 부팅 다음 12 ~ 24 시간 동안에서 예약 된) 2 수 있도록 즉시 (5 분)에 다시 부팅 하는 동안. 없는 매개 변수를 제공 하는 경우 기본값은 1입니다. |  

> [!NOTE] 
> 스크립트를 기존 클러스터에 적용 하는 경우를 지속형으로 표시 해야 합니다. 그렇지 않은 경우 크기 조정 작업을 통해 만들어진 모든 새 노드는 기본 패치 일정을 사용합니다.  클러스터를 만드는 프로세스의 일부로 스크립트를 적용하는 경우 자동으로 유지됩니다.


## <a name="next-steps"></a>다음 단계

다음 섹션을 참조 하는 스크립트 작업을 사용 하 여 특정 단계에 대 한 [스크립트 동작을 사용 하 여 사용자 지정 Linux 기반 HDInsight 클러스터](hdinsight-hadoop-customize-cluster-linux.md):

* [클러스터를 만드는 동안 스크립트 작업 사용](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [실행 중인 클러스터에 스크립트 작업 적용](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
