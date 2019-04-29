---
title: Red Hat 업데이트 인프라 | Microsoft Docs
description: Microsoft Azure의 주문형 Red Hat Enterprise Linux 인스턴스에 대한 Red Hat 업데이트 인프라에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 1/7/2019
ms.author: borisb
ms.openlocfilehash: 018ad851b223caa4017d544ca12e2a6397654205
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799204"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Azure에서 주문형 Red Hat Enterprise Linux VM에 대한 Red Hat 업데이트 인프라
 [RHUI(Red Hat 업데이트 인프라)](https://access.redhat.com/products/red-hat-update-infrastructure)를 사용하면 클라우드 공급자(예: Azure)가 Red Hat 호스트 리포지토리 콘텐츠를 미러링하고, Azure 관련 콘텐츠를 포함한 사용자 지정 저장소를 만들고, 최종 사용자 VM에 사용할 수 있도록 합니다.

RHEL(Red Hat Enterprise Linux) 종량제(PAYG) 이미지는 Azure RHUI에 액세스하도록 미리 구성됩니다. 추가 구성은 필요하지 않습니다. 최신 업데이트를 가져오려면 RHEL 인스턴스가 준비된 후 `sudo yum update`를 실행합니다. 이 서비스는 RHEL PAYG 소프트웨어 요금의 일부로 포함됩니다.

게시 및 재방문 주기 정책을 포함하여 Azure에서 RHEL 이미지에 추가 정보가 [여기](./rhel-images.md)에 제공됩니다.

모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.

## <a name="important-information-about-azure-rhui"></a>Azure RHUI에 대한 중요한 정보
* 현재 Azure RHUI는 각 RHEL 제품군(RHEL6 또는 RHEL7)의 최신 부 릴리스만 지원합니다. RHUI에 연결된 RHEL VM 인스턴스를 최신 부 버전으로 업그레이드하려면 `sudo yum update`를 실행합니다.

    예를 들어 RHEL 7.4 PAYG 이미지에서 VM을 프로비전하고 `sudo yum update`를 실행하는 경우 RHEL 7.6 VM(RHEL7 제품군에서 최신 부 버전)이 설치됩니다.

    이러한 동작을 피하려면 [Azure에 대한 Red Hat 기반 가상 머신 만들기 및 업로드](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서에 설명된 대로 사용자 고유의 이미지를 빌드해야 합니다. 그런 다음 다른 업데이트 인프라([Red Hat 콘텐츠 전달 서버에 직접](https://access.redhat.com/solutions/253273) 또는 [Red Hat 위성 서버](https://access.redhat.com/products/red-hat-satellite))에 연결해야 합니다.

* Azure 호스티드 RHUI에 대한 액세스는 RHEL PAYG 이미지 가격에 포함됩니다. Azure 호스티드 RHUI에서 PAYG RHEL VM의 등록을 취소하면 가상 머신을 사용자 라이선스 필요(BYOL) 형식 VM으로 변환하지 않습니다. 동일한 VM을 다른 업데이트 소스에 등록하면 _간접_ 요금이 이중으로 부과될 수 있습니다. 최초의 Azure RHEL 소프트웨어 요금이 사용자에게 청구됩니다. 이전에 구매한 Red Hat 구독에 대한 요금이 두 번째로 부과됩니다. Azure 호스티드 RHUI 이외의 업데이트 인프라를 지속적으로 사용해야 한다면 사용자 고유의(BYOL 형식) 이미지를 만들어 배포하도록 고려합니다. 이 과정은 [Azure용 Red Hat 기반 가상 머신 만들기 및 업데이트](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 설명합니다.

* Azure(RHEL for SAP HANA 및 RHEL for SAP Business Applications)에서 RHEL PAYG 이미지는 SAP 인증에 필요한 특정 RHEL 부 버전에 남아 있는 전용 RHUI 채널에 연결됩니다.

* Azure 호스티드 RHUI에 대한 액세스는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 내의 VM에 제한됩니다. 온-프레미스 네트워크 인프라를 통해 모든 VM 트래픽을 프록시로 사용하는 경우 Azure RHUI에 액세스하도록 RHEL PAYG VM에 대한 사용자 정의 경로를 설정해야 할 수 있습니다.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEl EUS 및 버전 잠금 RHEL VM
일부 고객은 특정 RHEL 부 릴리스로 RHEL VM을 잠글 수도 있습니다. Extended Update Support 리포지토리에 연결하도록 리포지토리를 업데이트하여 RHEL VM을 특정 부 버전으로 버전을 잠글 수 있습니다. 다음 지침을 사용하여 특정 부 릴리스로 RHEL VM을 잠급니다.

>[!NOTE]
> 이는 EUS를 사용할 수 있는 RHEL 버전에만 적용됩니다. 이 문서 작성 당시 여기에는 RHEL 7.2-7.6이 포함됩니다. 자세한 내용은 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 제공됩니다.

1. 비 EUS 리포지토리를 비활성화합니다.
    ```bash
    sudo yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS 리포지토리를 추가 합니다.
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. releasever 변수를 잠급니다.
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 위의 명령을 사용하여 현재 부 릴리스로 RHEL 부 릴리스를 잠급니다. 최신 릴리스가 아닌 추후 부 릴리스로 업그레이드하고 잠그려는 경우 특정 부 릴리스를 입력합니다. 예를 들어 `echo 7.5 > /etc/yum/vars/releasever`는 RHEL 7.5로 RHEL 버전을 잠급니다.

1. RHEL VM 업데이트
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI 콘텐츠 배달 서버에 대한 IP

RHUI는 RHEL 주문형 이미지를 사용할 수 있는 모든 지역에서 제공됩니다. RHUI는 현재 [Azure 상태 대시보드](https://azure.microsoft.com/status/) 페이지, Azure US Government 및 Microsoft Azure 독일 지역에 나열된 모든 공용 지역을 포함합니다.

네트워크 구성을 사용하여 RHEL PAYG VM에서 추가 액세스를 제한하려는 경우 사용자의 환경에 따라 작동하도록 다음 IP를 `yum update`에 대해 허용해야 합니다.


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Azure RHUI 인프라


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>VM에서 만료된 RHUI 클라이언트 인증서 업데이트

RHEL 7.4(이미지 URN: `RedHat:RHEL:7.4:7.4.2018010506`)와 같은 이전 RHEL VM 이미지를 사용하는 경우 현재는 만료된 SSL 클라이언트 인증서로 인해 RHUI에 연결 문제가 발생합니다. 이 경우 _“인증서가 만료되어 SSL 피어에서 거부되었습니다.”_ 또는 _“오류: 리포지토리에 대한 리포지토리 메타데이터(repomd.xml)를 검색할 수 없습니다. 해당 경로를 확인하고 다시 시도하세요.”_ 와 같은 오류가 표시됩니다. 이 문제를 해결하려면 다음 명령을 사용하여 VM에서 RHUI 클라이언트 패키지를 업데이트합니다.

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

또는 `sudo yum update`를 실행하면 다른 리포지토리에 대해 표시되는 "만료된 SSL 인증서" 오류에 관계없이 클라이언트 인증서 패키지(RHEL 버전에 따라 다름)를 업데이트할 수도 있습니다. 이 업데이트가 성공하면 다른 RHUI 리포지토리에 대한 일반 연결이 복원되어 `sudo yum update`를 성공적으로 실행할 수 있습니다.

실행 하는 동안 404 오류를 실행 하는 경우는 `yum update`, yum 캐시를 새로 고치려면 다음을 시도 합니다.
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Azure RHUI에 대한 연결 문제 해결
RHEL PAYG Azure VM에서 Azure RHUI에 연결할 때 문제가 발생하는 경우 다음 단계를 수행합니다.

1. Azure RHUI 엔드포인트에 대한 VM 구성 검사

    a. `/etc/yum.repos.d/rh-cloud.repo` 파일이 해당 파일에 있는 `[rhui-microsoft-azure-rhel*]` 섹션의 `baseurl`에서 `rhui-[1-3].microsoft.com`에 대한 참조를 포함하는지 확인합니다. 그렇다면 새 Azure RHUI를 사용 중입니다.

    b. 다음 `mirrorlist.*cds[1-4].cloudapp.net` 패턴으로 위치를 가리키는 경우 구성 업데이트가 필요합니다. 이전 VM 스냅숏을 사용하는 경우 새 Azure RHUI를 가리키도록 업데이트해야 합니다.

1. Azure 호스티드 RHUI에 대한 액세스는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 내의 VM에 제한됩니다.

1. 새 구성을 사용하고 VM이 Azure IP 범위에서 연결된다고 확인되며 여전히 Azure RHUI에 연결할 수 없으면 Microsoft 또는 Red Hat을 사용하여 지원 사례를 정리합니다.

### <a name="infrastructure-update"></a>인프라 업데이트

2016년 9월에는 업데이트된 Azure RHUI를 배포했습니다. 2017년 4월에는 이전 Azure RHUI를 종료했습니다. 2016년 9월부터 RHEL PAYG 이미지(또는 해당 스냅숏)를 사용한 경우 새 Azure RHUI에 자동으로 연결됩니다. 그러나 VM에 이전 스냅숏이 설치되어 있다면 해당 구성은 다음 섹션에 설명한 대로 Azure RHUI에 액세스하도록 수동으로 업데이트되어야 합니다.

새로운 Azure RHUI 서버가 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)와 함께 배포됩니다. Traffic Manager에서는 모든 VM에서 하위 지역에 관계없이 단일 엔드포인트(rhui-1.micrsoft.com)를 사용할 수 있습니다.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Azure RHUI 서버를 사용하기 위한 수동 업데이트 절차
이 절차는 참조용으로만 제공됩니다. RHEL PAYG 이미지에 Azure RHUI에 연결하기 위한 올바른 구성이 이미 있습니다. Azure RHUI 서버를 사용하도록 구성을 수동으로 업데이트하려면 다음 단계를 따릅니다.

1. curl을 통해 공개 키 서명을 다운로드합니다.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc
   ```

1. 다운로드한 키의 유효성을 확인합니다.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

1. 출력을 확인한 후 `keyid` 및 `user ID packet`을 확인합니다.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

1. 공개 키를 설치합니다.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

1. 클라이언트 RPM 패키지 관리자(RPM)를 다운로드, 확인 및 설치합니다.

    >[!NOTE]
    >패키지 버전이 변경됩니다. Azure RHUI에 수동으로 연결하는 경우 갤러리에서 최신 이미지를 프로비전하여 각 RHEL 제품군에 대한 최신 버전의 클라이언트 패키지를 찾을 수 있습니다.

   a. 다운로드

    - RHEL 6의 경우:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.2-74.noarch.rpm
        ```

    - RHEL 7의 경우:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.2-74.noarch.rpm
        ```

   b. 확인

   ```bash
   rpm -Kv azureclient.rpm
   ```

   다. 출력을 확인하여 패키지의 서명이 정상인지 확인합니다.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. RPM 설치

    ```bash
    sudo rpm -U azureclient.rpm
    ```

1. 완료되면 VM에서 Azure RHUI에 액세스할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계
* Azure Marketplace PAYG 이미지에서 Red Hat Enterprise Linux VM을 만들고 Azure 호스티드 RHUI를 활용하려면 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)로 이동합니다.
* Azure의 Red Hat 이미지에 대한 자세한 내용은 [설명서 페이지](./rhel-images.md)로 이동합니다.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
