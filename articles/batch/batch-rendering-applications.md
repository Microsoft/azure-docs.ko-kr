---
title: 렌더링 애플리케이션 - Azure Batch
description: Azure Batch를 모든 렌더링 애플리케이션에 사용할 수 있습니다. 그러나 Azure Marketplace VM 이미지는 미리 설치된 일반적인 애플리케이션에 사용할 수 있습니다.
services: batch
ms.service: batch
author: LauraBrenner
ms.author: labrenne
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 77672534b2aad993a44e9b637fbed58df8610e97
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022990"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>렌더링 VM 이미지에 미리 설치된 애플리케이션

Azure Batch를 모든 렌더링 애플리케이션에 사용할 수 있습니다. 그러나 Azure Marketplace VM 이미지는 미리 설치된 일반적인 애플리케이션에 사용할 수 있습니다.

해당하는 경우 미리 설치된 렌더링 애플리케이션에 종량제 라이선스를 사용할 수 있습니다. Batch 풀을 만들 때 필요한 애플리케이션을 지정할 수 있고 VM 및 애플리케이션 비용이 분 단위로 청구됩니다. 애플리케이션 가격은 [Azure Batch 가격 책정 페이지](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)에 나열되어 있습니다.

일부 애플리케이션은 Windows만 지원하나 대부분 Windows와 Linux를 모두 지원합니다.

## <a name="applications-on-centos-7-rendering-images"></a>CentOS 7 렌더링 이미지의 응용 프로그램

다음 목록은 CentOS 7.6, 버전 1.1.6 렌더링 이미지에 적용 됩니다.

* Autodesk Maya I/O 2017 업데이트 5(201708032230 잘라내기)
* Autodesk Maya I/O 2018 업데이트 2(201711281015 컷)
* Autodesk Maya i/o 2019 업데이트 1
* Maya 2017에 대한 Autodesk Arnold (Arnold version 5.3.1.1) MtoA-3.2.1.1-2017
* Maya 2018에 대한 Autodesk Arnold (Arnold version 5.3.1.1) MtoA-3.2.1.1-2018
* Maya 2019 Autodesk Arnold (Arnold version 5.3.1.1) MtoA-3.2.1.1-2019
* Maya 2017용 Chaos Group V-Ray(버전 3.60.04)
* Maya 2018용 Chaos Group V-Ray(버전 3.60.04)
* Blender(2.68)
* Blender (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>최신 Windows Server 2016 이미지 렌더링 이미지의 응용 프로그램

다음 목록은 Windows Server 2016, 버전 1.3.8 렌더링 이미지에 적용 됩니다.

* Autodesk Maya I/O 2017 업데이트 5(버전 17.4.5459)
* Autodesk Maya i/o 2018 업데이트 6 (버전 18.4.0.7622)
* Autodesk Maya i/o 2019
* Autodesk 3ds Max I/O 2018 업데이트 4(버전 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 업데이트 1(버전 21.2.0.2219)
* Autodesk 3ds Max i/o 2020 업데이트 2
* Maya 2017에 대한 Autodesk Arnold (Arnold version 5.3.0.2) MtoA-3.2.0.2-2017
* Maya 2018에 대한 Autodesk Arnold (Arnold version 5.3.0.2) MtoA-3.2.0.2-2018
* Maya 2019 Autodesk Arnold (Arnold version 5.3.0.2) MtoA-3.2.0.2-2019
* 3ds Max 2018 (Arnold version 5.3.0.2) (버전 1.2.926)에 대한 Autodesk Arnold
* 3ds Max 2019 (Arnold version 5.3.0.2) (버전 1.2.926)에 대한 Autodesk Arnold
* 3ds Max 2020 (Arnold version 5.3.0.2) (버전 1.2.926)에 대한 Autodesk Arnold
* Maya 2017 (버전 4.12.01)의 비정상 그룹 V-광선
* Maya 2018 (버전 4.12.01)의 비정상 그룹 V-광선
* Maya 2019 (버전 4.04.03)의 비정상 그룹 V-광선
* 3ds Max 2018 (version 4.20.01)의 비정상 그룹 V-광선
* 3ds Max 2019 (version 4.20.01)의 비정상 그룹 V-광선
* 3ds Max 2020 (version 4.20.01)의 비정상 그룹 V-광선
* Blender(2.79)
* Blender (2.80)
* AZ 10

> [!IMPORTANT]
> Maya를 사용 하 여 [Azure Batch 확장 템플릿](https://github.com/Azure/batch-extension-templates)외부에서 V 레이를 실행 하려면 렌더링을 실행 하기 전에 `vrayses.exe`를 시작 합니다. 템플릿 외부에서 vrayses를 시작 하려면 다음 명령을 `%MAYA_2017%\vray\bin\vrayses.exe"`사용할 수 있습니다.
>
> 예를 들어 GitHub의 [Maya 및 V-광선 템플릿](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) 시작 작업을 참조 하세요.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>이전 Windows Server 2016의 응용 프로그램 렌더링 이미지

다음 목록은 Windows Server 2016, 버전 1.3.7 렌더링 이미지에 적용 됩니다.

* Autodesk Maya I/O 2017 업데이트 5(버전 17.4.5459)
* Autodesk Maya I/O 2018 업데이트 4(버전 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 업데이트 1(버전 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 업데이트 4(버전 20.4.0.4254)
* Autodesk Arnold for Maya 2017(Arnold 버전 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018(Arnold 버전 5.2.0.1) MtoA-3.1.0.1-2018
* 3ds Max 2018 (Arnold version 5.0.2.4) (버전 1.2.926)에 대한 Autodesk Arnold
* 3ds Max 2019 (Arnold version 5.0.2.4) (버전 1.2.926)에 대한 Autodesk Arnold
* Maya 2018 (버전 3.52.03)의 비정상 그룹 V-광선
* 3ds Max 2018 (version 3.60.02)의 비정상 그룹 V-광선
* Maya 2019 (버전 3.52.03)의 비정상 그룹 V-광선
* 3ds Max 2019 (version 4.10.01)의 비정상 그룹 V-광선
* Blender(2.79)

> [!NOTE]
> 3ds Max 2019 (version 4.10.01)에 대한 비정상 그룹 V-광선은 V 광선의 주요 변경 사항을 소개 합니다. 이전 버전 (버전 3.60.02)을 사용 하려면 Windows Server 2016, 버전 1.3.2 렌더링 노드를 사용 합니다.

## <a name="next-steps"></a>다음 단계

렌더링 VM 이미지를 사용하려면 풀을 만들 때 풀 구성에서 지정해야 합니다. [렌더링을 위한 Batch 풀 기능](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)을 참조하세요.
