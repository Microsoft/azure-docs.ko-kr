---
title: 렌더링 애플리케이션 - Azure Batch
description: 미리 설치된 Batch 렌더링 애플리케이션
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 03/26/2018
ms.topic: conceptual
ms.openlocfilehash: 84b2ca30f1ccd49e18e2f9d42133f8672d3f8ad6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776172"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>렌더링 VM 이미지에 미리 설치된 애플리케이션

Azure Batch를 모든 렌더링 애플리케이션에 사용할 수 있습니다. 그러나 Azure Marketplace VM 이미지는 미리 설치된 일반적인 애플리케이션에 사용할 수 있습니다.

해당하는 경우 미리 설치된 렌더링 애플리케이션에 종량제 라이선스를 사용할 수 있습니다. Batch 풀을 만들 때 필요한 애플리케이션을 지정할 수 있고 VM 및 애플리케이션 비용이 분 단위로 청구됩니다. 애플리케이션 가격은 [Azure Batch 가격 책정 페이지](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)에 나열되어 있습니다.

일부 애플리케이션은 Windows만 지원하나 대부분 Windows와 Linux를 모두 지원합니다.

## <a name="applications-on-centos-7-rendering-images"></a>이미지를 렌더링 하는 CentOS 7에서 응용 프로그램

* Autodesk Maya I/O 2017 업데이트 5(201708032230 잘라내기)
* Autodesk Maya I/O 2018 업데이트 2(201711281015 컷)
* Autodesk Arnold for Maya 2017(Arnold 버전 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018(Arnold 버전 5.0.1.4) MtoA-2.1.0.3-2018
* Maya 2017용 Chaos Group V-Ray(버전 3.60.04)
* Maya 2018용 Chaos Group V-Ray(버전 3.60.04)
* Blender(2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>최신 Windows Server 2016 이미지 렌더링에 응용 프로그램

다음은 Windows Server 2016 버전 1.3.4 렌더링 이미지에 적용 됩니다.

* Autodesk Maya I/O 2017 업데이트 5(버전 17.4.5459)
* Autodesk Maya I/O 2018 업데이트 4(버전 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 업데이트 1(버전 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 업데이트 4(버전 20.4.0.4254)
* Autodesk Arnold for Maya 2017(Arnold 버전 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018(Arnold 버전 5.2.0.1) MtoA-3.1.0.1-2018
* 3ds Max용 Autodesk Arnold(Arnold 버전 5.0.2.4) (버전 1.2.926)
* Chaos Group V-ray Maya 2018 (버전 3.52.03)
* Chaos Group V-ray 3ds Max 2018 (버전 3.60.02)
* Chaos Group V-ray Maya 2019 (버전 3.52.03)에 대 한
* Chaos Group V-ray 3ds Max 2019 (4.10.01 버전)에 대 한
* Blender(2.79)

> [!NOTE]
> Chaos Group V-ray 3ds Max 2019 (4.10.01 버전)에 대 한 주요 변경 내용을 v-ray에서는 도입 되었습니다. 이전 버전 (버전 3.60.02)을 사용 하려면 Windows Server 2016 버전 1.3.2 렌더링 노드를 사용 합니다.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>이전 Windows Server 2016 이미지 렌더링에 응용 프로그램

다음은 Windows Server 2016 버전 1.3.2 렌더링 이미지에 적용 됩니다.

* Autodesk Maya I/O 2017 업데이트 5(버전 17.4.5459)
* Autodesk Maya I/O 2018 업데이트 4(버전 18.4.0.7622)  
* Autodesk 3ds Max I/O 2019 업데이트 1(버전 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 업데이트 4(버전 20.4.0.4254)
* Autodesk Arnold for Maya 2017(Arnold 버전 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018(Arnold 버전 5.2.0.1) MtoA-3.1.0.1-2018
* 3ds Max용 Autodesk Arnold(Arnold 버전 5.0.2.4) (버전 1.2.926)
* Chaos Group V-ray Maya 2019 (버전 3.52.03)에 대 한
* Chaos Group V-ray 3ds Max 2018 (버전 3.60.02)
* Blender(2.79)

## <a name="next-steps"></a>다음 단계

렌더링 VM 이미지를 사용하려면 풀을 만들 때 풀 구성에서 지정해야 합니다. [렌더링을 위한 Batch 풀 기능](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)을 참조하세요.