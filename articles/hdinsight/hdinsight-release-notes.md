---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대 한 개발 팁 및 세부 정보를 확인 하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 091ca4d632d89405d85c66e264aff9867979fcd4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905240"
---
# <a name="release-notes"></a>릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객 들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-01092020"></a>릴리스 날짜: 01/09/2020

이 릴리스는 HDInsight 3.6 및 4.0에 모두 적용 됩니다. HDInsight 릴리스는 며칠 동안 모든 지역에서 사용할 수 있습니다. 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래 변경 내용이 표시 되지 않으면 며칠 동안 해당 지역에서 릴리스가 라이브 될 때까지 기다려 주세요.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 버전 관리 문서](hdinsight-component-versioning.md)를 참조하세요.

## <a name="new-features"></a>새로운 기능
### <a name="tls-12-enforcement"></a>TLS 1.2 적용
TLS(전송 계층 보안) 및 SSL(Secure Sockets Layer)은 컴퓨터 네트워크를 통해 통신 보안을 제공하는 암호화 프로토콜입니다. [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)에 대해 자세히 알아보세요. HDInsight는 공용 HTTPs 엔드포인트에서 TLS 1.2를 사용 하지만 TLS 1.1은 이전 버전과의 호환성을 위해 계속 지원 됩니다. 

이 릴리스에서는 고객이 공용 클러스터 엔드포인트을 통한 모든 연결에 대해서만 TLS 1.2을 옵트인 (opt in) 할 수 있습니다. 이를 지원 하기 위해 새 속성 **Minsupportedtlsversion** 이 도입 되었으며 클러스터를 만드는 동안이 속성을 지정할 수 있습니다. 속성이 설정 되어 있지 않으면 클러스터는 여전히 TLS 1.0, 1.1 및 1.2을 지원 하며,이는 오늘날의 동작과 동일 합니다. 고객은이 속성의 값을 "1.2"로 설정할 수 있습니다. 즉, 클러스터가 TLS 1.2 이상만 지원 합니다. 자세한 내용은 [가상 네트워크 전송 계층 보안 계획](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security)을 참조 하세요.

### <a name="bring-your-own-key-for-disk-encryption"></a>디스크 암호화를 위한 고유 키 가져오기
HDInsight의 모든 관리 디스크는 Azure SSE(스토리지 서비스 암호화)로 보호됩니다. 이러한 디스크의 데이터는 기본적으로 Microsoft 관리 키에 의해 암호화 됩니다. 이 릴리스부터는 디스크 암호화에 대 한 Bring Your Own Key (BYOK) Azure Key Vault를 사용 하 여 관리할 수 있습니다. BYOK 암호화는 클러스터를 만드는 동안 추가 비용 없이 1 단계 구성입니다. Azure Key Vault를 사용 하 여 HDInsight를 관리 되는 id로 등록 하 고 클러스터를 만들 때 암호화 키를 추가 하기만 하면 됩니다. 자세한 내용은 [고객이 관리 하는 키 디스크 암호화](https://docs.microsoft.com/azure/hdinsight/disk-encryption)를 참조 하세요.

## <a name="deprecation"></a>사용 중단
이 릴리스에 대 한 결함 없습니다. 예정 된 결함을 준비 하려면 예정 된 [변경 내용](#upcoming-changes)을 참조 하세요.

## <a name="behavior-changes"></a>동작 변경
이 릴리스에 대 한 동작은 변경 되지 않습니다. 예정 된 변경 내용을 준비 하려면 예정 된 [변경 내용](#upcoming-changes)을 참조 하세요.

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행 됩니다. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>헤드 노드에는 최소 4 코어 VM이 필요 합니다. 
최소 4 코어 VM은 HDInsight 클러스터의 고가용성 및 안정성을 보장 하기 위해 헤드 노드에 필요 합니다. 4 월 6 일 2020부터 고객은 새 HDInsight 클러스터에 대 한 헤드 노드로 4 코어 이상 VM만 선택할 수 있습니다. 기존 클러스터는 계속해서 예상대로 실행됩니다. 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 클러스터 노드 크기 변경 
이후 릴리스에서는 ESP Spark 클러스터에 허용 되는 최소 노드 크기가 Standard_D13_V2로 변경 됩니다. A 시리즈 Vm은 비교적 낮은 CPU와 메모리 용량 때문에 ESP 클러스터 문제를 일으킬 수 있습니다. A 시리즈 Vm은 새 ESP 클러스터를 만드는 데 사용 되지 않습니다.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합으로 이동
이제 HDInsight는 Azure virtual machines를 사용 하 여 클러스터를 프로 비전 합니다. 향후 릴리스에서 HDInsight는 Azure virtual machine scale sets를 대신 사용 합니다. Azure 가상 머신 확장 집합에 대 한 자세한 내용을 참조 하세요.

### <a name="hbase-20-to-21"></a>HBase 2.0 ~ 2.1
예정 된 HDInsight 4.0 릴리스에서 HBase 버전은 버전 2.0에서 2.1로 업그레이드 됩니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속 해 서 클러스터 안정성과 성능을 향상 시킵니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스에 대 한 구성 요소 버전이 변경 되지 않았습니다. HDInsight 4.0 ad HDInsight 3.6의 최신 구성 요소 버전은 여기에서 찾을 수 있습니다.

## <a name="known-issues"></a>알려진 문제

2020 년 1 월 29 일에는 Jupyter 노트북을 사용 하려고 할 때 오류가 발생할 수 있는 활성 문제가 있습니다. 아래 단계를 사용 하 여 문제를 해결 하십시오. 최신 정보는이 [MSDN 게시물](https://social.msdn.microsoft.com/Forums/en-us/8c763fb4-79a9-496f-a75c-44a125e934ac/hdinshight-create-not-create-jupyter-notebook?forum=hdinsight) 또는이 [stackoverflow 게시물](https://stackoverflow.com/questions/59687614/azure-hdinsight-jupyter-notebook-not-working/59831103) 을 참조 하거나 추가 질문을 할 수도 있습니다. 이 페이지는 문제가 해결 될 때 업데이트 됩니다.

**Errors**

* ValueError: 해당 버전이 없으므로 노트북을 v5로 변환할 수 없습니다.
* 노트북 로드 중 오류가 발생 했습니다 .이 노트북을 로드 하는 동안 알 수 없는 오류가 발생 했습니다. 이 버전은 전자 필기장 형식 v4 또는 이전을 로드할 수 있습니다.

**원인** 

클러스터의 py 파일이 4.4. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x로 업데이트 되었습니다. _version

**해결 방법**

새 Jupyter 노트북을 만들고 위에 나열 된 오류 중 하나를 수신 하는 경우 다음 단계를 수행 하 여 문제를 해결 합니다.

1. `https://CLUSTERNAME.azurehdinsight.net`로 이동 하 여 웹 브라우저에서 Ambari을 엽니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.
1. Ambari의 왼쪽 메뉴에서 **Jupyter**를 클릭 한 다음 **서비스 작업**에서 **중지**를 클릭 합니다.
1. Jupyter 서비스를 실행 하는 클러스터 헤드 노드로 ssh를 실행 합니다.
1. Sudo 모드에서 다음 _version/usr/bin/anaconda/lib/python2.7/site-packages/nbformat/py 파일을 엽니다.
1. Version_info의 값을 확인 합니다.
1. Version_info 값이로 설정 되어 있으면 다음을 수행 합니다. 

    version_info = (5, 0, 3)

    그런 다음 항목을 다음으로 수정 합니다. 
    
    version_info = (4, 4, 0)

    파일을 저장 합니다. 

    Version_info 이미 (4, 4, 0)로 설정 된 경우에는 Ambari만 다시 시작 해야 하는 경우에는 추가 변경이 필요 하지 않으므로 다음 단계로 계속 진행 합니다.
1. Ambari로 돌아가서 **서비스 작업**에서 **모두 다시 시작**을 클릭 합니다.
