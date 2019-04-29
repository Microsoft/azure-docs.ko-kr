---
title: Azure에서 IBM DB2 pureScale 배포
description: z/OS에서 실행되는 IBM DB2 환경에서 Azure의 IBM Db2 pureScale로 엔터프라이즈를 마이그레이션하는 데 최근 사용된 예제 아키텍처를 배포하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: fba6b5308b380b374611c09747302dbf8305dd9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716050"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Azure에서 IBM DB2 pureScale 배포

이 문서에서는 엔터프라이즈 고객이 z/OS에서 실행되는 IBM DB2 환경에서 Azure의 IBM DB2 pureScale로 마이그레이션하는 데 최근 사용한 [예제 아키텍처](ibm-db2-purescale-azure.md)를 배포하는 방법을 설명합니다.

마이그레이션에 사용되는 단계를 진행하려면 GitHub의 [DB2onAzure](https://aka.ms/db2onazure) 리포지토리에서 설치 스크립트를 참조하세요. 이러한 스크립트는 일반적인 중간 크기의 OLTP(온라인 트랜잭션 처리) 워크로드용 아키텍처를 기준으로 합니다.

## <a name="get-started"></a>시작하기

이 아키텍처를 배포하려면 GitHub의 [DB2onAzure](https://aka.ms/db2onazure) 리포지토리에 있는 deploy.sh 스크립트를 다운로드하여 실행합니다.

이 리포지토리에는 Grafana 대시보드 설정용 스크립트도 있습니다. 대시보드를 사용하면 DB2에 포함된 오픈 소스 모니터링 및 경고 시스템인 Prometheus를 쿼리할 수 있습니다.

> [!NOTE]
> 클라이언트의 deploy.sh 스크립트는 비공개 SSH 키를 만들고 HTTPS를 통해 배포 템플릿에 전달합니다. 보안을 강화하기 위해 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)를 사용하여 비밀, 키 및 암호를 저장하는 것이 좋습니다.

## <a name="how-the-deployment-script-works"></a>배포 스크립트 작동 방법

deploy.sh 스크립트는 이 아키텍처용 Azure 리소스를 만들고 구성합니다. 스크립트는 대상 환경에서 사용되는 Azure 구독 및 가상 머신에 대한 메시지를 표시한 다음, 다음 작업을 수행합니다.

-   Azure에서 설치용 리소스 그룹, 가상 네트워크 및 서브넷 설정

-   환경용 네트워크 보안 그룹 및 SSH 설정

-   GlusterFS 및 DB2 pureScale 가상 머신에서 NIC 설정

-   GlusterFS 스토리지 가상 머신 만들기

-   jumpbox 가상 머신 만들기

-   DB2 pureScale 가상 머신 만들기

-   DB2 pureScale이 ping을 수행하는 감시 가상 머신 만들기

-   테스트에 사용할 Windows 가상 머신 만들기(가상 머신에 아무 항목도 설치는 하지 않음)

다음으로 배포 스크립트는 Azure의 공유 스토리지용으로 iSCSI vSAN(가상 저장 영역 네트워크)을 설정합니다. 이 예제에서 iSCSI는 GlusterFS에 연결합니다. 또한 이 솔루션은 단일 Windows 노드로 iSCSI 대상 설치 옵션을 제공합니다. iSCSI는 TCP/IP를 통해 공유 블록 스토리지 인터페이스를 제공합니다. DB2 pureScale 설치 절차에서는 이 인터페이스를 통해 디바이스 인터페이스를 사용하여 공유 스토리지에 연결할 수 있습니다. GlusterFS 관련 기본 사항은 Gluster Docs에서 [아키텍처: 볼륨 유형](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) 항목을 참조하세요.

배포 스크립트는 다음 일반 단계를 실행합니다.

1.  GlusterFS를 사용하여 Azure에서 공유 스토리지 클러스터를 설치합니다. 이 단계에서는 Linux 노드 2개 이상이 설치됩니다. 설치 세부 정보는 Red Hat Gluster 설명서에서 [Microsoft Azure에서 Red Hat Gluster Storage 설정](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure)을 참조하세요.

2.  대상 Linux 서버에서 GlusterFS에 대한 iSCSI Direct 인터페이스를 설정합니다. 설치 세부 정보는 GlusterFS Administration 가이드에서 [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)를 참조하세요.

3.  Linux 가상 머신에서 iSCSI 초기자를 설치합니다. 초기자는 iSCSI 대상을 사용하여 GlusterFS 클러스터에 액세스합니다. 설치 세부 정보는 RootUsers 설명서에서 [Linux에서 iSCSI 대상 및 초기자를 구성하는 방법](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/)을 참조하세요.

4.  iSCSI 인터페이스용 스토리지 계층으로 GlusterFS를 설치합니다.

스크립트는 iSCSI 디바이스를 만든 후 마지막 단계에서 DB2 pureScale을 설치합니다. DB2 pureScale 설치의 일부분으로 [IBM 스펙트럼 확장](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html)(구 GPFS)이 컴파일되어 GlusterFS 클러스터에 설치됩니다. 이 클러스터된 파일 시스템을 통해 DB2 pureScale은 DB2 pureScale 엔진을 실행하는 여러 가상 머신 간에 데이터를 공유할 수 있습니다. 자세한 내용은 IBM 웹 사이트에서 [IBM 스펙트럼 확장](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) 설명서를 참조하세요.

## <a name="db2-purescale-response-file"></a>DB2 pureScale 지시 파일

GitHub 리포지토리에는 DB2 pureScale 설치용 자동 스크립트를 생성하는 데 사용할 수 있는 지시(.rsp) 파일인 DB2server.rsp가 포함되어 있습니다. 아래 표에는 지시 파일이 설치에 사용하는 DB2 pureScale 옵션이 나와 있습니다. 환경의 필요에 따라 지시 파일을 사용자 지정할 수 있습니다.

> [!NOTE]
> 샘플 지시 파일인 DB2server.rsp는 GitHub의 [DB2onAzure](https://aka.ms/db2onazure) 리포지토리에 포함되어 있습니다. 이 파일을 사용하는 경우 사용자 환경에서 사용할 수 있도록 편집해야 합니다.

| 화면 이름               | 필드                                        | 값                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| 시작                   |                                              | 새 설치                                                                                           |
| 제품 선택          |                                              | DB2 버전 11.1.3.3. DB2 pureScale이 포함된 Server Edition                                              |
| 구성             | 디렉터리                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | 설치 유형 선택                 | 일반                                                                                               |
|                           | IBM 사용 약관에 동의함                     | 선택                                                                                               |
| 인스턴스 소유자            | 인스턴스, 사용자 이름에 대한 기존 사용자        | DB2sdin1                                                                                              |
| 펜스된 사용자               | 기존 사용자, 사용자 이름                     | DB2sdin1                                                                                              |
| 클러스터 파일 시스템       | 공유 디스크 파티션 디바이스 경로            | /dev/dm-2                                                                                             |
|                           | 탑재 지점                                  | /DB2sd\_1804a                                                                                         |
|                           | 데이터에 대한 공유 디스크                         | /dev/dm-1                                                                                             |
|                           | 탑재 지점(데이터)                           | /DB2fs/datafs1                                                                                        |
|                           | 로그에 대한 공유 디스크                          | /dev/dm-0                                                                                             |
|                           | 탑재 지점(로그)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 클러스터 서비스 Tiebreaker. 디바이스 경로 | /dev/dm-3                                                                                             |
| 호스트 목록                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | 기본 설정된 주 CF                         | cf1                                                                                                   |
|                           | 기본 설정된 보조 CF                       | cf2                                                                                                   |
| 지시 파일 및 요약 | 첫 번째 옵션                                 | IBM DB2 pureScale 기능이 포함된 DB2 Server Edition을 설치하고 지시 파일에 설정 저장 |
|                           | 지시 파일 이름                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>이 배포에 대한 정보

- /dev-dm0, /dev-dm1, /dev-dm2 및 /dev-dm3의 값은 설치를 수행하는 가상 머신(자동화된 스크립트에서는 d0)을 다시 시작하고 나면 변경될 수 있습니다. 올바른 값을 찾으려는 경우 설치를 실행할 서버에서 지시 파일을 완료하기 전에 다음 명령을 실행하면 됩니다.

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- 설치 스크립트는 실제 이름을 쉽게 찾을 수 있도록 iSCSI 디스크에 대한 별칭을 사용합니다.

- d0에서 설치 스크립트를 실행할 때 d1, cf0, cf1에서는 **/dev/dm-\*** 값이 달라질 수 있습니다. 값이 다르더라도 DB2 pureScale 설치에는 영향이 없습니다.

## <a name="troubleshooting-and-known-issues"></a>문제 해결 및 알려진 문제

GitHub 리포지토리에는 작성자들이 유지 관리하는 기술 자료가 포함되어 있습니다. 이러한 기술 자료에는 발생 가능한 문제와 시도해 볼 수 있는 해결 방법이 나와 있습니다. 예를 들어 다음과 같은 경우에 알려진 문제가 발생할 수 있습니다.

-   게이트웨이 IP 주소에 연결하려는 경우

-   GPL(General Public License)을 컴파일하는 경우

-   호스트 간의 보안 핸드셰이크가 실패합니다.

-   DB2 설치 관리자가 기존 파일 시스템을 검색하는 경우

-   IBM Spectrum Scale을 수동으로 설치하는 경우

-   IBM Spectrum Scale이 이미 생성된 상태에서 DB2 pureScale을 설치하는 경우

-   DB2 pureScale 및 IBM Spectrum Scale을 제거하는 경우

이러한 문제 및 기타 알려진 문제에 대한 자세한 내용은 [DB2onAzure](https://aka.ms/DB2onAzure) 리포지토리에서 kb.md 파일을 참조하세요.

## <a name="next-steps"></a>다음 단계

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [DB2 pureScale 기능 설치에 필요한 사용자 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - 인스턴스 만들기 명령](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale 클러스터 데이터 솔루션](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Platform Modernization Alliance: Azure의 IBM DB2](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Azure Virtual Data Center 리프트 앤 시프트 가이드](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
