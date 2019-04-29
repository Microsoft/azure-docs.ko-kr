---
title: Azure Active Directory 사용자를 클러스터와 동기화 - Azure HDInsight
description: Azure Active Directory에서 인증된 사용자를 클러스터와 동기화합니다.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 2be67c604bebbe9b4c4356e241d1480ca0778d4a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764063"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory 사용자를 HDInsight 클러스터와 동기화

[ESP(Enterprise Security Package)가 포함된 HDInsight 클러스터](hdinsight-domain-joined-introduction.md)는 Azure AD(Azure Active Directory) 사용자에 대해 강력한 인증을 사용하고, RBAC(*역할 기반 액세스 제어*) 정책도 사용할 수 있습니다. Azure AD에 사용자 및 그룹을 추가하면 클러스터에 액세스해야 하는 사용자를 동기화할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

아직 하지 않은 경우 [Enterprise Security Package가 포함된 HDInsight 클러스터를 만듭니다](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>새 Azure AD 사용자 추가

호스트를 보려면 Ambari 웹 UI를 엽니다. 각 노드는 새 무인 업그레이드 설정으로 업데이트됩니다.

1. [Azure Portal](https://portal.azure.com)에서 ESP 클러스터와 연결된 Azure AD 디렉터리로 이동합니다.

2. 왼쪽 메뉴에서 **모든 사용자**를 선택한 후 **새 사용자**를 선택합니다.

    ![모든 사용자 창](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. 새 사용자 양식을 완료합니다. 클러스터 기반 사용 권한 할당을 위해 만든 그룹을 선택합니다. 이 예제에서는 새 사용자를 할당할 수 있는 "HiveUsers"라는 그룹을 만듭니다. ESP 클러스터를 만들기 위한 [예제 지침](hdinsight-domain-joined-configure.md)에는 2개의 그룹인 `HiveUsers` 및 `AAD DC Administrators`의 추가가 포함됩니다.

    ![새 사용자 창](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. **만들기**를 선택합니다.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Apache Ambari REST API를 사용하여 사용자 동기화

클러스터 생성 프로세스 동안 지정된 사용자 그룹은 해당 시간에 동기화됩니다. 사용자 동기화는 1시간마다 자동으로 발생합니다. 사용자를 즉시 동기화하거나 클러스터를 만드는 동안 지정된 그룹 이외의 그룹을 동기화하려면 Ambari REST API를 사용합니다.

다음 방법은 Ambari REST API에서 POST를 사용합니다. 자세한 내용은 [Apache Ambari REST API를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari-rest-api.md)를 참조하세요.

1. [SSH 사용하여 클러스터에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)합니다. Azure Portal의 클러스터에 대한 개요 창에서 **SSH(보안 셸)** 단추를 선택합니다.

    ![SSH(보안 셸)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. 표시되는 `ssh` 명령을 복사한 후 SSH 클라이언트에 붙여 넣습니다. ssh 사용자 암호를 입력하라고 요구되면 입력합니다.

3. 인증 후에 다음 명령을 입력합니다.

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    응답은 다음과 같아야 합니다.

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. 동기화 상태를 보려면 새 `curl` 명령을 실행합니다.

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    응답은 다음과 같아야 합니다.
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

5. 이 결과는 상태가 **완료**이고, 하나의 새 사용자가 생성되었으며 해당 사용자에게 구성원 자격이 할당되었음을 나타냅니다. 이 예제에서 해당 사용자는 Azure AD의 동일한 그룹에 추가되었으므로 "HiveUsers" 동기화 LDAP 그룹에 할당됩니다.

> [!NOTE]  
> 이전 방법은 클러스터 생성 동안 도메인 설정의 **사용자 그룹 액세스** 속성에 지정된 Azure AD 그룹만 동기화됩니다. 자세한 내용은 [HDInsight 클러스터 만들기](domain-joined/apache-domain-joined-configure.md)를 참조하세요.

## <a name="verify-the-newly-added-azure-ad-user"></a>새로 추가된 Azure AD 사용자 확인

[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)를 열어 새 Azure AD 사용자가 추가되었는지 확인합니다. **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** 으로 이동하여 Ambari 웹 UI에 액세스합니다. 클러스터 관리자 사용자 이름 및 암호를 입력합니다.

1. Ambari 대시보드의 **관리자** 메뉴 아래에서 **Ambari 관리**를 선택합니다

    ![Ambari 관리](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. 페이지 왼쪽의 **사용자 + 그룹 관리** 메뉴 그룹 아래에서 **사용자**를 선택합니다.

    ![사용자 메뉴 항목](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. 새 사용자가 사용자 테이블 내에 표시되어야 합니다. 유형은 `Local`이 아닌 `LDAP`로 설정됩니다.

    ![사용자 페이지](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>새 사용자 권한으로 Ambari에 로그인

새 사용자(또는 다른 도메인 사용자)는 Ambari에 로그인할 때 전체 Azure AD 사용자 이름 및 도메인 자격 증명을 사용합니다.  Ambari는 Azure AD에서 사용자의 표시 이름으로 사용되는 사용자 별칭을 표시합니다. 새로운 예제 사용자의 사용자 이름은 `hiveuser3@contoso.com`입니다. Ambari에서 이 새 사용자는 `hiveuser3`로 표시되지만 사용자는 Ambari에 `hiveuser3@contoso.com`으로 로그인합니다.

## <a name="see-also"></a>참고 항목

* [ESP가 포함된 HDInsight에서 Apache Hive 정책 구성](hdinsight-domain-joined-run-hive.md)
* [ESP가 포함된 HDInsight 클러스터 관리](hdinsight-domain-joined-manage.md)
* [사용자에게 Apache Ambari 권한 부여](hdinsight-authorize-users-to-ambari.md)
