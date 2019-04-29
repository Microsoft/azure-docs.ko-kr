---
title: 템플릿을 사용하여 Apache Hadoop 클러스터 만들기 - Azure HDInsight
description: Resource Manager 템플릿을 사용하여 HDInsight 클러스터를 만드는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 73402421a87d2cf14719ff34201890ea96c90519
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097425"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

이 문서에서는 Azure Resource Manager 템플릿으로 Azure HDInsight 클러스터를 만드는 몇 가지 방법에 대해 알아봅니다. 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 애플리케이션 배포](../azure-resource-manager/resource-group-template-deploy.md)를 참조하세요. 다른 클러스터 만들기 도구 및 기능을 알아보려면 이 페이지 위쪽에 있는 탭 선택기를 클릭하거나 [클러스터 생성 방법](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 지침을 따르려면 다음이 필요합니다.

* [Azure 구독](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell 및/또는 Azure 클래식 CLI

### <a name="resource-manager-templates"></a>리소스 관리자 템플릿
Resource Manager 템플릿을 쉽게 조정 된 단일 작업에서 응용 프로그램에 대 한 다음 리소스를 만듭니다.
* HDInsight 클러스터 및 해당 종속 리소스(예: 기본 스토리지 계정)
* 기타 리소스(예: [Apache Sqoop](https://sqoop.apache.org/)을 사용하는 Azure SQL Database).

템플릿에서 애플리케이션에 필요한 리소스를 정의합니다. 또한 다양한 환경에 대한 값을 입력하기 위한 배포 매개 변수를 지정합니다. 템플릿은 배포에 대한 값을 생성하는 데 사용하는 JSON과 식으로 구성됩니다.

HDInsight 템플릿 샘플을 찾을 수 있습니다 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?term=hdinsight)합니다. [Resource Manager 확장](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) 또는 텍스트 편집기에서 플랫폼 간 [Visual Studio Code](https://code.visualstudio.com/#alt-downloads)를 사용하여 템플릿을 워크스테이션의 파일에 저장합니다. 

Resource Manager 템플릿에 대한 자세한 내용은 다음 문서와 예제를 참조하세요.

* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager 템플릿으로 애플리케이션 배포](../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions) 템플릿 참조
* [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>템플릿 생성

Resource Manager를 사용하면 여러 다른 도구를 사용하여 구독의 기존 리소스에서 Resource Manager 템플릿을 내보낼 수 있습니다. 생성된 템플릿을 사용하여 템플릿 구문에 대해 알아보거나 필요에 따라 솔루션 재배포를 자동화할 수 있습니다. 자세한 내용은 [템플릿 내보내기](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)합니다.

## <a name="deploy-using-the-portal"></a>Portal을 사용하여 배포

Azure Portal을 사용하여 Resource Manager 템플릿을 배포할 수 있습니다. 자세한 내용은 [사용자 지정 템플릿에서 리소스 배포](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)를 참조하세요.

## <a name="deploy-using-powershell"></a>PowerShell을 사용하여 배포

Azure PowerShell을 사용하여 Resource Manager 템플릿을 배포할 수 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md) 및 [SAS 토큰과 Azure PowerShell을 사용하여 개인 Resource Manager 템플릿 배포](../azure-resource-manager/resource-manager-powershell-sas-token.md)를 참조하세요.

## <a name="deploy-using-azure-cli"></a>Azure CLI를 사용하여 배포

클래식 CLI를 사용하여 Resource Manager 템플릿을 배포할 수 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md) 및 [SAS 토큰과 Azure CLI를 사용하여 개인 Resource Manager 템플릿 배포](../azure-resource-manager/resource-manager-cli-sas-token.md)를 참조하세요.

## <a name="deploy-using-the-rest-api"></a>REST API를 사용하여 배포
REST API를 사용하여 Resource Manager 템플릿을 배포할 수 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Resource Manager REST API로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-rest.md)를 참조하세요.

## <a name="deploy-with-visual-studio"></a>Visual Studio를 사용하여 배포
 Visual Studio를 사용하여 리소스 그룹 프로젝트를 만들고 사용자 인터페이스를 통해 Azure에 이 프로젝트를 배포할 수 있습니다. 프로젝트에 포함할 리소스 종류를 선택합니다. 이러한 리소스는 Resource Manager 템플릿에 자동으로 추가됩니다. 또한 프로젝트에서는 템플릿을 배포할 수 있는 PowerShell 스크립트를 제공합니다.

Visual Studio를 리소스 그룹과 함께 사용하는 방법에 대한 소개는 [Visual Studio를 통해 Azure 리소스 그룹 생성 및 배포](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조하세요.

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](hdinsight-hadoop-create-linux-clusters-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* 자세한 HDInsight 관련된 템플릿은 참조 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?term=hdinsight)합니다.
* .NET 클라이언트 라이브러리를 통해 리소스를 배포하는 예제는 [.NET 라이브러리 및 템플릿을 사용하여 리소스 배포](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* 애플리케이션 배포에 대한 자세한 예제는 [Azure에서 마이크로 서비스를 예측 가능하게 프로비전 및 배포](../app-service/deploy-complex-application-predictably.md)를 참조하세요.
* 다른 환경에 솔루션 배포에 관한 지침은 [Microsoft Azure의 개발 및 테스트 환경](../solution-dev-test-environments.md)을 참조하세요.
* Azure Resource Manager 템플릿 섹션에 대한 자세한 내용은 [템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.
* Azure Resource Manager 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](../azure-resource-manager/resource-group-template-functions.md)를 참조하세요.
