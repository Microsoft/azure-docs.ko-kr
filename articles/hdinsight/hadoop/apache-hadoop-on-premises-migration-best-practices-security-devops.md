---
title: 온-프레미스 Apache Hadoop 클러스터를 Azure HDInsight로 마이그레이션 - 보안 및 DevOps 모범 사례
description: 온-프레미스 Hadoop 클러스터를 Azure HDInsight로 마이그레이션하는 보안 및 DevOps 모범 사례를 알아봅니다.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 1d13b52d253562a24946e6df2fc069f41b485fef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095381"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>온-프레미스 Apache Hadoop 클러스터를 Azure HDInsight로 마이그레이션 - 보안 및 DevOps 모범 사례

이 문서에서는 Azure HDInsight 시스템의 보안 및 DevOps에 대한 권장 사항을 제공합니다. 온-프레미스 Apache Hadoop 시스템을 Azure HDInsight로 마이그레이션하는 데 도움을 주는 모범 사례를 제공하는 시리즈의 일부입니다.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Enterprise Security Package를 사용하여 클러스터 보안 유지 및 관리

ESP(Enterprise Security Package)는 Active Directory 기반 인증, 다중 사용자 지원 및 역할 기반 액세스 제어를 지원합니다. ESP 옵션을 선택하면 HDInsight 클러스터가 Active Directory 도메인에 조인되고 엔터프라이즈 관리자는 Apache Ranger를 사용하여 Apache Hive 보안을 위한 RBAC(역할 기반 액세스 제어)를 구성할 수 있습니다. 또한 관리자는 직원의 데이터 액세스 및 액세스 제어 정책에 대한 변경 내용을 감사할 수 있습니다.

ESP는 ESP는 Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka 및 Interactive Query(Hive LLAP)의 클러스터 유형에서 사용할 수 있습니다. 

도메인에 조인된 HDInsight 클러스터를 배포하려면 다음 단계를 수행합니다.

- 도메인 이름을 전달하여 AAD(Azure Active Directory)를 배포합니다.
- AAD DS(Azure Active Directory Domain Services)를 배포합니다.
- 필요한 Virtual Network 및 서브넷을 만듭니다.
- Virtual Network에 AAD DS를 관리할 VM을 배포합니다.
- VM을 도메인에 조인합니다.
- AD 및 DNS 도구를 설치합니다.
- AAD DS 관리자에게 OU(조직 구성 단위)를 만들도록 합니다.
- AAD DS에 LDAPS를 사용하도록 설정합니다.
- OU를 읽고 쓸 수 있도록 OU에 대한 위임된 읽기 및 쓰기 관리 권한이 있는 서비스 계정을 Azure Active Directory에 만듭니다. 그러면 이 서비스 계정이 머신을 도메인에 조인하고 머신 보안 주체를 OU 내에 배치할 수 있습니다. 또한 클러스터를 만드는 동안 관리자가 지정하는 OU 내에 서비스 사용자를 만들 수 있습니다.


    > [!Note]
    > 서비스 계정이 AD 도메인 관리자 계정일 필요는 없습니다.


- 다음 매개 변수를 설정하여 HDInsight ESP 클러스터를 배포합니다.
    - **도메인 이름**: Azure AD DS와 연결된 도메인 이름입니다.
    - **도메인 사용자 이름**: 이전 섹션에서 만든 Azure AD DS DC 관리되는 도메인의 서비스 계정입니다(예: `hdiadmin@contoso.onmicrosoft.com`). 이 도메인 사용자는 이 HDInsight 클러스터의 관리자가 됩니다.
    - **도메인 암호**: 서비스 계정의 암호입니다.
    - **조직 구성 단위**: HDInsight 클러스터에 사용하려는 OU의 고유 이름입니다(예: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`). 이 OU가 없는 경우 HDInsight 클러스터는 서비스 계정의 권한을 사용하여 OU를 만들려고 시도합니다.
    - **LDAPS URL**: `ldaps://contoso.onmicrosoft.com:636`를 예로 들 수 있습니다.
    - **액세스 사용자 그룹**: 클러스터에 동기화할 사용자가 속하는 보안 그룹입니다(예: `HiveUsers`). 여러 사용자 그룹을 지정하려면 세미콜론 ‘;’으로 구분합니다. 그룹은 ESP 클러스터를 만들기 전에 디렉터리에 있어야 합니다.

자세한 내용은 다음 문서를 참조하세요.

- [도메인에 가입된 HDInsight 클러스터를 사용하는 Apache Hadoop 보안 소개](../domain-joined/apache-domain-joined-introduction.md)

- [HDInsight에서 Azure 도메인에 가입된 Apache Hadoop 클러스터 계획](../domain-joined/apache-domain-joined-architecture.md)
- [Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터 구성](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Azure Active Directory 사용자를 HDInsight 클러스터와 동기화](../hdinsight-sync-aad-users-to-cluster.md)
- [도메인에 가입된 HDInsight에서 Apache Hive 정책 구성](../domain-joined/apache-domain-joined-run-hive.md)
- [도메인에 가입된 HDInsight Hadoop 클러스터에서 Apache Oozie 실행](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>엔드 투 엔드 엔터프라이즈 보안 구현

다음 컨트롤을 사용하여 종단 간 엔터프라이즈 보안을 달성할 수 있습니다.

- **보호되는 비공개 데이터 파이프라인(경계 수준 보안)**
    - Azure Virtual Networks, 네트워크 보안 그룹 및 게이트웨이 서비스를 통해 경계 수준 보안을 달성할 수 있습니다.

- **데이터 액세스를 위한 인증 및 권한 부여**
    - Azure Active Directory Domain Services를 사용하여 도메인에 조인된 HDInsight 클러스터를 만듭니다. (Enterprise Security Package)
    - Ambari를 사용하여 AD 사용자용 클러스터 리소스에 대한 역할 기반 액세스를 제공합니다.
    - Apache Ranger를 사용하여 테이블/열/행 수준에서 Hive에 대한 액세스 제어 정책을 설정합니다.
    - 클러스터에 대한 SSH 액세스를 관리자로 제한할 수 있습니다.

- **감사**
    - HDInsight 클러스터 리소스 및 데이터에 대한 모든 액세스를 확인하고 보고할 수 있습니다.
    - 모든 액세스 제어 정책 변경 내용을 확인하고 보고합니다.

- **암호화**
    - Microsoft 관리 키 또는 고객 관리 키를 사용하는 투명한 서버 쪽 암호화입니다.
    - 전송 중 암호화에 클라이언트 쪽 암호화, https 및 TLS를 사용합니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure Virtual Networks 개요](../../virtual-network/virtual-networks-overview.md)
- [Azure 네트워크 보안 그룹 개요](../../virtual-network/security-overview.md)
- [Azure Virtual Network 피어링](../../virtual-network/virtual-network-peering-overview.md)
- [Azure Storage 보안 가이드](../../storage/common/storage-security-guide.md)
- [Azure Storage 서비스 저장 데이터 암호화](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>모니터링 및 경고 사용

자세한 내용은 다음 문서를 참조하세요.

[Azure Monitor 개요](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>클러스터 업그레이드

최신 기능을 활용하려면 정기적으로 최신 HDInsight 버전으로 업그레이드하세요. 다음 단계에 따라 클러스터를 최신 버전으로 업그레이드할 수 있습니다.

1. 사용 가능한 최신 HDInsight 버전을 사용하여 새 TEST HDInsight 클러스터를 만듭니다.
1. 새 클러스터에서 테스트를 수행하여 작업 및 워크로드가 예상대로 작동하는지 확인합니다.
1. 작업, 애플리케이션 또는 워크로드를 필요한 대로 수정합니다.
1. 클러스터 노드에 로컬로 저장된 모든 임시 데이터를 백업합니다.
1. 기존 클러스터를 삭제합니다.
1. 이전 클러스터와 동일한 기본 데이터 및 메타 저장소를 사용하여 동일한 VNET 서브넷에 최신 HDInsight 버전의 클러스터를 만듭니다.
1. 백업된 임시 데이터를 가져옵니다.
1. 새 클러스터를 사용하여 작업을 시작하거나 계속 처리합니다.

자세한 내용은 다음 문서를 참조하세요. [HDInsight 클러스터를 최신 버전으로 업그레이드](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>클러스터 운영 체제 패치

관리되는 Hadoop 서비스인 HDInsight는 HDInsight 클러스터에서 사용하는 VM의 OS를 패치하는 작업을 담당합니다.

자세한 내용은 다음 문서를 참조하세요. [HDInsight의 OS 패치](../hdinsight-os-patching.md)

## <a name="post-migration"></a>마이그레이션 후 작업

1. **애플리케이션 재구성** - 반복적으로 작업, 프로세스 및 스크립트를 필요한 대로 변경합니다.
2. **테스트 수행** - 반복적으로 기능 및 성능 테스트를 실행합니다.
3. **최적화** - 위의 테스트 결과에 따라 성능 문제를 해결한 후 다시 테스트하여 성능이 개선되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)에 대해 자세히 알아보기
