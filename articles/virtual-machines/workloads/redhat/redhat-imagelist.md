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
ms.openlocfilehash: e6109a87750e588b12bfc9836c5db3db55420ec2
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133792"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure에서 사용할 수 있는 Red Hat Enterprise Linux (RHEL) 이미지
Azure는 다양 한 사용 사례에 대 한 다양 한 RHEL 이미지를 제공 합니다.

> [!NOTE]
> 모든 RHEL 이미지는 Azure 공용 및 Azure Government 클라우드에서 사용할 수 있습니다. Azure 중국 클라우드에서 사용할 수 없습니다.

## <a name="list-of-rhel-images"></a>RHEL 이미지 목록
Azure에서 사용할 수 있는 RHEL 이미지 목록입니다. 달리 명시 되지 않은 경우 모든 이미지는 LVM으로 분할 되 고 일반 RHEL 리포지토리에 연결 됩니다 (E4S가 아닌 EUS는 아님). 다음 이미지는 현재 일반적으로 사용할 수 있습니다.

제안| SKU | 분할 | 프로비전 | 참고
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 에이전트 |
|             | 6.8      | RAW    | Linux 에이전트 |
|             | 6.9      | RAW    | Linux 에이전트 |
|             | 6.10     | RAW    | Linux 에이전트 |
|             | 7-RAW    | RAW    | Linux 에이전트 | RHEL 7.x 이미지 제품군 <br> 기본적으로 일반 리포지토리에 연결 됩니다 (EUS 아님).
|             | 7-LVM    | LVM    | Linux 에이전트 | RHEL 7.x 이미지 제품군 <br> 기본적으로 일반 리포지토리에 연결 됩니다 (EUS 아님). 배포할 표준 RHEL 이미지를 찾고 있는 경우이 이미지 집합 및/또는 해당 하는 2 세대를 사용 합니다.
|             | 7lvm-gen2| LVM    | Linux 에이전트 | 2 세대, RHEL 7.x 이미지 제품군 <br> 기본적으로 일반 리포지토리에 연결 됩니다 (EUS 아님). 배포할 표준 RHEL 이미지를 찾고 있는 경우이 이미지 집합 및/또는 해당 하는 1 세대를 사용 합니다.
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x 이미지 제품군 <br> 기본적으로 일반 리포지토리에 연결 됩니다 (EUS 아님).
|             | 7.2      | RAW    | Linux 에이전트 |
|             | 7.3      | RAW    | Linux 에이전트 |
|             | 7.4      | RAW    | Linux 에이전트 | 2019 년 4 월 기준으로 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 74-gen2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 7.5      | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다. 6 월 2019을 기준으로 합니다.
|             | 75-gen2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 7.6      | RAW    | Linux 에이전트 | 2019 년 5 월에 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 76-gen2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 7.7      | LVM    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 8        | LVM    | Linux 에이전트 | RHEL .x 이미지 제품군
|             | 8-gen2   | LVM    | Linux 에이전트 | Hyper-v 세대 2-RHEL 8. x 이미지 제품군
RHEL-SAP      | 7.4      | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.4. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 74sap-gen2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.4. 2 세대 이미지입니다. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 7.5       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.5. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 75sap-gen2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.5. 2 세대 이미지입니다. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 7.6       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.6. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 76sap-gen2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.6. 2 세대 이미지입니다. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 7.7       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.7. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
RHEL-SAP-HANA | 6.7       | RAW    | Linux 에이전트 | SAP HANA RHEL 6.7입니다. 오래 된 RHEL 이미지를 선호 합니다.
|             | 7.2       | LVM    | Linux 에이전트 | SAP HANA RHEL 7.2입니다. 오래 된 RHEL 이미지를 선호 합니다.
|             | 7.3       | LVM    | Linux 에이전트 | SAP HANA RHEL 7.3입니다. 오래 된 RHEL 이미지를 선호 합니다.
RHEL-SAP-APPS | 6.8       | RAW    | Linux 에이전트 | RHEL 6.8 for SAP Business Applications. 오래 된 RHEL 이미지를 선호 합니다.
|             | 7.3       | LVM    | Linux 에이전트 | RHEL 7.3 for SAP Business Applications. 오래 된 RHEL 이미지를 선호 합니다.
RHEL-HA       | 7.4       | LVM    | Linux 에이전트 | HA 추가 기능을 사용 하는 RHEL 7.4. 는 기본 계산 요금을 RHEL HA 및에 대 한 프리미엄을 부과 합니다.
|             | 7.5       | LVM    | Linux 에이전트 | HA 추가 기능을 사용 하는 RHEL 7.5. 는 기본 계산 요금을 RHEL HA 및에 대 한 프리미엄을 부과 합니다.
|             | 7.6       | LVM    | Linux 에이전트 | HA 추가 기능을 사용 하는 RHEL 7.6. 는 기본 계산 요금을 RHEL HA 및에 대 한 프리미엄을 부과 합니다.
RHEL-HA   | 7.4          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.4. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 74sapha-gen2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.4. 2 세대 이미지입니다. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 7.5          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.5. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 7.6          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.6. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 76sapha-gen2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.6. 2 세대 이미지입니다. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 7.7          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.7. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 77sapha-gen2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.7. 2 세대 이미지입니다. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
rhel byos     |rhel-lvm74| LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 7.4 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.
|             |rhel-lvm75| LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 7.5 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.
|             |rhel-lvm76| LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 7.6 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.
|             |rhel-lvm77| LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 7.7 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.
|             |rhel-lvm8 | LVM    | Linux 에이전트 | RHEL 8 BYOS 이미지 (RHEL 부 버전이 이미지 버전 값에 표시 됨) 업데이트 원본에 연결 되지 않은 경우 RHEL premium이 부과 되지 않습니다.

## <a name="next-steps"></a>다음 단계
* [Azure의 Red Hat 이미지](./redhat-images.md)에 대해 자세히 알아보세요.
* [Red Hat 업데이트 인프라](./redhat-rhui.md)에 대해 자세히 알아보세요.
* [RHEL BYOS 제품](./byos.md)에 대해 자세히 알아보세요.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
