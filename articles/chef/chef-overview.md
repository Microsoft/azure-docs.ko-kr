---
title: Azure로 Chef 사용하기
description: Azure 인프라를 구성하고 테스트하기 위한 Chef 사용 소개
ms.service: virtual-machines-linux
keywords: azure, chef, devops, 가상 머신, 개요, 자동화
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 5c4e20177c1b334a34f7ce9328dfaa7bd8c66d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656577"
---
# <a name="using-chef-with-azure"></a>Azure로 Chef 사용하기
[Chef](https://www.chef.io)는 Azure에서 가상 머신 인프라를 코드로 변환하는 강력한 자동화 플랫폼입니다. Chef는 크기에 관계 없이 네트워크를 통해 인프라를 구성하고 배포하고 관리하는 방법을 자동화합니다.

이 문서에서는 Chef를 사용하여 Azure 인프라를 관리하는 이점에 대해 설명합니다.

## <a name="chef-extension-on-azure"></a>Azure의 Chef 확장
Azure Portal에서 [Chef 확장](https://docs.microsoft.com/azure/chef/chef-extension-portal)을 통해 Chef 클라이언트를 백그라운드 서비스로 실행하여 가상 머신을 프로비전합니다. 프로비전되고 나면 이러한 가상 머신을 Chef 서버에서 관리할 준비가 된 것입니다.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Azure Cloud Shell에서 Chef 워크스테이션을 직접 사용하세요! Cloud Shell에서 바로, 모든 Chef 유틸리티 및 InSpec을 실행합니다. 다음에서 Chef 명령을 이용할 수 있습니다.

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

명령 유틸리티를 Cloud Shell에서 사용할 수 있는 다른 도구(`git`, `az-cli` 및 `terraform`)와 결합하여 브라우저에서 인프라 및 규정 준수 자동화를 작성합니다.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>인프라, 앱 및 하나의 플랫폼 준수 자동화
회사는 디지털 시장에서 경쟁하려면 속도, 개발속도 및 안전을 요구합니다. Chef 및 Microsoft는 개인, 팀 및 기업이 이러한 모든 작업을 완수하도록 도와줍니다. 하나의 플랫폼인 Chef Automate를 사용하여 이제 Microsoft 자산 전체에서 인프라, 애플리케이션 및 규정 준수를 자동화하고 지속적으로 제공할 수 있습니다.

## <a name="test-drive-chef-automate-on-azure"></a>Azure에서 Chef 자동화 시험 사용
Chef의 지원을 받아 [Chef Automate Azure Marketplace 솔루션](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate)은 인프라와 애플리케이션을 공동으로 빌드하고 배포하고 관리할 수 있습니다. 한 번의 클릭으로 Chef Automate에 포함된 모든 상용 기능에 즉각적으로 액세스하고, 전체 장치에 대한 통합 가시성을 확보하며, 지속적인 준수를 가능하게 하고, 통합 워크플로를 통해 모든 변경 사항을 관리할 수 ​​있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Chef를 사용하여 Windows 가상 머신 만들기](/azure/virtual-machines/windows/chef-automation)
