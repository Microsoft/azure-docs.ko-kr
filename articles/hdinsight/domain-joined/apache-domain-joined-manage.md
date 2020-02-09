---
title: Enterprise Security Package 클러스터 관리-Azure HDInsight
description: Enterprise Security Package를 사용 하 여 Azure HDInsight 클러스터를 관리 하는 방법을 알아봅니다.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435923"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Enterprise Security Package를 사용하여 HDInsight 클러스터 관리

HDInsight ESP(Enterprise Security Package)의 사용자 및 역할과 ESP 클러스터 관리 방법을 알아봅니다.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>VSCode를 사용하여 도메인 가입된 클러스터에 연결

Apache Ambari에서 관리하는 사용자 이름을 사용하여 정상적인 클러스터를 연결하고, 도메인 사용자 이름(예: `user1@contoso.com`)을 사용하여 보안 Apache Hadoop 클러스터를 연결할 수 있습니다.

1. [Visual Studio Code](https://code.visualstudio.com/)를 사용하여 ASP.NET 5 API 앱을 만드는 방법을 보여줍니다. [Spark & Hive 도구](../hdinsight-for-vscode.md) 확장이 설치 되어 있는지 확인 합니다.

1. Visual Studio Code에 대 한 [클러스터 연결](../hdinsight-for-vscode.md#link-a-cluster) 의 단계를 따릅니다.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>IntelliJ를 사용하여 도메인 가입된 클러스터에 연결

Ambari에서 관리하는 사용자 이름을 사용하여 정상적인 클러스터를 연결할 수 있고 도메인 사용자 이름(예: `user1@contoso.com`)을 사용하여 보안 Hadoop 클러스터를 연결할 수 있습니다.

1. IntelliJ IDEA를 엽니다. 모든 [필수 구성 요소가](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) 충족 되는지 확인 합니다.

1. IntelliJ에 대 한 [클러스터 연결](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) 의 단계를 따릅니다.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Eclipse를 사용하여 도메인 가입된 클러스터에 연결

Ambari에서 관리하는 사용자 이름을 사용하여 정상적인 클러스터를 연결할 수 있고 도메인 사용자 이름(예: `user1@contoso.com`)을 사용하여 보안 Hadoop 클러스터를 연결할 수 있습니다.

1. Eclipse를 엽니다. 모든 [필수 구성 요소가](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) 충족 되는지 확인 합니다.

1. Eclipse 용 [클러스터 연결](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) 의 단계를 따릅니다.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Enterprise Security Package를 사용 하 여 클러스터에 액세스

엔터프라이즈 보안 패키지(이전의 HDInsight Premium)은 클러스터에 대해 다중 사용자 액세스를 제공합니다. 여기서는 Active Directory에 의해 인증이 수행되고 Apache Ranger 및 Storage ACL(ADLS ACL)에 의해 권한 부여가 수행됩니다. 권한 부여는 여러 사용자 간에 보안 경계를 제공하고, 권한 부여 정책에 따라 권한 있는 사용자만 데이터에 액세스할 수 있도록 허용합니다.

보안 및 사용자 격리는 엔터프라이즈 보안 패키지를 사용하는 HDInsight 클러스터에 중요합니다. 이러한 요구 사항을 충족하기 위해 엔터프라이즈 보안 패키지를 사용하는 클러스터에 대한 SSH 액세스가 차단됩니다. 다음 표에서는 각 클러스터 유형에 대해 권장되는 액세스 방법을 보여 줍니다.

|작업|시나리오|액세스 방법|
|--------|--------|-------------|
|Apache Hadoop|Hive – 대화형 작업/쿼리  |<ul><li>[Beeline](#beeline)</li><li>[Hive 보기](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|대화형 작업/쿼리, PySpark 대화형|<ul><li>[Beeline](#beeline)</li><li>[Livy를 사용한 Zeppelin](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive 보기](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|일괄 처리 시나리오 – Spark 제출, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|대화형 쿼리(LLAP)|대화형|<ul><li>[Beeline](#beeline)</li><li>[Hive 보기](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|모두|사용자 지정 애플리케이션 설치|<ul><li>[스크립트 작업](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter는 Enterprise Security Package에 설치/지원되지 않습니다.

표준 API를 사용하면 보안 측면에서 도움이 됩니다. 또한 다음과 같은 이점을 얻을 수 있습니다.

- **관리** – 표준 API(Livy, HS2 등)를 사용하여 코드를 관리하고 작업을 자동화할 수 있습니다.
- **Audit** – ssh를 사용 하 여 사용자가 클러스터에 대해 ssh를 할 수 있는 사용자를 감사할 수 있는 방법이 없습니다. 작업이 사용자 컨텍스트에서 실행될 때처럼 표준 엔드포인트를 통해 생성되는 경우는 여기에 해당되지 않습니다.

### <a name="beeline"></a>Beeline 사용

컴퓨터에 Beeline을 설치하고 공용 인터넷을 통해 연결한 후 다음 매개 변수를 사용합니다.

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Beeline을 로컬로 설치했고 Azure Virtual Network를 통해 연결하는 경우 다음 매개 변수를 사용합니다.

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

헤드 노드의 정규화된 도메인 이름을 찾으려면 Ambari REST API를 사용하여 HDInsight 관리 문서의 정보를 사용합니다.

## <a name="users-of-hdinsight-clusters-with-esp"></a>ESP가 포함된 HDInsight 클러스터의 사용자

비ESP HDInsight 클러스터에는 클러스터를 만드는 중에 생성되는 두 개의 사용자 계정이 있습니다.

- **Ambari 관리자**: 이 계정을 *Hadoop 사용자* 또는 *HTTP 사용자*라고도 합니다. 이 계정은 `https://CLUSTERNAME.azurehdinsight.net`에서 Ambari에 로그인 하는 데 사용할 수 있습니다. 또한 Ambari 뷰에서 쿼리를 실행 하 고, 외부 도구 (예: PowerShell, Templeton, Visual Studio)를 통해 작업을 실행 하 고, Hive ODBC 드라이버와 BI 도구 (예: Excel, Power BI 또는 Tableau)를 사용 하 여 인증 하는 데 사용할 수 있습니다.

ESP가 포함된 HDInsight 클러스터에는 Ambari 관리자 외에 세 명의 새로운 사용자가 있습니다.

- **Ranger 관리자**: 이 계정은 로컬 Apache Ranger 관리자 계정입니다. Active directory 도메인 사용자가 아닙니다. 이 계정은 정책을 설정하고 다른 사용자를 관리자 또는 위임된 관리자로 만드는 데(해당 사용자가 정책을 관리할 수 있도록) 사용할 수 있습니다. 기본적으로 사용자 이름은 *admin*이고 암호는 Ambari 관리자 암호와 동일합니다. 암호는 Ranger의 설정 페이지에서 업데이트할 수 있습니다.

- **클러스터 관리 도메인 사용자**: 이 계정은 Ambari 및 Ranger를 포함하여 Hadoop 클러스터 관리자로 지정된 Active Directory 도메인 사용자입니다. 클러스터를 만드는 동안 이 사용자의 자격 증명을 입력해야 합니다. 이 사용자는 다음과 같은 권한을 갖고 있습니다.
    - 컴퓨터를 도메인에 가입하고 클러스터를 만드는 동안 지정하는 OU 내에 배치합니다.
    - 클러스터를 만드는 동안 지정하는 OU 내에 서비스 사용자를 만듭니다.
    - 역방향 DNS 항목을 만듭니다.

    다른 AD 사용자도 이러한 권한이 있습니다.

    클러스터 내의 엔드포인트 (예: Templeton)이 레인저에서 관리 되지 않으므로 안전 하지 않습니다. 이러한 엔드포인트은 클러스터 관리 도메인 사용자를 제외한 모든 사용자에게 잠겨 있습니다.

- **일반**: 클러스터를 만들 때 여러 Active Directory 그룹을 제공할 수 있습니다. 이러한 그룹의 사용자는 Ranger 및 Ambari와 동기화됩니다. 이러한 사용자는 도메인 사용자이며 Ranger를 통해 관리되는 엔드포인트(예: Hiveserver2)에만 액세스할 수 있습니다. 이러한 사용자에게는 모든 RBAC 정책 및 감사가 적용됩니다.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>ESP가 포함된 HDInsight 클러스터의 역할

HDInsight Enterprise Security Package에는 다음과 같은 역할이 있습니다.

- 클러스터 관리자
- 클러스터 운영자
- 서비스 관리자
- 서비스 운영자
- 클러스터 사용자

**이러한 역할의 사용 권한을 보려면**

1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **역할**을 선택 합니다.
3. 사용 권한을 보려면 파란색 물음표를 선택 합니다.

    ![ESP HDInsight 역할 사용 권한](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Ambari 관리 UI 열기

1. `https://CLUSTERNAME.azurehdinsight.net/`로 이동 합니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.
1. 클러스터 관리자 도메인 사용자 이름 및 암호를 사용 하 여 Ambari에 로그인 합니다.
1. 오른쪽 위 모서리에서 **관리** 드롭다운 메뉴를 선택한 다음 **Ambari 관리**를 선택 합니다.

    ![ESP HDInsight의 Apache Ambari 관리](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    UI는 다음과 같습니다.

    ![ESP HDInsight Apache Ambari 관리 UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Active Directory에서 동기화된 도메인 사용자 나열

1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **사용자**를 선택 합니다. Active Directory에서 HDInsight 클러스터로 동기화된 모든 사용자가 표시됩니다.

    ![ESP HDInsight Ambari 관리 UI 사용자 나열](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Active Directory에서 동기화된 도메인 그룹 나열

1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **그룹**을 선택 합니다. Active Directory에서 HDInsight 클러스터로 동기화된 모든 그룹이 표시됩니다.

    ![ESP HDInsight Ambari 관리 UI 그룹 나열](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Hive 뷰 사용 권한 구성

1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **보기**를 선택 합니다.
3. **HIVE** 를 선택 하 여 세부 정보를 표시 합니다.

    ![ESP HDInsight Ambari 관리 UI Hive 뷰](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Hive 보기를 구성 하려면 **Hive 보기** 링크를 선택 합니다.
5. 아래에 있는 **사용 권한** 섹션으로 스크롤합니다.

    ![ESP HDInsight Ambari 관리 UI Hive 뷰 사용 권한 구성](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. **사용자 추가** 또는 **그룹 추가**를 선택 하 고 Hive 보기를 사용할 수 있는 사용자 또는 그룹을 지정 합니다.

## <a name="configure-users-for-the-roles"></a>역할에 대해 사용자 구성

 역할 및 해당 사용 권한의 목록을 보려면 ESP가 포함된 HDInsight 클러스터의 역할을 참조하세요.

1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **역할**을 선택 합니다.
3. **사용자 추가** 또는 **그룹 추가** 를 선택 하 여 사용자 및 그룹을 다른 역할에 할당 합니다.

## <a name="next-steps"></a>다음 단계

- Enterprise Security Package가 포함된 HDInsight 클러스터 구성에 대한 내용은 [ESP가 포함된 HDInsight 클러스터 구성](apache-domain-joined-configure.md)을 참조하세요.
- Hive 정책 구성 및 Hive 쿼리 실행에 대한 내용은 [ESP가 포함된 HDInsight 클러스터에 대한 Apache Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
