---
title: Azure HDInsight에서 관리 되는 id
description: Azure HDInsight에서 관리 되는 id의 구현에 대 한 개요를 제공합니다.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 5012b669b7460a44cb2732d7db7bf76fd1f567cf
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766947"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight에서 관리 되는 id

관리 되는 id는 Azure Active Directory (Azure AD) 자격 증명은 Azure에서 관리에 등록 id입니다. 관리 되는 id를 사용 하 여 Azure AD에서 서비스 주체를 등록 또는 인증서와 같은 자격 증명을 유지 관리할 필요가 없습니다.

관리 되는 id 클러스터를 Azure AD 도메인 서비스를 액세스, Azure Key Vault 액세스 또는 Azure Data Lake 저장소 Gen2의 파일에 액세스할 수 있도록 Azure HDInsight에서 사용할 수 있습니다.

관리 되는 id의 두 가지가: 사용자 할당 및 시스템 할당 합니다. Azure HDInsight는 관리 되는 사용자 할당 id를 사용합니다. 사용자 할당 관리 id는 독립 실행형 Azure service 인스턴스가 하나 이상 할당할 수 있는 Azure 리소스 생성 됩니다. 반면, 시스템 할당 관리 id는 Azure AD에서 만들어지고 특정 Azure 서비스 인스턴스에서 직접 자동으로 설정 합니다. 다음에서 사용할 수 있는 서비스 인스턴스의 수명 동안 해당 시스템에서 지정한 관리 되는 id의 수명 동안 연결 됩니다.

## <a name="hdinsight-managed-identity-implementation"></a>관리 되는 HDInsight identity 구현

Azure HDInsight 클러스터의 각 노드에서 관리 되는 id 프로 비전 됩니다. 그러나이 id 구성 요소는 HDInsight 서비스에서 사용할 수만 합니다. 현재 지원 되는 메서드가 없는 HDInsight 클러스터 노드에 설치 된 관리 되는 id를 사용 하 여 액세스 토큰을 생성할 수 있습니다. 일부 Azure 서비스에 대 한 관리 되는 id는 다른 Azure 서비스를 직접 상호 작용 하기 위한 액세스 토큰을 획득 하는 데 사용할 수 있는 끝점을 사용 하 여 구현 됩니다.

## <a name="create-a-managed-identity"></a>관리 되는 id 만들기

다음 방법 중 하나를 사용 하 여 관리 되는 id는 만들 수 있습니다.

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure 리소스 관리자](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

관리 되는 id를 구성 하는 나머지 단계를 사용 하는 시나리오에 따라 달라 집니다.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight에서 관리 되는 id 시나리오

관리 되는 id는 여러 시나리오에서 Azure HDInsight에서 사용 됩니다. 자세한 설치 및 구성 지침에 대 한 관련된 문서를 참조 하세요.

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>다음 단계

* [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)
