---
title: Azure HDInsight에서 관리 되는 id
description: Azure HDInsight에서 관리 되는 id의 구현에 대 한 개요를 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 07a8c26f7fc314680c51270ebafe03d4e3a84757
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749847"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight에서 관리 되는 id

관리 id는 Azure AD (Azure Active Directory)에 등록 된 id로, Azure에서 자격 증명을 관리 합니다. 관리 id를 사용 하면 Azure AD에서 서비스 주체를 등록할 필요가 없습니다. 또는 인증서와 같은 자격 증명을 유지 관리 합니다.

관리 id는 azure AD domain services에 액세스 하거나 필요한 경우 Azure Data Lake Storage Gen2에서 파일에 액세스 하는 데 사용 됩니다.

관리 id에는 사용자 할당 및 시스템 할당 이라는 두 가지 유형이 있습니다. Azure HDInsight는 사용자 할당 관리 id만 지원 합니다. HDInsight는 시스템 할당 관리 id를 지원 하지 않습니다. 사용자 할당 관리 id는 독립 실행형 Azure 리소스로 만들어지므로 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 반면, 시스템 할당 관리 id는 Azure AD에서 생성 된 다음 특정 Azure 서비스 인스턴스에서 직접 사용 하도록 설정 됩니다. 그런 다음 해당 시스템 할당 관리 id의 수명은 사용 하도록 설정 된 서비스 인스턴스의 수명에 연결 됩니다.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 관리 id 구현

Azure HDInsight에서 관리 되는 id는 HDInsight 서비스에서 내부 구성 요소에 대해서만 사용할 수 있습니다. 현재 외부 서비스에 액세스 하기 위해 HDInsight 클러스터 노드에 설치 된 관리 되는 id를 사용 하 여 액세스 토큰을 생성 하는 데 지원 되는 방법은 없습니다. 계산 Vm과 같은 일부 Azure 서비스의 경우 관리 되는 id는 액세스 토큰을 획득 하는 데 사용할 수 있는 끝점을 사용 하 여 구현 됩니다. 이 끝점은 현재 HDInsight 노드에서 사용할 수 없습니다.

분석 작업 (예: SCALA 작업)에 비밀/암호를 넣지 않도록 응용 프로그램을 부트스트랩 해야 하는 경우 스크립트 작업을 사용 하 여 클러스터 노드에 자신의 인증서를 distrubte 다음 해당 인증서를 사용 하 여 액세스 토큰을 가져오기가 수 있습니다 (예: Azure KeyVault 액세스).

## <a name="create-a-managed-identity"></a>관리 ID 만들기

관리 되는 id는 다음 방법 중 하나를 사용 하 여 만들 수 있습니다.

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

관리 id를 구성 하는 나머지 단계는 사용 되는 시나리오에 따라 달라 집니다.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight의 관리 되는 id 시나리오

관리 id는 여러 시나리오에서 Azure HDInsight에 사용 됩니다. 자세한 설정 및 구성 지침은 관련 문서를 참조 하세요.

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [고객 관리형 키 디스크 암호화](disk-encryption.md)

## <a name="faq"></a>FAQ

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>클러스터를 만든 후에 관리 되는 id를 삭제 하면 어떻게 되나요?

관리 id가 필요할 때 클러스터는 문제가 발생 합니다. 현재 클러스터를 만든 후에 관리 되는 id를 업데이트 하거나 변경할 수 있는 방법은 없습니다. 따라서 클러스터 런타임 중에 관리 id가 삭제 되지 않도록 하는 것이 좋습니다. 또는 클러스터를 다시 만들고 새 관리 id를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)
