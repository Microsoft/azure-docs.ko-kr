---
title: 구성을 상태 구성에 대한 복합 리소스로 변환-Azure Automation
description: Azure Automation에서 상태 구성에 대한 구성을 복합 리소스로 변환 하는 방법에 대해 알아봅니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c060462a84b197cdd0aec1beecc0b4d4807feb49
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030106"
---
# <a name="convert-configurations-to-composite-resources"></a>구성을 복합 리소스로 변환

> 적용 대상: Windows PowerShell 5.1

구성 제작을 시작한 후에는 설정 그룹을 관리 하는 "시나리오"를 빠르게 만들 수 있습니다.
예제는 다음과 같습니다.

- 웹 서버 만들기
- DNS 서버 만들기
- SharePoint 서버 만들기
- SQL 클러스터 구성
- 방화벽 설정 관리
- 암호 설정 관리

이 작업을 다른 사용자와 공유 하는 데 관심이 있는 경우 가장 좋은 방법은 구성을 [복합 리소스로](/powershell/scripting/dsc/resources/authoringresourcecomposite)패키징하는 것입니다.
처음으로 복합 리소스를 만드는 것은 매우 어려울 수 있습니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리 하는 솔루션을 의미 합니다.
> 지원은 Microsoft가 아닌 GitHub 공동 작업의 형태로만 사용할 수 있습니다.

## <a name="community-project-compositeresource"></a>커뮤니티 프로젝트: CompositeResource

[CompositeResource](https://github.com/microsoft/compositeresource) 라는 커뮤니티에서 유지 관리 하는 솔루션을 만들어이 문제를 해결 했습니다.

CompositeResource는 구성에서 새 모듈을 만드는 프로세스를 자동화 합니다.
메모리에 로드 되도록 워크스테이션 (또는 빌드 서버)에서 구성 스크립트를 [소싱](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) 하 여 시작 합니다.
그런 다음 구성을 실행 하 여 MOF 파일을 생성 하는 대신 CompositeResource 모듈에서 제공 하는 함수를 사용 하 여 변환을 자동화 합니다.
Cmdlet은 구성의 콘텐츠를 로드 하 고, 매개 변수 목록을 가져오고, 필요한 모든 항목을 포함 하는 새 모듈을 생성 합니다.

모듈을 생성 한 후에는 버전을 증가 시키고 변경할 때마다 릴리스 정보를 추가 하 고 사용자의 [PowerShellGet 리포지토리에](https://kevinmarquette.github.io/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)게시할 수 있습니다.

구성 (또는 여러 구성)을 포함 하는 복합 리소스 모듈을 만든 후에는 Azure의 구성 가능한 [작성 환경](/azure/automation/compose-configurationwithcompositeresources) 에서 사용 하거나, [DSC 구성 스크립트](/powershell/scripting/dsc/configurations/configurations) 에 추가 하 여 mof 파일을 생성 하 고 [mof 파일을 Azure Automation로 업로드할](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)수 있습니다.
그런 다음 [온-프레미스](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) 또는 [Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) 에서 서버를 등록 하 여 구성을 가져옵니다.
프로젝트에 대한 최신 업데이트에는 PowerShell 갤러리에서 구성 가져오기 프로세스를 자동화 하는 Azure Automation에 대한 [runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) 도 게시 되어 있습니다.

DSC에 대한 복합 리소스 만들기를 자동화 하려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages/compositeresource/) 방문 하 여 솔루션을 다운로드 하거나 "프로젝트 사이트"를 클릭 하 여 [설명서](https://github.com/microsoft/compositeresource)를 확인 하세요.

## <a name="next-steps"></a>다음 단계

- [Windows PowerShell Desired State Configuration 개요](/powershell/scripting/dsc/overview/overview)
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)
- [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)
