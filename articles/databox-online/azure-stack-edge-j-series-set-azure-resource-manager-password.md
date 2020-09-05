---
title: Azure Stack에 지 GPU 장치에서 Azure Resource Manager 암호를 설정 합니다.
description: Azure PowerShell를 사용 하 여 Azure Stack에 지 GPU에서 실행 되는 Azure Resource Manager에 연결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 6a59510b342f7ebd3969a4bb4fcfd75fffd04804
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254154"
---
# <a name="set-azure-resource-manager-password-on-azure-stack-edge-gpu-device"></a>Azure Stack에 지 GPU 장치에서 Azure Resource Manager 암호 설정

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 문서에서는 Azure Resource Manager 암호를 설정 하는 방법을 설명 합니다. Azure Resource Manager를 통해 장치 로컬 Api에 연결 하는 경우이 암호를 설정 해야 합니다.

암호를 설정 하는 절차는 Azure Portal 또는 PowerShell cmdlet을 사용 하는지 여부에 따라 달라질 수 있습니다. 다음 섹션에서는 이러한 각 절차에 대해 설명 합니다.


## <a name="reset-password-via-the-azure-portal"></a>Azure Portal를 통해 암호 다시 설정

1. Azure Portal에서 만든 Azure Stack Edge 리소스로 이동 하 여 장치를 관리 합니다. **Edge 컴퓨팅 > 시작**으로 이동합니다.

2. 오른쪽 창의 명령 모음에서 **EDGE ARM 암호 다시 설정**을 선택 합니다. 

    ![EdgeARM 사용자 암호 다시 설정 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. **EdgeArm 사용자 암호 다시 설정** 블레이드에서 Azure Resource Manager를 통해 장치 로컬 api에 연결 하기 위한 암호를 제공 합니다. 암호를 확인 하 고 **재설정**을 선택 합니다.

    ![EdgeARM 사용자 암호 2 다시 설정](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>PowerShell을 통해 암호 다시 설정

1. Azure Portal에서 만든 Azure Stack Edge 리소스로 이동 하 여 장치를 관리 합니다. **개요** 페이지에서 다음 매개 변수를 적어 둡니다.

    - Azure Stack Edge 리소스 이름
    - 구독 ID

2. **설정 > 속성**으로 이동 합니다. **속성** 페이지에서 다음 매개 변수를 적어 둡니다.

    - 리소스 그룹
    - CIK 암호화 키: 보기를 선택한 후 **암호화 키**를 복사 합니다.

    ![CIK 암호화 키 가져오기](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Azure Resource Manager에 연결 하는 데 사용할 암호를 식별 합니다.

4. Cloud shell을 시작 합니다. 오른쪽 위 모서리의 아이콘을 선택 합니다.

    ![Cloud shell 시작](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Cloud shell이 시작 되 면 PowerShell로 전환 해야 할 수 있습니다.

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. 컨텍스트를 설정 합니다. 유형:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    샘플 출력은 다음과 같습니다.

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  이전 PS 모듈이 있는 경우 해당 모듈을 설치 해야 합니다.

    `Remove-Module  Az.DataBoxEdge -force`

    샘플 출력은 다음과 같습니다. 이 예제에서는 설치할 이전 모듈이 없습니다.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. 다음 명령 집합은 PowerShell 모듈을 설치 하는 스크립트를 다운로드 하 여 실행 합니다.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. 다음 명령 집합에서 리소스 이름, 리소스 그룹 이름, 암호화 키 및 이전 단계에서 확인 한 암호를 제공 해야 합니다.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    암호 및 암호화 키 매개 변수는 보안 문자열로 전달 해야 합니다. 다음 cmdlet을 사용 하 여 암호 및 암호화 키를 보안 문자열로 변환 합니다.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    위의 생성 된 보안 문자열을 AzDataBoxEdgeUser cmdlet의 매개 변수로 사용 하 여 암호를 다시 설정 합니다. Azure Stack Edge/Data Box Gateway 리소스를 만들 때 사용한 것과 동일한 리소스 그룹을 사용 합니다.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    샘플 출력은 다음과 같습니다.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
새 암호를 사용 하 여 Azure Resource Manager에 연결 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager에 연결](azure-stack-edge-j-series-connect-resource-manager.md)
