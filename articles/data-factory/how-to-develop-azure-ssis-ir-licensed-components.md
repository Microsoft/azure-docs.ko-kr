---
title: Azure-SSIS 통합 런타임에 대한 라이선스 구성 요소 설치 | Microsoft Docs
description: ISV가 Azure-SSIS 통합 런타임에 대한 유료 또는 라이선스 사용자 지정 구성 요소를 개발하고 설치하는 방법을 알아봄
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4fff03422beacccd3aff3cdd8cb1047d5f5af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344656"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임에 대한 라이선스 사용자 지정 구성 요소 설치

이 문서에서는 ISV가 Azure-SSIS 통합 런타임의 Azure에서 실행되는 SSIS(SQL Server Integration Services) 패키지에 대한 유료 또는 사용자 지정 구성 요소를 개발하고 설치하는 방법을 설명합니다.

## <a name="the-problem"></a>문제

Azure-SSIS Integration Runtime은 기본적으로 사용자 지정 구성 요소의 온-프레미스 설치에 사용되는 일반적인 라이선싱 방법을 부적절하게 만드는 몇 가지 문제를 포함합니다. 결과적으로 Azure SSIS IR에는 다른 접근 방법이 필요합니다.

-   Azure-SSIS IR의 노드는 일시적이므로 언제든지 할당 또는 해제할 수 있습니다. 예를 들어, 노드를 시작 또는 중지하여 비용을 관리하거나 다양한 노드 크기를 확대 및 축소할 수 있습니다. 결과적으로, MAC 주소 또는 CPU ID가 같은 컴퓨터 관련 정보를 사용하여 특정 노드에 타사 구성 요소 라이선스를 바인딩하는 방식은 더 이상 가능하지 않습니다.

-   또한 노드 수를 언제든지 축소하거나 확장할 수 있으므로 Azure-SSIS IR도 축소하거나 확장할 수 있습니다.

## <a name="the-solution"></a>솔루션

이전 섹션에서 설명된 기존 라이선스 방법에 대한 제한의 결과로 Azure SSIS IR은 새 솔루션을 제공합니다. 이 솔루션은 라이선스 바인딩 및 타사 구성 요소의 유효성 검사를 위한 Windows 환경 변수 및 SSIS 시스템 변수를 사용합니다. ISV는 이러한 변수를 사용하여 클러스터 ID 및 클러스터 노드 수 같은 Azure-SSIS IR에 대한 고유한 영구 정보를 얻을 수 있습니다. 이 정보를 사용하여 ISV는 구성 요소에 대한 라이선스를 *클러스터로* Azure SSIS IR에 바인딩할 수 있습니다. 이 바인딩은 고객이 어떤 방식으로든 Azure-SSIS IR을 시작 또는 중지, 강화 또는 약화, 감축 또는 확장 또는 다시 구성할 경우 변경되지 않는 ID를 사용합니다.

다음 다이어그램은 이러한 새 변수를 사용하는 타사 구성 요소에 대한 표준 설치, 정품 인증 및 라이선스 바인딩, 유효성 검사 흐름을 보여 줍니다.

![라이선스 구성 요소의 설치](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>지침
1. ISV는 다양한 SKU 또는 계층의 라이선스 구성 요소(예: 단일 노드, 최대 5개 노드, 최대 10개 노드 등)를 제공할 수 있습니다. ISV는 고객이 제품을 구매할 때 해당 제품 키를 제공합니다. 또한 ISV는 ISV 설치 스크립트 및 관련 파일을 포함하는 Azure Storage Blob 컨테이너를 제공할 수도 있습니다. 고객은 자신의 저장소 컨테이너에 이러한 파일을 복사하고 자체 제품 키로 수정할 수 있습니다(예: `IsvSetup.exe -pid xxxx-xxxx-xxxx`를 실행하여). 그런 후 고객은 컨테이너의 SAS URI를 매개 변수로 사용하여 Azure-SSIS IR을 프로비전하거나 다시 구성할 수 있습니다. 자세한 내용은 [Azure-SSIS Integration Runtime 사용자 지정 설정](how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요.

2. Azure-SSIS IR이 프로비전되거나 다시 구성될 때 ISV 설치 프로그램이 각 노드에서 실행되어 Windows 환경 변수 `SSIS_CLUSTERID` 및 `SSIS_CLUSTERNODECOUNT`를 쿼리합니다. 그런 다음 Azure-SSIS IR은 라이선스 제품에 대한 클러스터 ID 및 제품 키를 ISV 정품 인증 서버에 제출하여 정품 인증 키를 생성합니다.

3. 정품 인증 키를 받은 후 ISV 설치 프로그램은 각 노드(예: 레지스트리)에 로컬로 키를 저장할 수 있습니다.

4. 고객이 Azure-SSIS IR의 노드에서 ISV 라이선스 구성 요소를 사용하는 패키지를 실행하면 패키지는 로컬에 저장된 정품 인증 키를 읽고, 노드의 클러스터 ID에 대해 유효성 검사를 수행합니다 또한 패키지는 필요에 따라 ISV 정품 인증 서버에 클러스터 노드 수를 보고할 수도 있습니다.

    다음은 정품 인증 키의 유효성을 검사하고 클러스터 노드 수를 보고하는 코드의 예입니다.

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV 파트너

이 블로그 게시물, [엔터프라이즈 버전, 사용자 지정 설치 및 ADF에서 SSIS에 대한 타사 확장성](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)의 끝에서 Azure SSIS IR에 대한 해당 구성 요소 및 확장을 채택한 ISV 파트너의 목록을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

-   [Azure-SSIS Integration Runtime의 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure-SSIS Integration Runtime Enterprise 버전](how-to-configure-azure-ssis-ir-enterprise-edition.md)
