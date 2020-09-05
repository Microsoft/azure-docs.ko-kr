---
title: Azure 응용 프로그램 게시 가이드 솔루션 템플릿 제품-Azure Marketplace
description: 이 문서에서는 Azure Marketplace에 솔루션 템플릿을 게시 하기 위한 요구 사항을 설명 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 04/22/2020
ms.openlocfilehash: 2ccf5b99a5b1ec1db5e6bfb32911ee2f0094995e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506131"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Azure 응용 프로그램 게시 가이드 솔루션 템플릿 제품

이 문서에서는 Azure Marketplace에 Azure 응용 프로그램 제품을 게시 하는 한 가지 방법인 솔루션 템플릿 제품을 게시 하기 위한 요구 사항을 설명 합니다. 솔루션 템플릿 제품 형식에 솔루션 인프라를 자동으로 배포 하려면 [Azure Resource Manager 템플릿 (ARM 템플릿)](../azure-resource-manager/templates/overview.md) 이 필요 합니다.

다음 조건에서 Azure 응용 프로그램 *솔루션 템플릿* 제품 유형을 사용 합니다.

- 솔루션에는 vm, 네트워킹 및 저장소 리소스의 조합과 같은 단일 VM (가상 머신) 이외에 추가 배포 및 구성 자동화가 필요 합니다.
- 고객은 솔루션을 직접 관리할 예정입니다.

고객이이 제안 유형에 대해 확인 하는 동작에 대 한 호출은 *지금 가져옵니다*.

## <a name="requirements-for-solution-template-offers"></a>솔루션 템플릿 제품에 대 한 요구 사항

| **요구 사항** | **세부 정보**  |
| ---------------  | -----------  |
|청구 및 계량    |  솔루션 템플릿 제품은 트랜잭션 제안이 아니라 Microsoft 상업적 marketplace를 통해 청구 되는 유료 VM 제품을 배포 하는 데 사용할 수 있습니다. 솔루션의 ARM 템플릿에서 배포 하는 리소스는 고객의 Azure 구독에 설정 됩니다. 종 량 제 가상 머신은 Microsoft를 통해 고객과 거래 되며 고객의 Azure 구독을 통해 청구 됩니다.<br/> BYOL (사용자 라이선스 보유) 청구의 경우 Microsoft는 고객 구독에서 발생 하는 인프라 비용을 청구 하지만 고객과 직접 소프트웨어 라이선스 요금을 지불 합니다.   |
|Azure 호환 VHD(가상 하드 디스크)  |   VM은 Windows 또는 Linux에서 빌드해야 합니다. 자세한 내용은 다음을 참조하세요. <ul> <li>Windows Vhd에 대 한 [Azure 응용 프로그램 제품을 만듭니다](./partner-center-portal/create-new-azure-apps-offer.md) .</li><li>[Azure에서 linux 배포판 보증](../virtual-machines/linux/endorsed-distros.md) (linux vhd의 경우).</li></ul> |
| 고객 사용량 특성 | Azure Marketplace에 게시 된 모든 솔루션 템플릿에서 고객 사용 특성을 사용 하도록 설정 해야 합니다. 고객 사용 특성 및이를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Azure 파트너 고객 사용 특성](./azure-partner-customer-usage-attribution.md)을 참조 하세요.  |
| 관리 디스크 사용 | [Managed disks](../virtual-machines/windows/managed-disks-overview.md) 는 Azure에서 IaaS (infrastructure as a Service) vm의 지속형 디스크에 대 한 기본 옵션입니다. 솔루션 템플릿에서 managed disks를 사용 해야 합니다. <ul><li>솔루션 템플릿을 업데이트 하려면 [Azure Resource Manager 템플릿에서 managed Disks 사용](../virtual-machines/using-managed-disks-template-deployments.md)의 지침을 따르고 제공 된 [샘플](https://github.com/Azure/azure-quickstart-templates)을 사용 합니다.<br><br> </li><li>VHD를 Azure Marketplace 이미지로 게시 하려면 다음 방법 중 하나를 사용 하 여 관리 디스크의 기본 VHD를 저장소 계정으로 가져옵니다.<ul><li>[Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md) </li> <li> [Azure CLI](../virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md) </li> </ul></ul> |

## <a name="next-steps"></a>다음 단계

아직 수행하지 않은 경우 [Azure Marketplace로 클라우드 비즈니스를 확장](https://azuremarketplace.microsoft.com/sell)하는 방법을 알아보세요.

파트너 센터에서 등록하고 작업을 시작하려면

- 제품을 만들거나 완성할 수 있도록 [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)합니다.
- 자세한 내용은 [Azure 응용 프로그램 제품 만들기](./partner-center-portal/create-new-azure-apps-offer.md) 를 참조 하세요.
