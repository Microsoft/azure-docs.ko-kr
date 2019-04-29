---
title: 'Azure Cloud Shell에서 Databricks CLI 사용 '
description: Azure Cloud Shell에서 Databricks CLI를 사용하는 방법을 알아봅니다.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: dae481fb477223f149404c6a09cad024bc15cd90
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108740"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Azure Cloud Shell에서 Databricks CLI 사용

Azure Cloud Shell에서 Databricks CLI를 사용하여 Databricks에서 작업을 수행하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* Azure Databricks 작업 영역 및 클러스터. 지침은 [Azure Databricks 시작](quickstart-create-databricks-workspace-portal.md)을 참조하세요. 

* Databricks에서 개인용 액세스 토큰을 설정합니다. 지침은 [토큰 관리](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)를 참조하세요.

## <a name="use-the-azure-cloud-shell"></a>Azure Cloud Shell 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
 
2. 오른쪽 위 모서리에서 **Cloud Shell** 아이콘을 클릭합니다.

   ![Cloud Shell 시작](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Azure Cloud Shell 시작")

3. Cloud Shell 환경에 대해 **Bash**를 선택해야 합니다. 다음 스크린샷과 같이 드롭다운 옵션에서 선택할 수 있습니다.

   ![Cloud Shell 환경에 대해 Bash 선택](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Bash 선택") 

4. Databricks CLI를 설치할 수 있는 가상 환경을 만듭니다. 아래 코드 조각에서는 `databrickscli`라는 가상 환경을 만듭니다.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. 만든 가상 환경으로 전환합니다.

       source databrickscli/bin/activate

6. Databricks CLI를 설치합니다.

       pip install databricks-cli

7. 만든 액세스 토큰(필수 조건의 일부로 나열됨)을 사용하여 Databricks에 인증을 설정합니다. 다음 명령을 사용합니다.

       databricks configure --token

    다음과 같은 메시지가 표시됩니다.

    * 먼저 Databricks 호스트를 입력하라는 메시지가 표시됩니다. 해당 값을 `https://eastus2.azuredatabricks.net` 형식으로 입력합니다. 여기서 **eastus2**는 Azure Databricks 작업 영역을 만든 Azure 영역입니다.

    * 다음으로, 토큰을 입력하라는 메시지가 표시됩니다. 앞에서 만든 토큰을 입력합니다.

이러한 단계가 완료되면 Azure Cloud Shell에서 Databricks CLI를 사용할 수 있습니다.

## <a name="use-databricks-cli"></a>Databricks CLI 사용

이제 Databricks CLI 사용을 시작할 수 있습니다. 예를 들어 다음 명령을 실행하여 작업 영역에 있는 모든 Databricks 클러스터를 나열합니다.

    databricks clusters list

또한 다음 명령을 사용하여 DBFS(Databricks 파일 시스템)에 액세스할 수도 있습니다.

    databricks fs ls


명령에 대한 전체 참조는 [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)를 참조하세요.


## <a name="next-steps"></a>다음 단계

* Azure CLI에 대한 자세한 내용은 [Azure CLI 개요](../cloud-shell/overview.md)를 참조하세요.
* Azure CLI에 대한 명령 목록을 보려면 [Azure CLI 참조](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)를 참조하세요.
* Databricks CLI에 대한 명령 목록을 보려면 [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)를 참조하세요.


