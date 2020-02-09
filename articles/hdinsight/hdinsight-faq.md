---
title: Azure HDInsight 질문과 대답
description: HDInsight에 대 한 질문과 대답
keywords: 질문과 대답 (faq)
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: b05b83086cc9d8449d9517897f347b6e2685aa95
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720353"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: 질문과 대답

이 문서에서는 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)를 실행 하는 방법에 대해 가장 일반적인 질문 중 일부에 대 한 답변을 제공 합니다.

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight 클러스터 만들기 또는 삭제

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>HDInsight 클러스터를 프로 비전 할 어떻게 할까요? 있나요?

사용 가능한 HDInsight 클러스터 유형과 프로 비전 방법에 대 한 검토 하려면 [Apache Hadoop, Apache Spark, Apache Kafka 등을 사용 하 여 hdinsight에서 클러스터 설정](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)을 참조 하세요.

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>기존 HDInsight 클러스터를 삭제 어떻게 할까요??

더 이상 사용 하지 않을 때 클러스터를 삭제 하는 방법에 대 한 자세한 내용은 [HDInsight 클러스터 삭제](hdinsight-delete-cluster.md)를 참조 하세요.

만들기 및 삭제 작업 사이에 30 ~ 60 분 이상 남겨 둡니다. 그렇지 않으면 다음과 같은 오류 메시지와 함께 작업이 실패할 수 있습니다.

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>워크 로드에 대 한 올바른 코어 또는 노드 수를 선택 어떻게 할까요??

적절 한 수의 코어 및 기타 구성 옵션은 다양 한 요인에 따라 달라 집니다.

자세한 내용은 [HDInsight 클러스터에 대 한 용량 계획](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)을 참조 하세요.

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>용량 문제로 인해 클러스터 프로 비전이 실패할 경우 어떻게 해야 하나요?

일반적인 용량 문제 오류 및 완화 기술은이 섹션에서 제공 됩니다.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>오류: 배포가 ' 800 '의 할당량을 초과 합니다.

Azure의 할당량 한도는 리소스 그룹당 800개 배포입니다. 리소스 그룹, 구독, 계정 또는 기타 범위 마다 다른 할당량이 적용 됩니다. 예를 들어 지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용 된 용량 보다 더 많은 코어가 있는 가상 컴퓨터를 배포 하려고 하면 할당량을 초과 했음을 나타내는 오류 메시지가 표시 됩니다.

이 문제를 해결 하려면 Azure Portal, CLI 또는 PowerShell을 사용 하 여 더 이상 필요 하지 않은 배포를 삭제 합니다.

자세한 내용은 [리소스 할당량에 대한 오류 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)을 참조하세요.

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>오류: 최대 노드가이 영역에서 사용 가능한 코어 수를 초과 했습니다.

지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용 된 용량 보다 더 많은 코어가 포함 된 리소스를 배포 하려고 하면 할당량을 초과 했음을 나타내는 오류 메시지가 표시 됩니다.

할당량 증가를 요청하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **도움말 + 지원**을 선택 합니다.
   
1. **새 지원 요청**을 선택합니다.
   
1. **새 지원 요청** 페이지의 **기본 사항** 탭에서 다음 정보를 제공 합니다.
   
   - **문제 유형:** **서비스 및 구독 제한 (할당량)** 을 선택 합니다.
   - **구독:** 수정 하려는 구독을 선택 합니다.
   - **할당량 유형:** **HDInsight**를 선택 합니다.

자세한 내용은 [코어를 늘리기 위한 지원 티켓 만들기](hdinsight-capacity-planning.md#quotas)를 참조하세요.

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>HDInsight 클러스터에는 다양 한 유형의 노드가 있나요?

Azure HDInsight 클러스터에는 서로 다른 유형의 가상 머신 또는 노드가 있습니다. 각 노드 유형은 시스템 작업에서 역할을 수행 합니다.

자세한 내용은 [Azure HDInsight 클러스터의 리소스 종류](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)를 참조 하세요.

## <a name="individual-components"></a>개별 구성 요소

### <a name="can-i-install-additional-components-on-my-cluster"></a>클러스터에 추가 구성 요소를 설치할 수 있나요?

예. 추가 구성 요소를 설치 하거나 클러스터 구성을 사용자 지정 하려면 다음을 사용 합니다.

- 스크립트를 생성 합니다. 스크립트는 스크립트 작업을 통해 호출 됩니다. [스크립트 작업](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)은 Azure Portal, Hdinsight Windows PowerShell Cmdlet 또는 HDINSIGHT .net SDK에서 사용할 수 있는 구성 옵션입니다. 이 구성 옵션은 Azure Portal, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있습니다.

- 에코 시스템 응용 프로그램을 설치 하는 [HDInsight 응용 프로그램 플랫폼](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) 입니다.

지원 되는 구성 요소 목록은 [HDInsight에서 사용할 수 있는 Apache Hadoop 구성 요소 및 버전](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions) 을 참조 하세요.

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>클러스터에 미리 설치 된 개별 구성 요소를 업그레이드할 수 있나요?

클러스터에 미리 설치 된 기본 제공 구성 요소 또는 응용 프로그램을 업그레이드 하는 경우 결과 구성이 Microsoft에서 지원 되지 않습니다. 이러한 시스템 구성은 Microsoft에서 테스트 되지 않았습니다. 구성 요소의 업그레이드 된 버전이 이미 설치 되어 있을 수 있는 다른 버전의 HDInsight 클러스터를 사용 하려고 합니다.

예를 들어 Hive를 개별 구성 요소로 업그레이드 하는 것은 지원 되지 않습니다. HDInsight는 관리 되는 서비스 이며 많은 서비스가 Ambari 서버와 통합 되 고 테스트 되었습니다. Hive를 자체적으로 업그레이드 하면 다른 구성 요소의 인덱싱된 이진 파일이 변경 되 고 클러스터에서 구성 요소 통합 문제가 발생 합니다.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark와 Kafka을 동일한 HDInsight 클러스터에서 실행할 수 있나요?

아니요, 동일한 HDInsight 클러스터에서 Apache Kafka 및 Apache Spark를 실행할 수 없습니다. 리소스 경합 문제를 방지 하기 위해 Kafka 및 Spark에 대해 별도의 클러스터를 만듭니다.

### <a name="how-do-i-change-timezone-in-ambari"></a>Ambari에서 표준 시간대를 변경 어떻게 할까요??

1. `https://CLUSTERNAME.azurehdinsight.net`에서 Ambari 웹 UI를 엽니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.
2. 오른쪽 위 모서리에서 관리를 선택 합니다. 설정. 

   ![Ambari 설정](media/hdinsight-faq/ambari-settings.png)

3. 사용자 설정 창의 표준 시간대 드롭다운에서 새 표준 시간대를 선택 하 고 저장을 클릭 합니다.

   ![Ambari 사용자 설정](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>메타 저장소

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>기존 metastore에서 Azure SQL Server로 마이그레이션하려면 어떻게 해야 하나요? 

SQL Server에서 Azure SQL Server로 마이그레이션하려면 [자습서: DMS를 사용 하 여 오프 라인 Azure SQL Database에서 단일 데이터베이스 또는 풀링된 데이터베이스로 SQL Server 마이그레이션](../dms/tutorial-sql-server-to-azure-sql.md)을 참조 하세요.

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>클러스터가 삭제 될 때 Hive metastore 삭제 됩니까?

클러스터에서 사용 하도록 구성 된 metastore 유형에 따라 달라 집니다.

기본 metastore: 기본 metastore는 클러스터 수명 주기의 일부입니다. 클러스터를 삭제하면 해당하는 metastore와 메타데이터도 삭제됩니다.

사용자 지정 metastore: metastore의 수명 주기는 클러스터의 수명 주기와 연결 되지 않습니다. 따라서 메타 데이터 손실 없이 클러스터를 만들고 삭제할 수 있습니다. HDInsight 클러스터를 삭제 하 고 다시 만든 후에도 Hive 스키마와 같은 메타 데이터가 유지 됩니다.

자세한 내용은 [Azure HDInsight에서 외부 메타데이터 저장소 사용](hdinsight-use-external-metadata-stores.md)을 참조하세요.

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Hive metastore 마이그레이션하는 경우에도 레인저 데이터베이스의 기본 정책이 마이그레이션 되나요?

아니요, 정책 정의는 레인저 데이터베이스에 있기 때문에 레인저 데이터베이스를 마이그레이션하면 해당 정책이 마이그레이션됩니다.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Hive metastore를 ESP (Enterprise Security Package) 클러스터에서 비 ESP 클러스터로 또는 그 반대로 마이그레이션할 수 있나요?

예, Hive metastore을 ESP에서 비 ESP 클러스터로 마이그레이션할 수 있습니다.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hive metastore 데이터베이스의 크기를 예측 하려면 어떻게 해야 하나요?

Hive metastore는 Hive 서버에서 사용 하는 데이터 원본에 대 한 메타 데이터를 저장 하는 데 사용 됩니다. 크기 요구 사항은 Hive 데이터 원본의 수와 복잡성에 따라 달라 지 며 앞으로 예측할 수 없습니다. [Hive metastore 모범 사례](hdinsight-use-external-metadata-stores.md#hive-metastore-best-practices)에 설명 된 대로 50 DTU 및 250 GB의 저장소를 제공 하는 S2 계층에서 시작할 수 있으며, 병목 현상이 발생 하면 데이터베이스를 확장할 수 있습니다.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>외부 metastore Azure SQL Database 아닌 다른 데이터베이스를 지원 하나요?

아니요, Microsoft는 외부 사용자 지정 metastore Azure SQL Database만 지원 합니다.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>여러 클러스터에서 metastore를 공유할 수 있나요?

예, 동일한 버전의 HDInsight를 사용 하는 한 여러 클러스터에서 사용자 지정 metastore를 공유할 수 있습니다.

## <a name="connectivity-and-virtual-networks"></a>연결 및 가상 네트워크  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>네트워크에서 22 및 23 포트가 차단 되는 의미는 무엇 인가요?

포트 22 및 포트 23을 차단 하는 경우 클러스터에 대 한 SSH 액세스 권한이 없는 것입니다. 이러한 포트는 HDInsight 서비스에서 사용 되지 않습니다.

자세한 내용은 다음 문서를 참조하세요.

- [네트워크 트래픽 제어](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [개인 엔드포인트을 사용 하 여 가상 네트워크에서 HDInsight 클러스터에 들어오는 트래픽 보안](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight 관리 IP 주소](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>HDInsight 클러스터와 동일한 서브넷 내에 추가 가상 컴퓨터를 배포할 수 있나요?

예, HDInsight 클러스터와 동일한 서브넷 내에 추가 가상 컴퓨터를 배포할 수 있습니다. 다음과 같은 구성을 사용할 수 있습니다.

- 에 지 노드: [HDInsight의 Apache Hadoop 클러스터에서 빈에 지 노드 사용](hdinsight-apps-use-edge-node.md)에 설명 된 대로 클러스터에 다른에 지 노드를 추가할 수 있습니다.

- 독립 실행형 노드: 독립 실행형 가상 컴퓨터를 동일한 서브넷에 추가 하 고 개인 엔드포인트 `https://<CLUSTERNAME>-int.azurehdinsight.net`를 사용 하 여 해당 가상 컴퓨터에서 클러스터에 액세스할 수 있습니다. 자세한 내용은 [네트워크 트래픽 제어](hdinsight-plan-virtual-network-deployment.md#networktraffic)를 참조 하세요.

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>에 지 노드의 로컬 디스크에 데이터를 저장 해야 하나요?

아니요, 로컬 디스크에 데이터를 저장 하는 것은 좋지 않습니다. 노드가 실패 하면 로컬에 저장 된 모든 데이터가 손실 됩니다. 데이터를 저장 하기 위해 Azure Files 공유를 탑재 하거나 Azure Data Lake Storage Gen2 또는 Azure Blob 저장소에 데이터를 저장 하는 것이 좋습니다.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>기존 HDInsight 클러스터를 다른 가상 네트워크에 추가할 수 있나요?

너는 할 수 없어요. 프로 비전 시 가상 네트워크를 지정 해야 합니다. 프로 비전 하는 동안 가상 네트워크가 지정 되지 않은 경우 배포는 외부에서 액세스할 수 없는 내부 네트워크를 만듭니다. 자세한 내용은 [기존 가상 네트워크에 HDInsight 추가](hdinsight-plan-virtual-network-deployment.md#existingvnet)를 참조 하세요.

## <a name="security-and-certificates"></a>보안 및 인증서

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터의 맬웨어 보호에 대 한 권장 사항은 무엇 인가요?

맬웨어 보호에 대 한 자세한 내용은 [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지](../security/fundamentals/antimalware.md)를 참조 하세요.

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>HDInsight ESP 클러스터에 대 한 keytab를 만들 어떻게 할까요? 있나요?

도메인 사용자 이름에 대 한 Kerberos keytab를 만듭니다. 나중에이 keytab를 사용 하 여 암호를 입력 하지 않고도 원격 도메인에 가입 된 클러스터에 인증할 수 있습니다. 도메인 이름은 대문자입니다.

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>기존 Azure Active Directory 테넌트를 사용 하 여 ESP를 포함 하는 HDInsight 클러스터를 만들 수 있나요?

ESP를 사용 하 여 HDInsight 클러스터를 만들려면 먼저 Azure Active Directory Domain Services (Azure AD DS)를 사용 하도록 설정 해야 합니다. 오픈 소스 Hadoop은 인증을 위해 Kerberos를 사용 합니다 (OAuth와 반대).

Vm을 도메인에 가입 시키려면 도메인 컨트롤러가 있어야 합니다. Azure AD DS는 관리 되는 도메인 컨트롤러 이며 관리 되는 방식으로 보안 Hadoop 클러스터를 빌드하기 위한 모든 Kerberos 요구 사항을 제공 하는 Azure Active Directory의 확장으로 간주 됩니다. 관리 서비스인 HDInsight는 Azure AD DS와 통합 되어 종단 간 보안을 제공 합니다.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>AAD DS secure LDAP 설정에서 자체 서명 된 인증서를 사용 하 고 ESP 클러스터를 프로 비전 할 수 있나요?

인증 기관에서 발급 한 인증서를 사용 하는 것이 좋지만, 자체 서명 된 인증서를 사용 하는 것도 ESP 에서도 지원 됩니다. 자세한 내용은 다음을 참조하세요.

- [Azure Active Directory Domain Services 사용](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [자습서: Azure Active Directory Domain Services 관리 되는 도메인에 대 한 보안 LDAP 구성](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>레인저에 표시 된 로그인 작업을 어떻게 끌어올 수 있나요?

감사 요구 사항에 대 한 자세한 내용은 [Azure Monitor 로그를 사용 하 여 HDInsight 클러스터 모니터링](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)에 설명 된 대로 Azure Monitor 로그를 사용 하도록 권장 합니다.

### <a name="can-i-disable-clamscan-on-my-cluster"></a>클러스터에서 Clamscan를 사용 하지 않도록 설정할 수 있나요?

Clamscan는 HDInsight 클러스터에서 실행 되 고 Azure 보안 (azsecd)에서 클러스터를 바이러스 공격 으로부터 보호 하는 데 사용 하는 바이러스 백신 소프트웨어입니다. 사용자가 기본 Clamscan 구성을 변경 하지 않는 것이 좋습니다.

이 프로세스는 다른 프로세스를 방해 하거나 다른 프로세스를 벗어나지 않습니다. 항상 다른 프로세스로 생성 됩니다. Clamscan의 CPU 급증은 시스템이 유휴 상태일 때만 표시 되어야 합니다.  

일정을 제어 해야 하는 경우 다음 단계를 사용할 수 있습니다.

1. 다음 명령을 사용 하 여 자동 실행을 사용 하지 않도록 설정 합니다.
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Root로 다음 명령을 실행 하는 Cron 작업을 추가 합니다.
   
   `/usr/local/bin/azsecd manual -s clamav`

Cron 작업을 설정 및 실행 하는 방법에 대 한 자세한 내용은 [cron 작업 어떻게 할까요? 설정](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)을 참조 하세요.

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Spark ESP 클러스터에서 LLAP을 사용할 수 있는 이유는 무엇 인가요?
ESP Spark 클러스터에서 LLAP은 성능이 아닌 보안상의 이유로 (예: Apache 레인저) 사용할 수 있습니다. LLAP의 리소스 사용을 수용할 수 있도록 더 큰 노드 Vm을 사용 해야 합니다 (예: 최소 D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>ESP 클러스터를 만든 후 추가 AAD 그룹을 추가 하려면 어떻게 해야 하나요?
다음 두 가지 방법으로이 작업을 수행할 수 있습니다. 1-클러스터를 만들 때 클러스터를 다시 만들고 추가 그룹을 추가할 수 있습니다. AAD에서 범위 동기화를 사용 하는 경우 그룹 B가 범위 지정 동기화에 포함 되어 있는지 확인 하세요.
2-그룹을 ESP 클러스터를 만드는 데 사용 된 이전 그룹의 중첩 된 하위 그룹으로 추가 합니다. 예를 들어 그룹 `A`를 사용 하 여 ESP 클러스터를 만든 경우 나중에 그룹 `B`를 `A` 중첩 된 하위 그룹으로 추가 하 고 약 1 시간 후에는 클러스터에서 자동으로 동기화 되 고 사용할 수 있습니다. 

## <a name="storage"></a>스토리지

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>기존 HDInsight 클러스터에 Azure Data Lake Storage Gen2 추가 저장소 계정으로 추가할 수 있나요?

아니요, 현재 blob storage를 기본 저장소로 포함 하는 클러스터에 Azure Data Lake Storage Gen2 Storage 계정을 추가할 수 없습니다. 자세한 내용은 [저장소 옵션 비교](hdinsight-hadoop-compare-storage-options.md)를 참조 하세요.

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Data Lake storage 계정에 대해 현재 연결 된 서비스 주체를 찾으려면 어떻게 하나요?

Azure Portal의 클러스터 속성에서 **Data Lake Storage Gen1 액세스** 에서 설정을 찾을 수 있습니다. 자세한 내용은 [클러스터 설정 확인](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)을 참조 하세요.
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>HDInsight 클러스터에 대 한 저장소 계정 및 blob 컨테이너의 사용을 계산 하려면 어떻게 해야 하나요?

다음 작업 중 하나를 수행합니다.

- [PowerShell 사용](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- /User/hive/.의 크기를 확인 합니다.다음 명령줄을 사용 하 여 HDInsight 클러스터의 휴지통/폴더:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>내 blob storage 계정에 대 한 감사를 설정 하려면 어떻게 해야 하나요?

Blob storage 계정을 감사 하려면 [Azure Portal에서 저장소 계정 모니터링의](../storage/common/storage-monitor-storage-account.md)절차를 사용 하 여 모니터링을 구성 합니다. HDFS 감사 로그는 로컬 HDFS 파일 시스템에 대해서만 감사 정보를 제공 합니다 (hdfs://mycluster).  원격 저장소에서 수행 되는 작업은 포함 되지 않습니다.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Blob 컨테이너와 HDInsight 헤드 노드 간에 파일을 전송 하려면 어떻게 해야 하나요?

헤드 노드에서 다음 셸 스크립트와 비슷한 스크립트를 실행 합니다.

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> 파일 *이름 .txt* 는 blob 컨테이너에 있는 파일의 절대 경로를 포함 합니다.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>저장소에 대 한 레인저 플러그 인이 있나요?

현재 blob storage 및 Azure Data Lake Storage Gen1 또는 Gen2에 대 한 레인저 플러그 인이 없습니다. ESP 클러스터의 경우에는 HDFS 도구를 사용 하 여 파일 시스템 수준에서 수동으로 세분화 된 권한을 설정할 수 있으므로 Azure Data Lake Storage을 사용 해야 합니다. 또한 Azure Data Lake Storage 사용 하는 경우 ESP 클러스터는 클러스터 수준에서 Azure Active Directory을 사용 하 여 파일 시스템 액세스 제어 중 일부를 수행 합니다. 

Azure Storage 탐색기를 사용 하 여 사용자의 보안 그룹에 데이터 액세스 정책을 할당할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [Hive 또는 다른 서비스를 사용 하 여 Data Lake Storage Gen2에서 데이터를 쿼리할 수 있도록 Azure AD 사용자에 대 한 권한을 설정 어떻게 할까요??](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Azure Data Lake Storage Gen2와 함께 Azure Storage Explorer를 사용하여 파일 및 디렉터리 수준 사용 권한 설정](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>작업자 노드의 디스크 크기를 늘리지 않고 클러스터에서 HDFS 저장소를 늘릴 수 있나요?

아니요, 작업자 노드의 디스크 크기를 늘릴 수 없습니다. 따라서 디스크 크기를 늘리는 유일한 방법은 클러스터를 삭제 하 고 더 큰 작업자 Vm을 사용 하 여 다시 만드는 것입니다. 클러스터를 삭제 하는 경우 데이터가 삭제 되므로, HDInsight 데이터를 저장 하는 데 HDFS를 사용 하지 마세요. 대신 Azure에 데이터를 저장 합니다. 클러스터를 확장 하면 HDInsight 클러스터에 추가 용량이 추가 될 수도 있습니다.

## <a name="edge-nodes"></a>에지 노드

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>클러스터를 만든 후에 지 노드를 추가할 수 있나요?

클러스터를 만들 때 HDInsight 클러스터 또는 새 클러스터를 만들 수 있습니다. 자세한 내용은 [HDInsight의 Apache Hadoop 클러스터에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md)을 참조하세요.

### <a name="how-can-i-connect-to-an-edge-node"></a>에 지 노드에 연결 하려면 어떻게 해야 하나요?

에 지 노드를 만든 후에는 포트 22에서 SSH를 사용 하 여 연결할 수 있습니다. 클러스터 포털에서에 지 노드의 이름을 찾을 수 있습니다. 이름은 *일반적으로로 종료 됩니다.*

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>지속형 스크립트가 새로 생성 된에 지 노드에서 자동으로 실행 되지 않는 이유는 무엇 인가요?

지속형 스크립트를 사용 하 여 크기 조정 작업을 통해 클러스터에 추가 된 새 작업자 노드를 사용자 지정할 수 있습니다. 지속형 스크립트는에 지 노드에 적용 되지 않습니다.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>클러스터에서 Tez 쿼리 뷰를 끌어오기 위한 REST API 호출은 무엇 인가요?

다음 REST 엔드포인트을 사용 하 여 JSON 형식으로 필요한 정보를 가져올 수 있습니다. 기본 인증 헤더를 사용 하 여 요청을 만듭니다.

- Tez 쿼리 뷰: *https:\//\<클러스터 이름 >* . m s p s/m s/m a p/타임 라인/HIVE_QUERY_ID/
- Tez Dag View: *https:\//\<클러스터 이름 >. azurehdinsight/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Azure Active Directory 사용자를 사용 하 여 HDI 클러스터에서 구성 정보를 검색 어떻게 할까요??

AAD 사용자와 적절 한 인증 토큰을 협상 하려면 다음 형식을 사용 하 여 게이트웨이를 진행 합니다.

* https://`<cluster dnsname>`azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Ambari Restful API를 사용 하 여 YARN 성능을 모니터링할 어떻게 할까요? 있습니까?

동일한 가상 네트워크 또는 피어 링 가상 네트워크에서 말아 넘기기 명령을 호출 하는 경우 명령은 다음과 같습니다.

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
가상 네트워크 외부에서 또는 피어 링이 아닌 가상 네트워크에서 명령을 호출 하는 경우 명령 형식은 다음과 같습니다.

- 비 ESP 클러스터의 경우:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- ESP 클러스터의 경우:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> 말아가 암호를 묻는 메시지를 표시 합니다. 클러스터 로그인 사용자 이름에 대해 올바른 암호를 입력 해야 합니다.

## <a name="billing"></a>결제

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>HDInsight 클러스터를 배포 하는 비용은 얼마 인가요?

청구와 관련 된 가격 책정 및 FAQ에 대 한 자세한 내용은 [Azure HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/) 페이지를 참조 하세요.

### <a name="when-does-hdinsight-billing-start--stop"></a>HDInsight 청구 시작 & 중지 하는 시기는 언제 입니까?

클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 청구는 분당 등급이 지정 됩니다.

### <a name="how-do-i-cancel-my-subscription"></a>내 구독을 취소 어떻게 할까요? 시겠습니까?

구독을 취소 하는 방법에 대 한 자세한 내용은 [Azure 구독 취소](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)를 참조 하세요.

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>종 량 제 구독의 경우 구독을 취소 하면 어떻게 되나요?

구독이 취소 된 후 구독에 대 한 자세한 내용은 [구독을 취소 하면 어떻게 되나요?](/azure/billing/billing-how-to-cancel-azure-subscription) 를 참조 하세요.

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>HDInsight 3.6 클러스터를 실행 하는 경우에도 Hive 버전이 Ambari UI에서 2.1 대신 1.2.1000로 표시 되는 이유는 무엇 인가요?

Ambari UI에 1.2만 표시 되기는 하지만 HDInsight 3.6에는 Hive 1.2와 Hive 2.1이 모두 포함 되어 있습니다.

## <a name="other-faq"></a>기타 FAQ

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>HDInsight는 실시간 스트림 처리 기능을 기준으로 어떤 기능을 제공 하나요?

Azure HDInsight에서 스트림 처리의 통합 기능에 대 한 자세한 내용은 [azure에서 스트림 처리 기술 선택](/azure/architecture/data-guide/technology-choices/stream-processing)을 참조 하세요.

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>클러스터가 특정 기간 동안 유휴 상태일 때 클러스터의 헤드 노드를 동적으로 종료 하는 방법이 있나요?

HDInsight 클러스터에서는이 작업을 수행할 수 없습니다. 이러한 시나리오에 Azure Data Factory를 사용할 수 있습니다.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>HDInsight에서 제공 하는 규정 준수 제품은 무엇 인가요?

호환성에 대 한 자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/trust-center) 및 [Microsoft Azure 준수 개요](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)를 참조 하세요.
