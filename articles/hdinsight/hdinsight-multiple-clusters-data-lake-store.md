---
title: Azure Data Lake Storage 계정으로 여러 HDInsight 클러스터 사용 - Azure
description: 단일 Data Lake Storage 계정으로 하나 이상의 HDInsight 클러스터를 사용하는 방법에 대해 알아봅니다
keywords: hdinsight 저장소, hdfs, 구조화된 데이터, 구조화되지 않은 데이터, Data Lake Store
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: b580890b1663aa6ce742443e927e4d760585d4ce
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764401"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Azure Data Lake Storage 계정으로 여러 HDInsight 클러스터 사용

HDInsight 버전 3.5 이상에서는 기본 파일 시스템으로 Azure Data Lake Storage 계정을 사용하여 HDInsight 클러스터를 만들 수 있습니다.
Data Lake Storage는 많은 양의 데이터 호스팅뿐만 아니라 단일 Data Lake Storage 계정을 공유하는 여러 HDInsight 클러스터 호스팅에 적합하도록 만드는 제한되지 않은 스토리지를 지원합니다. Data Lake Storage를 스토리지로 사용하여 HDInsight 클러스터를 만드는 방법에 대한 지침은 [빠른 시작: HDInsight에서 클러스터 설정](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.

이 문서에서는 Data Lake Storage 관리자에게 여러 **활성** HDInsight 클러스터에서 사용할 수 있는 단일 및 공유 Data Lake Storage 계정 설정에 대한 권장 사항을 제공합니다. 이러한 권장 사항은 여러 보안 호스팅뿐만 아니라 공유 Data Lake Storage 계정에서 비보안 Apache Hadoop 클러스터에도 적용됩니다.


## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage 파일 및 폴더 수준 ACL

이 문서의 나머지 부분에서는 [Azure Data Lake Storage의 액세스 제어](../data-lake-store/data-lake-store-access-control.md)에 자세히 설명되어 있는 Azure Data Lake Storage의 파일 및 폴더 수준 ACL에 대해 잘 알고 있다고 가정합니다.

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>여러 HDInsight 클러스터에 대한 Data Lake Storage 설정
Data Lake Storage 계정을 사용 하 여 여러 HDInsight 클러스터를 사용 하는 것에 대 한 권장 사항을 설명 하는 두 수준의 폴더 계층 구조를 살펴보겠습니다. **/클러스터/재무** 폴더 구조의 Data Lake Storage 계정이 있는 것이 좋습니다. 이 구조로 재무 부서에 필요한 모든 클러스터는 저장소 위치로 /클러스터/재무를 사용할 수 있습니다. 향후 다른 조직에서(예: 마케팅) 동일한 Data Lake Storage 계정을 사용하여 HDInsight 클러스터를 만들려는 경우 /클러스터/마케팅을 만들 수 있습니다. 이제 **/클러스터/재무**를 사용해보겠습니다.

HDInsight 클러스터에서 이 폴더 구조를 효과적으로 사용하려면 Data Lake Storage 관리자는 테이블에 설명된 대로 적절한 권한을 할당해야 합니다. 테이블에 표시된 사용 권한은 액세스-ACL에 해당하며 기본 ACL에 해당하지 않습니다. 


|폴더  |권한  |소유 사용자  |소유 그룹  | 명명된 사용자 | 명명된 사용자 권한 | 명명된 그룹 | 명명된 그룹 권한 |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |관리자 |관리자  |서비스 주체 |--x  |FINGRP   |r-x         |
|/클러스터 | rwxr-x--x |관리자 |관리자 |서비스 주체 |--x  |FINGRP |r-x         |
|/클러스터/재무 | rwxr-x--t |관리자 |FINGRP  |서비스 주체 |rwx  |-  |-     |

테이블에서,

- **관리자**는 Data Lake Storage 계정의 작성자 및 관리자입니다.
- **서비스 주체**는 계정과 연결된 AAD(Azure Active Directory) 서비스 주체입니다.
- **FINGRP**는 재무 부서의 사용자를 포함하는 AAD에서 만든 사용자 그룹입니다.

AAD 애플리케이션을 만드는 방법에 대한 지침은(서비스 주체도 만듦) [AAD 애플리케이션을 만들기](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)를 참조하세요. AAD에서 사용자 그룹을 만드는 방법에 대한 자세한 내용은 [Azure Active Directory에서 그룹 관리](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

몇 가지 고려할 주요 사항은 다음과 같습니다.

- 두 수준 폴더 구조(**/클러스터/재무/**)는 클러스터의 스토리지 계정을 사용하기 **전에** Data Lake Storage 관리자에 의해 적절한 권한으로 생성 및 프로비전되어야 합니다. 이 구조는 클러스터를 만드는 동안 자동으로 생성되지 않습니다.
- 위의 예제에서는 **FINGRP**로 **/클러스터/재무**의 소유 그룹 설정 및 루트에서 시작하는 전체 폴더 계층에 대한 FINGRP에 **r-x** 액세스 허용을 권장합니다. 이렇게 하면 FINGRP의 멤버는 루트에서 시작하는 폴더 구조를 탐색할 수 있습니다.
- 다른 AAD 서비스 주체에서 **/클러스터/재무**에 클러스터를 만들 수 있는 경우, 고정 비트(**재무** 폴더에서 설정된 경우)를 통해 하나의 서비스 주체에서 만든 폴더는 다른 서비스 주체에서 삭제할 수 없습니다.
- HDInsight 클러스터 만들기 프로세스에서 클러스터 특정 저장소 위치를 만듭니다는 폴더 구조 및 사용 권한을 적용 되 면 **/클러스터/재무/** 합니다. 예를 들어 fincluster01이라는 이름의 클러스터에 대한 저장소는 **/클러스터/재무/fincluster01**이 될 수 있습니다. HDInsight 클러스터에 의해 생성된 폴더에 대한 소유권 및 권한은 테이블에 표시됩니다.

    |폴더  |권한  |소유 사용자  |소유 그룹  | 명명된 사용자 | 명명된 사용자 권한 | 명명된 그룹 | 명명된 그룹 권한 |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/클러스터/재무/fincluster01 | rwxr-x---  |서비스 주체 |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>작업 입력 및 출력 데이터에 대한 권장 사항

작업에 대한 입력 데이터 및 작업의 출력은 **/클러스터** 외부의 폴더에 저장하는 것이 좋습니다. 이렇게 하면 클러스터 특정 폴더가 일부 저장소 공간을 확보하도록 삭제되는 경우에도 작업 입력 및 출력을 나중에 계속 사용할 수 있습니다. 이 경우 작업 입력 및 출력을 저장하기 위한 폴더 계층 구조가 서비스 주체에 대한 적절한 수준의 액세스를 허용하는지 확인합니다.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>단일 저장소 계정을 공유하는 클러스터에 대한 제한

단일 Data Lake Storage 계정을 공유할 수 있는 클러스터 수에 대한 제한은 해당 클러스터에서 실행되는 작업에 따라 달라집니다. 클러스터가 너무 많거나 저장소 계정을 공유하는 클러스터에 작업이 매우 많으면 저장소 계정 송/수신에 제한이 발생할 수 있습니다.

## <a name="support-for-default-acls"></a>기본 ACL에 대한 지원

명명된 사용자 액세스 권한이 있는 서비스 주체를 만들 때(위의 테이블에 나와 있는 대로) 기본 ACL로 명명된 사용자를 추가하지 **않는** 것이 좋습니다. 기본 ACL을 사용하여 명명된 사용자 액세스를 프로비전하면 소유 사용자, 소유 그룹 및 다른 사용자에 대한 770 사용 권한 할당이 발생합니다. 770의 기본값은 소유 사용자(7) 또는 소유 그룹(7)에서 권한을 가져가지 않는 반면 다른 사용자(0)에 대한 모든 권한을 가져갑니다. 이로 인해 [알려진 문제 및 해결 방법](#known-issues-and-workarounds) 섹션에서 자세히 설명되어 있는 하나의 특정 사용 사례로 알려진 문제가 발생합니다.

## <a name="known-issues-and-workarounds"></a>알려진 문제 및 해결 방법

이 섹션에서는 Data Lake Storage로 HDInsight 사용에 대한 알려진 문제 및 해당 해결 방법을 나열합니다.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>공개적으로 표시되는 지역화된 Apache Hadoop YARN 리소스

새 Azure Data Lake Storage 계정이 만들어질 때 루트 디렉터리는 770으로 설정된 액세스-ACL 사용 권한 비트로 자동으로 프로비전됩니다. 루트 폴더의 소유 사용자는 계정(Data Lake Storage 관리자)을 만든 사용자로 설정되고 소유 그룹은 계정을 만든 사용자의 주 그룹으로 설정됩니다. "다른 사용자"에 대한 액세스는 제공되지 않습니다.

이러한 설정은 [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247)에서 캡처된 하나의 특정 HDInsight 사용 사례에 영향을 줄 것으로 알려졌습니다. 작업 제출은 다음과 유사한 오류 메시지와 함께 실패할 수 있습니다.

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

이전에 연결된 YARN JIRA에 명시된 대로 공용 리소스를 지역화하는 동안 지역화 담당자는 원격 파일 시스템에서 해당 사용 권한을 확인하여 요청된 모든 리소스가 실제로 공용인지 확인합니다. 지역화를 위해 해당 조건에 맞지 않는 모든 LocalResource는 거부됩니다. 사용 권한에 대한 확인은 "다른 사용자"에 대한 파일에 읽기 액세스를 포함합니다. Azure Data Lake는 루트 폴더 수준에서 "다른 사용자"에 대한 모든 액세스를 거부하므로 이 시나리오는 Azure Data Lake에서 HDInsight 클러스터를 호스팅할 때 기본으로 작동하지 않습니다.

#### <a name="workaround"></a>해결 방법
계층을 통해 **다른 사용자**에 대한 읽기-실행 권한을 설정합니다(예: **/** 에서 위의 테이블에 나와 있는 것처럼 **/클러스터** 및 **/클러스터/재무**).

## <a name="see-also"></a>참고 항목

* [빠른 시작: HDInsight에서 클러스터 설정](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.
* [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](hdinsight-hadoop-use-data-lake-storage-gen2.md)
