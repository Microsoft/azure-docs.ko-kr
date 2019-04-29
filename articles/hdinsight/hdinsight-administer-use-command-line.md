---
title: Azure Classic CLI를 사용하여 Apache Hadoop 클러스터 관리 - Azure HDInsight
description: Azure Classic CLI를 사용하여 Azure HDInsight의 Apache Hadoop 클러스터를 관리하는 방법을 알아봅니다.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tyfox
ms.openlocfilehash: 94ef5a60ecc5d943d78b16a386660049cc52d82e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096018"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-using-the-azure-classic-cli"></a>Azure Classic CLI를 사용하여 HDInsight의 Apache Hadoop 클러스터 관리
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure Classic CLI](../cli-install-nodejs.md)를 사용하여 Azure HDInsight의 [Apache Hadoop](https://hadoop.apache.org/) 클러스터를 관리하는 방법을 알아봅니다. Classic CLI는 Node.js로 구현되며 Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* **Azure Classic CLI** - 설치 및 구성 정보는 [Azure Classic CLI 설치 및 구성](../cli-install-nodejs.md) 을 참조하세요.
* **Azure에 연결**. 다음 명령을 사용합니다.

    ```cli
    azure login
    ```
  
    회사 또는 학교 계정을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure Classic CLI에서 Azure 구독에 연결](/cli/azure/authenticate-azure-cli)을 참조하세요.
* **Azure Resource Manager 모드로 전환**은 다음 명령을 사용합니다.
  
    ```cli
    azure config mode arm
    ```

도움말을 보려면 **-h** 스위치를 사용합니다.  예를 들면 다음과 같습니다.

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>CLI를 사용하여 클러스터 만들기
[Azure Classic CLI를 사용하여 HDInsight의 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-azure-cli.md)를 참조하세요.

## <a name="list-and-show-cluster-details"></a>클러스터 세부 정보 나열 및 표시
클러스터 세부 정보를 나열하고 표시하려면 다음 명령을 사용합니다.

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![클러스터 목록의 명령줄 보기][image-cli-clusterlisting]

## <a name="delete-clusters"></a>클러스터 삭제
클러스터를 삭제하려면 다음 명령을 사용합니다.

```cli
azure hdinsight cluster delete <Cluster Name>
```

또한 클러스터를 포함하는 리소스 그룹을 삭제하여 클러스터를 삭제할 수도 있습니다. 이렇게 하면 기본 저장소 계정을 포함한 그룹 내 모든 리소스가 삭제됩니다.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>클러스터 크기 조정
Apache Hadoop 클러스터 크기를 변경하려면:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>클러스터에 대한 HTTP 액세스 사용/사용 안 함

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight 클러스터 관리 작업을 수행하는 여러 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure Portal을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리](hdinsight-administer-use-portal-linux.md)
* [Azure PowerShell을 사용하여 HDInsight 클러스터 관리][hdinsight-admin-powershell]
* [Azure HDInsight 시작][hdinsight-get-started]
* [Azure Classic CLI를 사용하는 방법][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "클러스터 나열 및 표시"
