---
title: -에뮬레이터-Azure HDInsight는 Apache Hadoop 샌드박스를 사용 하 여에 대해 알아봅니다.
description: 'Apache Hadoop 에코 시스템을 사용하는 방법에 대해 알아보려면 Azure 가상 머신에서 Hortonworks의 Hadoop 샌드박스를 설정할 수 있습니다. '
keywords: Hadoop 에뮬레이터, Hadoop 샌드박스
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 15152196e45265985c8abb409523982bd4c5d427
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129454"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Apache Hadoop 샌드박스를 가상 컴퓨터에서 에뮬레이터를 사용 하 여 시작

Hadoop 에코 시스템에 대해 알아보기 위해 가상 머신에서 Hortonworks의 Apache Hadoop 샌드박스를 설치하는 방법을 알아봅니다. 이 샌드박스는 Hadoop, HDFS(Hadoop Distributed File System) 및 작업 제출에 대해 알아보는 로컬 개발 환경을 제공합니다. Hadoop에 익숙해졌으면 HDInsight 클러스터를 만들어 Azure에서 Hadoop 사용을 시작할 수 있습니다. 시작 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* [Oracle VirtualBox](https://www.virtualbox.org/) [여기](https://www.virtualbox.org/wiki/Downloads)에서 다운로드하여 설치합니다.



## <a name="download-and-install-the-virtual-machine"></a>가상 컴퓨터 다운로드 및 설치
1. [Hortonworks 다운로드](https://hortonworks.com/downloads/#sandbox)로 이동합니다.

2. **VIRTUALBOX용 다운로드**를 클릭하여 VM 기반 최신 Hortonworks 샌드박스를 다운로드합니다. 다운로드가 시작되기 전에 Hortonworks에 등록하라는 메시지가 표시됩니다. 네트워크 속도에 따라 다운로드하는 데 1 ~ 2 시간이 걸립니다.

    ![VirtualBox에 대한 Hortonworks Sandbox를 다운로드용 링크 이미지](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. 동일한 웹 페이지에서 **Virtual Box에서 가져오기** 링크를 클릭하여 가상 머신에 대한 설치 지침이 포함된 PDF를 다운로드합니다.

이전 HDP 버전 샌드박스를 다운로드하려면 보관 저장소를 확장하세요.

![Hortonworks 샌드박스 보관](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>가상 머신 시작합

1. Open Oracle VM VirtualBox를 엽니다.
2. **파일** 메뉴에서 **Import Appliance(어플라이언스 가져오기)** 를 클릭한 다음 Hortonworks Sandbox 이미지를 지정합니다.
1. Hortonworks Sandbox를 선택한 다음 **시작**, **일반 시작**을 차례로 선택합니다. 가상 머신이 부팅 프로세스를 완료하면 로그인 지침이 표시됩니다.

    ![일반 시작](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. 웹 브라우저를 열고 표시 되는 URL로 이동 (일반적으로 `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>샌드박스 암호 설정

1. Hortonworks Sandbox 페이지의 **시작** 단계에서 **고급 옵션 보기**를 선택합니다. 이 페이지의 정보를 사용하여 SSH를 통해 샌드박스에 로그인합니다. 제공된 이름 및 암호를 사용합니다.

   > [!NOTE]
   > SSH 클라이언트를 설치하지 않은 경우 **http://localhost:4200/** 의 가상 머신에 제공된 웹 기반 SSH를 사용할 수 있습니다.

    SSH를 통해 처음 연결하는 경우 루트 계정의 암호를 변경하라는 메시지가 표시됩니다. 새 암호를 입력합니다. 이 암호는 SSH를 사용하여 로그인할 때 사용됩니다.

2. 일단 로그인하면 다음 명령을 입력합니다.

        ambari-admin-password-reset

    대화 상자가 나타나면 Ambari 관리자 계정에 대한 암호를 입력합니다. Ambari 웹 UI에 액세스할 때 사용됩니다.

## <a name="use-hive-commands"></a>Hive 명령 사용

1. 샌드박스에 대한 SSH 연결에서 다음 명령을 사용하여 Hive 셸을 시작합니다.

        hive
2. 셸이 시작되면 다음을 사용하여 샌드박스와 함께 제공되는 테이블을 봅니다.

        show tables;
3. 다음을 사용하여 `sample_07` 테이블에서 10개의 행을 검색합니다

        select * from sample_07 limit 10;

## <a name="next-steps"></a>다음 단계
* [Hortonworks Sandbox와 Visual Studio를 사용하는 방법 알아보기](../hdinsight-hadoop-emulator-visual-studio.md)
* [Hortonworks Sandbox의 방법 알아보기](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop 자습서 - HDP 시작](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

