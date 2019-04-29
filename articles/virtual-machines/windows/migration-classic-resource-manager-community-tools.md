---
title: 커뮤니티 도구 - 클래식 리소스를 Azure Resource Manager로 이동 | Microsoft 문서
description: 이 문서에는 IaaS 리소스를 클래식에서 Azure Resource Manager 배포 모델로 마이그레이션하도록 지원하기 위해 커뮤니티에서 제공해온 도구가 설명되어 있습니다.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 085a0ff0eee260069d693b339521a9489df78e40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848235"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>클래식에서 Azure Resource Manager로 IaaS 리소스를 마이그레이션하기 위한 커뮤니티 도구
이 문서에는 IaaS 리소스를 클래식에서 Azure Resource Manager 배포 모델로 마이그레이션하도록 지원하기 위해 커뮤니티에서 제공해온 도구가 설명되어 있습니다.

> [!NOTE]
> 이러한 도구는 Microsoft 지원을 통해 공식적으로 지원되지는 않습니다. 따라서 GitHub에 오픈 소스로 제공되며 수정 사항 또는 추가 시나리오에 대한 PR을 환영합니다. 문제를 보고하려면 GitHub의 문제 기능을 사용합니다.
> 
> 이러한 도구를 사용하여 마이그레이션을 수행하면 클래식 Virtual Machine의 가동이 중지됩니다. 플랫폼에서 지원하는 마이그레이션 방법은 다음 페이지를 참조하세요. 
> 
>   * [클래식에서 Azure Resource Manager 스택으로 IaaS 리소스의 플랫폼 지원 마이그레이션](migration-classic-resource-manager-overview.md)
>   * [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)
>   * [Azure PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Azure 서비스 관리에서 Azure Resource Manager로의 엔터프라이즈 마이그레이션 과정에서 생성되는 도우미 도구의 컬렉션입니다. 이 도구를 사용하면 인프라를 마이그레이션 테스트에 사용할 수 있는 다른 구독으로 복제하고, 프로덕션 구독에서 마이그레이션을 실행하기 전에 문제를 해결할 수 있습니다.

[도구 설명서 링크](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz는 전체 클래식 IaaS 리소스 집합을 Azure Resource Manager IaaS 리소스로 마이그레이션할 수 있는 추가 옵션입니다. 동일한 구독 내에서 또는 다른 구독 및 구독 형식 간에 마이그레이션이 발생할 수 있습니다 (예: CSP 구독)입니다.

[도구 설명서 링크](https://github.com/Azure/migAz)

## <a name="next-steps"></a>다음 단계

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

