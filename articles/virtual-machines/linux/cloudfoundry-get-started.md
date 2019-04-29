---
title: Microsoft Azure의 Cloud Foundry 시작 | Microsoft Docs
description: Microsoft Azure에서 OSS 또는 Pivotal Cloud Foundry 실행
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 68ae01b814de08098c0ba6b5713f420cfebc3d97
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127030"
---
# <a name="cloud-foundry-on-azure"></a>Azure의 Cloud Foundry

Cloud Foundry는 다양한 언어 및 프레임워크로 개발되는 12개 요소(12-factor) 애플리케이션을 빌드하고, 배포하고, 운영하기 위한 오픈 소스 PaaS(Platform-as-a-Service)입니다. 이 문서는 Azure에서 Cloud Foundry를 실행할 수 있는 옵션과 시작하는 방법을 설명합니다.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry 제품

Azure에서 실행할 수 있는 Cloud Foundry에는 오픈 소스 Cloud Foundry(OSS CF) 및 Pivotal Cloud Foundry(PCF)라는 두 가지 형식이 있습니다. OSS CF는 Cloud Foundry Foundation에서 관리하는 Cloud Foundry의 완전한 [오픈-소스](https://github.com/cloudfoundry) 버전입니다. Pivotal Cloud Foundry는 Pivotal Software i n c.에서 Cloud foundry 엔터프라이즈 배포판 두 제품 간의 차이점 중 일부에 대해 살펴봅니다.

### <a name="open-source-cloud-foundry"></a>오픈 소스 Cloud Foundry

Azure에 OSS Cloud Foundry를 배포하려면 [GitHub에 제공된 지침](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)을 사용하여 우선 BOSH 디렉터를 배포한 다음 Cloud Foundry를 배포합니다. OSS CF 사용에 대한 자세한 내용은 Cloud Foundry Foundation에서 제공하는 [설명서](https://docs.cloudfoundry.org/)를 참조하세요.

Microsoft는 다음과 같은 커뮤니티 채널을 통해 OSS CF에 대한 최선의 지원을 제공합니다.

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>[Cloud Foundry Slack](https://slack.cloudfoundry.org/)의 bosh-azure-cpi 채널
- [cf-bosh mailing list](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) 및 [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues)에 대한 GitHub Issues

>[!NOTE]
> Cloud Foundry를 실행하는 가상 머신과 같은 Azure 리소스에 대한 지원 수준은 Azure 지원 계약을 기반으로 합니다. 최선의 커뮤니티 지원은 Cloud Foundry에 해당하는 구성 요소에만 적용됩니다.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry는 독점적인 관리 도구 및 엔터프라이즈 지원과 더불어 OSS 배포판과 동일한 핵심 플랫폼을 포함합니다. PCF를 Azure에서 실행하려면 Pivotal로부터 라이선스를 취득해야 합니다. Azure Marketplace의 PCF 제품에는 90일 평가판 라이선스가 포함됩니다.

이 도구는 Cloud Foundry Foundation의 배포 및 관리를 간소화하는 웹 애플리케이션인 [Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/)와 사용자 및 애플리케이션 관리를 위한 웹 애플리케이션인 [Pivotal Apps Manager](https://docs.pivotal.io/pivotalcf/console/)를 포함합니다.

위에 나열된 OSS CF에 대한 지원 채널 외에도 PCF 라이선스를 취득하면 Pivotal에 지원을 요청할 수 있습니다. Microsoft와 Pivotal에는 두 회사 중 어느 곳에라도 지원을 요청하면 문제의 소재에 따라 적절하게 질의가 전달될 수 있는 지원 워크플로가 설정되어 있습니다.

## <a name="azure-service-broker"></a>Service Broker

Cloud Foundry는 상태 비저장 애플리케이션 프로세스와 상태 저장 백업 서비스의 완전한 분리를 조장하는 ["twelve-factor app"](https://12factor.net/) 방법론을 장려합니다. [Service brokers](https://docs.cloudfoundry.org/services/api.html)는 애플리케이션에 대한 백업 서비스를 바인딩하고 프로비전하는 일관적인 방법을 제공합니다. [Azure Service Broker](https://github.com/Azure/meta-azure-service-broker)는 이러한 채널을 통해 Azure Storage 및 Azure SQL을 비롯한 주요한 Azure 서비스를 제공합니다.

Pivotal Cloud Foundry를 사용하는 경우 Service Broker는 Pivotal Network의 [타일로도 제공](https://docs.pivotal.io/azure-sb/installing.html)됩니다.

## <a name="related-resources"></a>관련 리소스

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services 플러그인

Cloud Foundry는 연속 통합(CI) 및 지속적인 업데이트(CD) 사용을 포함하는 Agile 소프트웨어 개발에 적합합니다. Azure DevOps Services를 사용하여 프로젝트를 관리하는 경우 Cloud Foundry를 대상으로 하는 CI/CD 파이프라인을 설정하려면 [Azure DevOps Services Cloud Foundry 빌드 확장](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension)을 사용할 수 있습니다. 이 플러그 인은 Azure 또는 기타 환경에서 실행 중인 Cloud Foundry에 대한 배포를 구성하고 자동화하는 작업을 간소화합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Marketplace에서 Pivotal Cloud Foundry 배포](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Azure에서 Cloud Foundry에 앱 배포](./cloudfoundry-deploy-your-first-app.md)
