---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 데이터 계층 및 확장 노드 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA에 대한 데이터 계층 및 확장 노드입니다.
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
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5868aaf29d6b57ea608763840a08f1991a982e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628594"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA 데이터 계층화 및 확장 노드 사용

SAP는 다양한 SAP NetWeaver 버전의 SAP BW 및 SAP BW/4HANA에 대한 데이터 계층화 모델을 지원합니다. 데이터 계층 모델에 대한 자세한 내용은 [SAP HANA 확장 노드가 있는 SAP BW/4HANA 및 SAP BW on HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#) SAP 문서를 참조하세요.
HANA 대규모 인스턴스를 사용하면 FAQ 및 SAP 블로그 문서에서 설명한 대로 SAP HANA 확장 노드의 옵션 1 구성을 사용할 수 있습니다. 옵션 2 구성은 다음 HANA 대규모 인스턴스 Sku를 사용 하 여 설정할 수 있습니다. S72m, S192, S192m, S384 및 S384m 합니다. 

설명서를 살펴볼 때 장점이 바로 표시되지 않을 수 있습니다. 하지만 SAP 크기 조정 지침을 살펴보면 옵션 1과 옵션 2 SAP HANA 확장 노드를 사용하여 장점을 확인할 수 있습니다. 예는 다음과 같습니다.

- SAP HANA 크기 조정 지침은 일반적으로 메모리로 데이터 볼륨 크기의 두 배를 필요로 합니다. 핫 데이터를 사용하여 SAP HANA 인스턴스를 실행하는 경우 50% 이하의 메모리만 데이터로 채워집니다. 메모리의 나머지 부분은 해당 작업을 수행하는 SAP HANA에 대해 이상적으로 유지됩니다.
- 즉, SAP BW 데이터베이스를 실행하는 2TB의 메모리가 있는 HANA 대규모 인스턴스 S192 단위에서 데이터 볼륨으로 1TB만을 갖습니다.
- 옵션 1의 추가 SAP HANA 확장 노드 및 S192 HANA 대규모 인스턴스 SKU를 사용하는 경우 데이터 볼륨에 2TB 용량을 추가로 제공합니다. 옵션 2 구성에서는 웜 데이터 볼륨에 4TB를 추가로 제공합니다. 핫 노드와 비교할 때 "웜" 확장 노드의 전체 메모리 용량은 옵션 1에 대한 데이터 저장에 사용할 수 있습니다. 옵션 2 SAP HANA 확장 노드 구성의 데이터 볼륨에 메모리를 두 배로 사용할 수 있습니다.
- 최종적으로 데이터 용량은 3TB이며, 옵션 1의 경우 핫 대 웜 비율은 1:2입니다. 옵션 2 확장 노드 구성에서는 데이터 용량이 5TB이고, 비율은 1:4입니다.

메모리에 비해 데이터 볼륨이 높을수록 요청하는 웜 데이터가 디스크 저장소에 저장될 확률이 높아집니다.

**다음 단계**
- [Azure의 SAP HANA(대규모 인스턴스) 아키텍처](hana-architecture.md) 참조