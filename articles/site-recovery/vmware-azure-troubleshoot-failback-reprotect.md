---
title: Azure Site Recovery를 사용하여 Azure로 VMware VM 재해 복구 시 온-프레미스로 장애 복구(failback) 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 VMware VM 재해 복구하는 동안 발생하는 장애 복구(failback) 및 다시 보호 문제를 해결하는 방법을 설명합니다.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 20cb7a446befb1d31f0e069d91d0230fc4a2a901
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565602"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Azure에서 온-프레미스로 장애 복구(failback) 문제 해결

이 아티클에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure로 장애 조치한 후 Azure VM을 온-프레미스 VMware 인프라로 장애 복구할 때 발생할 수 있는 문제를 해결하는 방법에 대해 설명합니다.

장애 복구(failback)는 기본적으로 두 가지 기본 단계로 이루어집니다. 첫 번째 단계로 장애 조치 후에 Azure VM을 온-프레미스로 다시 보호해야 복제가 시작됩니다. 두 번째 단계는 Azure에서 장애 조치를 실행하여 온-프레미스 사이트로 장애 복구하는 것입니다.

## <a name="troubleshoot-reprotection-errors"></a>다시 보호 오류 문제 해결

이 섹션에서는 일반적인 다시 보호 오류 및 해당 오류를 해결하는 방법에 대해 자세히 설명합니다.

### <a name="error-code-95226"></a>오류 코드 95226

**Azure 가상 컴퓨터가 온-프레미스 구성 서버에 연결하지 못했기 때문에 다시 보호에 실패했습니다.**

이 오류가 발생하는 경우는 다음과 같습니다.

* Azure VM에서 온-프레미스 구성 서버에 연결할 수 없습니다. VM을 검색하여 구성 서버에 등록할 수 없습니다.
* InMage Scout 애플리케이션 서비스가 장애 조치 후에 Azure VM에서 실행되지 않습니다. 이 서비스는 온-프레미스 구성 서버와 통신하는 데 필요합니다.

이 문제를 해결하려면:

* Azure VM 네트워크에서 Azure VM이 온-프레미스 구성 서버와 통신할 수 있는지 확인합니다. 온-프레미스 데이터 센터에 사이트 간 VPN을 설정하거나 Azure VM의 가상 네트워크에서 개인 피어링을 사용하여 Azure ExpressRoute 연결을 구성할 수 있습니다.
* VM이 온-프레미스 구성 서버와 통신할 수 있는 경우 VM에 로그인합니다. 그런 다음, InMage Scout 애플리케이션 서비스를 확인합니다. 해당 서비스가 실행되지 않는 경우 서비스를 수동으로 시작합니다. 서비스 시작 형식이 **자동**으로 설정되었는지 확인합니다.

### <a name="error-code-78052"></a>오류 코드 78052

**가상 머신에 대한 보호를 완료할 수 없습니다.**

이 문제는 장애 조치하는 마스터 대상 서버에 동일한 이름의 VM이 이미 있는 경우에 발생할 수 있습니다.

이 문제를 해결하려면:

* 다시 보호에서 이름이 충돌하지 않는 다른 호스트에 컴퓨터를 만들 수 있도록 다른 호스트에 있는 다른 마스터 대상 서버를 선택합니다.
* vMotion을 사용하여 이름 충돌이 발생하지 않는 다른 호스트로 마스터 대상을 이동시킬 수도 있습니다. 기존 VM이 이탈된 컴퓨터인 경우 새 VM을 동일한 ESXi 호스트에 만들 수 있도록 이름을 변경합니다.


### <a name="error-code-78093"></a>오류 코드 78093

**응답이 없는 상태에서 VM이 실행되지 않거나 액세스할 수 없습니다.**

이 문제를 해결하려면:

장애 조치된 VM을 다시 보호하려면 모바일 서비스를 온-프레미스 구성 서버에 등록하고 프로세스 서버와 통신하여 복제를 시작할 수 있도록 Azure VM을 실행해야 합니다. 컴퓨터에 잘못 된 네트워크 또는 (응답 하지 않거나 종료) 실행 중이 아닌 경우 다시 보호를 시작 하도록 VM에서 모바일 서비스를 구성 서버에 연결할 수 없습니다.

* 온-프레미스와의 통신을 다시 시작할 수 있도록 VM을 다시 시작합니다.
* Azure 가상 머신을 시작한 후에 다시 보호 작업을 다시 시작합니다.

### <a name="error-code-8061"></a>오류 코드 8061

**데이터 저장소가 ESXi 호스트에서 액세스할 수 없습니다.**

장애 복구에 대해 [마스터 대상 필수 구성 요소 및 지원되는 데이터 저장소](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)를 확인합니다.


## <a name="troubleshoot-failback-errors"></a>장애 복구 오류 문제 해결

이 섹션에서는 장애 복구 시 발생할 수 있는 일반적인 오류에 대해 설명합니다.

### <a name="error-code-8038"></a>오류 코드 8038

**오류로 인해 온-프레미스 가상 머신을 불러오지 못했습니다.**

이 문제는 메모리가 충분히 프로비전되지 않은 호스트에서 온-프레미스 VM을 가동하는 경우에 발생합니다. 

이 문제를 해결하려면:

* ESXi 호스트에 더 많은 메모리를 프로비전합니다.
* 또한 vMotion을 사용하여 VM을 부팅하는 데 충분한 메모리가 있는 다른 ESXi 호스트로 VM을 이동시킬 수 있습니다.
