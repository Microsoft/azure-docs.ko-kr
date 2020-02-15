---
title: Red Hat 업데이트 인프라 | Microsoft Docs
description: Microsoft Azure의 주문형 Red Hat Enterprise Linux 인스턴스에 대한 Red Hat 업데이트 인프라에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: e4213e67d9d752f3fc6450236b41e8bbf61f9957
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169316"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Azure에서 주문형 Red Hat Enterprise Linux VM에 대한 Red Hat 업데이트 인프라
 [RHUI(Red Hat 업데이트 인프라)](https://access.redhat.com/products/red-hat-update-infrastructure)를 사용하면 클라우드 공급자(예: Azure)가 Red Hat 호스트 리포지토리 콘텐츠를 미러링하고, Azure 관련 콘텐츠를 포함한 사용자 지정 저장소를 만들고, 최종 사용자 VM에 사용할 수 있도록 합니다.

RHEL(Red Hat Enterprise Linux) 종량제(PAYG) 이미지는 Azure RHUI에 액세스하도록 미리 구성됩니다. 추가 구성은 필요하지 않습니다. 최신 업데이트를 가져오려면 RHEL 인스턴스가 준비된 후 `sudo yum update`를 실행합니다. 이 서비스는 RHEL PAYG 소프트웨어 요금의 일부로 포함됩니다.

게시 및 재방문 주기 정책을 포함하여 Azure에서 RHEL 이미지에 추가 정보가 [여기](./redhat-images.md)에 제공됩니다.

모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.

## <a name="important-information-about-azure-rhui"></a>Azure RHUI에 대한 중요한 정보

* Azure RHUI는 Azure에서 만든 모든 RHEL PAYG Vm을 지 원하는 업데이트 인프라입니다. PAYG RHEL Vm을 구독 관리자 또는 위성 이나 다른 업데이트 원본으로 등록 하는 것을 배제 하지는 않지만 PAYG VM을 사용 하 여이 작업을 수행 하면 간접 이중 청구가 발생 합니다. 자세한 내용은 다음을 참조 하세요.
* Azure 호스티드 RHUI에 대한 액세스는 RHEL PAYG 이미지 가격에 포함됩니다. Azure 호스티드 RHUI에서 PAYG RHEL VM의 등록을 취소하면 가상 머신을 사용자 라이선스 필요(BYOL) 형식 VM으로 변환하지 않습니다. 동일한 VM을 다른 업데이트 소스에 등록하면 _간접_ 요금이 이중으로 부과될 수 있습니다. 최초의 Azure RHEL 소프트웨어 요금이 사용자에게 청구됩니다. 이전에 구매한 Red Hat 구독에 대한 요금이 두 번째로 부과됩니다. Azure에서 호스트 되는 RHUI 이외의 업데이트 인프라를 지속적으로 사용 해야 하는 경우 [RHEL BYOS 이미지](./byos.md)를 사용 하도록 등록 하는 것이 좋습니다.

* Azure(RHEL for SAP HANA 및 RHEL for SAP Business Applications)에서 RHEL PAYG 이미지는 SAP 인증에 필요한 특정 RHEL 부 버전에 남아 있는 전용 RHUI 채널에 연결됩니다.

* Azure 호스티드 RHUI에 대한 액세스는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 내의 VM에 제한됩니다. 온-프레미스 네트워크 인프라를 통해 모든 VM 트래픽을 프록시로 사용하는 경우 Azure RHUI에 액세스하도록 RHEL PAYG VM에 대한 사용자 정의 경로를 설정해야 할 수 있습니다. 이 경우 _모든_ RHUI IP 주소에 대해 사용자 정의 경로를 추가 해야 합니다.

## <a name="image-update-behavior"></a>이미지 업데이트 동작

4 월 2019부터 Azure는 기본적으로 EUS (확장 업데이트 지원) 리포지토리에 연결 되는 RHEL 이미지를 제공 하 고, 기본적으로 일반 (비 EUS) 리포지토리에 연결 된 이미지를 RHEL 제공 합니다. RHEL EUS에 대 한 자세한 내용은 Red Hat의 [버전 수명 주기 설명서](https://access.redhat.com/support/policy/updates/errata) 및 [eus 설명서](https://access.redhat.com/articles/rhel-eus)에서 확인할 수 있습니다. `sudo yum update`의 기본 동작은 다양 한 이미지가 다른 리포지토리에 연결 되어 있으므로에서 프로 비전 한 RHEL 이미지에 따라 달라 집니다.

전체 이미지 목록에 대해 Azure CLI를 사용 하 여 `az vm image list --publisher redhat --all`를 실행 합니다.

### <a name="images-connected-to-non-eus-repositories"></a>비-EUS 리포지토리에 연결 된 이미지

비 EUS 리포지토리에 연결 된 RHEL 이미지에서 VM을 프로 비전 하는 경우 `sudo yum update`를 실행 하면 최신 RHEL 부 버전으로 업그레이드 됩니다. 예를 들어 RHEL 7.4 PAYG 이미지에서 VM을 프로 비전 하 고 `sudo yum update`를 실행 하는 경우 RHEL 7.7 VM (RHEL7 제품군의 최신 부 버전)으로 끝납니다.

비-EUS 리포지토리에 연결 된 이미지에는 SKU의 부 버전 번호가 포함 되지 않습니다. SKU는 URN의 세 번째 요소 (이미지의 전체 이름)입니다. 예를 들어 다음 이미지는 모두 EUS 리포지토리에 연결 됩니다.

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Sku는 7LVM 또는 7-RAW 중 하나입니다. 부 버전은 이러한 이미지의 버전 (URN의 네 번째 요소)에 표시 됩니다.

### <a name="images-connected-to-eus-repositories"></a>EUS 리포지토리에 연결 된 이미지

EUS 리포지토리에 연결 된 RHEL 이미지에서 VM을 프로 비전 하는 경우 `sudo yum update`를 실행 하면 최신 RHEL 부 버전으로 업그레이드 되지 않습니다. EUS 리포지토리에 연결 된 이미지도 해당 하는 부 버전에 대 한 버전 잠겨 있기 때문입니다.

EUS 리포지토리에 연결 된 이미지의 SKU에 부 버전 번호가 포함 됩니다. 예를 들어 다음 이미지는 모두 EUS 리포지토리에 연결 됩니다.

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEl EUS 및 버전 잠금 RHEL VM

EUS (확장 업데이트 지원) 리포지토리는 VM을 프로 비전 한 후에 RHEL Vm을 특정 RHEL 부 릴리스로 잠글 수 있는 고객에 게 제공 됩니다. Extended Update Support 리포지토리에 연결하도록 리포지토리를 업데이트하여 RHEL VM을 특정 부 버전으로 버전을 잠글 수 있습니다. EUS 버전 잠금 작업을 실행 취소할 수도 있습니다.

>[!NOTE]
> EUS는 RHEL 특별 기능에서 지원 되지 않습니다. 즉, 일반적으로 RHEL 특별 채널에서 사용할 수 있는 패키지를 설치 하는 경우 EUS에서이 작업을 수행할 수 없습니다. Red Hat 제품 수명 주기는 [여기](https://access.redhat.com/support/policy/updates/extras/)에 자세히 설명 되어 있습니다.

이 문서를 작성할 당시 RHEL < = 7.4에 대 한 EUS 지원이 종료 되었습니다. 자세한 내용은 [Red Hat 설명서](https://access.redhat.com/support/policy/updates/errata/) 의 "더 긴 Red Hat Enterprise Linux 추가 기능 지원" 섹션을 참조 하세요.
* RHEL 7.4 EUS 지원이 2019 년 8 월 31 일에 종료 됩니다.
* RHEL 7.5 EUS 지원은 2020 년 4 월 30 일에 종료 됩니다.
* RHEL 7.6 EUS 지원이 2020 년 10 월 31 일에 종료 됩니다.
* RHEL 7.7 EUS 지원이 2021 년 8 월 30 일에 종료 됩니다.

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL VM을 EUS (버전-특정 부 버전으로 잠금)로 전환 합니다.
다음 지침을 사용 하 여 RHEL VM을 특정 부 릴리스 (루트로 실행)에 잠급니다.

>[!NOTE]
> 이는 EUS를 사용할 수 있는 RHEL 버전에만 적용됩니다. 이 문서를 작성할 당시에는 RHEL 7.2-7.7이 포함 됩니다. 자세한 내용은 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 제공됩니다.

1. 비 EUS 리포지토리를 비활성화합니다.
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS 리포지토리를 추가 합니다.
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Releasever 변수 잠금 (실행 루트):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 위의 명령을 사용하여 현재 부 릴리스로 RHEL 부 릴리스를 잠급니다. 최신 릴리스가 아닌 추후 부 릴리스로 업그레이드하고 잠그려는 경우 특정 부 릴리스를 입력합니다. 예를 들어 `echo 7.5 > /etc/yum/vars/releasever`는 RHEL 7.5로 RHEL 버전을 잠급니다.

1. RHEL VM 업데이트
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL VM을 EUS (버전 잠금 제거)로 다시 전환 합니다.
Root로 다음을 실행 합니다.
1. Releasever 파일을 제거 합니다.
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS 리포지토리 사용 안 함:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. RHEL VM 구성
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

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

RHEL 7.4(이미지 URN: `RedHat:RHEL:7.4:7.4.2018010506`)와 같은 이전 RHEL VM 이미지를 사용하는 경우 현재는 만료된 SSL 클라이언트 인증서로 인해 RHUI에 연결 문제가 발생합니다. 표시 되는 오류는 _"SSL 피어가 만료 된 것으로 인증서를 거부_ 했습니다." 또는 _"오류: 리포지토리의 리포지토리 메타 데이터 (repomd .xml)를 검색할 수 없습니다."와 같은 것 처럼 보일 수 있습니다. 경로를 확인 하 고 다시 시도 하십시오. "_ 이 문제를 해결하려면 다음 명령을 사용하여 VM에서 RHUI 클라이언트 패키지를 업데이트합니다.

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

또는 `sudo yum update`를 실행하면 다른 리포지토리에 대해 표시되는 "만료된 SSL 인증서" 오류에 관계없이 클라이언트 인증서 패키지(RHEL 버전에 따라 다름)를 업데이트할 수도 있습니다. 이 업데이트가 성공하면 다른 RHUI 리포지토리에 대한 일반 연결이 복원되어 `sudo yum update`를 성공적으로 실행할 수 있습니다.

`yum update`를 실행 하는 동안 404 오류가 발생 하는 경우 다음을 시도 하 여 yum 캐시를 새로 고칩니다.
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Azure RHUI에 대한 연결 문제 해결
RHEL PAYG Azure VM에서 Azure RHUI에 연결할 때 문제가 발생하는 경우 다음 단계를 수행합니다.

1. Azure RHUI 엔드포인트에 대한 VM 구성 검사

    1. `/etc/yum.repos.d/rh-cloud.repo` 파일이 해당 파일에 있는 `rhui-[1-3].microsoft.com` 섹션의 `baseurl`에서 `[rhui-microsoft-azure-rhel*]`에 대한 참조를 포함하는지 확인합니다. 그렇다면 새 Azure RHUI를 사용 중입니다.

    1. 다음 `mirrorlist.*cds[1-4].cloudapp.net` 패턴으로 위치를 가리키는 경우 구성 업데이트가 필요합니다. 이전 VM 스냅샷을 사용하는 경우 새 Azure RHUI를 가리키도록 업데이트해야 합니다.

1. Azure 호스티드 RHUI에 대한 액세스는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 내의 VM에 제한됩니다.

1. 새 구성을 사용하고 VM이 Azure IP 범위에서 연결된다고 확인되며 여전히 Azure RHUI에 연결할 수 없으면 Microsoft 또는 Red Hat을 사용하여 지원 사례를 정리합니다.

### <a name="infrastructure-update"></a>인프라 업데이트

2016년 9월에는 업데이트된 Azure RHUI를 배포했습니다. 2017년 4월에는 이전 Azure RHUI를 종료했습니다. 2016년 9월부터 RHEL PAYG 이미지(또는 해당 스냅샷)를 사용한 경우 새 Azure RHUI에 자동으로 연결됩니다. 그러나 VM에 이전 스냅샷이 설치되어 있다면 해당 구성은 다음 섹션에 설명한 대로 Azure RHUI에 액세스하도록 수동으로 업데이트되어야 합니다.

새로운 Azure RHUI 서버가 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)와 함께 배포됩니다. Traffic Manager에서는 모든 VM에서 하위 지역에 관계없이 단일 엔드포인트(rhui-1.micrsoft.com)를 사용할 수 있습니다.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Azure RHUI 서버를 사용하기 위한 수동 업데이트 절차
이 절차는 참조용으로만 제공됩니다. RHEL PAYG 이미지에 Azure RHUI에 연결하기 위한 올바른 구성이 이미 있습니다. Azure RHUI 서버를 사용하도록 구성을 수동으로 업데이트하려면 다음 단계를 따릅니다.

- RHEL 6의 경우:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- RHEL 7의 경우:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- RHEL 8의 경우:
    1. 구성 파일을 만듭니다.
        ```bash
        vi rhel8.config
        ```
    1. 구성 파일에 다음 콘텐츠를 추가 합니다.
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. 파일을 저장 하 고 다음 명령을 실행 합니다.
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. VM 업데이트
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>다음 단계
* Azure Marketplace PAYG 이미지에서 Red Hat Enterprise Linux VM을 만들고 Azure 호스티드 RHUI를 활용하려면 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)로 이동합니다.
* Azure의 Red Hat 이미지에 대한 자세한 내용은 [설명서 페이지](./redhat-images.md)로 이동합니다.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
