---
title: SAP HANA(대규모 인스턴스)에 대한 운영 체제 호환성 매트릭스 | Microsoft Docs
description: 호환성 매트릭스는 다양한 하드웨어 유형(대규모 인스턴스)을 사용하는 여러 운영 체제 버전의 호환성을 나타냅니다.
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18e97aea5c556c4f8e6ff1fb1b91a82da6de1b59
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749367"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>HANA 대규모 인스턴스에 대해 호환되는 운영 체제

## <a name="hana-large-instance-type-i"></a>HANA 대규모 인스턴스 유형 I     
  | 운영 체제 | 가용성        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | 더 이상 제공되지 않음 | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | 사용 가능           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | 사용 가능           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 12 SP5      | 사용 가능           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 15 SP1      | 사용 가능           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | RHEL 7.6         | 사용 가능           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>영구 메모리 SKU
  | 운영 체제 | 가용성 | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | 사용 가능    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA 대규모 인스턴스 유형 II     
  |  운영 체제       | 가용성        | SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | 더 이상 제공되지 않음 | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | 사용 가능           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP4             | 사용 가능           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP5             | 사용 가능           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | SLES 15 SP1             | 사용 가능           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  
## <a name="related-documents"></a>관련 문서

- [사용 가능한 SKU](hana-available-skus.md)에 대해 자세히 알아보려면
- [운영 체제 업그레이드](os-upgrade-hana-large-instance.md)에 대해 알아보려면
  

  
  
