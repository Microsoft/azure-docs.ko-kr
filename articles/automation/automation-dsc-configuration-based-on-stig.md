---
title: 상태 구성에서 사용 하기 위한 STIG 기반 구성-Azure Automation
description: Azure Automation에서 상태 구성을 위한 STIG를 기반으로 하는 구성에 대해 알아봅니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028283"
---
# <a name="configuration-based-on-stig"></a>STIG 기반 구성

> 적용 대상: Windows PowerShell 5.1

처음으로 구성 콘텐츠를 만드는 것은 어려울 수 있습니다.
대부분의 경우 목표는 업계 권장 사항에 부합 하는 "기준"에 따라 서버 구성을 자동화 하는 것입니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리 하는 솔루션을 의미 합니다.
> 지원은 Microsoft가 아닌 GitHub 공동 작업의 형태로만 사용할 수 있습니다.

## <a name="community-project-powerstig"></a>커뮤니티 프로젝트: PowerSTIG

[Powerstig](https://github.com/microsoft/powerstig) 라는 커뮤니티 프로젝트는 stig (보안 기술 구현 가이드)에 대해 제공 된 [공개 정보](https://public.cyber.mil/stigs/) 에 따라 DSC 콘텐츠를 생성 하 여이 문제를 해결 하는 데 목적이 있습니다.

기준에 대한 처리는 소리 보다 복잡 합니다.
많은 조직에서 규칙에 대한 [예외를 문서화](https://github.com/microsoft/powerstig#powerstigdata) 하 고 대규모로 해당 데이터를 관리 해야 합니다.
PowerSTIG는 단일 파일에서 전체 설정 범위를 처리 하지 않고 구성의 각 영역을 처리 하는 [복합 리소스](https://github.com/microsoft/powerstig#powerstigdsc) 를 제공 하 여 문제를 해결 합니다.

구성이 생성 된 후에는 [DSC 구성 스크립트](/powershell/scripting/dsc/configurations/configurations) 를 사용 하 여 mof 파일을 생성 하 고 [Azure Automation에 mof 파일을 업로드할](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)수 있습니다.
그런 다음 [온-프레미스](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) 또는 [Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) 에서 서버를 등록 하 여 구성을 가져옵니다.

PowerSTIG를 사용해 보려면 [PowerShell 갤러리](https://www.powershellgallery.com) 를 방문 하 여 솔루션을 다운로드 하거나 "프로젝트 사이트"를 클릭 하 여 [설명서](https://github.com/microsoft/powerstig)를 확인 하세요.

## <a name="next-steps"></a>다음 단계

- [Windows PowerShell Desired State Configuration 개요](/powershell/scripting/dsc/overview/overview)
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)
- [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)
