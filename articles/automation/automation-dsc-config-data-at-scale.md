---
title: 규모 Azure Automation의 구성 데이터
description: Azure Automation에서 상태 구성에 맞게 데이터를 구성 하는 방법에 대해 알아봅니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a77a366ee8e06b2d8c47eb3b47eeaf9ae809598
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028293"
---
# <a name="configuration-data-at-scale"></a>규모에 맞는 구성 데이터

> 적용 대상: Windows PowerShell 5.1

수백 또는 수천 대의 서버를 관리 하는 것은 쉽지 않을 수 있습니다.
고객은 가장 어려운 측면에서 실제로 [구성 데이터](/powershell/scripting/dsc/configurations/configdata)를 관리 하는 데 대한 피드백을 제공 했습니다.
위치, 형식, 환경 등의 논리적 구문으로 정보를 구성 합니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리 하는 솔루션을 의미 합니다.
> 지원은 Microsoft가 아닌 GitHub 공동 작업의 형태로만 사용할 수 있습니다.

## <a name="community-project-datum"></a>커뮤니티 프로젝트: 데이텀

이 챌린지를 해결 하기 위해 [데이텀](https://github.com/gaelcolas/Datum) 이라는 커뮤니티에서 유지 관리 하는 솔루션을 만들었습니다.
데이텀은 다른 구성 관리 플랫폼에서 뛰어난 아이디어를 빌드하고 PowerShell DSC에 대해 동일한 유형의 솔루션을 구현 합니다.
정보는 논리적 아이디어에 따라 [텍스트 파일에 구성](https://github.com/gaelcolas/Datum#3-intended-usage) 됩니다.
예제는 다음과 같습니다.

- 전역적으로 적용 해야 하는 설정
- 한 위치의 모든 서버에 적용 해야 하는 설정
- 모든 데이터베이스 서버에 적용 해야 하는 설정
- 개별 서버 설정

이 정보는 선호 하는 파일 형식 (JSON, Yaml 또는 PSD1)으로 구성 됩니다.
그런 다음 cmdlet을 제공 하 여 각 파일의 [정보](https://github.com/gaelcolas/Datum#datum-tree) 를 서버 또는 서버 역할의 단일 뷰로 통합 함으로써 구성 데이터 파일을 생성 합니다.

데이터 파일을 생성 한 후에는 [DSC 구성 스크립트](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) 와 함께 사용 하 여 mof 파일을 생성 하 고 [Azure Automation에 mof 파일을 업로드할](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)수 있습니다.
그런 다음 [온-프레미스](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) 또는 [Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) 에서 서버를 등록 하 여 구성을 가져옵니다.

데이텀을 확인 하려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages/datum/) 방문 하 여 솔루션을 다운로드 하거나 "프로젝트 사이트"를 클릭 하 여 [설명서](https://github.com/gaelcolas/Datum#2-getting-started--concepts)를 확인 하세요.

## <a name="next-steps"></a>다음 단계

- [Windows PowerShell Desired State Configuration 개요](/powershell/scripting/dsc/overview/overview)
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)
- [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)
