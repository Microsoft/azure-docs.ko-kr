---
title: Azure HDInsight 애플리케이션 게시
description: HDInsight 애플리케이션을 만든 다음, Azure Marketplace에 게시하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e64bf253a73df3a2f8170109dc1dfb9a59613733
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097403"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Azure Marketplace에 HDInsight 애플리케이션 게시
Azure HDInsight 애플리케이션은 Linux 기반 HDInsight 클러스터에 설치할 수 있습니다. 이 문서에서는 HDInsight 애플리케이션을 Azure Marketplace에 게시하는 방법에 대해 알아봅니다. Azure Marketplace에 게시하는 방법에 대한 일반적인 내용은 [Azure Marketplace에 제품 게시](../marketplace/marketplace-publishers-guide.md)를 참조하세요.

HDInsight 애플리케이션은 *BYOL(사용자 라이선스 필요)* 모델을 사용합니다. BYOL 시나리오에서 애플리케이션 공급자는 애플리케이션 사용자에게 애플리케이션에 대한 라이선스를 부여하는 역할을 담당합니다. 응용 프로그램 사용자는 HDInsight 클러스터, 클러스터의 VM 및 노드와 같이 자신들이 만드는 Azure 리소스에 대해서만 요금이 청구됩니다. 애플리케이션 자체에 대한 청구는 현재 Azure에서 수행되지 않습니다.

자세한 내용은 다음 HDInsight 애플리케이션 관련 문서를 참조하세요.

* [HDInsight 애플리케이션 설치](hdinsight-apps-install-applications.md) - 클러스터에 HDInsight 애플리케이션을 설치하는 방법을 알아봅니다.
* [사용자 지정 HDInsight 애플리케이션 설치](hdinsight-apps-install-custom-applications.md) - 사용자 지정 HDInsight 애플리케이션을 설치하고 테스트하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건
Marketplace에 사용자 지정 애플리케이션을 제출하려면 먼저 [사용자 지정 애플리케이션을 만들고 테스트해야 합니다](hdinsight-apps-install-custom-applications.md).

또한 개발자 계정도 등록해야 합니다. 자세한 내용은 [Azure Marketplace에 제품 게시](../marketplace/marketplace-publishers-guide.md) 및 [Microsoft 개발자 계정 만들기](../marketplace/marketplace-publishers-guide.md)를 참조하세요.

## <a name="define-the-application"></a>애플리케이션 정의
Marketplace에 애플리케이션을 게시하는 데에는 두 단계가 있습니다. 먼저 *createUiDef.json* 파일을 정의합니다. createUiDef.json 파일은 애플리케이션과 호환되는 클러스터를 나타냅니다. 그런 다음 Azure Portal에서 템플릿을 게시합니다. 다음은 createUiDef.json 샘플 파일입니다.

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| 필드 | 설명 | 가능한 값 |
| --- | --- | --- |
| types |애플리케이션과 호환되는 클러스터 종류입니다. |Hadoop, HBase, Storm, Spark(또는 이러한 클러스터의 조합) |
| versions |애플리케이션과 호환되는 HDInsight 클러스터 종류입니다. |3.4 |

## <a name="application-installation-script"></a>애플리케이션 설치 스크립트
애플리케이션이 클러스터(기존 클러스터 또는 새 클러스터)에 설치되면 에지 노드가 만들어집니다. 애플리케이션 설치 스크립트는 에지 노드에서 실행됩니다.

  > [!IMPORTANT]  
  > 애플리케이션 설치 스크립트의 이름은 특정 클러스터에 대해 고유해야 합니다. 스크립트 이름의 형식은 다음과 같아야 합니다.
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > 스크립트 이름은 다음 세 부분으로 구성됩니다.
  > 
  > * 스크립트 이름 접두사 - 애플리케이션 이름 또는 애플리케이션에 관련된 이름이 포함되어야 함
  > * 하이픈(-) - 쉽게 읽을 수 있게 함
  > * 고유 문자열 함수 - 애플리케이션 이름이 매개 변수로 사용됨
  > 
  > 위의 예제는 지속형 스크립트 동작 목록에서 **hue-install-v0-4wkahss55hlas**로 표시됩니다. [JSON 샘플 페이로드](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json)를 참조하세요.
  > 

설치 스크립트는 다음 특성을 가져야 합니다.
* 스크립트는 멱등원(idempotent)이어야 합니다. 스크립트를 여러 번 호출하더라도 동일한 결과가 생성됩니다.
* 스크립트 버전이 올바르게 지정되어야 합니다. 변경 내용을 업그레이드하거나 테스트하는 경우 스크립트에 다른 위치를 사용합니다. 이렇게 하면 애플리케이션을 설치하는 고객이 업데이트 또는 테스트에 따른 영향을 받지 않습니다. 
* 스크립트의 각 지점마다 적절한 로깅이 있어야 합니다. 일반적으로 스크립트 로그는 애플리케이션 설치 문제를 디버그하는 유일한 방법입니다.
* 외부 서비스 또는 리소스에 대한 호출에 적절한 재시도가 있어야 설치가 일시적인 네트워크 문제로 인해 영향을 받지 않습니다.
* 스크립트가 노드에서 서비스를 시작하는 경우 서비스를 모니터링하고 노드 재부팅 시 자동으로 시작되도록 구성해야 합니다.

## <a name="package-the-application"></a>애플리케이션 패키징
HDInsight 애플리케이션을 설치하는 데 필요한 모든 파일을 포함하는 zip 파일을 만듭니다. .zip 파일을 사용하여 애플리케이션을 게시합니다. 이 zip 파일에 포함되는 파일은 다음과 같습니다.

* createUiDefinition.json
* mainTemplate.json(샘플의 경우 [사용자 지정 HDInsight 애플리케이션 설치](hdinsight-apps-install-custom-applications.md) 참조)
* 필요한 모든 스크립트

> [!NOTE]  
> 공개적으로 액세스할 수 있는 엔드포인트에서 애플리케이션 파일(모든 웹앱 파일 포함)을 호스팅할 수 있습니다.

## <a name="publish-the-application"></a>애플리케이션 게시
HDInsight 애플리케이션을 게시하려면

1. [Azure 게시](https://publish.windowsazure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **솔루션 템플릿**을 선택합니다.
3. 제목을 입력한 다음 **새 솔루션 템플릿 만들기**를 선택합니다.
4. 조직을 아직 등록하지 않은 경우 **개발자 센터 계정 만들기 및 Azure 프로그램 조인**을 선택합니다.  자세한 내용은 [Microsoft 개발자 계정 만들기](../marketplace/marketplace-publishers-guide.md)를 참조하세요.
5. **시작할 몇 가지 토폴로지 정의**를 선택합니다. 솔루션 템플릿은 해당하는 모든 토폴로지의 "부모"입니다. 하나의 제품 또는 솔루션 템플릿에 여러 토폴로지를 정의할 수 있습니다. 제품이 스테이징으로 푸시될 때 해당 토폴로지도 모두 함께 푸시됩니다. 
6. 토폴로지 이름을 입력한 다음 **+** 를 선택합니다.
7. 새 버전을 입력한 다음 **+** 를 선택합니다.
8. 애플리케이션을 패키지했으면 만든 .zip 파일을 업로드합니다.  
9. **인증 요청**을 선택합니다. Microsoft 인증 팀에서 해당 파일을 검토하고 토폴로지를 인증합니다.

## <a name="next-steps"></a>다음 단계
* 클러스터에 [HDInsight 애플리케이션을 설치](hdinsight-apps-install-applications.md)하는 방법을 알아봅니다.
* [사용자 지정 HDInsight 애플리케이션을 설치](hdinsight-apps-install-custom-applications.md)하고 게시되지 않은 HDInsight 애플리케이션을 HDInsight에 배포하는 방법을 알아봅니다.
* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터를 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)하고 더 많은 애플리케이션을 추가하는 방법을 알아봅니다. 
* [Azure Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Apache Hadoop 클러스터를 만드는](hdinsight-hadoop-create-linux-clusters-arm-templates.md) 방법을 알아봅니다.
* [HDInsight에서 비어 있는 에지 노드를 사용](hdinsight-apps-use-edge-node.md)하여 HDInsight 클러스터에 액세스하고, HDInsight 애플리케이션을 테스트하고, HDInsight 애플리케이션을 호스팅하는 방법을 알아봅니다.

