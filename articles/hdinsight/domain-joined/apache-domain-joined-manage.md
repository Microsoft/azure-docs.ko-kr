---
title: Enterprise Security Package - Azure를 사용하여 HDInsight 클러스터 관리
description: Enterprise Security Package를 사용하여 HDInsight 클러스터를 관리하는 방법을 알아봅니다.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: 951bd74c67c77c944a17e41646c4fe49ef46b33f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128316"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Enterprise Security Package를 사용하여 HDInsight 클러스터 관리
HDInsight ESP(Enterprise Security Package)의 사용자 및 역할과 ESP 클러스터 관리 방법을 알아봅니다.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>VSCode를 사용하여 도메인 가입된 클러스터에 연결

Apache Ambari에서 관리하는 사용자 이름을 사용하여 정상적인 클러스터를 연결하고, 도메인 사용자 이름(예: user1@contoso.com)을 사용하여 보안 Apache Hadoop 클러스터를 연결할 수 있습니다.
1. **Ctrl+Shift+P**를 선택하여 명령 팔레트를 연 다음, **HDInsight: Link a cluster**를 입력합니다.

   ![클러스터 연결 명령](./media/apache-domain-joined-manage/link-cluster-command.png)

2. HDInsight 클러스터 URL 입력 -> 사용자 이름 입력 -> 암호 입력 -> 클러스터 유형 선택 -> 확인을 통과하면 성공 정보가 표시됩니다.
   
   ![클러스터 연결 대화 상자](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > 클러스터가 Azure 구독 및 연결된 클러스터 모두에 로그인되어 있으면, 연결된 사용자 이름 및 암호가 사용됩니다. 
   
3. **List cluster** 명령을 사용하여 연결된 클러스터를 볼 수 있습니다. 이제 연결된 클러스터에 스크립트를 제출할 수 있습니다.

   ![연결된 클러스터](./media/apache-domain-joined-manage/linked-cluster.png)

4. 명령 팔레트에서 **HDInsight: Unlink a Cluster**를 입력하여 클러스터의 연결을 끊을 수도 있습니다.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>IntelliJ를 사용하여 도메인 가입된 클러스터에 연결

Ambari에서 관리하는 사용자 이름을 사용하여 정상적인 클러스터를 연결할 수 있고 도메인 사용자 이름(예: user1@contoso.com)을 사용하여 보안 Hadoop 클러스터를 연결할 수 있습니다. 
1. **Azure Explorer**에서 **Link a cluster**(클러스터 연결)를 클릭합니다.

   ![클러스터 연결 상황에 맞는 메뉴](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. **클러스터 이름**, **사용자 이름** 및 **암호**를 입력합니다. 인증에 실패하면 사용자 이름과 암호를 확인해야 합니다. 필요에 따라 저장소 계정, 저장소 키를 추가한 다음, 저장소 컨테이너에서 컨테이너를 선택합니다. 저장소 정보는 왼쪽 트리의 저장소 탐색기용입니다.
   
   ![클러스터 연결 대화 상자](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > 클러스터가 Azure 구독 및 연결된 클러스터 모두에 로그인되어 있으면, 연결된 저장소 키, 사용자 이름 및 암호를 사용합니다.
   > ![IntelliJ의 저장소 탐색기](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. 입력 정보가 올바르면 연결된 클러스터가 **HDInsight** 노드에 표시됩니다. 이제 애플리케이션을 연결된 클러스터에 제출할 수 있습니다.

   ![연결된 클러스터](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. **Azure Explorer**에서 클러스터 연결을 해제할 수도 있습니다.
   
   ![연결되지 않은 클러스터](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Eclipse를 사용하여 도메인 가입된 클러스터에 연결

Ambari에서 관리하는 사용자 이름을 사용하여 정상적인 클러스터를 연결할 수 있고 도메인 사용자 이름(예: user1@contoso.com)을 사용하여 보안 Hadoop 클러스터를 연결할 수 있습니다.
1. **Azure Explorer**에서 **Link a cluster**(클러스터 연결)를 클릭합니다.

   ![클러스터 연결 상황에 맞는 메뉴](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. **클러스터 이름**, **사용자 이름** 및 **암호**를 입력한 다음, 확인 단추를 클릭하여 클러스터에 연결합니다. 선택적으로 저장소 계정, 저장소 키를 입력한 다음, 저장소 탐색기의 저장소 컨테이너를 선택하여 왼쪽 트리 뷰에서 작업합니다.
   
   ![클러스터 연결 대화 상자](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]  
   > 클러스터가 Azure 구독 및 연결된 클러스터 모두에 로그인되어 있으면, 연결된 저장소 키, 사용자 이름 및 암호를 사용합니다.
   > ![Eclipse의 저장소 탐색기](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. 입력 정보가 올바르면 확인 단추를 클릭한 후 **HDInsight** 노드에 연결된 클러스터가 표시됩니다. 이제 애플리케이션을 연결된 클러스터에 제출할 수 있습니다.

   ![연결된 클러스터](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. **Azure Explorer**에서 클러스터 연결을 해제할 수도 있습니다.
   
   ![연결되지 않은 클러스터](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>엔터프라이즈 보안 패키지를 사용하여 클러스터에 액세스합니다.

엔터프라이즈 보안 패키지(이전의 HDInsight Premium)은 클러스터에 대해 다중 사용자 액세스를 제공합니다. 여기서는 Active Directory에 의해 인증이 수행되고 Apache Ranger 및 Storage ACL(ADLS ACL)에 의해 권한 부여가 수행됩니다. 권한 부여는 여러 사용자 간에 보안 경계를 제공하고, 권한 부여 정책에 따라 권한 있는 사용자만 데이터에 액세스할 수 있도록 허용합니다.

보안 및 사용자 격리는 엔터프라이즈 보안 패키지를 사용하는 HDInsight 클러스터에 중요합니다. 이러한 요구 사항을 충족하기 위해 엔터프라이즈 보안 패키지를 사용하는 클러스터에 대한 SSH 액세스가 차단됩니다. 다음 표에서는 각 클러스터 유형에 대해 권장되는 액세스 방법을 보여 줍니다.

|워크로드|시나리오|액세스 방법|
|--------|--------|-------------|
|Apache Hadoop|Hive – 대화형 작업/쿼리  |<ul><li>[Beeline](#beeline)</li><li>[Hive 보기](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|대화형 작업/쿼리, PySpark 대화형|<ul><li>[Beeline](#beeline)</li><li>[Livy를 사용한 Zeppelin](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive 보기](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|일괄 처리 시나리오 – Spark 제출, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|대화형 쿼리(LLAP)|대화형|<ul><li>[Beeline](#beeline)</li><li>[Hive 보기](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|모두|사용자 지정 애플리케이션 설치|<ul><li>[스크립트 작업](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter는 Enterprise Security Package에 설치/지원되지 않습니다.

표준 API를 사용하면 보안 측면에서 도움이 됩니다. 또한 다음과 같은 이점도 얻을 수 있습니다.

1.  **관리** – 표준 API(Livy, HS2 등)를 사용하여 코드를 관리하고 작업을 자동화할 수 있습니다.
2.  **감사** – SSH를 사용하면 클러스터에 대해 SSH를 수행한 사용자를 감사할 방법이 없습니다. 작업이 사용자 컨텍스트에서 실행될 때처럼 표준 엔드포인트를 통해 생성되는 경우는 여기에 해당되지 않습니다. 



### <a name="beeline"></a>Beeline 사용 
컴퓨터에 Beeline을 설치하고 공용 인터넷을 통해 연결한 후 다음 매개 변수를 사용합니다. 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Beeline을 로컬로 설치했고 Azure Virtual Network를 통해 연결하는 경우 다음 매개 변수를 사용합니다. 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

헤드 노드의 정규화된 도메인 이름을 찾으려면 Ambari REST API를 사용하여 HDInsight 관리 문서의 정보를 사용합니다.














## <a name="users-of-hdinsight-clusters-with-esp"></a>ESP가 포함된 HDInsight 클러스터의 사용자
비ESP HDInsight 클러스터에는 클러스터를 만드는 중에 생성되는 두 개의 사용자 계정이 있습니다.

* **Ambari 관리자**: 이 계정을 *Hadoop 사용자* 또는 *HTTP 사용자*라고도 합니다. 이 계정은 https:// Ambari에 로그인 할 수&lt;clustername >. azurehdinsight.net. Ambari 뷰에서 쿼리를 실행 하 고, 외부 도구 (예: PowerShell, Templeton, Visual Studio)를 통해 작업을 실행 하 고, Hive ODBC 드라이버와 BI 도구 (예: Excel, Power BI 또는 Tableau)를 사용 하 여 인증 하려면 데도 수 있습니다.

ESP가 포함된 HDInsight 클러스터에는 Ambari 관리자 외에 세 명의 새로운 사용자가 있습니다.

* **Ranger 관리자**:  이 계정은 로컬 Apache Ranger 관리자 계정입니다. 이 계정은 Active Directory 도메인 사용자가 아닙니다. 이 계정은 정책을 설정하고 다른 사용자를 관리자 또는 위임된 관리자로 만드는 데(해당 사용자가 정책을 관리할 수 있도록) 사용할 수 있습니다. 기본적으로 사용자 이름은 *admin*이고 암호는 Ambari 관리자 암호와 동일합니다. 암호는 Ranger의 설정 페이지에서 업데이트할 수 있습니다.
* **클러스터 관리 도메인 사용자**: 이 계정은 Ambari 및 Ranger를 포함하여 Hadoop 클러스터 관리자로 지정된 Active Directory 도메인 사용자입니다. 클러스터를 만드는 동안 이 사용자의 자격 증명을 입력해야 합니다. 이 사용자는 다음과 같은 권한을 갖고 있습니다.

  * 컴퓨터를 도메인에 가입하고 클러스터를 만드는 동안 지정하는 OU 내에 배치합니다.
  * 클러스터를 만드는 동안 지정하는 OU 내에 서비스 사용자를 만듭니다.
  * 역방향 DNS 항목을 만듭니다.

    다른 AD 사용자도 이러한 권한이 있습니다.

    클러스터 내에는 Ranger를 통해 관리되지 않는 끝점(예: Templeton)이 있으며, 따라서 이러한 끝점은 안전하지 않습니다. 이러한 끝점은 클러스터 관리 도메인 사용자를 제외한 모든 사용자에게 잠겨 있습니다.
* **일반**: 클러스터를 만들 때 여러 Active Directory 그룹을 제공할 수 있습니다. 이러한 그룹의 사용자는 Ranger 및 Ambari와 동기화됩니다. 이러한 사용자는 도메인 사용자이며 Ranger를 통해 관리되는 엔드포인트(예: Hiveserver2)에만 액세스할 수 있습니다. 이러한 사용자에게는 모든 RBAC 정책 및 감사가 적용됩니다.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>ESP가 포함된 HDInsight 클러스터의 역할
HDInsight Enterprise Security Package에는 다음과 같은 역할이 있습니다.

* 클러스터 관리자
* 클러스터 운영자
* 서비스 관리자
* 서비스 운영자
* 클러스터 사용자

**이러한 역할의 사용 권한을 보려면**

1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **역할**을 클릭합니다.
3. 파란색 물음표를 클릭하여 사용 권한을 살펴봅니다.

    ![ESP HDInsight 역할 사용 권한](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Ambari 관리 UI 열기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. HDInsight 클러스터를 엽니다.
3. 위쪽 메뉴에서 **대시보드** 를 클릭하여 Ambari를 엽니다.
4. 클러스터 관리자 도메인 사용자 이름 및 암호를 사용 하 여 Ambari에 로그인 합니다.
5. 오른쪽 상단 모서리에서 **관리자** 드롭다운 메뉴를 클릭한 다음 **Ambari 관리**를 클릭합니다.

    ![ESP HDInsight Ambari 관리](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    UI는 다음과 같습니다.

    ![ESP HDInsight Ambari 관리 UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Active Directory에서 동기화된 도메인 사용자 나열
1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **사용자**를 클릭합니다. Active Directory에서 HDInsight 클러스터로 동기화된 모든 사용자가 표시됩니다.

    ![ESP HDInsight Ambari 관리 UI 사용자 나열](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Active Directory에서 동기화된 도메인 그룹 나열
1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **그룹**을 클릭합니다. Active Directory에서 HDInsight 클러스터로 동기화된 모든 그룹이 표시됩니다.

    ![ESP HDInsight Ambari 관리 UI 그룹 나열](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Hive 뷰 사용 권한 구성
1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **뷰**를 클릭합니다.
3. **HIVE**를 클릭하여 세부 정보를 표시합니다.

    ![ESP HDInsight Ambari 관리 UI Hive 뷰](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. **Hive 뷰**를 클릭하여 Hive 뷰를 구성합니다.
5. 아래에 있는 **사용 권한** 섹션으로 스크롤합니다.

    ![ESP HDInsight Ambari 관리 UI Hive 뷰 사용 권한 구성](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. **사용자 추가** 또는 **그룹 추가**를 클릭한 다음 Hive 뷰를 사용할 수 있는 사용자 또는 그룹을 지정합니다.

## <a name="configure-users-for-the-roles"></a>역할에 대해 사용자 구성
 역할 및 해당 사용 권한의 목록을 보려면 ESP가 포함된 HDInsight 클러스터의 역할을 참조하세요.

1. Ambari 관리 UI를 엽니다.  [Ambari 관리 UI 열기](#open-the-ambari-management-ui)를 참조하세요.
2. 왼쪽 메뉴에서 **역할**을 클릭합니다.
3. **사용자 추가** 또는 **그룹 추가**를 클릭하여 여러 역할에 사용자 및 그룹을 할당합니다.

## <a name="next-steps"></a>다음 단계
* Enterprise Security Package가 포함된 HDInsight 클러스터 구성에 대한 내용은 [ESP가 포함된 HDInsight 클러스터 구성](apache-domain-joined-configure.md)을 참조하세요.
* Hive 정책 구성 및 Hive 쿼리 실행에 대한 내용은 [ESP가 포함된 HDInsight 클러스터에 대한 Apache Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
