---
title: 사용자에게 Ambari 보기에 대한 권한 부여 - Azure HDInsight
description: ESP가 사용되는 HDInsight 클러스터에 대한 Ambari 사용자 및 그룹 권한을 관리하는 방법을 설명합니다.
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 69ae1bd05b64912b3d53ca88b468a72a90ff5a74
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097039"
---
# <a name="authorize-users-for-apache-ambari-views"></a>사용자에게 Apache Ambari Views에 대한 권한 부여

[ESP(Enterprise Security Package) 사용 HDInsight 클러스터](./domain-joined/apache-domain-joined-introduction.md)는 Azure Active Directory 기반 인증을 비롯한 엔터프라이즈급 기능을 제공합니다. 클러스터에 대한 액세스를 제공 받은 Azure AD 그룹에 추가된 [새 사용자를 동기화](hdinsight-sync-aad-users-to-cluster.md)하여 특정 사용자가 특정 작업을 수행할 수 있게 할 수 있습니다. [Apache Ambari](https://ambari.apache.org/)의 사용자, 그룹, 권한 작업은 ESP HDInsight 클러스터와 표준 HDInsight 클러스터 모두에 대해 지원됩니다.

Active Directory 사용자는 자신의 도메인 자격 증명을 사용 하 여 클러스터 노드에 로그인 수 있습니다. 또한 자체 도메인 자격 증명을 사용하여 [Hue](https://gethue.com/), Ambari 보기, ODBC, JDBC, PowerShell, REST API 등의 승인된 다른 엔드포인트와 클러스터의 상호 작용을 인증할 수 있습니다.

> [!WARNING]  
> Linux 기반 HDInsight 클러스터에서 Ambari watchdog(hdinsightwatchdog)의 암호는 변경하지 마세요. 암호를 변경하면 스크립트 동작을 사용하거나 클러스터에서 크기 조정 작업을 수행하는 기능이 중단됩니다.

새 ESP 클러스터를 아직 프로비전하지 않은 경우 [다음 지침](./domain-joined/apache-domain-joined-configure.md)에 따라 프로비전하세요.

## <a name="access-the-ambari-management-page"></a>Ambari 관리 페이지 액세스

[Apache Ambari 웹 UI](hdinsight-hadoop-manage-ambari.md)에서 **Ambari 관리 페이지**로 이동하려면 **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** 으로 이동하세요. 클러스터를 만들 때 정의한 클러스터 관리자 사용자 이름 및 암호를 입력합니다. 다음으로, Ambari 대시보드의 **관리자** 메뉴 아래에서 **Ambari 관리**를 선택합니다

![Ambari 관리](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Apache Hive View에 대한 권한 부여

Ambari에는 [Apache Hive](https://hive.apache.org/) 및 [Apache TEZ](https://tez.apache.org/)에 대한 보기 인스턴스가 함께 제공됩니다. 하나 이상의 Hive 보기 인스턴스에 대한 액세스 권한을 부여하려면 **Ambari 관리 페이지**로 이동합니다.

1. 관리 페이지에서, 왼쪽에 있는 **Views** 메뉴 머리글 아래에서 **보기** 연결을 선택합니다.

    ![보기 연결](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. 보기 페이지에서 **HIVE** 행을 확장합니다. 클러스터에 Hive 서비스가 추가될 때 만들어진 기본 Hive 보기가 하나 있습니다. 필요한 만큼 Hive 보기 인스턴스를 더 만들 수 있습니다. Hive 보기를 선택합니다.

    ![보기 - Hive 보기](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. 보기 페이지의 맨 아래로 스크롤합니다. *권한* 섹션 아래를 보면 도메인 사용자에게 보기에 대한 권한을 부여하는 두 가지 옵션이 있습니다.

**다음 사용자에게 권한 부여** ![다음 사용자에게 권한 부여](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**다음 그룹에게 권한 부여** ![다음 그룹에게 권한 부여](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

1. 사용자를 추가하려면 **사용자 추가** 단추를 선택합니다.

   * 사용자 이름 입력을 시작하면 이전에 정의한 이름의 드롭다운 목록이 표시될 것입니다.

     ![사용자 자동 완성](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * 사용자 이름을 선택하거나 직접 입력합니다. 이 사용자 이름을 새 사용자로 추가하려면 **새로 만들기** 단추를 선택합니다.

   * 변경 내용을 저장하려면 **파란색 확인란**을 선택합니다.

     ![사용자 입력됨](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. 그룹을 추가하려면 **그룹 추가** 단추를 선택합니다.

   * 그룹 이름 입력을 시작합니다. 기존 그룹 이름을 선택하거나 새 그룹을 추가하는 프로세스는 사용자를 추가하는 프로세스와 똑같습니다.
   * 변경 내용을 저장하려면 **파란색 확인란**을 선택합니다.

     ![그룹 입력됨](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

보기를 사용할 사용자에게 권한을 할당하되, 해당 사용자를 추가 권한이 있는 그룹 구성원으로 할당하지 않으려면 보기에 사용자를 직접 추가하는 것이 좋습니다. 관리 오버헤드를 줄이려는 경우 그룹에 권한을 할당하는 것이 좀 더 간단할 수 있습니다.

## <a name="grant-permissions-to-apache-tez-views"></a>Apache TEZ 보기에 대한 권한 부여

사용자는 [Apache TEZ](https://tez.apache.org/) 보기 인스턴스를 사용하여 [Apache Hive](https://hive.apache.org/) 쿼리 및 [Apache Pig](https://pig.apache.org/) 스크립트를 통해 제출된 모든 Tez 작업을 모니터링하고 디버그할 수 있습니다. 클러스터가 프로비전될 때 만들어진 기본 Tez 보기 인스턴스 하나 있습니다.

Tez 보기 인스턴스에 사용자 및 그룹을 할당하려면 앞서 설명했듯이 보기 페이지에서 **TEZ** 행을 확장합니다.

![보기 - Tez 보기](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

사용자 또는 그룹을 추가하려면 이전 섹션의 3-5단계를 반복합니다.

## <a name="assign-users-to-roles"></a>역할에 사용자 할당

사용자 및 그룹에 대한 5가지 보안 역할이 있으며, 액세스 권한 내림차순으로 나열됩니다.

* 클러스터 관리자
* 클러스터 운영자
* 서비스 관리자
* 서비스 운영자
* 클러스터 사용자

역할을 관리하려면 **Ambari 관리 페이지**로 이동한 후 왼쪽에 있는 *클러스터* 메뉴 내에서 **역할** 연결을 선택합니다.

![역할 메뉴 연결](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

각 역할에 지정된 권한 목록을 보려면 역할 페이지의 **역할** 테이블 머리글 옆에 있는 파란색 물음표를 클릭합니다.

![역할 메뉴 연결](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

이 페이지에는 사용자 및 그룹에 대한 역할을 관리하는 데 사용할 수 있는 두 가지 보기가 있습니다. 블록 및 목록

### <a name="block-view"></a>블록 보기

블록 보기는 각 역할을 자체 행에 표시하며, 앞서 설명한 대로 **다음 사용자에게 역할 할당** 및 **다음 그룹에게 역할 할당** 옵션을 제공합니다.

![역할 블록 보기](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>목록 보기

목록 보기는 두 개의 범주에서 신속한 편집 기능을 제공합니다. 사용자 및 그룹

* 목록 보기의 사용자 범주에는 모든 사용자 목록이 표시되므로 드롭다운 목록에서 각 사용자의 역할을 선택할 수 있습니다.

    ![역할 목록 보기 - 사용자](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  목록 보기의 그룹 범주에는 모든 그룹 그리고 각 그룹에 할당된 역할이 표시됩니다. 이 예제에서는 클러스터 도메인 설정의 **사용자 그룹 액세스** 속성에 지정된 Microsoft Azure Active Directory 그룹에서 그룹 목록이 동기화됩니다. [ESP 사용 HDInsight 클러스터 만들기](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)를 참조하세요.

    ![역할 목록 보기 - 그룹](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    위의 이미지에서 "hiveusers" 그룹에는 *클러스터 사용자* 역할이 할당되어 있습니다. 이 역할은 해당 그룹의 사용자가 서비스 구성 및 클러스터 메트릭을 보는 것은 허용하지만 변경하는 것은 허용하지 않은 읽기 전용 역할입니다.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>보기 전용 사용자로 Ambari에 로그인

Microsoft Azure Active Directory 도메인 사용자 "hiveuser1" 권한을 Hive 및 Tez 보기에 할당했습니다. Ambari 웹 UI를 실행하고 이 사용자의 도메인 자격 증명(전자 메일 형식의 Microsoft Azure Active Directory 사용자 이름과 암호)을 입력하면 사용자가 Ambari 보기 페이지로 리디렉션됩니다. 여기서 사용자는 액세스 가능한 보기를 선택할 수 있습니다. 사용자는 대시보드, 서비스, 호스트, 경고 또는 관리 페이지를 포함하여 사이트의 다른 부분을 방문할 수 없습니다.

![보는 것만 가능한 사용자](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>클러스터 사용자로 Ambari에 로그인

Microsoft Azure Active Directory 도메인 사용자 "hiveuser2"를 *클러스터 사용자* 역할에 할당했습니다. 이 역할은 대시보드와 모든 메뉴 항목에 액세스할 수 있습니다. 클러스터 사용자에게 허용되는 옵션 수는 관리자보다 적습니다. 예를 들어 hiveuser2 사용자는 각 서비스의 구성을 볼 수 있지만 편집할 수는 없습니다.

![클러스터 사용자 역할이 있는 사용자](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>다음 단계

* [ESP가 포함된 HDInsight에서 Apache Hive 정책 구성](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight 클러스터 관리](./domain-joined/apache-domain-joined-manage.md)
* [HDInsight에서 Apache Hadoop과 Apache Hive View 사용](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [클러스터에 Azure AD 사용자 동기화](hdinsight-sync-aad-users-to-cluster.md)
