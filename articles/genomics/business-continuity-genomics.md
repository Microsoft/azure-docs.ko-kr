---
title: 비즈니스 연속성 - Microsoft Genomics | Microsoft Docs
titleSuffix: Azure
description: 이 개요에서는 Microsoft Genomics가 비즈니스 연속성 및 재해 복구를 위해 제공하는 기능에 대해 설명합니다. 데이터 손실을 야기할 수 있는 Azure 지역 가동 중단과 같은 재해 이벤트에서 복구하기 위한 옵션에 대해 알아봅니다.
keywords: 비즈니스 연속성, 재해 복구
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: 7a51477dbbf6f4e50959a6d979342961c7e49ad9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641112"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Microsoft Genomics를 사용한 비즈니스 연속성 개요
이 개요에서는 Microsoft Genomics가 비즈니스 연속성 및 재해 복구를 위해 제공하는 기능에 대해 설명합니다. 데이터 손실을 야기할 수 있는 Azure 지역 가동 중단과 같은 재해 이벤트에서 복구하기 위한 옵션에 대해 알아봅니다. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>비즈니스 연속성을 지원하는 Microsoft Genomics 기능 
드물긴 하지만 Azure 데이터 센터에 몇 분에서 몇 시간까지 가동 중단 상황이 지속되어 비즈니스 중단을 야기할 수 있습니다. 가동 중단이 발생하면 데이터 센터에서 현재 실행 중인 모든 작업이 실패하고, Microsoft Genomics 서비스는 자동으로 작업을 보조 지역으로 다시 전송하지 못 합니다. 

* 한 가지 옵션은 데이터 센터 가동 중단이 끝날 때 데이터 센터가 온라인 상태가 되기까지 기다리는 것입니다. 짧은 가동 중단의 경우 Microsoft Genomics 서비스가 실패한 작업을 자동으로 검색하고, 워크플로는 자동으로 다시 시작됩니다.

* 또 다른 옵션은 사전에 다른 데이터 영역에서 워크플로를 전송하는 것입니다. Microsoft Genomics는 몇 개의 [Azure 지역](https://azure.microsoft.com/regions/services/)에 인스턴스를 배포하며, 각각의 지역별 인스턴스는 독립적입니다. 따라서 Microsoft Genomics 인스턴스 중 하나에 지역 로컬 오류가 발생해도 Microsoft Genomics의 인스턴스를 실행하는 다른 지역은 작업을 계속 처리합니다. 이러한 대체 지역으로의 전송은 사용자가 제어하며 언제든지 가능합니다.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Microsoft Genomics 워크플로를 다른 지역으로 수동 장애 조치(Failover)
지역 데이터 센터 가동 중단의 경우 개별 데이터 주권 및 비즈니스 연속성 요구 사항에 따라, 보조 지역에 Microsoft Genomics 워크플로를 전송하도록 선택할 수 있습니다. Microsoft Genomics 워크플로를 수동으로 장애 조치(Failover)하려면 다른 지역별 Genomics 계정을 사용하고 해당 지역별 Genomics 및 저장소 계정 자격 증명으로 작업을 전송합니다.

특히 다음을 수행해야 합니다.
* Azure Portal을 사용하여 보조 지역에서 Genomics 계정을 만듭니다. 
* 보조 지역의 저장소 계정으로 입력 데이터를 마이그레이션하고 보조 지역에서 출력 폴더를 설정합니다.
* 보조 지역에 워크플로를 제출합니다.

원래 지역이 복원되면 Microsoft Genomics 서비스는 보조 지역의데이 터를 원래 지역으로 다시 마이그레이션하지 않습니다. 보조 지역의 입력 및 출력 파일을 원래 지역으로 다시 이동하도록 선택할 수 있습니다.  해당 데이터를 이동하도록 선택한 경우 Genomics 서비스 외부가 되며, 데이터 이동과 관련된 모든 비용은 사용자의 책임입니다. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>가능한 지역별 가동 중단 대비
데이터 센터 가동 중단에서 더 빠르게 복구하고 싶은 경우 다음과 같은 몇 가지 단계를 수행하여 Microsoft Genomics 워크플로를 보조 지역으로 수동으로 다시 전송하는 데 걸리는 시간을 단축할 수 있습니다.

* 해당 보조 지역을 식별하고 해당 지역에서 Genomics 계정을 미리 만듭니다.
* 보조 지역에서 데이터를 즉시 사용할 수 있도록 기본 및 보조 지역에서 데이터를 복제합니다. 이 작업은 수동으로 완료하거나 Azure Storage의 [지역 중복 스토리지](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 기능을 사용하여 수행할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Genomics 서비스를 사용할 때의 비즈니스 연속성 및 재해 복구 옵션에 대해 알아보았습니다. Azure 내의 일반적인 비즈니스 연속성 및 재해 복구에 대한 자세한 내용은 [Azure 복원 기술 지침](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)을 참조하세요. 