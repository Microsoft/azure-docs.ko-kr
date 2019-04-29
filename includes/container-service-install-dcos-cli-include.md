---
title: DC/OS CLI 설치 | Microsoft 문서
description: DC/OS CLI를 설치합니다.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: 컨테이너, 마이크로 서비스, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: e2601d5200f0b8ebcfb856bffb871f01b3acb215
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557586"
---
> [!NOTE]
> DC/OS 기반 ACS 클러스터와 함께 작업하기 위한 것입니다. Swarm 기반 ACS 클러스터에 대해 이 작업을 수행하지 않아도 됩니다.
> 
> 

먼저, [DC/OS 기반 ACS 클러스터에 연결](../articles/container-service/container-service-connect.md)합니다. 이 작업을 수행한 후 아래 명령을 사용하여 클라이언트 컴퓨터에서 DC/OS CLI를 설치할 수 있습니다.

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

이전 버전의 Python을 사용하는 경우 몇 가지 "InsecurePlatformWarnings"가 발생할 수 있습니다. 무시해도 됩니다.

셸을 다시 시작하지 않고 시작하려면 다음을 실행합니다.

```bash
source ~/.bashrc
```

이 단계는 새 셸을 시작할 때 반드시 필요하지는 않습니다.

이제 CLI가 설치되어 있는 것을 확인할 수 있습니다.

```bash
dcos --help
```