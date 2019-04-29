---
title: Enterprise Security Package를 사용하여 Apache Oozie 워크플로 보안 유지 - Azure HDInsight
description: Azure HDInsight Enterprise Security Package를 사용하여 Apache Oozie 워크플로 보안을 유지합니다. 또한 Oozie 워크플로를 정의하고 Oozie 작업을 제출하는 방법에 대해서도 살펴봅니다.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 7d7fbf5d72654c26edf09ab27f024eaf39f8c387
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112896"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Enterprise Security Package가 포함된 HDInsight Hadoop 클러스터에서 Apache Oozie 실행

Apache Oozie는 Apache Hadoop 작업을 관리하는 워크플로 및 코디네이션 시스템입니다. Oozie는 Hadoop 스택과 통합되며 다음 작업을 지원합니다.
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Oozie를 사용하여 Java 프로그램이나 셸 스크립트와 같은 시스템에 특정한 작업을 예약할 수도 있습니다.

## <a name="prerequisite"></a>필수 요소

- ESP(Enterprise Security Package)가 포함된 Azure HDInsight Hadoop 클러스터입니다. [ESP가 포함된 HDInsight 클러스터 구성](./apache-domain-joined-configure-using-azure-adds.md)을 참조하세요.

    > [!NOTE]  
    > ESP가 아닌 클러스터에서 Oozie 사용에 관한 자세한 지침은 [Linux 기반 Azure HDInsight에서 Apache Oozie 워크플로 사용](../hdinsight-use-oozie-linux-mac.md)을 참조하세요.

## <a name="connect-to-an-esp-cluster"></a>ESP 클러스터에 연결

SSH(Secure Shell)에 대한 자세한 내용은 [SSH를 사용하여 HDInsight(Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.  
   ```bash
   ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
   ```

2. Kerberos 인증에 성공했는지 확인하려면 `klist` 명령을 사용합니다. 그렇지 않은 경우 `kinit`를 사용하여 Kerberos 인증을 시작합니다.

3. HDInsight 게이트웨이에 로그인하여 Azure Data Lake Storage에 액세스하는 데 필요한 OAuth 토큰을 등록합니다.   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    **200 정상** 상태 응답 코드는 성공적인 등록을 나타냅니다. 인증되지 않은 응답이 수신되면(예: 401) 사용자 이름과 암호를 확인하세요.

## <a name="define-the-workflow"></a>워크플로 정의
Oozie 워크플로 정의는 hPDL(Apache Hadoop 프로세스 정의 언어)로 작성되었습니다. hPDL은 XML 프로세스 정의 언어입니다. 다음 단계를 사용하여 워크플로를 정의합니다.

1. 도메인 사용자의 작업 영역을 설정합니다.
   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```
   `DomainUser`를 도메인 사용자 이름으로 바꿉니다. 
   `DomainUserPath`를 도메인 사용자의 홈 디렉터리 경로로 바꿉니다. 
   `ClusterVersion`을 클러스터 HDP(Hortonworks Data Platform) 버전으로 바꿉니다.

2. 다음 문을 사용하여 새 파일을 만들고 편집합니다.
   ```bash
   nano workflow.xml
   ```

3. nano 편집기가 열리면 파일 내용으로 다음 XML을 입력합니다.
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
   ```
4. `clustername`을 클러스터의 이름으로 바꿉니다. 

5. 파일을 저장하려면 Ctrl + X를 선택합니다. [https://slack.botframework.com](`Y`) 을 입력합니다. 그런 다음, **Enter** 키를 선택합니다.

    워크플로는 두 부분으로 구분됩니다.
   * **자격 증명 섹션.** 이 섹션에서는 Oozie 작업을 인증하는 데 사용되는 자격 증명을 사용합니다.

     이 예제에서는 Hive 작업에 대한 인증이 사용됩니다. 자세한 내용은 [작업 인증](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html)을 참조하세요.

     자격 증명 서비스를 사용하면 Oozie 작업이 Hadoop 서비스에 액세스하기 위해 사용자를 가장할 수 있습니다.

   * **작업 섹션.** 이 섹션에서는 map-reduce, Hive 서버 2 및 Hive 서버 1의 세 가지 작업이 있습니다.

     - map-reduce 작업은 집계된 단어 수를 출력하는 map-reduce에 대한 Oozie 패키지의 예제를 실행합니다.

     - Hive 서버 2 및 Hive 서버 1 작업은 HDInsight와 함께 제공되는 샘플 Hive 테이블에 대한 쿼리를 실행합니다.

     Hive 작업은 action 요소에서 `cred` 키워드를 사용하여 인증을 위해 자격 증명 섹션에 정의된 자격 증명을 사용합니다.

6. 다음 명령을 사용하여 `workflow.xml` 파일을 `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`에 복사합니다.
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. `domainuser`를 도메인에 대한 사용자 이름으로 바꿉니다.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Oozie 작업에 대한 속성 파일 정의

1. 다음 명령문을 사용하여 작업 속성에 대한 새 파일을 만들고 편집합니다.

   ```bash
   nano job.properties
   ```

2. nano 편집기가 열리면 파일 내용으로 다음 XML을 사용합니다.

   ```bash
       nameNode=adl://home
       jobTracker=headnodehost:8050
       queueName=default
       examplesRoot=examples
       oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
       hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
       hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
       oozie.use.system.libpath=true
       user.name=[domainuser]
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   * Azure Data Lake Storage Gen1을 기본 클러스터 스토리지로 사용하는 경우 `nameNode` 속성에 대해 `adl://home` URI를 사용합니다. Azure Blob Storage를 사용하는 경우 이 값을 `wasb://home`으로 변경합니다. Azure Data Lake Storage Gen2를 사용하는 경우에는 `abfs://home`으로 변경합니다.
   * `domainuser`를 도메인에 대한 사용자 이름으로 바꿉니다.  
   * `ClusterShortName`을 클러스터의 약식 이름으로 바꿉니다. 예를 들어 클러스터 이름이 https:// *[예제 링크]* sechadoopcontoso.azurehdisnight.net인 경우 `clustershortname`은 클러스터의 첫 6자인 **sechad**입니다.  
   * `jdbcurlvalue`를 Hive 구성의 JDBC URL로 바꿉니다. 예를 들어 jdbc:hive2://headnodehost:10001/;transportMode=http입니다.      
   * 파일을 저장하려면 Ctrl+X를 선택하고 `Y`를 입력한 후 **Enter** 키를 누릅니다.

   이 속성 파일은 Oozie 작업을 실행할 때 로컬에 존재해야 합니다.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Oozie 작업에 대한 사용자 지정 Hive 스크립트 만들기

다음 섹션에 나와 있는 것처럼 Hive 서버 1 및 Hive 서버 2에 대해 두 개의 Hive 스크립트를 만들 수 있습니다.

### <a name="hive-server-1-file"></a>Hive 서버 1 파일

1.  Hive 서버 1 작업에 대한 파일을 만들고 편집합니다.
    ```bash
    nano countrowshive1.hql
    ```

2.  스크립트를 만듭니다.
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  파일을 HDFS(Apache Hadoop 분산 파일 시스템)에 저장합니다.
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive 서버 2 파일

1.  Hive 서버 2 작업에 대한 파일을 만들고 편집합니다.
    ```bash
    nano countrowshive2.hql
    ```

2.  스크립트를 만듭니다.
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  파일을 HDFS에 저장합니다.
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Oozie 작업 제출

ESP 클러스터에 대한 Oozie 작업을 제출하는 것은 ESP가 아닌 클러스터에서 Oozie 작업을 제출하는 것과 유사합니다.

자세한 내용은 [Apache Hadoop과 함께 Apache Oozie를 사용하여 Linux 기반 Azure HDInsight에서 워크플로 정의 및 실행](../hdinsight-use-oozie-linux-mac.md)을 참조하세요.

## <a name="results-from-an-oozie-job-submission"></a>Oozie 작업 제출 결과
Oozie 작업은 사용자에 대해 실행됩니다. 따라서 Apache Hadoop YARN 및 Apache Ranger 감사 로그는 모두 가장된 사용자로 실행 중인 작업을 표시합니다. Oozie 작업의 명령줄 인터페이스 출력은 다음 코드와 유사합니다.



```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

Hive 서버 2 작업에 대한 Ranger 감사 로그는 Oozie가 사용자에 대한 작업을 실행 중임을 보여줍니다. Ranger 및 YARN 보기는 클러스터 관리자에게만 표시됩니다.

## <a name="configure-user-authorization-in-oozie"></a>Oozie에서 사용자 권한 부여 구성

Oozie는 자체적으로 사용자 권한 부여 구성을 포함하여, 사용자가 다른 사용자의 작업을 중지하거나 종료하지 못하도록 할 수 있습니다. 이 구성을 사용하도록 설정하려면 `oozie.service.AuthorizationService.security.enabled`를 `true`로 설정합니다. 

자세한 내용은 [Apache Oozie 설치 및 구성](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html)을 참조하세요.

Hive 서버 1과 같은 구성 요소(여기서 Ranger 플러그 인은 사용 불가/지원되지 않음)의 경우 세분화되지 않은 HDFS 권한 부여만 가능합니다. 세분화된 권한 부여는 Ranger 플러그 인을 통해서만 사용할 수 있습니다.

## <a name="get-the-oozie-web-ui"></a>Oozie 웹 UI 가져오기

Oozie 웹 UI는 클러스터의 Oozie 작업 상태에 대한 웹 기반 보기를 제공합니다. 웹 UI를 가져오려면 ESP 클러스터에서 다음 단계를 수행합니다.

1. [에지 노드](../hdinsight-apps-use-edge-node.md)를 추가하고 [SSH Kerberos 인증](../hdinsight-hadoop-linux-use-ssh-unix.md)을 사용하도록 설정합니다.

2. [Oozie 웹 UI](../hdinsight-use-oozie-linux-mac.md) 단계에 따라 에지 노드로의 SSH 터널링을 사용하고 웹 UI에 액세스합니다.

## <a name="next-steps"></a>다음 단계
* [Apache Hadoop과 함께 Apache Oozie를 사용하여 Linux 기반 Azure HDInsight에서 워크플로를 정의 및 실행합니다](../hdinsight-use-oozie-linux-mac.md).
* [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결합니다](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
