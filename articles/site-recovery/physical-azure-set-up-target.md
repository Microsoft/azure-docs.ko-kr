---
title: Azure Site Recovery에서 물리적 서버에 대한 대상 환경 설정
description: 이 문서에서는 Azure Site Recovery를 사용하여 물리적 서버의 재해 복구를 위한 대상 Azure 환경을 설정하는 방법을 설명합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953895"
---
# <a name="prepare-target-vmware-to-azure"></a>대상 준비(VMware에서 Azure로)

이 문서에서는 Azure 환경을 준비하여 Windows 또는 Linux를 실행 중인 물리적 서버(x64)를 Azure에 복제하기 시작하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

문서에서는 다음을 가정합니다.
- 물리적 컴퓨터를 보호하기 위해 Recovery Services 자격 증명 모음을 만들었습니다. [Azure Portal](https://portal.azure.com "Azure portal")에서 Recovery Services 자격 증명 모음을 만들 수 있습니다.
- 물리적 컴퓨터를 Azure에 복제하도록 [온-프레미스 환경을 설정](physical-azure-disaster-recovery.md)했습니다.

## <a name="prepare-target"></a>대상 준비

**1단계: 보호 목표 선택** 및 **2단계: 원본 준비** 를 완료한 후 **3단계: 대상** 으로 이동합니다.

![대상 준비](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **구독:** 드롭다운 메뉴에서 물리적 컴퓨터를 복제할 구독을 선택합니다.
2. **배포 모델:** 배포 모델(클래식 또는 리소스 관리자)을 선택합니다.

선택한 배포 모델에 따라 유효성 검사를 실행하여 물리적 컴퓨터를 복제하고 장애 조치를 수행할 대상 구독에 하나 이상의 호환되는 스토리지 계정 및 가상 네트워크가 있는지 확인합니다.

유효성 검사가 성공적으로 완료되면 확인을 클릭하여 다음 단계로 이동합니다.

호환되는 Resource Manager 스토리지 계정 또는 가상 네트워크가 없는 경우 페이지 맨 위에 있는 **+Storage 계정** 또는 **+네트워크** 단추를 클릭하여 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
[복제 설정 구성](vmware-azure-set-up-replication.md)
