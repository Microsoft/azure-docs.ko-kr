---
title: Azure HDInsight의 Hive에 대한 보안 옵션
description: 표준 및 ESP 클러스터의 Hive에 대한 보안 옵션입니다.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: a608c34225641a3c7764d6c7dd3872c5f61fe3c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869721"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Azure HDInsight의 Hive에 대한 보안 옵션

이 문서에서는 HDInsight의 Hive에 대한 권장 보안 옵션을 설명합니다. 이러한 옵션은 Ambari를 통해 구성할 수 있습니다.

:::image type="content" source="./media/hdinsight-security-options-for-hive/security-options-hive.png " alt-text="`Hive에 대한 보안 옵션`" border="true":::

## <a name="hiveserver2-authentication"></a>HiveServer2 인증

표준 클러스터의 경우 HiveServer2 인증에 권장되는 설정은 기본값(없음)입니다. 인증을 사용하도록 설정하려면 [ESP](../domain-joined/hdinsight-security-overview.md)(Enterprise Security Package) 클러스터로 업그레이드하는 것이 좋습니다. 

ESP 클러스터의 경우 [Kerberos](https://web.mit.edu/Kerberos/) 인증이 기본적으로 사용됩니다. PAM(플러그형 인증 모듈) 및 사용자 지정 인증 체계는 지원되지 않습니다.

## <a name="hiveserver2-authorization"></a>HiveServer2 권한 부여

표준 클러스터의 경우 기본 설정은 없음입니다. [SqlStdAuth(SQL 표준 기반 권한 부여)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization)를 사용하도록 설정할 수 있습니다. 표준 클러스터에는 [Apache Ranger](https://ranger.apache.org/)를 통한 권한 부여가 지원되지 않습니다. Ranger 권한 부여를 위해 ESP 클러스터로 업그레이드하는 것이 좋습니다. 

ESP 클러스터의 경우에는 기본적으로 Ranger를 통한 권한 부여가 사용하도록 설정됩니다. 


## <a name="ssl-encryption-for-hiveserver2"></a>HiveServer2에 대한 SSL 암호화

표준 또는 ESP 클러스터에 대해서는 Hiveserver2 SSL을 사용하지 않는 것이 좋습니다. 대신 SSL이 게이트웨이에서 사용됩니다. [전송 중 암호화](../domain-joined/encryption-in-transit.md)는 [IPSec(인터넷 프로토콜 보안)](https://en.wikipedia.org/wiki/IPsec)를 사용하여 클러스터 노드 간 통신을 암호화하는 데 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [HiveServer2 인증 개요](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [HiveServer2 권한 부여 개요](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [SQL 표준 기반 Hive 권한 부여 사용](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Hive를 포함하는 Apache Ranger](../domain-joined/apache-domain-joined-run-hive.md)
