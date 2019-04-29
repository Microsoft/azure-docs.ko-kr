---
title: Azure의 IoT(사물 인터넷) 보안 | Microsoft Docs
description: " Azure IoT(사물 인터넷) 서비스는 광범위한 기능을 제공합니다. 이 문서에서는 Azure에서 IoT 솔루션을 보호하는 방법을 이해하도록 도움을 줍니다. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: db1c2b9c852479b9af3674f6c5e1f1135ee289f1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107442"
---
# <a name="internet-of-things-security-overview"></a>사물 인터넷 보안 개요
Azure IoT(사물 인터넷) 서비스는 광범위한 기능을 제공합니다. 이러한 엔터프라이즈급 서비스를 사용하면 다음과 같은 작업을 할 수 있습니다.

* 디바이스에서 데이터 수집
* 동작 내 데이터 스트림 분석
* 큰 데이터 집합 저장 및 쿼리
* 실시간 및 기록 데이터 시각화
* 백 오피스 시스템과 통합

이러한 기능을 제공하기 위해 Azure IoT Solution Accelerator는 사용자 지정 확장이 포함된 여러 Azure 서비스를 미리 구성된 솔루션으로 패키지화했습니다. 이러한 미리 구성된 솔루션은 IoT 솔루션을 제공하는 데 걸릴 시간을 줄이는 데 도움이 되는 일반적인 IoT 솔루션 패턴의 기본 구현입니다. IoT 소프트웨어 개발자 키트를 통해 이러한 솔루션을 사용자 지정 및 확장하여 사용자의 요구 사항을 충족할 수 있습니다. 또한 새로운 IoT 솔루션을 개발할 때 이러한 솔루션을 예제 또는 템플릿으로 사용할 수도 있습니다.

Azure IoT Solution Accelerator는 IoT 요구 사항에 대한 강력한 솔루션입니다. 그러나 IoT 솔루션은 처음부터 보안을 염두에 두고 설계되었다는 점이 가장 중요합니다. 수많은 IoT 디바이스로 인해 보안 문제는 심각한 결과로 빠르게 광범위한 문제가 될 수 있습니다.

IoT 솔루션을 보호하는 방법을 이해할 수 있도록 다음과 같은 정보를 제공합니다.

## <a name="security-architecture"></a>보안 아키텍처
시스템을 디자인하고 아키텍처를 설계할 때 해당 시스템에 대한 잠재적 위협을 파악하고, 그에 따라 적절한 방어 수단을 추가해야 합니다. 처음부터 보안을 염두에 두고 제품을 설계하는 것이 매우 중요합니다. 공격자가 시스템을 손상시킬 수 있는 방법을 파악하면 처음부터 적절한 위협 완화 조치를 수행할 수 있기 때문입니다.

[사물 인터넷 보안 아키텍처](/azure/iot-fundamentals/iot-security-architecture)를 읽어 IoT 보안 아키텍처에 대해 알아볼 수 있습니다.

이 문서는 다음 항목을 설명합니다.

* [보안은 위협 모델에서 출발](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [IoT의 보안](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Azure IoT 참조 아키텍처 위협 모델링](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>처음부터 보안을 고려
IoT는 전 세계 기업에 고유한 보안, 개인 정보 및 규정 준수 문제를 제기합니다. 문제가 소프트웨어와 구현 방식을 중심으로 발생하는 기존의 사이버 기술과는 달리 IoT는 사이버 세계와 실제 세계가 만날 때 일어나는 일과 관련하여 문제가 발생합니다. IoT 솔루션을 보호하기 위해서는 디바이스의 안전한 프로비전, 이러한 디바이스 및 클라우드 간의 보안 연결, 처리 및 저장 중에 클라우드에서 데이터 보호 설정이 요구됩니다. 그러나 이러한 기능에 대한 작업에는 리소스가 제한된 디바이스, 배포의 지리적 분산 및 솔루션 내 많은 수의 디바이스에 대한 작업이 포함됩니다.

[처음부터 사물 인터넷 보안](/azure/iot-fundamentals/iot-security-ground-up)을 읽어 이러한 영역에서 보안을 처리하는 방법을 배울 수 있습니다.

이 문서는 다음 항목을 설명합니다.

* [처음부터 보안 인프라](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure - 비즈니스를 위한 보안 IoT 인프라](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>모범 사례
IoT 인프라를 보호하려면 엄격한 보안 심층 전략이 필요합니다. 클라우드의 데이터 보안부터 공용 인터넷을 통해 전송 중인 데이터 무결성을 보호하고 디바이스를 안전하게 프로비전하는 기능까지, 각 계층이 전체 인프라에서 우수한 보안을 구축합니다.

[사물 인터넷 보안 모범 사례](/azure/iot-fundamentals/iot-security-best-practices)를 읽어 사물 인터넷 보안 모범 사례에 대해 알아볼 수 있습니다.

이 문서는 다음 항목을 설명합니다.

* [IoT 하드웨어 제조업체/통합업체](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [IoT 솔루션 개발자](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [IoT 솔루션 배포자](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [IoT 솔루션 운영자](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
