---
title: '참조: Data Science Virtual Machine 이미지 Retirements'
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine에 영향을 주는 retirements에 대 한 세부 정보
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001623"
---
# <a name="reference-retirements-of-dsvm-images"></a>참조: DSVM 이미지의 Retirements

아래에서는 Azure Data Science Virtual Machine에 대 한 사용 중지 (사용 중단) 및 예정 된 retirements 처리에 대 한 제안 사항을 설명 합니다.

## <a name="why-we-retire-dsvm-images"></a>DSVM 이미지를 사용 중지 하는 이유

현재 Data Science Virtual Machine에는 두 가지 버전이 있습니다.

* Ubuntu
* Windows Server

이러한 버전의 DSVM 이미지는 특정 운영 체제 버전 (예: Ubuntu 18.04 LTS)을 기반으로 빌드됩니다. 시간이 지남에 따라 운영 체제 _버전_ 에 대 한 유지 관리 기간이 종료 되거나 데이터 과학 도구는 더 이상 이전 버전의 운영 체제를 지원 하지 않습니다. 최신 운영 체제 및 데이터 과학 도구를 사용 하 여 DSVM 이미지를 최신 상태로 유지 하기 위해 최신 운영 체제 버전에 따라 새 DSVM 이미지를 릴리스 합니다.

## <a name="how-we-retire-dsvm-images"></a>DSVM 이미지를 사용 중지 하는 방법

기존 DSVM 사용자가 예정 된 DSVM 이미지 사용 중지를 알리는 (전자 메일을 통해) 사용 중지 _알림을_ 실행 합니다. 사용 중지 알림은 사용 중지 _날짜_ (이 날짜 이후 이미지를 사용할 수 없음) 및 완화 권장 사항 (예: 최신 dsvm 이미지로 업그레이드)에 대해 자세히 설명 합니다.

_발표_ 날짜에서 다음과 같이 marketplace에서 이미지를 숨깁니다.

1. 새 사용자가 실수로 오래 된 DSVM 이미지를 프로 비전 하는 것을 방지 합니다.
2. 기존 사용자는 사용 중지 날짜까지 ARM 배포를 사용할 수 있습니다.

숨겨진 이미지는 사용 중지 _날짜_ 까지 운영 체제 패치를 수신 하지만 데이터 과학 도구 및 프레임 워크에 대 한 업데이트 __를 받지 않습니다__ . 사용 중지 _날짜_에는 ARM 배포에 이미지를 사용할 수 없습니다.

구독에서 프로 비전 된 DSVM 이미지는 사용 중지 날짜 후에도 계속 작동 합니다. 그러나 최신 운영 체제 및 데이터 과학 도구를 사용할 수 있도록 최신 DSVM 이미지로 업그레이드 하는 것이 좋습니다.

## <a name="impact"></a>영향

구독에서 기존 DSVM 프로 비전 된 이미지는 사용 중지 날짜 후에도 계속 작동 합니다. 그러나 사용자가 Azure Portal 또는 ARM 템플릿을 사용 하 여 DSVM 이미지를 최신 버전으로 업그레이드 하는 것이 좋습니다.

> [!WARNING]
> Virtual Machine Scale Sets를 사용 하 여 프로 비전 된 사용 중지 된 DSVM 이미지는 사용 중지 날짜 이후 확장 되지 않습니다.
>
> 새 DSVM 이미지 세부 정보를 사용 하 여 업데이트 되지 않은 ARM 템플릿은 사용 중지 날짜 이후에 배포 되지 않습니다.

