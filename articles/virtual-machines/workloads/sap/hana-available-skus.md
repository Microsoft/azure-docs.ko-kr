---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 SKU | Microsoft Docs
description: Azure(대규모 인스턴스)의 SAP HANA에 대한 SKU입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b80f872c82061c0cb87f4f1e2714183e71cf02cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794015"
---
# <a name="available-skus-for-hli"></a>HLI에 사용 가능한 SKU

SAP HANA on Azure(대규모 인스턴스) 서비스는 미국 서부, 미국 동부, 오스트레일리아 동부, 오스트레일리아 남동부, 유럽 서부, 북유럽, 일본 동부 및 일본 서부의 Azure 지역에서 여러 구성으로 사용할 수 있습니다.

[HANA 대규모 인스턴스의 SAP HANA 인증 SKU](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)는 다음과 유사합니다.

| SAP 솔루션 | CPU | 메모리 | Storage | 가용성 |
| --- | --- | --- | --- | --- |
| OLAP에 대해 최적화됨: SAP BW, BW/4HANA<br /> 또는 SAP HANA(일반 OLAP 워크로드용) | Azure S72에서 SAP HANA<br /> – 2 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 36 CPU 코어 및 72 CPU 스레드 |  768 GB |  3 TB | 더 이상 제공되지 않음 |
| --- | Azure S144에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 72 CPU 코어 및 144 CPU 스레드 |  1.5 TB |  6 TB | 더 이상 제공되지 않음 |
| --- | Azure S192에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 96 CPU 코어 및 192 CPU 스레드 |  2.0 TB |  8 TB | 사용 가능 |
| --- | Azure S384에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  4.0 TB |  16 TB | 사용 가능 |
| OLTP에 대해 최적화됨: SAP Business Suite<br /> SAP HANA 또는 S/4HANA(OLTP)에서,<br /> 일반 OLTP | Azure S72m에서 SAP HANA<br /> – 2 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 36 CPU 코어 및 72 CPU 스레드 |  1.5 TB |  6 TB | 더 이상 제공되지 않음 |
|---| Azure S144m에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v3<br /> 72 CPU 코어 및 144 CPU 스레드 |  3.0 TB |  12 TB | 더 이상 제공되지 않음 |
|---| Azure S192m에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 96 CPU 코어 및 192 CPU 스레드  |  4.0 TB |  16 TB | 사용 가능 |
|---| Azure S384m에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  6.0 TB |  18 TB | 사용 가능 |
|---| Azure S384xm에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  8.0 TB |  22 TB |  사용 가능 |
|---| Azure의 SAP HANA S576m<br /> – 12 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 288 CPU 코어 및 576 CPU 스레드 |  12.0 TB |  28 TB | 사용 가능 |
|---| Azure의 SAP HANA S768m<br /> – 16 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 384 CPU 코어 및 768 CPU 스레드 |  16.0 TB |  36 TB | 사용 가능 |
|---| Azure의 SAP HANA S960m<br /> – 20 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 480 CPU 코어 및 960 CPU 스레드 |  20.0 TB |  46 TB | 사용 가능 |


SAP HANA TDIv5에서 SAP는 고객 특정 규모 지정과 고객 특정 프로젝트를 허용하는데, 다음에서 인증으로 나열되지 않은 서버 구성으로 이어질 수 있습니다.

- [SAP HANA 인증 어플라이언스](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

많은 경우 이러한 고객 특정 서버 구성에는 SAP 인증 서버 단위보다 더 많은 메모리가 따릅니다. SAP 작업 시 고객은 SAP 지원을 받을 수 있고 고객 특정 규모 서버 구성에 대해 확인할 수 있습니다. Azure에서는 다음 HANA 대규모 인스턴스 표준 SKU를 사용할 수 있고 이러한 TDIv5 고객 특정 규모 프로젝트의 Microsoft 가격 목록이 제공됩니다.

| SKU|CPU | 메모리 | Storage | 가용성 |
| ---| --- | --- | --- | --- |
| S96 | Azure의 SAP HANA S96<br /> – 2개 Intel® Xeon® Processor E7-8890 v4,<br /> 48개 CPU 코어 및 96개 CPU 스레드 |  768 GB |  3 TB | 사용 가능 |


| 메모리 확장이 가능한 <br /> 원래의 SKU | CPU | 메모리 | Storage | 가용성 |
| --- | --- | --- | --- | --- |
| S192m 확장 가능 | Azure S192xm에서 SAP HANA<br /> – 4 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 96 CPU 코어 및 192 CPU 스레드 |  6.0 TB |  16 TB | 사용 가능 |
| S384xm 확장 가능 | Azure S384xxm에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  12.0 TB |  28 TB | 사용 가능 |
| S576m 확장 가능 | Azure S576xm에서 SAP HANA<br /> – 12 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 288 CPU 코어 및 576 CPU 스레드 |  18.0TB |  41TB | 사용 가능 |
| S768m 확장 가능 | Azure S768xm에서 SAP HANA<br /> – 16 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 384 CPU 코어 및 768 CPU 스레드 |  24.0TB |  56TB | 사용 가능 |

- CPU 코어 = 서버 단위 프로세서 합계의 비하이퍼 스레드 CPU 코어 합계
- CPU 스레드 = 서버 단위 프로세서 합계의 하이퍼 스레드된 CPU 코어에서 제공하는 컴퓨팅 스레드 합계. 대부분의 단위는 기본적으로 하이퍼 스레딩 기술을 사용하도록 구성됩니다.
- 공급업체 권고에 따라 S768m, S768xm 및 S960m은 SAP HANA 실행에 하이퍼 스레딩을 사용하도록 구성되지 않았습니다.


선택한 특정 구성은 워크로드, CPU 리소스 및 원하는 메모리에 따라 달라집니다. OLTP 워크로드에서 OLAP 워크로드에 최적화된 SKU를 사용할 수 있습니다. 

고객 특정 규모 프로젝트 단위를 제외하고 제공되는 하드웨어 기준은 SAP HANA TDI 인증입니다.  SKU를 구분하는 두 가지 하드웨어 클래스는 다음과 같습니다.

- SKU의 '유형 I 클래스': S72, S72m, S96, S144, S144m, S192, S192m 및 S192xm
- SKU의 '유형 II 클래스': S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm 및 S960m

HANA 대규모 인스턴스 스탬프 전체는 단일 고객용으로만 할당되지 않습니다. 이 사실은 Azure에 배포된 네트워크 패브릭을 통해 연결된 계산 및 스토리지 리소스 랙에도 적용됩니다. HANA 대규모 인스턴스 인프라는 Azure와 마찬가지로 다음 세 가지 수준에서 서로 격리된 서로 다른 고객 &quot;테넌트&quot;를 배포합니다.

- **네트워크**: HANA 대규모 인스턴스 스탬프 내에서 가상 네트워크를 통해 격리됩니다.
- **저장소**: 스토리지 볼륨을 할당하고 테넌트 간 스토리지 볼륨을 격리하는 스토리지 가상 머신을 통해 격리됩니다.
- **컴퓨팅**: 서버 단위를 단일 테넌트에 전용으로 할당합니다. 서버 장치에 대한 하드 분할 또는 소프트 분할이 없습니다. 테넌트 간 단일 서버 또는 호스트 단위의 공유 없음. 

서로 다른 테넌트 간의 HANA 대규모 인스턴스 장치 배포는 상호 간에 표시되지 않습니다. 서로 다른 테넌트에 배포된 HANA 대규모 인스턴스 장치는 HANA 대규모 인스턴스 스탬프 수준에서 서로 직접 통신 할 수 없습니다. 한 테넌트 내의 HANA 대규모 인스턴스 장치만 HANA 대규모 인스턴스 스탬프 수준에서 서로 통신할 수 있습니다.

대규모 인스턴스 스탬프에 배포된 테넌트는 청구를 위해 하나의 Azure 구독에 할당됩니다. 네트워크의 경우 동일한 Azure 등록 내에 있는 다른 Azure 구독의 가상 네트워크에서 액세스할 수 있습니다. 또한 동일한 Azure 지역의 또 다른 Azure 구독을 사용하여 배포할 경우 별도의 HANA 대규모 인스턴스 테넌트를 요청하기로 선택할 수 있습니다.

HANA 대규모 인스턴스에서 SAP HANA를 실행하는 것과 Azure에 배포된 VM에서 SAP HANA를 실행하는 것 사이에는 중요한 차이가 있습니다.

- Azure(큰 인스턴스)의 SAP HANA의 경우 가상화 계층이 없습니다. 기본적인 완전 복구 하드웨어의 성능을 얻을 수 있습니다.
- Azure와 달리 Azure(큰 인스턴스) 서버에서 SAP HANA는 특정 고객이 전용으로 사용할 수 있습니다. 서버 장치 또는 호스트가 하드 또는 소프트 분할될 가능성이 없습니다. 결과적으로 HANA 대규모 인스턴스 장치는 테넌트에 전체적으로 할당된 대로 사용되며, 이에 따라 사용자에게 제공됩니다. 서버를 다시 부팅하거나 종료해도 운영 체제 및 SAP HANA가 다른 서버에 자동으로 배포되지 않습니다. (유형 I 클래스 SKU의 경우, 유일한 예외는 서버에서 문제가 발생하고 다른 서버에서 다시 배포해야 하는 경우입니다.)
- Azure와 달리 최고의 가성비를 위해 호스트 프로세서 유형을 선택한 경우 Azure(큰 인스턴스)에서 SAP HANA에 대해 선택한 프로세서 유형은 Intel E7v3 및 E7v4 프로세서 제품군에서 최고의 성능을 보입니다.

**다음 단계**
- [HLI 크기 조정](hana-sizing.md) 참조
