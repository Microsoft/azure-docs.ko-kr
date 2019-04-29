---
ms.openlocfilehash: aec6282daadc61b4e1bcf6bbaf1266d9bc98cdac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232905"
---
<!--save a copy of this file to your local repo. It's important that you follow the naming conventions by starting with the service name, and use lowercase only for the file name. See "file-names-and-locations.md" under the "contributor-guide" folder in your repo.

Info to help you use the template are enclosed in the Markdown comments using the caret, hyphen, dash syntax. Delete these from your file.

Text not wrapped in comment syntax is intended to be used as is, or with updates enclosed in [  ]. Add the info and delete the bracket. 

Pay attention to spacing and indents. They affect formatting. 

--> 

<!--replace this with Properties and Tags sections. These are required sections. See "article-metadata.md" in under the "contributor-guide" folder in your repo. Attributes in each section can be placed on separate lines to make them easier to read and check-->

# <a name="use-azure-powershell-to-task"></a>Azure PowerShell을 사용 하 여 [작업]
이 문서에서는 Azure 모듈 및 Azure Resource Manager 모듈에서 명령을 사용 하 여 [작업]을 하는 방법을 보여 줍니다. 이 뿐만 아니라 새 명령에 기존 스크립트를 마이그레이션하고 새 명령을 배우는 데 사용 됩니다.

## <a name="prerequisite-install-a-recent-version-of-azure-powershell"></a>필수 조건: 최신 버전의 Azure PowerShell 설치
아직 수행 하지 않았다면, 이상을 설치 [버전 번호] 버전의 Azure PowerShell 로컬 컴퓨터에 있습니다. 이전 버전을 사용 하는 경우이 문서에 설명 된 Azure Resource Manager cmdlet은 없습니다. 자세한 내용은 다음을 참조하세요.

* [Azure PowerShell 설치 및 구성 하는 방법을](install-configure-powershell.md) Azure PowerShell 설정에 대 한 지침에 대 한 합니다.
* [Resource Manager를 사용 하 여 Windows PowerShell을 사용 하 여](powershell-azure-resource-manager.md) Resource Manager를 사용 하 여 기본 사항에 대 한 합니다.

> [!NOTE]
> 대부분의 작업을 사용 하면 관리자 수준의 Azure PowerShell 명령 프롬프트를 사용 하도록 해야 합니다.
> 
> 

## <a name="command-comparison"></a>명령 비교
이 [테이블 | 섹션] 명령 구문을 보여 줍니다.

<!--[optional image - to use an image in this article, add a folder with the same name as the article file name without extension, inside the Media folder of the repo. Use only this folder to store the images. Don't attempt to use a common folder to share images you want to use in more than 1 file.]
Then, use the following syntax to add a reference to the image in your article:
![](./media/name-of-file-without-extension/image-name-no-spaces.png)
-->

<!--if a command string uses variables, define the variables first, using the  following construction. In some cases the variable is straightforward and doesn't need much explanation. But parameters such as location and size can benefit from brief explanation or listing all accepted values:--> 

다음 명령 예제는 다음 변수를 사용 합니다.

$FriendlyName"<Describe value>"

<!-- if it makes more sense to present this in a table, use this. Otherwise, delete. The table won't render until it's in GitHub or published to Sandbox.-->

| 서비스 관리 | Resource Manager |
| --- | --- |
| `syntax` |`syntax` |

<!--if it makes more sense to present this one command block after the other instead of a table, use this. Otherwise, delete-->

[명령에 대 한 간단한 소개 문장입니다. 이를 전혀 없습니다를 생략 합니다. 그러나 이러한 방법을 사용 하는 경우는 파이프라인을 설명 하는]:

    [command string]

## <a name="script-examples"></a>스크립트 예제
[작업] 예 [cmdlet 이름])를 사용 하는 다음과 같습니다. 여기에 명령입니다.

* [짧은 동사를 사용에는, 등]
* [다음 짧은 동사] 

<!--include this statement if it uses variables that weren't introduced earlier--> 다음 변수를 포함합니다.

* [변수 1]
* [variable 2]

<!--This shows you how a recent example was presented as well as how it was formatted. Preceding each line with one tab or four spaces to format in a code block-->

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="additional-resources"></a>추가 리소스
<!--At a minimum, include a link back to the migration task list article. Use the formats shown below. See create-links-markdown.md for more info -->
<!--use this format for links to other articles, such as the migration task list. -->
[가용성 관리](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

<!--To link to an ACOM page outside the /documentation/ subdomain (such as a pricing page, SLA page or anything else that is not a documentation article), use an absolute URL, but omit the locale:

    [link text](https://azure.microsoft.com/pricing/details/virtual-machines/)-->

<!--use this for URLs outside of ACOM. Be sure to locale, and if you're linking to the Azure library on MSDN, include the '/azure/' part of the URL-->
[가상 머신 설명서](/previous-versions/azure/jj156003(v=azure.100))

