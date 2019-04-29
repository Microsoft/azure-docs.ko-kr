---
title: Enterprise Security Package를 사용하여 Apache Hadoop 보안 소개
description: Enterprise Security Package가 엔터프라이즈 보안의 네 가지 핵심 요소를 지원하는 방법을 알아봅니다.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: deff2aa3cd475a3ed37adc4408a133ed1fd0757e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113209"
---
# <a name="an-introduction-to-apache-hadoop-security-with-enterprise-security-package"></a>Enterprise Security Package를 사용하여 Apache Hadoop 보안 소개

이전에 Azure HDInsight는 단일 사용자 로컬 관리자만 지원했습니다. 따라서 소규모 애플리케이션 팀이나 부서의 경우에 유용하게 작동했습니다. Apache Hadoop 기반 워크로드가 기업 부문에서 더 많은 인기를 얻게 되면서 Active Directory 기반 인증, 다중 사용자 지원 및 역할 기반 액세스 제어와 같은 엔터프라이즈급 기능에 대한 필요성이 점점 더 중요해졌습니다. 

Active Directory 도메인에 가입된 ESP(Enterprise Security Package)가 포함된 HDInsight 클러스터를 만들 수 있습니다. HDInsight 클러스터에 로그인 하려면 Azure Active Directory를 통해 인증할 수 있는 기업의 직원 목록을 구성할 수 있습니다. 아무도에서 회사 외부에서 로그인 하거나 HDInsight 클러스터에 액세스 합니다. 

기업 관리자는 [Apache Ranger](https://hortonworks.com/apache/ranger/)를 사용하여 Apache Hive 보안을 위한 RBAC(역할 기반 액세스 제어)를 구성할 수 있습니다. RBAC를 구성하면 필요한 만큼 데이터 액세스를 제한할 수 있습니다. 마지막으로 관리자는 직원의 데이터 액세스 및 액세스 제어 정책에 대한 변경 내용을 감사할 수 있습니다. 그런 다음, 관리자는 높은 수준의 회사 리소스 거버넌스를 달성할 수 있습니다.

> [!NOTE]  
> 이제 ESP 클러스터에서 Apache Oozie를 사용할 수 있습니다. Oozie 웹 UI에 액세스하려면 사용자가 [터널링](../hdinsight-linux-ambari-ssh-tunnel.md)을 사용하도록 설정해야 합니다.

기업 보안에는 네 가지 주요 요소인 경계 보안, 인증, 권한 부여 및 암호화가 포함됩니다.

![엔터프라이즈 보안의 네 가지 핵심 요소를 고려한 Enterprise Security Package HDInsight 클러스터의 이점](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>경계 보안
HDInsight의 경계 보안은 가상 네트워크와 Azure VPN Gateway 서비스를 통해 수행됩니다. 기업 관리자는 가상 네트워크 내에서 ESP 클러스터를 만들고 네트워크 보안 그룹(방화벽 규칙)을 사용하여 가상 네트워크에 대한 액세스를 제한할 수 있습니다. 인바운드 방화벽 규칙에 정의된 IP 주소만이 HDInsight 클러스터와 통신할 수 있게 됩니다. 이 구성은 경계 보안을 제공합니다.

다른 경계 보안 계층은 VPN Gateway 서비스를 사용하여 이루어집니다. 게이트웨이는 HDInsight 클러스터에 들어오는 모든 요청에 대한 첫 번째 방어선의 역할을 합니다. 요청을 받아들이고, 유효성 검사를 수행한 다음, 요청을 클러스터의 다른 노드로 전달하도록 허용합니다. 이 방법으로 게이트웨이는 클러스터에서 다른 이름 및 데이터 노드에 대한 경계 보안을 제공합니다.

## <a name="authentication"></a>Authentication
기업 관리자는 [가상 네트워크](https://azure.microsoft.com/services/virtual-network/)에서 ESP가 포함된 HDInsight 클러스터를 만들 수 있습니다. HDInsight 클러스터의 모든 노드는 기업에서 관리하는 도메인에 가입됩니다. 이 작업은 [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md)를 사용하여 이루어집니다. 

이 설정을 사용 하면 기업 직원 수 로그인 클러스터 노드에 해당 도메인 자격 증명을 사용 하 여 합니다. 또한 클러스터와의 상호 작용하기 위해 해당 도메인 자격 증명을 사용하여 Apache Ambari 뷰, ODBC, JDBC, PowerShell 및 REST API 등의 다른 승인된 엔드포인트로 인증할 수도 있습니다. 관리자는 이러한 엔드포인트를 통해 클러스터와 상호 작용하는 사용자의 수를 완벽히 제한합니다.

## <a name="authorization"></a>권한 부여
대부분의 기업이 따르는 선택한 모범 사례는 모든 직원에게 기업 리소스에 대한 모든 액세스를 부여하지 않도록 하는 것입니다. 마찬가지로 관리자는 클러스터 리소스에 대한 역할 기반 액세스 제어 정책을 정의할 수 있습니다. 

예를 들어, 관리자는 [Apache Ranger](https://hortonworks.com/apache/ranger/)를 구성하여 Hive에 대한 액세스 제어 정책을 설정할 수 있습니다. 이 기능을 사용하여 직원이 작업을 수행하는 데 필요한 만큼의 데이터에만 액세스할 수 있도록 합니다. 클러스터에 대한 SSH 액세스는 관리자에게만 제한됩니다.

## <a name="auditing"></a>감사
클러스터 리소스에 대한 모든 액세스를 감사하는 작업은 리소스의 무단 또는 실수로 실행된 액세스를 추적하는 데 필요합니다. 권한 없는 사용자로부터 HDInsight 클러스터 리소스를 보호하고 데이터를 보호하는 것만큼 중요합니다. 

관리자는 HDInsight 클러스터 리소스 및 데이터에 대한 모든 액세스를 확인하고 보고할 수 있습니다. 관리자는 Apache Ranger가 지원되는 엔드포인트에서 만들어진 액세스 제어 정책에 대한 모든 변경 내용을 확인하고 보고할 수도 있습니다. 

ESP가 포함된 HDInsight 클러스터는 Apache Ranger UI를 사용하여 감사 로그를 검색합니다. Ranger는 백 엔드에서 로그를 저장하고 검색하는 데 [Apache Solr](https://hortonworks.com/apache/solr/)을 사용합니다.

## <a name="encryption"></a>암호화
조직 보안 및 준수 요구 사항을 충족하는 데는 데이터 보호가 중요합니다. 권한 없는 직원의 데이터 액세스를 제한하고 데이터를 암호화해야 합니다. 

HDInsight 클러스터용 데이터 저장소(Azure Blob 스토리지 및 Azure Data Lake Storage Gen1/Gen2)는 둘 다 투명한 서버 쪽 [저장 데이터 암호화](../../storage/common/storage-service-encryption.md)를 지원합니다. 보안 HDInsight 클러스터는 미사용 데이터의 이 서버 쪽 암호화 기능에서 원활하게 작동합니다.

## <a name="next-steps"></a>다음 단계

* [ESP가 포함된 HDInsight 클러스터 계획](apache-domain-joined-architecture.md)
* [ESP가 포함된 HDInsight 클러스터 구성](apache-domain-joined-configure.md)
* [ESP가 포함된 HDInsight 클러스터 관리](apache-domain-joined-manage.md)
* [ESP가 포함된 HDInsight 클러스터용 Apache Hive 정책 구성](apache-domain-joined-run-hive.md)
* [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

