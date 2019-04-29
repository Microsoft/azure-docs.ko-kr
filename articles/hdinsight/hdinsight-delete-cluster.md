---
title: HDInsight 클러스터를 삭제하는 방법 - Azure
description: HDInsight 클러스터를 삭제할 수 있는 다양한 방법에 대한 정보입니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097189"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>브라우저, PowerShell 또는 Azure CLI를 사용하여 HDInsight 클러스터 삭제

클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다. 이 문서를 사용 하 여 클러스터를 삭제 하는 방법을 알아봅니다 합니다 [Azure portal](https://portal.azure.com)를 [Az Azure PowerShell 모듈](https://docs.microsoft.com/powershell/azure/overview), 및 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)합니다.

> [!IMPORTANT]  
> HDInsight 클러스터를 삭제해도 클러스터와 연결된 Azure Storage 계정 또는 Data Lake Storage는 삭제되지 않습니다. 나중에 해당 서비스에 저장된 데이터를 다시 사용할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽된 메뉴에서로 이동 **모든 서비스** > **Analytics** > **HDInsight 클러스터** 클러스터를 선택 합니다.

3. 기본 보기에서 선택 합니다 **삭제** 아이콘입니다. 클러스터를 삭제 하는 프롬프트를 따릅니다.
   
    ![삭제 아이콘](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell Az 모듈

대체 `CLUSTERNAME` 아래 코드에서 HDInsight 클러스터의 이름입니다. PowerShell 프롬프트에서 클러스터를 삭제 하려면 다음 명령을 입력 합니다.

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

바꿉니다 `CLUSTERNAME` HDInsight 클러스터의 이름 및 `RESOURCEGROUP` 아래 코드에서 리소스 그룹의 이름입니다.  명령 프롬프트에서 클러스터를 삭제 하려면 다음을 입력 합니다.

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
