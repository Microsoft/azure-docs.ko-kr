---
title: Azure 데이터 과학 Virtual Machines를 사용 합니다.
description: 에 Azure 가상 머신 DSVM (데이터 과학) Azure Notebook에 사용할 수 있는 계산 능력 확장에 연결 합니다.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: getroyer
ms.openlocfilehash: 0ac50a5f52682c4315b8d08cf5632c4a6fa5242f
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357576"
---
# <a name="use-azure-data-science-virtual-machines"></a>Azure 데이터 과학 Virtual Machines를 사용 합니다.

기본적으로 프로젝트에서 실행 합니다 **무료 계산** 4GB의 메모리 및 1GB의 데이터 남용을 방지 하기 위해 제한 되는 계층입니다. Azure 구독에서 프로비전한 다른 가상 머신을 사용하여 이러한 제한 사항을 무시할 수 있습니다. 이 작업을 위해 가장 좋은 방법은 Azure 데이터 과학 Virtual Machine (DSVM)를 사용 하는 **Linux (Ubuntu) 용 데이터 과학 Virtual Machine** 이미지입니다. DSVM을 미리 구성 된 모든 Azure Notebooks에 필요한 하에서 자동으로 표시 되는 합니다 **실행** Azure Notebooks의 드롭 다운 목록.

> [!Note]
> Azure Notebooks on Linux Ubuntu 이미지를 사용 하 여 만든 Dsvm 에서만 지원 됩니다. Windows 2012, Windows 2016 또는 CentOS Linux 이미지에서 notebook은 사용할 수 없습니다.

## <a name="create-a-dsvm-instance"></a>DSVM 인스턴스 만들기

새 DSVM 인스턴스를 만들려면 [Ubuntu Data Science VM 만들기](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)의 지침을 따르세요. 가격 책정 세부 정보를 포함 하 여 자세한 내용은 [데이터 과학 Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)합니다.

## <a name="connect-to-the-dsvm"></a>DSVM에 연결

DSVM 만든 후 선택 합니다 **실행** Azure Notebook에서 드롭 다운 목록 대시보드, 프로젝트 및 적절 한 DSVM 인스턴스를 선택 합니다. 드롭다운 목록에서 다음 조건이 true 인 경우 DSVM 인스턴스를 보여 줍니다.

- 회사 계정 등의 AAD(Azure Active Directory)를 사용하는 계정으로 Azure Notebooks에 로그인했습니다.
- 계정이 Azure 구독에 연결되어 있습니다.
- 하나 이상의 가상 머신이 있는 해당 구독에서 사용 하 여 적어도 Linux (Ubuntu) 이미지에 대 한 데이터 과학 Virtual Machine을 사용 하는 판독기 액세스 합니다.)

![프로젝트 대시보드의 드롭다운 목록에 있는 Data Science Virtual Machine 인스턴스](media/project-compute-tier-dsvm.png)

DSVM 인스턴스를 선택하는 경우 Azure Notebooks에서 VM을 만들 때 사용한 특정 머신 자격 증명을 묻는 메시지가 표시될 수도 있습니다.

조건 중 하나가 충족 되지 않으면 DSVM에 연결할 수 있습니다. 드롭다운 목록에서 선택 합니다 **직접 계산** (목록에 표시) 하는 이름, VM의 IP 주소 및 포트 (일반적으로 8000 JupyterHub 수신 대기 하는 기본 포트), 및 VM 자격 증명에 대 한 라는 메시지를 표시 하는 옵션:

![직접 컴퓨팅 옵션에 대한 서버 정보 수집 프롬프트](media/project-compute-tier-direct.png)

Azure portal에서 DSVM 페이지에서 이러한 값을 가져옵니다.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>DSVM에서 Azure Notebooks 파일 액세스

파일 시스템 액세스는 이상 19.06.15 DSVM 버전에 대 한 지원 됩니다. 버전을 확인 하려면 먼저 SSH (IP 주소는 Azure portal에서 사용할 수 있는)를 통해 DSVM에 연결 합니다. 다음을 사용 하 여 다음 명령을 실행 하면 `<ip_address>`: `curl -H Metadata:true "http://<ip_address>/metadata/instance?api-version=2018-10-01"`합니다. 버전 번호를 "version"에 대 한 출력에 표시 됩니다.

파일 경로와 패리티를 유지 하는 **무료 계산** DSVM에서 한 번에 하나의 프로젝트 에서만 열 수 있는 계층입니다. 새 프로젝트를 열려면를 종료 해야 열려 있는 프로젝트 우선 합니다.

파일은 Jupyter 서버 (JupyterHub에 표시 된 디렉터리)의 루트 디렉터리에 탑재 된 프로젝트를 VM에서 실행 되 면 기본 Azure Notebooks 파일을 대체 합니다. 사용 하 여 VM 종료 합니다 **종료** Azure Notebooks notebook UI의 단추는 기본 파일을 복원 합니다.

![Azure Notebooks 종료 단추](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>새 DSVM 사용자 만들기

여러 사용자가 공유할 DSVM을 만들고 각 노트북 사용자에 대 한 DSVM 사용자를 사용 하 여 서로 차단 것을 방지할 수 있습니다.

1. 에 [Azure Portal](https://portal.azure.com), 가상 컴퓨터를 이동 합니다.
1. 아래 **지원 + 문제 해결** 왼쪽된 여백에서 선택 **암호 재설정**합니다.
1. 새 사용자 이름 및 암호를 입력 하 고 선택 **업데이트**합니다. (기존 사용자 이름 적용 되지 않습니다.)
1. 모든 추가 사용자에 대해 이전 단계를 반복 합니다.

## <a name="next-steps"></a>다음 단계

Dsvm에 대 한 자세히 알아보세요 [도입을 Azure 데이터 과학 Virtual Machines](/azure/machine-learning/data-science-virtual-machine/overview)합니다.
