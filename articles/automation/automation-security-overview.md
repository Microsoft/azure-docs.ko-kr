---
title: Azure Automation의 인증 소개
description: 이 문서는 Automation 보안의 개요와 Azure Automation의 Automation 계정에 사용 가능한 다양한 인증 방법에 대해 설명합니다.
keywords: 자동화 보안, 안전한 자동화, 자동화 인증
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ROBOTS: NOINDEX
ms.openlocfilehash: 60175a5b9309c08d5fdab997469a9d0526afaefe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737026"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Azure Automation의 인증 소개  
Azure Automation을 사용하여 Azure, 온-프레미스 및 AWS(Amazon 웹 서비스)와 같은 기타 클라우드 공급자의 리소스에 대해 작업을 자동화할 수 있습니다.  Runbook이 필요한 작업을 수행하려면 구독 내에서 최소의 권한으로 리소스에 안전하게 액세스할 수 있는 권한이 있어야 합니다.

이 문서에서는 Azure Automation에서 지원하는 다양한 인증 시나리오를 살펴보고 관리해야 하는 환경에 따라 시작하는 방법에 대해 설명합니다.  

## <a name="automation-account-overview"></a>Automation 계정 개요
Azure Automation을 처음 시작하려면 Automation 계정을 하나 이상 만들어야 합니다. Automation 계정을 사용하면 다른 Automation 계정에 포함된 리소스에서 사용자의 Automation 리소스(Runbook, 자산, 구성)를 격리할 수 있습니다. Automation 계정을 사용하여 리소스를 별도의 논리적 환경으로 구분할 수 있습니다. 예를 들어 개발, 프로덕션, 온-프레미스 환경에 서로 다른 계정을 사용할 수 있습니다.  Azure Automation 계정은 Microsoft 계정 또는 Azure 구독에서 만든 계정과 다릅니다.

각 Automation 계정의 Automation 리소스는 단일 Azure 지역과 연결되지만 Automation 계정은 구독 내 모든 리소스를 관리할 수 있습니다. 여러 지역에서 Automation 계정을 만드는 주된 이유는 데이터 및 리소스를 특정 지역으로 격리해야 하는 정책이 있을 수 있기 때문입니다.

Azure Automation에서 Azure Resource Manager 및 Azure cmdlet을 사용하여 리소스에 대해 수행하는 모든 작업은 Azure Active Directory 조직 ID 자격 증명 기반 인증을 사용하여 Azure에 인증해야 합니다.  원래는 Azure 클래식에서 인증서 기반 인증을 사용했지만 설치 방법이 복잡합니다.  Azure AD 사용자로 Azure에 인증하는 방법은 2014년에 인증 계정 구성을 간소화하는 한편 Azure Resource Manager와 클래식 리소스와 연동하는 단일 사용자 계정으로 Azure에 비 대화식으로 인증하는 기능을 지원하기 위해 도입되었습니다.   

현재 Azure Portal에서 새 Automation 계정을 만들 경우 다음을 자동으로 만듭니다.

* Azure Active Directory에서 새 서비스 주체, 인증서를 만들고 Runbook을 사용하여 Resource Manager 리소스를 관리하는 데 사용될 참여자 RBAC(역할 기반 액세스 제어)를 할당하는 실행 계정.
* Runbook을 사용하여 Azure 클래식 리소스를 관리하는 데 사용되는 관리 인증서를 업로드하는 클래식 실행 계정.  

Azure Resource Manager에서 역할 기반 액세스 제어를 사용하여 Azure AD 사용자 계정 및 실행 계정에 허용된 작업을 수락하고 서비스 주체를 인증할 수 있습니다.  Automation 사용 권한 관리 모델을 개발하는 방법에 대한 자세한 내용은 [Azure Automation에서 역할 기반 액세스 제어 문서](automation-role-based-access-control.md)를 참조하세요.  

데이터 센터의 Hybrid Runbook Worker에서 실행되거나 AWS의 계산 서비스에 대해 실행되는 Runbook은 일반적으로 Azure 리소스를 인증하는 Runbook에 사용되는 것과 동일한 방법을 사용할 수 없습니다.  이러한 리소스는 Azure 외부에서 실행되므로 로컬로 액세스하는 리소스에 인증하려면 Automation에서 정의한 자체 보안 자격 증명이 필요하기 때문입니다.  

## <a name="authentication-methods"></a>인증 방법
다음 테이블에는 Azure Automation에서 지원하는 각 환경에 대한 다양한 인증 방법과 Runbook에 대한 인증 설정 방법을 설명하는 문서가 요약되어 있습니다.

| 방법 | Environment | 문서 |
| --- | --- | --- |
| Azure AD 사용자 계정 만들기 |Azure Resource Manager 및 Azure 클래식 |[Azure AD 사용자 계정으로 Runbook 인증](automation-create-aduser-account.md) |
| Azure 실행 계정 |Azure 리소스 관리자 |[Azure 실행 계정으로 Runbook 인증](automation-sec-configure-azure-runas-account.md) |
| Azure 클래식 실행 계정 |Azure 클래식 |[Azure 실행 계정으로 Runbook 인증](automation-sec-configure-azure-runas-account.md) |
| Windows 인증 |온-프레미스 데이터 센터 |[Hybrid Runbook Worker용 Runbook 인증](automation-hybrid-runbook-worker.md) |
| AWS 자격 증명 |Amazon 웹 서비스 |[AWS(Amazon 웹 서비스)로 Runbook 인증](automation-config-aws-account.md) |

