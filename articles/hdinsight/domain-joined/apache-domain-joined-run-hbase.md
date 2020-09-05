---
title: Apache HBase 및 Enterprise Security Package - Azure HDInsight
description: 자습서 - Enterprise Security Package를 사용하여 Azure HDInsight에서 HBase용 Apache Ranger 정책을 구성하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 89e9faeb3c67d0fd0c57adea3a3f69ec5438e3a0
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "73044655"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>자습서: Enterprise Security Package를 사용하여 HDInsight에서 Apache HBase 정책 구성

ESP(Enterprise Security Package) Apache HBase 클러스터용 Apache Ranger 정책을 구성하는 방법을 알아봅니다. ESP 클러스터는 도메인에 연결되므로 사용자가 도메인 자격 증명을 사용하여 인증할 수 있습니다. 이 자습서에서는 HBase 테이블의 여러 열 패밀리에 대한 액세스를 제한하는 Range 정책 두 개를 만듭니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 도메인 사용자 만들기
> * Ranger 정책 만들기
> * HBase 클러스터에 테이블 만들기
> * Ranger 정책 테스트

## <a name="before-you-begin"></a>시작하기 전에

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* [Azure Portal](https://portal.azure.com/)에 로그인합니다.

* [Enterprise Security Package를 사용하여 HDInsight HBase 클러스터](apache-domain-joined-configure-using-azure-adds.md)를 만듭니다.

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger 관리 UI에 연결

1. 브라우저에서 `https://<ClusterName>.azurehdinsight.net/Ranger/` URL을 사용하여 Range 관리 사용자 인터페이스에 연결합니다. `<ClusterName>`은 HBase 클러스터 이름으로 변경해야 합니다.

    > [!NOTE]  
    > Ranger 자격 증명은 Hadoop 클러스터 자격 증명과는 다릅니다. 브라우저가 캐시된 Hadoop 자격 증명을 사용하지 않도록 하려면 새 InPrivate 브라우저 창을 사용하여 Ranger 관리 UI에 연결합니다.

2. Azure AD(Active Directory) 관리자 자격 증명을 사용하여 로그인합니다. Azure AD 관리자 자격 증명은 HDInsight 클러스터 자격 증명 또는 Linux HDInsight 노드 SSH 자격 증명과는 다릅니다.

## <a name="create-domain-users"></a>도메인 사용자 만들기

[Enterprise Security Package를 사용하여 HDInsight 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)를 방문하여 **sales_user1** 및 **marketing_user1** 도메인 사용자를 만드는 방법을 확인합니다. 프로덕션 시나리오에서는 Active Directory 테 넌트에서 도메인 사용자가 제공됩니다.

## <a name="create-hbase-tables-and-import-sample-data"></a>HBase 테이블을 만들고 샘플 데이터 가져오기

SSH를 사용하여 HBase 클러스터를 연결하고 [Apache HBase 셸](https://hbase.apache.org/0.94/book/shell.html)을 사용하여 HBase 테이블을 만들고 데이터 및 쿼리 데이터를 삽입할 수 있습니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

### <a name="to-use-the-hbase-shell"></a>HBase 셸을 사용하려면

1. SSH에서 다음 HBase 명령을 실행합니다.
   
    ```bash
    hbase shell
    ```

2. `Name` 및 `Contact`라는 두 열 패밀리가 있는 HBase 테이블 `Customers`를 만듭니다.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. 일부 데이터 삽입:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. 테이블의 콘텐츠를 봅니다.
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![HDInsight Hadoop HBase 셸 출력](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Ranger 정책 만들기

**sales_user1** 및 **marketing_user1** 사용자에 대해 Ranger 정책을 만듭니다.

1. **Ranger 관리 UI**를 엽니다. **HBase** 아래의 **\<ClusterName>_hbase**를 클릭합니다.

   ![HDInsight Apache Ranger 관리 UI](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. **정책 목록** 화면에 이 클러스터에 대해 생성된 모든 Ranger 정책이 표시됩니다. 미리 구성된 정책 하나가 나열될 수 있습니다. **새 정책 추가**를 클릭합니다.

    ![Apache Ranger HBase 정책 목록](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. **정책 만들기** 화면에서 다음 값을 입력합니다.

   |**설정**  |**제안 값**  |
   |---------|---------|
   |정책 이름  |  sales_customers_name_contact   |
   |HBase 테이블   |  고객 |
   |HBase 열 패밀리   |  이름, 연락처 |
   |HBase 열   |  * |
   |그룹 선택  | |
   |사용자 선택  | sales_user1 |
   |사용 권한  | 읽기 |

   항목 이름에 다음 와일드카드를 포함할 수 있습니다.

   * `*`는 문자가 0개 이상 나옴을 나타냅니다.
   * `?`는 문자 하나를 나타냅니다.

   ![Apache Ranger 정책 sales 만들기](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >**사용자 선택**에 도메인 사용자가 자동으로 입력되지 않으면 Ranger가 Azure AD와 동기화될 때까지 잠시 기다립니다.

4. **추가** 를 클릭하여 정책을 저장합니다.

5. **새 정책 추가**를 클릭하고 다음 값을 입력합니다.

   |**설정**  |**제안 값**  |
   |---------|---------|
   |정책 이름  |  marketing_customers_contact   |
   |HBase 테이블   |  고객 |
   |HBase 열 패밀리   |  연락처 |
   |HBase 열   |  * |
   |그룹 선택  | |
   |사용자 선택  | marketing_user1 |
   |사용 권한  | 읽기 |

   ![Apache Ranger 정책 만들기 marketing](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. **추가** 를 클릭하여 정책을 저장합니다.

## <a name="test-the-ranger-policies"></a>Ranger 정책 테스트

구성된 Ranger 정책에 따라 **sales_user1**은 `Name` 및 `Contact` 열 패밀리의 열에 대한 모든 데이터를 볼 수 있습니다. **marketing_user1**은 `Contact` 열 패밀리의 데이터만 볼 수 있습니다.

### <a name="access-data-as-sales_user1"></a>sales_user1로 데이터에 액세스

1. 클러스터에 대한 새 SSH 연결을 엽니다. 다음 명령을 사용하여 클러스터에 로그인합니다.

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. kinit 명령을 사용하여 원하는 사용자의 컨텍스트를 변경합니다.

   ```bash
   kinit sales_user1
   ```

2. HBase 셸을 열고 `Customers` 테이블을 검사합니다.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. sales user는 `Name` 열 패밀리의 2개 열과 `Contact` 열 패밀리의 5개 열을 포함한 `Customers` 테이블의 모든 열을 볼 수 있습니다.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketing_user1"></a>marketing_user1로 데이터에 액세스

1. 클러스터에 대한 새 SSH 연결을 엽니다. 다음 명령을 사용하여 **marketing_user1**로 로그인합니다.

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. kinit 명령을 사용하여 원하는 사용자의 컨텍스트 변경

   ```bash
   kinit marketing_user1
   ```

1. HBase 셸을 열고 `Customers` 테이블을 검사합니다.

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. marketing user는 `Contact` 열 패밀리의 5개 열만 볼 수 있습니다.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

1. Ranger UI에서 감사 액세스 이벤트를 확인합니다.

   ![HDInsight Ranger UI 정책 감사](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 다음 단계에 따라 생성된 HBase 클러스터를 삭제합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 맨 위에 있는 **검색** 상자에 **HDInsight**를 입력합니다. 
1. **서비스**에서 **HDInsight 클러스터**를 선택합니다.
1. 표시되는 HDInsight 클러스터 목록에서 이 자습서용으로 만든 클러스터 옆에 있는 **...** 를 클릭합니다. 
1. **삭제**를 클릭합니다. **예**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Apache HBase 시작](../hbase/apache-hbase-tutorial-get-started-linux.md)
