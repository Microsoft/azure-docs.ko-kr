---
title: Azure HDInsight에서 관리 되는 id
description: Azure HDInsight에서 관리 되는 id의 구현에 대 한 개요를 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 236f79c9060a0d6fdcb0f558373d02f32eba7abb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905610"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight에서 관리 되는 id

관리 id는 Azure AD (Azure Active Directory)에 등록 된 id로, Azure에서 자격 증명을 관리 합니다. 관리 id를 사용 하면 Azure AD에서 서비스 주체를 등록 하거나 인증서와 같은 자격 증명을 유지 관리할 필요가 없습니다.

관리 id는 azure AD domain services에 액세스 하거나 필요한 경우 Azure Data Lake Storage Gen2에서 파일에 액세스 하는 데 사용 됩니다.

관리 id에는 사용자 할당 및 시스템 할당 이라는 두 가지 유형이 있습니다. Azure HDInsight는 사용자 할당 관리 id를 사용 합니다. 사용자 할당 관리 id는 독립 실행형 Azure 리소스로 만들어지므로 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 반면, 시스템 할당 관리 id는 Azure AD에서 생성 된 다음 특정 Azure 서비스 인스턴스에서 직접 사용 하도록 설정 됩니다. 그런 다음 해당 시스템 할당 관리 id의 수명은 사용 하도록 설정 된 서비스 인스턴스의 수명에 연결 됩니다.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 관리 id 구현

Azure HDInsight에서 관리 id는 클러스터의 각 노드에 프로 비전 됩니다. 그러나 이러한 id 구성 요소는 HDInsight 서비스 에서만 사용할 수 있습니다. 현재 HDInsight 클러스터 노드에 설치 된 관리 되는 id를 사용 하 여 액세스 토큰을 생성 하는 데 지원 되는 방법은 없습니다. 일부 Azure 서비스의 경우 관리 되는 id는 다른 Azure 서비스와 상호 작용 하기 위해 액세스 토큰을 획득 하는 데 사용할 수 있는 엔드포인트을 사용 하 여 구현 됩니다.

## <a name="create-a-managed-identity"></a>관리 id 만들기

관리 되는 id는 다음 방법 중 하나를 사용 하 여 만들 수 있습니다.

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure 리소스 관리자](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

관리 id를 구성 하는 나머지 단계는 사용 되는 시나리오에 따라 달라 집니다.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight의 관리 되는 id 시나리오

관리 id는 여러 시나리오에서 Azure HDInsight에 사용 됩니다. 자세한 설정 및 구성 지침은 관련 문서를 참조 하세요.

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="faq"></a>FAQ
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>클러스터를 만든 후에 관리 되는 id를 삭제 하면 어떻게 되나요?
관리 id가 필요할 때 클러스터는 문제가 발생 합니다. 현재 클러스터를 만든 후에 관리 제거할 id를 업데이트 하거나 변경할 수 있는 방법은 없습니다. 따라서 클러스터 런타임 중에 관리 id가 삭제 되지 않도록 하는 것이 좋습니다. 또는 클러스터를 다시 만들고 새 관리 id를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)
