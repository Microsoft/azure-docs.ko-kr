---
title: Visual Studio에서 Azure Service Fabric 애플리케이션 관리 | Microsoft Docs
description: Visual Studio를 사용하여 Azure Service Fabric 애플리케이션과 서비스를 만들고, 개발하고, 배포하고, 디버그합니다.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 4744858869e10094389be58ddd3960cb8cc2773a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720102"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Visual Studio를 사용하여 Service Fabric 애플리케이션 쓰기 및 관리 단순화하기
Visual Studio를 통해 Azure Service Fabric 애플리케이션 및 서비스를 관리할 수 있습니다. [개발 환경을 설정](service-fabric-get-started.md)한 후, Visual Studio를 사용하여 로컬 개발 클러스터에서 Service Fabric 애플리케이션을 만들거나 서비스를 추가하거나 애플리케이션의 패키징, 등록 및 배포를 수행할 수 있습니다.

## <a name="deploy-your-service-fabric-application"></a>Service Fabric 애플리케이션 배포
기본적으로 애플리케이션을 배포하는 작업은 다음 단계를 하나의 간단한 작업으로 통합합니다.

1. 애플리케이션 패키지 만들기
2. 이미지 저장소에 애플리케이션 패키지 업로드
3. 애플리케이션 유형 등록
4. 실행 중인 모든 애플리케이션 인스턴스 제거
5. 애플리케이션 인스턴스 만들기

Visual Studio에서 **F5** 키를 누르면 애플리케이션이 배포되고 모든 애플리케이션 인스턴스에 디버거가 첨부됩니다. **Ctrl+F5** 를 사용하여 디버그하지 않고 애플리케이션을 배포하거나, 게시 프로필을 사용하여 로컬 또는 원격 클러스터에 게시할 수 있습니다.

### <a name="application-debug-mode"></a>애플리케이션 디버그 모드
Visual Studio는 Visual Studio에서 디버깅의 일부로 애플리케이션 배포를 처리하는 방법을 제어하는 **애플리케이션 디버그 모드**라는 속성을 제공합니다.

#### <a name="to-set-the-application-debug-mode-property"></a>애플리케이션 디버그 모드 속성을 설정하려면
1. Service Fabric 애플리케이션 프로젝트의 (*.sfproj) 바로 가기 메뉴에서 **속성**을 선택합니다(또는 **F4** 키 누름).
2. **속성** 창에서 **애플리케이션 디버그 모드** 속성을 설정합니다.

![애플리케이션 디버그 모드 속성 설정][debugmodeproperty]

#### <a name="application-debug-modes"></a>애플리케이션 디버그 모드

1. **애플리케이션 새로 고침** 이 모드를 사용하면 코드를 신속하게 변경하고 디버그할 수 있으며 디버깅하는 동안 정적 웹 파일 편집을 지원합니다. 이 모드는 로컬 개발 클러스터가 1-노드 모드인 경우에만 작동합니다. 기본 애플리케이션 디버그 모드입니다.
2. **애플리케이션 제거** 를 선택하면 디버그 세션이 종료될 때 애플리케이션이 제거됩니다.
3. **자동 업그레이드** 디버그 세션이 종료될 때 애플리케이션이 계속 실행됩니다. 다음 디버그 세션은 업그레이드로 배포를 처리합니다. 업그레이드 프로세스는 이전 디버그 세션에서 입력한 모든 데이터를 유지합니다.
4. **애플리케이션 유지** 디버그 세션이 종료될 때 클러스터에서 애플리케이션이 계속 실행됩니다. 다음 디버그 세션의 시작 부분에서 애플리케이션이 제거됩니다.

**자동 업그레이드**의 경우 데이터는 Service Fabric의 애플리케이션 업그레이드 기능을 적용하여 보존됩니다. 애플리케이션 업그레이드 및 실제 환경에서 업그레이드를 수행하는 방법에 대한 자세한 내용은 [Service Fabric 애플리케이션 업그레이드](service-fabric-application-upgrade.md)를 참조하세요.

## <a name="add-a-service-to-your-service-fabric-application"></a>Service Fabric 애플리케이션에 서비스 추가
애플리케이션에 새 서비스를 추가하여 기능을 확장할 수 있습니다. 애플리케이션 패키지에 서비스가 포함되도록 하려면 **새 패브릭 서비스...** 메뉴 항목을 통해 서비스를 추가합니다.

![새 Service Fabric 서비스 추가][newservice]

애플리케이션에 추가할 Service Fabric 프로젝트 유형을 선택하고 서비스의 이름을 지정합니다.  사용할 서비스 유형을 결정하는 데 도움이 필요하면 [서비스를 위한 프레임워크 선택](service-fabric-choose-framework.md) 을 참조하세요.

![애플리케이션에 추가할 Service Fabric 서비스 프로젝트 유형 선택][addserviceproject]

새 서비스가 솔루션 및 기존 애플리케이션 패키지에 추가됩니다. 서비스 참조 및 기본 서비스 인스턴스는 다음에 애플리케이션을 배포할 때 서비스가 만들어지고 시작되도록 애플리케이션 매니페스트에 추가됩니다.

![새 서비스가 애플리케이션 매니페스트에 추가됩니다.][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Service Fabric 애플리케이션 패키징
애플리케이션 및 해당 서비스를 클러스터에 배포하려면 애플리케이션 패키지를 만들어야 합니다.  패키지는 애플리케이션 매니페스트, 서비스 매니페스트 및 특정 레이아웃에서 필요한 기타 파일로 구성됩니다.  Visual Studio는 애플리케이션 프로젝트의 폴더인 'pkg' 디렉터리에서 패키지를 관리합니다.  **애플리케이션** 상황에 맞는 메뉴에서 **패키지**를 클릭하면 애플리케이션 패키지가 생성되거나 업데이트됩니다.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>클라우드 탐색기를 사용하여 애플리케이션 및 애플리케이션 유형 제거
**보기** 메뉴에서 시작할 수 있는 클라우드 탐색기를 사용하여 Visual Studio 내에서 기본 클러스터 관리 작업을 수행할 수 있습니다. 예를 들어 애플리케이션을 삭제하고 로컬 또는 원격 클러스터에서 애플리케이션 유형을 프로비전 해제할 수 있습니다.

![애플리케이션 제거][removeapplication]

> [!TIP]
> 다양한 클러스터 관리 기능은 [Service Fabric Explorer로 클러스터 시각화](service-fabric-visualizing-your-cluster.md)를 참조하세요.
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
* [Service Fabric 애플리케이션 모델](service-fabric-application-model.md)
* [Service Fabric 애플리케이션 배포](service-fabric-deploy-remove-applications.md)
* [여러 환경에 대한 애플리케이션 매개 변수 관리](service-fabric-manage-multiple-environment-app-configuration.md)
* [Service Fabric 애플리케이션 디버깅](service-fabric-debugging-your-application.md)
* [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
