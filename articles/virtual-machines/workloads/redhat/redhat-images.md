---
title: Azure의 Red Hat Enterprise Linux 이미지 | Microsoft Docs
description: Microsoft Azure의 Red Hat Enterprise Linux 이미지에 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 5ed5d9337dd4e7acdbba25c4cb66d2690793f250
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134402"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 이미지 개요
이 문서에서는 명명 및 보존 정책에 대한 정책과 함께 Azure Marketplace에서 사용할 수 있는 RHEL(Red Hat Enterprise Linux) 이미지를 설명합니다.

모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다. 가격 책정에 대 한 자세한 내용은 [Azure 가격 계산기](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 참조 하세요.

>[!IMPORTANT]
> Azure Marketplace에서 현재 사용할 수 있는 RHEL 이미지는 BYOS(Bring-Your-Own-Subscription) 또는 PANG(종량제) 라이선스 모델을 지원합니다. [Azure Hybrid Use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) 및 BYOS와 PAYG 간의 동적 전환은 지원되지 않습니다. 라이선스 모드를 전환하려면 해당 이미지에서 VM을 다시 배포해야 합니다.

>[!NOTE]
> Azure marketplace에서 RHEL 이미지와 관련 된 모든 문제에 대해 Microsoft에 지원 티켓을 제출 하세요.

## <a name="viewing-images-available-in-azure"></a>Azure에서 사용할 수 있는 이미지 보기
Marketplace에서 "Red Hat"를 검색 하거나 Azure Portal UI에서 리소스를 만들 때 사용 가능한 모든 RHEL 이미지의 하위 집합만 표시 됩니다. Azure CLI/PowerShell/API를 사용하여 항상 사용 가능한 VM 이미지의 전체 세트를 얻을 수 있습니다.

Azure에서 사용 가능한 Red Hat 이미지의 전체 세트를 보려면 다음 명령을 실행합니다.

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>명명 규칙
Azure에서 VM 이미지는 게시자, 제품, SKU 및 버전별로 구성됩니다. 게시자:제품:SKU:버전의 조합은 이미지 URN이며 사용할 이미지를 고유하게 식별합니다.

예를 들어 `RedHat:RHEL:7-LVM:7.6.2018103108`은 2018 년 10 월 31 일에 빌드된 RHEL 7.6 LVM 분할 이미지를 나타냅니다.

RHEL 7.6 VM을 만드는 방법의 샘플은 다음과 같습니다.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"최신" 모니커
Azure REST API는 특정 버전이 아닌 버전에 대해 모니커 "최신"을 사용할 수 있습니다. "최신"을 사용하면 지정된 게시자, 제품 및 SKU에 대해 사용 가능한 최신 이미지를 프로비전합니다.

예를 들어 `RedHat:RHEL:7-LVM:latest`는 사용 가능한 최신 RHEL 7 제품군 LVM 분할 이미지를 참조 합니다.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> 일반적으로 최신을 결정하는 버전의 비교는 [CompareTo 메서드](https://msdn.microsoft.com/library/a5ts8tb6.aspx)의 규칙을 따릅니다.
이 이미지 버전 비교는 문자열이 아닌 [버전](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) 개체로 값을 비교 하 여 수행 됩니다.

## <a name="rhel-6-image-types"></a>RHEL 6 이미지 형식
RHEL .x 이미지의 경우 이미지 형식은 다음과 같습니다.

|게시자 | 제안 | SKU 값 | 버전 | 세부 정보
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 부 버전 (예: 6.9) | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 6.9.2018010506) | 모든 표준 RHEL .x 이미지는이 규칙을 따릅니다.
|RedHat | rhel byos | rhel-raw69 | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 6.9.20181023) | 이 이미지는 RHEL 6.9 BYOS 이미지입니다.
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 6.8.2017053118) | 이는 RHEL 6.8 for SAP Applications 이미지입니다. SAP 응용 프로그램 리포지토리 뿐만 아니라 기본 RHEL 리포지토리에 액세스할 수 있습니다.
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 6.7.2017053121) | SAP HANA 이미지에 대 한 RHEL 6.7입니다. 기본 RHEL 리포지토리 뿐만 아니라 SAP HANA 리포지토리에 액세스할 수 있습니다.

## <a name="rhel-7-image-types"></a>RHEL 7 이미지 형식
RHEL 7.x 이미지의 경우 몇 가지 다른 이미지 유형이 있습니다. 다음 표에서는 제공 하는 다양 한 이미지 집합을 보여 줍니다. Az CLI 명령 `az vm image list --publisher redhat --all`를 사용 하 여 전체 목록을 볼 수 있습니다.

>[!NOTE]
> 달리 지정 되지 않은 경우 모든 이미지는 LVM으로 분할 되 고 일반 RHEL 리포지토리에 연결 됩니다 (예: E4S가 아닌 EUS). 앞으로는 LVM 분할 이미지만 게시로 전환 하지만이 결정에 대 한 피드백을 공개 합니다. SAP 용 확장 업데이트 지원 및 업데이트 서비스에 대 한 자세한 내용은 [Red Hat Enterprise Linux 수명 주기 페이지](https://access.redhat.com/support/policy/updates/errata)에서 확인할 수 있습니다.

|게시자 | 제안 | SKU 값 | 버전 | 세부 정보
|----------|-------|------------|---------|--------
|RedHat | RHEL | 부 버전 (예: 7.6) | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.6.2019102813) | 4 월 2019 일 전에 게시 된 이미지는 표준 RHEL 리포지토리에 연결 됩니다. 4 월 2019 일 이후에 게시 된 이미지는 Red Hat의 EUS (확장 업데이트 지원) 리포지토리에 연결 되어 특정 부 버전의 버전 잠금을 허용 합니다. 일반 리포지토리를 사용 하는 고객은 SKU 값에서 7LVM 또는 7-RAW를 포함 하는 이미지를 사용 해야 합니다 (아래 세부 정보). RHEL 7.7 이상 이미지는 LVM으로 분할 됩니다. 이 범주의 다른 모든 이미지는 원시 분할 됩니다.
|RedHat | RHEL | 7-RAW | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.6.2019102813) | 이러한 이미지는 원시 분할 됩니다 (즉, 논리적 볼륨이 추가 되지 않음).
|RedHat | RHEL | 7-RAW-CI | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.6.2019072418) | 이러한 이미지는 원시 분할 되어 있으며 (즉, 논리적 볼륨이 추가 되지 않음) 프로 비전을 위해 클라우드 초기화를 사용 합니다.
|RedHat | RHEL | 7-LVM | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.6.2019062414) | 이러한 이미지는 LVM으로 분할 됩니다.
|RedHat | rhel byos | rhel-{lvm, raw} | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.7.20190819) | 이러한 이미지는 RHEL 7 BYOS 이미지입니다. 모든 리포지토리에 연결 되지 않으며 RHEL 프리미엄 요금을 청구 하지 않습니다. RHEL BYOS 이미지에 관심이 있는 경우 [액세스를 요청](https://aka.ms/rhel-byos)하세요. SKU 값은 부 버전으로 종료 되며 이미지가 raw 또는 LVM 분할 인지 여부를 나타냅니다. 예를 들어 SKU 값 rhel-lvm77는 LVM 분할 RHEL 7.7 이미지를 나타냅니다.
|RedHat | RHEL | RHEL-SAP | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.6.2019071300) | 이러한 이미지는 SAP 이미지용으로 RHEL 됩니다. RHEL E4S 리포지토리 뿐만 아니라 SAP HANA 및 응용 프로그램 리포지토리에 액세스할 수 있습니다. 청구에는 기본 계산 요금 위에 RHEL premium 및 SAP premium이 포함 됩니다.
|RedHat | RHEL | RHEL-HA | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.6.2019062320) | 이러한 이미지는 고가용성 및 업데이트 서비스 이미지를 사용 하는 SAP에 RHEL. SAP HANA 및 응용 프로그램 리포지토리와 고가용성 리포지토리 뿐만 아니라 RHEL E4S 리포지토리에 액세스할 수 있습니다. 청구에는 기본 계산 요금 위에 RHEL premium, SAP premium 및 HA premium이 포함 됩니다.
|RedHat | RHEL | RHEL-HA | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.6.2019062019) | 이러한 이미지는 고가용성 추가 기능에 액세스할 수 있는 RHEL 이미지입니다. HA 추가 기능 프리미엄으로 인해 RHEL 및 기본 계산 요금 위에 약간의 요금이 부과 됩니다.
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.3.2017053118) | 이러한 이미지는 sap 응용 프로그램 및 SAP HANA 리포지토리가 SAP 리포지토리로 결합 되어 최신 상태가 아닙니다. 이러한 이미지는 SAP 응용 프로그램 이미지에 대 한 RHEL입니다. SAP 응용 프로그램 리포지토리 뿐만 아니라 기본 RHEL 리포지토리에 액세스할 수 있습니다.
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 7.3.2018051421) | 이러한 이미지는 sap 응용 프로그램 및 SAP HANA 리포지토리가 SAP 리포지토리로 결합 되어 최신 상태가 아닙니다. RHEL for SAP HANA 이미지입니다. 기본 RHEL 리포지토리 뿐만 아니라 SAP HANA 리포지토리에 액세스할 수 있습니다.

## <a name="rhel-8-image-types"></a>RHEL 8 이미지 형식
RHEL 8 이미지 형식에 대 한 세부 정보는 아래와 같습니다.

|게시자 | 제안 | SKU 값 | 버전 | 세부 정보
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 8.0.20191023) | 이러한 이미지는 표준 Red Hat 리포지토리에 연결 된 RHEL 8.0 LVM 분할 이미지입니다.
|RedHat | RHEL | 8-gen2 | RHEL 부 버전의 연결 된 값 및 게시 된 날짜 (예: 8.0.20191024) | 이러한 이미지는 표준 Red Hat 리포지토리에 연결 된 Hyper-v 세대 2 RHEL 8.0 LVM 분할 이미지입니다. Azure의 2 세대 Vm에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)에서 제공 됩니다.

## <a name="rhel-longer-support-add-ons"></a>RHEL 더 이상 추가 기능을 지원 합니다.

### <a name="extended-update-support-eus"></a>EUS (확장 업데이트 지원)
2019 4 월에는 기본적으로 EUS (확장 업데이트 지원) 리포지토리에 연결 된 RHEL 이미지를 사용할 수 있습니다. RHEL EUS에 대 한 자세한 내용은 [Red Hat 설명서](https://access.redhat.com/articles/rhel-eus)에서 제공 됩니다.

EUS 리포지토리로 전환 하는 것이 가능 하며 지원 됩니다. VM을 EUS로 전환 하는 방법에 대 한 지침과 EUS 지원 기간에 대 한 자세한 정보는 [여기](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)에서 확인할 수 있습니다.

>[!NOTE]
> EUS는 RHEL 특별 기능에서 지원 되지 않습니다. 즉, 일반적으로 RHEL 특별 채널에서 사용할 수 있는 패키지를 설치 하는 경우 EUS에서이 작업을 수행할 수 없습니다. Red Hat 제품 수명 주기는 [여기](https://access.redhat.com/support/policy/updates/extras/)에 자세히 설명 되어 있습니다.

#### <a name="differentiating-between-regular-and-eus-images"></a>일반 및 EUS 이미지를 구별 합니다.
EUS 리포지토리에 연결 된 이미지를 사용 하려는 고객은 SKU에 RHEL 부 버전 번호가 포함 된 RHEL 이미지를 사용 해야 합니다.

예를 들어 다음과 같은 두 RHEL 7.4 이미지를 사용할 수 있습니다.
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
이 경우 `RedHat:RHEL:7.6:7.6.2019102813`는 기본적으로 EUS 리포지토리에 연결 되 고 (SKU 값 7.4), `RedHat:RHEL:7-LVM:7.6.2019062414` 기본적으로 EUS 리포지토리에 연결 됩니다 (SKU 값 7-LVM).

일반 (비 EUS) 리포지토리를 사용 하려는 경우 SKU에 부 버전 번호가 포함 되지 않은 이미지를 사용 하 여 배포 합니다.

#### <a name="rhel-images-with-eus"></a>EUS를 사용 하 여 이미지 RHEL
다음 표는 EUS 리포지토리에 연결 된 RHEL 이미지에 적용 됩니다.

>[!NOTE]
> 이 문서를 작성할 당시에는 RHEL 7.4 이상 부 버전 에서만 EUS를 지원 합니다. RHEL < = 7.3에 대 한 EUS는 더 이상 지원 되지 않습니다.
>
> RHEL EUS 가용성에 대 한 자세한 내용은 [여기](https://access.redhat.com/support/policy/updates/errata)를 참조 하세요.

부 버전 |EUS 이미지 예제              |EUS 상태                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | 4 월 2019 일에 게시 된 이미지는 기본적으로 EUS입니다.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 6 월 2019 일 이후 게시 된 이미지는 기본적으로 EUS입니다. |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 2019 이상 게시 된 이미지는 기본적으로 EUS입니다.  |
RHEL 8.0      |N/A                            | Red Hat에서 제공 되는 EUS 없음                               |

### <a name="update-services-for-sap-e4s"></a>SAP 용 업데이트 서비스 (E4S)
Sap 용 최신 RHEL 이미지는 SAP 솔루션 구독 용 업데이트 서비스 (E4S)에 연결 됩니다. E4S에 대 한 자세한 내용은 Red Hat [설명서](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)에서 확인할 수 있습니다.

#### <a name="rhel-images-with-e4s"></a>E4S를 사용 하 여 이미지 RHEL
12 월 2019 일 이후에 생성 된 다음 제품의 이미지는 E4S 리포지토리에 연결 됩니다.

* RHEL (SAP 용 RHEL)
* RHEL (HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL)

## <a name="other-available-offers-and-skus"></a>기타 사용 가능한 제품 및 Sku
사용 가능한 제품 및 SKU의 전체 목록은 위의 표에 나열된 것 이외에 추가 이미지를 포함할 수 있습니다(예: `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`). 이러한 제품은 특정 마켓플레이스 솔루션의 지원을 제공하는 데 사용할 수 있거나 미리 보기 및 테스트 목적으로 게시될 수 있습니다. 경고 없이 언제든지 변경되거나 제거될 수 있습니다. 현재 상태가 Microsoft 또는 Red Hat에서 공개적으로 문서화되지 않는 한 사용하지 마십시오.

## <a name="publishing-policy"></a>정책 게시
Microsoft 및 Red Hat은 특정 CVE를 해결하기 위해 필요에 따라 또는 가끔 구성 변경/업데이트를 위해 새로운 부 버전이 출시될 때 이미지를 업데이트합니다. CVE 수정 사항의 릴리스 또는 가용성 이후 영업일 3일 이내에 가능한 빨리 업데이트된 이미지를 제공하기 위해 노력합니다.

지정된 이미지 제품군의 현재 부 릴리스만 업데이트합니다. 최신 부 버전 릴리스로 이전 부 버전 업데이트를 중지합니다. 예를 들어 RHEL 7.6의 릴리스로 RHEL 7.5 이미지는 더 이상 업데이트되지 않습니다.

>[!NOTE]
> RHEL 종량제 이미지에서 프로비전된 활성 Azure VM은 Azure RHUI에 연결되며 Red Hat에서 릴리스되고 Azure RHUI에 복제되는 대로 즉시 업데이트 및 수정 사항을 받을 수 있습니다(일반적으로 Red Hat의 공식 릴리스 후 24시간 이내). 이러한 VM에는 업데이트를 위해 게시된 새 이미지가 필요하지 않으며 고객은 업데이트를 시작하는 시기에 대한 모든 권한을 갖습니다.

## <a name="image-retention-policy"></a>이미지 보존 정책
현재 정책은 이전에 게시된 모든 이미지를 유지하는 것입니다. 모든 종류의 문제를 일으키는 것으로 알려진 이미지를 제거할 권리를 보유합니다. 예를 들어 후속 플랫폼 또는 구성 요소 업데이트로 인한 잘못된 구성의 이미지는 제거될 수 있습니다. 제거될 수 있는 이미지는 이미지 제거 전 최대 30일 알림을 제공하도록 현재 Marketplace 정책을 따릅니다.

## <a name="next-steps"></a>다음 단계
* [Azure에서 RHEL 이미지](./redhat-imagelist.md)의 전체 목록을 확인 합니다.
* [여기](https://aka.ms/rhui-update)에서 Azure Red Hat 업데이트 인프라에 대해 자세히 알아봅니다.
* [RHEL BYOS 제품](./byos.md)에 대해 자세히 알아보세요.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
