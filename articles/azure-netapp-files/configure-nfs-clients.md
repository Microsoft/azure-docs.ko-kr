---
title: Azure NetApp Files에 대 한 NFS 클라이언트 구성 | Microsoft Docs
description: Azure NetApp Files에서 사용 하도록 NFS 클라이언트를 구성 하는 방법을 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/19/2020
ms.author: b-juche
ms.openlocfilehash: 20cbc9b33e567ffe306aae694bb835d95c2d861e
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88704980"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Azure NetApp Files에 대한 NFS 클라이언트 구성

이 문서에 설명 된 NFS 클라이언트 구성은 [nfsv 4.1 Kerberos 암호화를 구성](configure-kerberos-encryption.md) 하거나 [이중 프로토콜 볼륨을 만들](create-volumes-dual-protocol.md)때 설치 프로그램의 일부입니다. Azure NetApp Files와 함께 사용할 수 있는 광범위 한 Linux 배포판입니다. 이 문서에서는 일반적으로 사용 되는 두 가지 환경 (RHEL 8 및 Ubuntu 18.04)에 대 한 구성을 설명 합니다. 

사용 하는 Linux 버전에 관계 없이 다음 구성이 필요 합니다.
* 시간 오차 문제를 방지 하도록 NTP 클라이언트를 구성 합니다.
* 이름 확인을 위해 Linux 클라이언트의 DNS 항목을 구성 합니다.  
    이 구성에는 "A" (전달) 레코드와 PTR (역방향) 레코드가 포함 됩니다. 
* 도메인 가입의 경우 대상 Active Directory (영역 조인 명령 중에 생성 됨)에 컴퓨터 계정을 만듭니다. 
    > [!NOTE] 
    > `$SERVICEACCOUNT`아래 명령에 사용 되는 변수는 대상 조직 구성 단위에 컴퓨터 계정을 만들 수 있는 권한 또는 위임이 있는 사용자 계정 이어야 합니다.
* 클라이언트에서 NFS 볼륨 및 기타 관련 모니터링 도구를 탑재할 수 있습니다.

## <a name="rhel-8-configuration"></a>RHEL 8 구성 

1. 패키지 설치:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. NTP 클라이언트를 구성 합니다.  
    RHEL 8은 `chrony` 기본적으로를 사용 합니다.  [Chrony suite를 사용 하 여 NTP 구성](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)의 구성 지침을 따릅니다.

3. Active Directory 도메인에 가입 합니다.  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu 구성 

1. 패키지 설치:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. NTP 클라이언트를 구성 합니다.  
    Ubuntu 18.04는 `chrony` 기본적으로를 사용 합니다.  Ubuntu Bionic의 구성 지침에 따라 [chrony를 사용 하 여 NTP를 구성](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)합니다.

3. Active Directory 도메인에 조인 합니다.  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대 한 이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)

