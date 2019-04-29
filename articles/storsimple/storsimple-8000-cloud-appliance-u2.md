---
title: StorSimple Cloud Appliance 업데이트 3 | Microsoft Docs
description: Microsoft Azure Virtual Network에서 StorSimple Cloud Appliance를 만들고 배포 및 관리하는 방법을 알아봅니다. StorSimple 업데이트 3 이상에 적용됩니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: df7866d4f87f55523e8139232e48d81cb17c80e4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62117336"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Azure에서 StorSimple Cloud Appliance 배포 및 관리(업데이트 3 이상)

## <a name="overview"></a>개요

StorSimple 8000 Series Cloud Appliance는 Microsoft Azure StorSimple 솔루션과 함께 제공되는 추가 기능입니다. StorSimple Cloud Appliance는 Microsoft Azure Virtual Network의 가상 머신에서 실행되며, 이 장치를 사용하여 호스트에서 데이터를 백업하고 복제할 수 있습니다.

이 문서에서는 Azure에서 StorSimple Cloud Appliance를 배포하고 관리하기 위한 단계별 프로세스에 대해 설명합니다. 이 문서를 읽고 나면:

* 클라우드 어플라이언스와 물리적 디바이스의 차이를 이해하게 됩니다.
* 클라우드 어플라이언스를 만들고 구성할 수 있게 됩니다.
* 클라우드 어플라이언스에 연결하게 됩니다.
* 클라우드 어플라이언스로 작업하는 방법을 알아봅니다.

이 자습서는 업데이트 3 이상을 실행하는 모든 StorSimple Cloud Appliance에 적용됩니다.

#### <a name="cloud-appliance-model-comparison"></a>클라우드 어플라이언스 모델 비교

StorSimple Cloud Appliance는 두 가지 모델 즉, 표준 8010(이전의 1100) 및 프리미엄 8020(업데이트 2에 도입된)으로 사용할 수 있습니다. 다음 표는 두 가지 모델을 비교하여 표시합니다.

| 디바이스 모델 | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **최대 용량** |30TB |64TB |
| **Azure VM** |Standard_A3(4 코어, 7GB 메모리)| Standard_DS3 (4 코어, 14GB 메모리)|
| **지역 가용성** |모든 Azure 지역 |Premium Storage 및 DS3 Azure VM을 지원하는 Azure 지역<br></br>[이 목록](https://azure.microsoft.com/regions/services/)을 사용하여 **Virtual Machines > DS 시리즈** 및 **저장소 > 디스크 저장소**를 자신의 지역에서 사용할 수 있습니다. |
| **저장소 유형** |로컬 디스크에 Azure Standard Storage 사용<br></br> [Standard Storage 계정을 만드는](../storage/common/storage-create-storage-account.md) |로컬 디스크용 Azure Premium Storage 사용<sup>2</sup> <br></br> |
| **워크로드 지침** |백업으로부터 항목 수준 파일 읽어오기 |클라우드 개발 및 테스트 시나리오 <br></br>짧은 대기 시간 및 더 높은 성능 워크로드<br></br>재해 복구용 보조 디바이스 |

<sup>1</sup> *이전에 1100로 알려짐*.

<sup>2</sup>*8010와 8020은 모두 클라우드 계층에 Azure Standard Storage를 사용합니다. 차이점은 디바이스* 내의 로컬 계층에만 존재한다는 것입니다.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>클라우드 어플라이언스와 물리적 디바이스의 차이

StorSimple Cloud Appliance는 Microsoft Azure Virtual Machine의 단일 노드에서 실행되는 StorSimple의 소프트웨어 전용 버전입니다. 클라우드 어플라이언스는 물리적 디바이스를 사용할 수 없는 재해 복구 시나리오를 지원합니다. 클라우드 어플라이언스는 백업, 온-프레미스 재해 복구 및 클라우드 개발/테스트 시나리오의 항목 수준 검색에서 사용하기에 적합합니다.

#### <a name="differences-from-the-physical-device"></a>물리적 디바이스와의 차이점

다음 표는 StorSimple Cloud Appliance와 StorSimple 물리적 디바이스 간의 몇 가지 주요 차이점을 보여줍니다

|  | 물리적 디바이스 | 클라우드 어플라이언스 |
| --- | --- | --- |
| **위치**: |데이터 센터에 상주합니다. |Azure에서 실행됩니다. |
| **네트워크 인터페이스** |네트워크 인터페이스가 6개(DATA 0부터 DATA 5까지) 있습니다. |네트워크 인터페이스가 하나만(DATA 0) 있습니다. |
| **등록** |초기 구성 단계 중에 등록됩니다. |등록은 별도의 작업입니다. |
| **서비스 데이터 암호화 키** |물리적 디바이스에서 다시 생성된 후 새 키로 클라우드 어플라이언스를 업데이트합니다. |클라우드 어플라이언스에서 다시 생성할 수 없습니다. |
| **지원되는 볼륨 유형** |로컬로 고정된 볼륨과 계층화된 볼륨을 모두 지원합니다. |계층화된 볼륨만 지원합니다. |

## <a name="prerequisites-for-the-cloud-appliance"></a>클라우드 어플라이언스에 대한 필수 구성 요소

다음 섹션에서는 StorSimple Cloud Appliance에 대한 구성 필수 구성 요소를 설명합니다. 클라우드 어플라이언스를 배포하기 전에 클라우드 어플라이언스를 사용하기 위한 보안 고려 사항 검토합니다.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Azure 요구 사항

클라우드 어플라이언스를 프로비전하기 전에 Azure 환경에서 다음 준비를 확인해야 합니다.

* 데이터 센터에 StorSimple 8000 시리즈 물리적 디바이스(8100 또는 8600 모델)가 배포되어 실행되고 있는지 확인합니다. StorSimple Cloud Appliance를 만들려는 동일한 StorSimple 디바이스 관리자 서비스에 이 디바이스를 등록합니다.
* 클라우드 어플라이언스의 경우, [Azure에서 가상 네트워크를 구성](../virtual-network/manage-virtual-network.md#create-a-virtual-network)합니다. Premium Storage를 사용하는 경우 Premium Storage를 지원하는 Azure 지역에 가상 네트워크를 만들어야 합니다. Premium Storage 지역은 [지역별 Azure 서비스 목록](https://azure.microsoft.com/regions/services/)에서 디스크 스토리지의 행에 해당하는 지역입니다.
* 사용자 고유의 DNS 서버 이름을 지정하는 대신 Azure에서 제공하는 기본 DNS 서버를 사용하는 것이 좋습니다. DNS 서버 이름이 유효하지 않거나 DNS 서버가 IP 주소를 제대로 확인할 수 없으면 클라우드 어플라이언스 만들기에 실패합니다.
* 지점 대 사이트간 및 사이트 대 사이트는 선택적이지만 필수는 아닙니다. 원하는 경우, 고급 시나리오에 대해 이 옵션을 구성할 수 있습니다.
* 클라우드 어플라이언스에 표시된 볼륨을 사용할 수 있는 [Azure Virtual Machines](../virtual-machines/virtual-machines-windows-quick-create-portal.md)(호스트 서버)를 가상 네트워크에 만들 수 있습니다. 이 서버는 다음 요구 사항을 충족해야 합니다.

  * iSCSI 초기자 소프트웨어가 설치된 Windows 또는 Linux VM입니다.
  * 클라우드 어플라이언스와 동일한 가상 네트워크에서 실행 중입니다.
  * 클라우드 어플라이언스의 내부 IP 주소를 통해 클라우드 어플라이언스의 iSCSI 대상에 연결할 수 있습니다.
  * 동일한 가상 네트워크에서 iSCSI 및 클라우드 트래픽에 대한 지원을 구성했는지 확인합니다.

#### <a name="storsimple-requirements"></a>StorSimple 요구 사항

클라우드 어플라이언스를 만들기 전에 StorSimple 디바이스 관리자 서비스에 대한 다음 업데이트를 확인합니다.

* 클라우드 어플라이언스에 대해 호스트 서버가 될 VM에 대해 [액세스 제어 레코드](storsimple-8000-manage-acrs.md)를 추가합니다.
* 클라우드 어플라이언스와 동일한 지역에 [저장소 계정](storsimple-8000-manage-storage-accounts.md#add-a-storage-account)을 사용합니다. 다른 영역의 Storage 계정으로 성능이 저하될 수 있습니다. 클라우드 어플라이언스에 Standard 또는 Premium Storage 계정을 사용할 수 있습니다. 자세한 내용은 [Standard Storage 계정](../storage/common/storage-create-storage-account.md)을 만드는 방법을 참조하세요.
* 데이터에 사용된 계정이 아닌 클라우드 어플라이언스 생성을 위해 다른 저장소 계정을 사용합니다. 동일한 저장소 계정을 사용하면 성능이 저하될 수 있습니다.

시작하기 전에 다음 정보가 있는지 확인합니다.

* 액세스 자격 증명이 있는 Azure Portal 계정
* StorSimple 디바이스 관리자 서비스에 등록된 물리적 디바이스에서 서비스 데이터 암호화 키의 복사본

## <a name="create-and-configure-the-cloud-appliance"></a>클라우드 어플라이언스 만들기 및 구성

이러한 절차를 수행하기 전에 [클라우드 어플라이언스에 대한 필수 구성 요소](#prerequisites-for-the-cloud-appliance)를 충족하는지 확인합니다.

StorSimple Cloud Appliance를 만들려면 다음 단계를 수행합니다.

### <a name="step-1-create-a-cloud-appliance"></a>1단계: 클라우드 어플라이언스 만들기

다음 단계를 수행하여 StorSimple Cloud Appliance를 만듭니다.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

이 단계에서 클라우드 어플라이언스 만들기에 실패하는 경우 인터넷에 연결되지 않을 수 있습니다. 자세한 내용을 보려면 클라우드 어플라이언스를 만들 때 [인터넷 연결 오류 문제 해결](#troubleshoot-internet-connectivity-errors)로 이동하세요.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>2단계: 클라우드 어플라이언스 구성 및 등록

이 절차를 시작하기 전에 서비스 데이터 암호화 키의 복사본을 가지고 있는지 확인합니다. 서비스 데이터 암호화 키는 StorSimple 디바이스 관리자 서비스에 첫 번째 StorSimple 물리적 디바이스를 등록할 때 생성됩니다. 안전한 위치에 저장하라는 메시지가 표시되었습니다. 서비스 데이터 암호화 키의 복사본이 없는 경우 Microsoft 지원에 문의해야 합니다.

다음 단계에 따라 StorSimple Cloud Appliance를 구성하고 등록합니다.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>3단계: (선택 사항) 디바이스 구성 설정 수정

다음 섹션에서는 CHAP 또는 StorSimple Snapshot Manager를 사용하거나 디바이스 관리자 암호를 변경하려는 경우 StorSimple Cloud Appliance에 필요한 디바이스 구성 설정을 설명합니다.

#### <a name="configure-the-chap-initiator"></a>CHAP 시작자 구성

이 매개 변수는 볼륨 액세스를 시도 중인 개시 장치(서버)에서 클라우드 어플라이언스(대상)가 예상하는 자격 증명을 포함합니다. 개시 디바이스는 CHAP 사용자 이름 및 CHAP 암호를 제공하여 인증 중 디바이스를 자체적으로 식별합니다. 자세한 단계를 보려면 [디바이스에 CHAP 구성](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication)으로 이동합니다.

#### <a name="configure-the-chap-target"></a>CHAP 대상 구성

이 매개 변수는 CHAP 지원 개시 장치가 상호 또는 양방향 인증을 요청하면 클라우드 어플라이언스에서 사용하는 자격 증명을 포함합니다. 클라우드 어플라이언스는 이 인증 프로세스 중 개시 장치에게 자체 식별을 위해 역방향 CHAP 사용자 이름 및 역방향 CHAP 암호를 사용합니다.

> [!NOTE]
> CHAP 대상 설정은 전역 설정입니다. 이 설정을 적용하면 클라우드 어플라이언스에 연결된 모든 볼륨이 CHAP 인증을 사용합니다.

자세한 단계를 보려면 [디바이스에 CHAP 구성](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication)으로 이동합니다.

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>StorSimple 스냅숏 관리자 암호 구성

StorSimple 스냅숏 관리자 소프트웨어는 Windows 호스트에 상주하며 관리자가 로컬 및 클라우드 스냅숏의 형태로 StorSimple 디바이스의 백업을 관리할 수 있습니다.

> [!NOTE]
> 클라우드 어플라이언스의 경우, Windows 호스트는 Azure 가상 머신입니다.

StorSimple Snapshot Manager에서 디바이스를 구성하면, StorSimple 디바이스 IP 주소 및 암호를 입력하여 저장소 디바이스를 인증하라는 메시지가 표시됩니다. 자세한 단계를 보려면 [StorSimple 스냅숏 관리자 암호 구성](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)으로 이동합니다.

#### <a name="change-the-device-administrator-password"></a>디바이스 관리자 암호 구성

Windows PowerShell 인터페이스를 사용하여 클라우드 어플라이언스에 액세스할 때 디바이스 관리자 암호를 입력해야 합니다. 데이터의 보안을 위해, 클라우드 어플라이언스를 사용하려면 먼저 이 암호를 변경해야 합니다. 자세한 단계를 보려면 [디바이스 관리자 암호 구성](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password)으로 이동합니다.

## <a name="connect-remotely-to-the-cloud-appliance"></a>클라우드 어플라이언스에 원격으로 연결

Windows PowerShell 인터페이스를 통한 클라우드 어플라이언스에 대한 원격 액세스는 기본적으로 사용할 수 없습니다. 클라우드 어플라이언스에 액세스하기 위해서는 클라우드 어플라이언스, 클라이언트 순서로 원격 관리를 사용하도록 설정해야 합니다.

다음 2단계 절차에서는 클라우드 어플라이언스에 원격으로 연결하는 방법을 설명합니다.

### <a name="step-1-configure-remote-management"></a>1단계: 원격 관리 구성

다음 단계에 따라 StorSimple Cloud Appliance에 대한 원격 관리를 구성합니다.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>2단계: 클라우드 어플라이언스에 원격으로 액세스

클라우드 어플라이언스에서 원격 관리를 사용하도록 설정한 후 Windows PowerShell 원격을 사용하여 동일한 가상 네트워크 내 다른 가상 머신에서 어플라이언스에 연결합니다. 예를 들어 iSCSI 연결을 위해 구성 및 사용한 호스트 VM에서 연결할 수 있습니다. 대부분의 배포에서는 클라우드 어플라이언스에 액세스하기 위해 사용할 수 있는 호스트 VM에 액세스하기 위해 공용 엔드포인트가 열려 있습니다.

> [!WARNING]
> **보안 강화를 위해 엔드포인트에 연결할 때 HTTPS를 사용하고 PowerShell 원격 세션을 완료한 후 엔드포인트를 삭제하는 것이 좋습니다.**

[StorSimple 디바이스에 원격으로 연결](storsimple-8000-remote-connect.md)의 절차에 따라 클라우드 어플라이언스에 대한 원격 서비스를 설정해야 합니다.

## <a name="connect-directly-to-the-cloud-appliance"></a>클라우드 어플라이언스에 직접 연결

클라우드 어플라이언스에 직접 연결할 수도 있습니다. Microsoft Azure 환경 외부 또는 가상 네트워크 외부의 다른 컴퓨터에서 클라우드 어플라이언스에 직접 연결하려는 경우 추가 엔드포인트를 만들어야 합니다.

클라우드 어플라이언스에 공용 엔드포인트를 만들려면 다음 단계를 수행합니다.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

가상 네트워크의 공용 엔드포인트의 수를 최소화하기 때문에 동일한 가상 네트워크 내 다른 가상 머신에서 연결하는 것이 좋습니다. 이 경우, 원격 데스크톱 세션을 통해 연결한 다음 로컬 네트워크의 다른 Windows 클라이언트와 같이 가상 머신을 사용하도록 구성하면 됩니다. 포트를 이미 알 수 있으므로 공용 포트 번호를 추가할 필요가 없습니다.

## <a name="get-private-ip-for-the-cloud-appliance"></a>클라우드 어플라이언스에 대한 개인 IP 가져오기

동일한 가상 네트워크에서 호스트 서버에 연결하는 클라우드 어플라이언스의 경우 클라우드 어플라이언스의 내부 또는 개인 IP 주소가 필요합니다. 다음 단계를 수행하여 클라우드 어플라이언스의 개인 IP 주소를 가져오기

1. 클라우드 어플라이언스에 대한 기본 가상 머신으로 이동합니다. 가상 머신에는 클라우드 어플라이언스와 동일한 이름이 있습니다. **모든 리소스**로 이동하고, 클라우드 어플라이언스 및 구독의 이름을 제공하고, 가상 머신으로 유형을 선택합니다. 제공된 가상 머신의 목록에서 클라우드 어플라이언스에 해당하는 가상 머신을 선택하고 클릭합니다.

     ![클라우드 어플라이언스에 대한 가상 머신 선택](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. **설정 > 네트워킹**으로 이동합니다. 오른쪽 창에 클라우드 어플라이언스의 개인 IP 주소가 표시됩니다. 기록해 둡니다.

    ![클라우드 어플라이언스에 대한 개인 IP 주소 가져오기](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance 작업

StorSimple Cloud Appliance를 만들고 구성했으므로 작업을 시작할 준비가 되었습니다. 물리적 StorSimple 디바이스에서처럼 클라우드 어플라이언스에 볼륨 컨테이너, 볼륨 및 백업 정책으로 작업할 수 있습니다. 유일한 차이점은 디바이스 목록에서 클라우드 어플라이언스를 선택했는지 확인해야 한다는 점입니다. 클라우드 어플라이언스에 대한 다양한 관리 작업의 단계별 절차는 [StorSimple 디바이스 관리자 서비스를 사용하여 클라우드 어플라이언스 관리](storsimple-8000-manager-service-administration.md)를 참조하세요.

다음 섹션에서는 클라우드 어플라이언스를 작업할 때 발생하는 차이점 중 일부에 대해 설명합니다.

### <a name="maintain-a-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance 유지 관리

소프트웨어 전용 디바이스이기 때문에 물리적 디바이스에 대한 유지 관리와 비교할 때 클라우드 어플라이언스의 유지 관리는 최소입니다.

클라우드 어플라이언스를 업데이트할 수 없습니다. 새 클라우드 어플라이언스를 만들려면 소프트웨어의 최신 버전을 사용합니다.


### <a name="storage-accounts-for-a-cloud-appliance"></a>클라우드 어플라이언스에 대한 Storage 계정

Storage 계정은 StorSimple 디바이스 관리자 서비스, 클라우드 어플라이언스 및 물리적 디바이스에서 사용하기 위해 만듭니다. 저장소 계정을 만들 때는 친숙한 이름의 지역 식별자를 사용하는 것이 좋습니다. 이렇게 하면 지역은 모든 시스템 구성 요소 전체에 걸쳐 일관성이 보장됩니다. 클라우드 어플라이언스의 경우, 성능 문제를 방지하도록 모든 구성 요소가 동일한 지역에 있는 것이 중요합니다.

단계별 절차를 보려면 [저장소 계정 추가](storsimple-8000-manage-storage-accounts.md#add-a-storage-account)로 이동하세요.

### <a name="deactivate-a-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance 비활성화

클라우드 어플라이언스를 비활성화하면 작업에서 프로비전 시 만들어진 VM 및 리소스를 삭제합니다. 클라우드 어플라이언스가 비활성화된 후 이전 상태로 복원할 수 없습니다. 클라우드 어플라이언스를 비활성화하기 전에 클라우드 어플라이언스에 의존하는 클라이언트와 호스트를 중지하거나 삭제했는지 확인합니다.

클라우드 어플라이언스를 비활성화하면 다음 작업이 수행됩니다.

* 클라우드 어플라이언스가 제거됩니다.
* OS 디스크 및 클라우드 어플라이언스에 대해 만든 데이터 디스크가 제거됩니다.
* 프로비전 중 호스티드 서비스 및 가상 네트워크가 유지됩니다. 사용하지 않는 경우 수동으로 삭제해야 합니다.
* 클라우드 어플라이언스에 대해 생성된 클라우드 스냅숏이 보존됩니다.

단계별 절차는 [StorSimple 디바이스 비활성화 및 삭제](storsimple-8000-deactivate-and-delete-device.md)를 참조하세요.

StorSimple 디바이스 관리자 서비스 블레이드에서 비활성화된 클라우드 어플라이언스로 표시되면 **디바이스** 블레이드의 디바이스 목록에서 해당 클라우드 어플라이언스를 즉시 삭제할 수 있습니다.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>클라우드 어플라이언스 시작, 중지 및 다시 시작
StorSimple 물리적 디바이스와 달리, StorSimple Cloud Appliance에서 누를 전원 켜짐 또는 꺼짐 단추가 없습니다. 그러나 어플라이언스를 중지하고 다시 시작해야 하는 경우가 있을 수 있습니다.

클라우드 어플라이언스를 시작, 중지 및 다시 시작하는 가장 쉬운 방법은 Virtual Machines 서비스 블레이드를 사용하는 것입니다. Virtual Machine 서비스로 이동합니다. VM 목록에서 클라우드 어플라이언스에 해당하는 VM을 식별(같은 이름)하고 VM 이름을 클릭합니다. 가상 머신 블레이드를 보면 클라우드 어플라이언스 상태가 생성된 후 시작되기 때문에 상태는 **실행 중**입니다. 언제든지 가상 머신을 시작하고 중지하고 다시 시작할 수 있습니다.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>공장 기본값으로 재설정
클라우드 어플라이언스로 다시 시작하려는지를 결정한 경우 비활성화 및 삭제한 후 새로 만들면 됩니다.

## <a name="fail-over-to-the-cloud-appliance"></a>클라우드 어플라이언스로 장애 조치(failover)
DR(재해 복구)는 StorSimple Cloud Appliance가 설계된 주요 시나리오 중 하나입니다. 이 시나리오에서는 StorSimple 물리적 디바이스 또는 전체 데이터 센터를 사용하지 못할 수 있습니다. 다행스럽게도 클라우드 어플라이언스를 사용하여 다른 위치에서 작업을 복원할 수 있습니다. DR 중, 원본 디바이스의 볼륨 컨테이너는 소유권을 변경하고 클라우드 어플라이언스로 전송됩니다.

DR의 필수 조건은 다음과 같습니다.

* 클라우드 어플라이언스가 만들어 구성되어야 합니다.
* 볼륨 컨테이너 내의 모든 볼륨이 오프라인 상태여야 합니다.
* 장애 조치하려는 볼륨 컨테이너에 연결된 클라우드 스냅숏이 있어야 합니다.

> [!NOTE]
> * DR에 대한 보조 디바이스로 클라우드 어플라이언스를 사용하는 경우에는 8010에 30TB의 Standard Storage가 있고 8020에 64TB의 Premium Storage가 있다는 것을 기억합니다. 높은 용량의 8020 클라우드 어플라이언스가 DR 시나리오에 보다 적합할 수 있습니다.

단계별 절차를 보려면 [클라우드 어플라이언스로 장애 조치](storsimple-8000-device-failover-cloud-appliance.md)로 이동합니다.

## <a name="delete-the-cloud-appliance"></a>클라우드 어플라이언스 삭제
StorSimple Cloud Appliance를 이전에 구성하고 사용했지만 이제 용도에 맞게 계산 비용 발생을 중지하려는 경우, 클라우드 어플라이언스를 종료해야 합니다. 클라우드 어플라이언스를 중지하면 VM의 할당이 취소됩니다. 이 작업은 구독에서 요금이 발생되지 않도록 합니다. 그러나 OS 및 데이터 디스크에 대한 저장소 비용은 계속 발생합니다.

모든 요금을 중지하려면 클라우드 어플라이언스를 삭제해야 합니다. 클라우드 어플라이언스에서 만든 백업을 삭제하려면 디바이스를 비활성화하거나 삭제할 수 있습니다. 자세한 내용은 [StorSimple 디바이스 비활성화 및 삭제](storsimple-8000-deactivate-and-delete-device.md)를 참조하세요.

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>인터넷 연결 오류 문제 해결
클라우드 어플라이언스를 만드는 동안 인터넷에 연결되지 않은 경우 생성 단계가 실패합니다. 인터넷 연결 오류 문제를 해결하려면 Azure Portal에서 다음 단계를 수행합니다.

1. [Azure Portal에서 Windows 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) 이 가상 머신은 클라우드 어플라이언스에서 사용한 동일한 저장소 계정, VNet 및 서브넷을 사용해야 합니다. Azure에서 동일한 저장소 계정, VNet 및 서브넷을 사용하는 기존 Windows Server 호스트가 있는 경우 인터넷 연결 문제를 해결하는 데 사용할 수 있습니다.
2. 이전 단계에서 만든 가상 머신에 원격 로그인합니다.
3. 가상 머신 내의 명령 창을 엽니다(Win + R 및 `cmd`을 입력).
4. 프롬프트에서 다음 cmd를 실행합니다.

    `nslookup windows.net`
5. `nslookup`이 실패하는 경우 인터넷 연결 실패로 인해 클라우드 어플라이언스를 StorSimple 디바이스 관리자 서비스에 등록하지 못하게 됩니다.
6. 가상 네트워크에 필요한 변경 사항을 수행하여 클라우드 어플라이언스가 _windows.net_과 같은 Azure 사이트에 액세스할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계
* [StorSimple 디바이스 관리자 서비스를 사용하여 클라우드 어플라이언스를 관리](storsimple-8000-manager-service-administration.md)하는 방법을 알아봅니다.
* [백업 세트에서 StorSimple 볼륨을 복원](storsimple-8000-restore-from-backup-set-u2.md)하는 방법을 알아봅니다.
