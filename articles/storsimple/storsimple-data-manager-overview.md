---
title: Microsoft Azure StorSimple 데이터 관리자 개요 | Microsoft Docs
description: StorSimple Data Manager 서비스 개요 제공
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: vidarmsft
ms.openlocfilehash: c5ffe3ec2ec3cb06297df6be4ba7021f692633bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630702"
---
# <a name="storsimple-data-manager-solution-overview"></a>StorSimple 데이터 관리자 솔루션 개요

## <a name="overview"></a>개요

Microsoft Azure StorSimple은 클라우드 저장소를 온-프레미스 솔루션의 확장으로 사용하여 자동으로 온-프레미스 솔루션 및 클라우드에서 데이터를 계층화합니다. 최대 효율성 및 비용 절감을 위해 데이터는 중복 제거 및 압축된 형식으로 클라우드에 저장됩니다. 데이터가 StorSimple 형식으로 저장되면 사용하려고 할 수 있는 다른 클라우드 애플리케이션에서 쉽게 사용할 수 없게 됩니다.

StorSimple 데이터 관리자를 사용하면 클라우드의 StorSimple 형식 데이터를 원활하게 액세스하고 사용할 수 있습니다. StorSimple 형식을 기본 Blob 및 파일로 변환하여 Azure Media Services, Azure HDInsights, Azure Machine Learning 등의 다른 서비스에서 사용할 수 있도록 합니다.

이 문서에서는 StorSimple 데이터 관리자 솔루션을 대략적으로 설명합니다. 또한 이 서비스를 사용하여 클라우드에서 StorSimple 데이터 및 기타 Azure 서비스를 사용하는 애플리케이션을 작성하는 방법을 설명합니다.

## <a name="how-it-works"></a>작동 원리

StorSimple 데이터 관리자 서비스는 StorSimple 8000 시리즈 온-프레미스 디바이스에서 클라우드의 StorSimple 데이터를 식별합니다. 클라우드의 StorSimple 데이터는 중복 제거되고 압축된 StorSimple 형식입니다. 이 데이터 관리자 서비스는 StorSimple 형식 데이터를 추출하여 Azure Blob 및 Azure Files 등의 다른 형식으로 변환하는 API를 제공합니다. 이러한 변환된 데이터는 Azure HDInsight 및 Azure Media Services에서 쉽게 사용될 수 있습니다. 따라서 이러한 데이터 변환을 통해 이러한 서비스는 StorSimple 8000 시리즈 온-프레미스 디바이스에서 변환된 StorSimple 데이터에 작동할 수 있습니다. 이 흐름은 다음 다이어그램에서 설명됩니다.

![높은 수준의 다이어그램](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>데이터 관리자 사용 사례

Azure Functions, Azure Automation 및 Azure Data Factory에서 이 데이터 관리자를 사용하여 StorSimple로 들어오는 데이터에 워크플로가 실행되도록 할 수 있습니다. Azure Media Services를 사용하여 StorSimple에 저장하는 미디어 콘텐츠를 처리하거나, 해당 데이터에 대해 Machine Learning 알고리즘을 실행하거나, Hadoop 클러스터를 불러와 StorSimple에 저장하는 데이터를 분석할 수 있습니다. Azure의 방대한 서비스 배열이 StorSimple의 데이터와 결합된 경우 데이터의 능력이 극대화될 수 있습니다.


## <a name="region-availability"></a>지역 가용성

StorSimple 데이터 관리자는 다음 7개 지역에서 사용할 수 있습니다.

 - 동남아시아
 - 미국 동부
 - 미국 서부
 - 미국 서부 2
 - 미국 중서부
 - 유럽 북부
 - 서유럽

그러나 다음 지역에서는 StorSimple 데이터 관리자를 사용하여 데이터를 변환할 수 있습니다. 

![데이터에 사용 가능한 지역](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

이 집합은 지위 역의 리소스 배포를 통해 아래 지역의 변환 프로세스를 실행할 수 있으므로 규모가 더 큽니다. 따라서 데이터가 19개 지역 중 하나에 있는 한, 이 서비스를 사용하여 데이터를 변환할 수 있습니다.


## <a name="choosing-a-region"></a>지역 선택

다음이 권장됩니다.
 - 원본 저장소 계정(StorSimple 디바이스와 연결된 계정) 및 대상 저장소 계정(데이터를 네이티브 형식으로 유지하려는 계정)은 같은 Azure 지역에 있는 것이 좋습니다.
 - StorSimple 저장소 계정을 포함하는 지역에서 데이터 관리자 및 작업 정의를 불러옵니다. 이것이 가능하지 않은 경우, 가장 가까운 Azure 지역에서 데이터 관리자를 불러온 다음, StorSimple 저장소 계정과 동일한 지역에 작업 정의를 만듭니다. 

    StorSimple 저장소 계정 작업 정의 생성을 지 원하는 26 개 지역에 없는 경우에 긴 대기 시간 및 잠재적인 송신 요금을 표시 된 대로 StorSimple 데이터 관리자 실행 하지 않는 것이 좋습니다.
    
Microsoft Azure 서비스는 항상 사용 가능한 모든 지역에서 확인 하려고 합니다. 그러나 특정 지역의 짧은 기간에 대 한 계획 되지 않은 서비스 중단이 발생할 수 있습니다. 이러한 경우에, 가동 중단의 영향을 받지 않는 지역에서 데이터 관리자 및 작업 정의 표시 하 고 변환 작업을 실행할 수 있습니다. 이러한 시나리오에서 일부 추가 지연이 발생할 수 있지만이 드물게 지역 가동 중단에서에서 복구 전략을 수 있습니다.

## <a name="security-considerations"></a>보안 고려 사항

StorSimple 데이터 관리자는 StorSimple 형식에서 네이티브 형식으로 변환하기 위해 서비스 데이터 암호화 키가 필요합니다. 서비스 데이터 암호화 키는 첫 번째 디바이스가 StorSimple 서비스에 등록될 때 생성됩니다. 이 키에 대한 자세한 내용은 [StorSimple 보안](storsimple-8000-security.md)으로 이동합니다.

입력으로 제공되는 서비스 데이터 암호화 키는 데이터 관리자를 만들 때 생성되는 Key Vault에 저장됩니다. 이 자격 증명 모음은 StorSimple 데이터 관리자와 동일한 Azure 지역에 있습니다. 이 키는 데이터 관리자 서비스를 삭제하면 삭제됩니다.

이 키는 계산 리소스에서 변환을 수행하는 데 사용합니다. 이러한 게산 리소스는 작업 정의와 동일한 Azure 지역에 있습니다. 이 지역은 데이터 관리자를 불러오는 지역과 같은 지역일 수도 있고 다른 지역일 수도 있습니다.

데이터 관리자 지역이 작업 정의 지역과 다른 경우, 이러한 각 지역에 있는 데이터/메타데이터를 이해하는 것이 중요합니다. 다음 다이어그램에서는 데이터 관리자 및 작업 정의에 대해 서로 다른 지역을 유지할 때의 결과를 보여 줍니다.

![다른 지역의 서비스 및 작업 정의](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>개인 정보 관리

StorSimple Data Manager는 개인 정보를 수집하거나 표시하지 않습니다. 자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침을 검토합니다.

## <a name="known-limitations"></a>알려진 제한 사항

서비스에는 현재 다음과 같은 제한 사항이 있습니다.
- StorSimple Data Manager는 현재 BitLocker 암호화된 볼륨으로 작동되지 않습니다. 암호화된 드라이브를 사용하여 서비스를 실행하려는 경우 작업 오류가 표시됩니다.
- 파일의 일부 메타데이터(ACL 포함)는 변환된 데이터 내에 유지되지 않습니다.
- 이 서비스는 NTFS 볼륨에서만 작동합니다.
- 파일 경로 길이는 256자 이하이어야 하며 그렇지 않은 경우 작업이 실패합니다.

## <a name="next-steps"></a>다음 단계

[StorSimple 데이터 관리자 UI를 사용하여 데이터를 변환합니다](storsimple-data-manager-ui.md).
