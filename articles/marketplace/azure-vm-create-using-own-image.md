---
title: 사용자 고유의 이미지를 사용하여 Azure Marketplace에서 Azure 가상 머신 제품 만들기
description: 사용자 고유의 이미지를 사용하여 Azure Marketplace에 가상 머신 제품을 게시하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200454"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>사용자 고유의 이미지를 사용하여 가상 머신을 만드는 방법

이 문서에서는 사용자 제공 VM(가상 머신) 이미지를 만들고 배포하는 방법을 설명합니다.

> [!NOTE]
> 이 절차를 시작하기 전에 VHD(가상 하드 디스크) 요구 사항을 포함하여 Azure VM 제품에 대한 [기술 요구 사항](marketplace-virtual-machines.md#technical-requirements)을 검토하세요.

그 대신 승인된 기본 이미지를 사용하려면 [승인된 기본 이미지를 사용하여 VM 이미지 만들기](azure-vm-create-using-approved-base.md) 지침을 수행합니다.

## <a name="configure-the-vm"></a>VM 구성

이 섹션에서는 Azure VM의 크기를 조정하고, 업데이트하고, 일반화하는 방법을 설명합니다. 이러한 단계는 Azure Marketplace에서 배포할 VM을 준비하는 데 필요합니다.

### <a name="size-the-vhds"></a>VHD 크기 조정

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>최신 업데이트 설치

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>추가 보안 검사 수행

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>사용자 지정 구성 및 예약된 작업 수행

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>이미지 일반화

Azure Marketplace의 모든 이미지는 일반적으로 다시 사용할 수 있어야 합니다. 이를 달성하려면 운영 체제 VHD가 일반화되어야 합니다. 이 작업은 VM에서 인스턴스 관련 식별자와 소프트웨어 드라이버를 모두 제거합니다.

## <a name="bring-your-image-into-azure"></a>이미지를 Azure로 가져오기

Azure에 이미지를 가져오는 방법에는 세 가지가 있습니다.

1. VHD를 SIG(Shared Image Gallery)에 업로드합니다.
1. Azure Storage 계정에 VHD를 업로드합니다.
1. 관리되는 이미지(이미지 작성 서비스를 사용하는 경우)에서 VHD를 추출합니다.

이어지는 세 개 섹션에서는 이러한 옵션에 대해 설명합니다.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>옵션 1: Shared Image Gallery로 VHD 업로드

1. 스토리지 계정에 VHD를 업로드합니다.
2. Azure Portal에서 **사용자 지정 템플릿 배포** 를 검색합니다.
3. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.
4. 다음 ARM(Azure Resource Manager) 템플릿을 복사합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. 템플릿을 편집기에 붙여 넣습니다.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="VM에 대한 샘플 코드 화면.":::

1. **저장** 을 선택합니다.
1. 이 표의 매개 변수를 사용하여 다음 화면에서 필드를 완료합니다.

| 매개 변수 | Description |
| --- | --- |
| sourceStorageAccountResourceId | BLOB VHD가 상주하는 원본 스토리지 계정의 리소스 ID입니다.<br><br>리소스 ID를 얻으려면 **Azure Portal** 의 **스토리지 계정** 으로 이동하여 **속성** 으로 이동하고 **ResourceID** 값을 복사합니다. |
| sourceBlobUri | OS 디스크 VHD BLOB의 BLOB URI입니다(제공된 스토리지 계정에 있어야 함).<br><br>BLOB URL을 가져오려면 **Azure Portal** 의 **스토리지 계정** 으로 이동하고 **BLOB** 으로 이동한 다음 **URL** 값을 복사합니다. |
| sourceBlobDataDisk0Uri | 데이터 디스크 VHD BLOB의 BLOB URI입니다(제공된 스토리지 계정에 있어야 함). 데이터 디스크가 없으면 템플릿에서 이 매개 변수를 제거합니다.<br><br>BLOB URL을 가져오려면 **Azure Portal** 의 **스토리지 계정** 으로 이동하고 **BLOB** 으로 이동한 다음 **URL** 값을 복사합니다. |
| sourceBlobDataDisk1Uri | 추가 데이터 디스크 VHD BLOB의 BLOB URI입니다(제공된 스토리지 계정에 있어야 함). 추가 데이터 디스크가 없으면 템플릿에서 이 매개 변수를 제거합니다.<br><br>BLOB URL을 가져오려면 **Azure Portal** 의 **스토리지 계정** 으로 이동하고 **BLOB** 으로 이동한 다음 **URL** 값을 복사합니다. |
| galleryName | Shared Image Gallery 이름 |
| galleryImageDefinitionName | 이미지 정의의 이름 |
| galleryImageVersionName | 만들 이미지 버전의 이름. 형식: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="사용자 지정 배포 창을 보여줍니다.":::

8. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

> [!TIP]
> SIG 이미지를 게시하려면 게시자 계정에 "소유자" 액세스 권한이 있어야 합니다. 필요한 경우 아래 단계에 따라 액세스 권한을 부여합니다.
>
> 1. SIG(Shared Image Gallery)로 이동합니다.
> 2. 왼쪽 패널에서 **액세스 제어**(IAM)를 선택합니다.
> 3. **추가** 를 선택한 다음 **역할 할당 추가** 를 선택합니다.
> 4. **역할** 에 대해 **소유자** 를 선택합니다.
> 5. **다음에 대한 액세스 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.
> 6. 이미지를 게시할 사용자의 Azure 이메일을 입력합니다.
> 7. **저장** 을 선택합니다.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="역할 할당 추가 창이 표시됩니다.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>옵션 2: 스토리지 계정에 VHD 업로드

[Azure에 업로드할 WINDOWS VHD 또는 VHDX 준비](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 또는 [Linux VHD 만들기 및 업로드](../virtual-machines/linux/create-upload-generic.md)에 설명된 대로 업로드할 VM을 구성하고 준비합니다.

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>옵션 3: 관리되는 이미지에서 VHD 추출(이미지 작성 서비스를 사용하는 경우)

[Packer](https://www.packer.io/)와 같은 이미지 작성 서비스를 사용하는 경우 이미지에서 VHD를 추출해야 할 수 있습니다. 이 작업을 수행하는 직접적인 방법은 없습니다. VM을 만들고 VM 디스크에서 VHD를 추출해야 합니다.

## <a name="create-the-vm-on-the-azure-portal"></a>Azure Portal에서 VM 만들기

[Azure Portal](https://ms.portal.azure.com/)에서 기본 VM 이미지를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
2. **가상 머신** 을 선택합니다.
3. **+ 추가** 를 선택하여 **가상 머신 만들기** 화면을 엽니다.
4. 드롭다운 목록에서 이미지를 선택하거나 **모든 퍼블릭 및 프라이빗 이미지 찾아보기** 를 선택하여 사용 가능한 모든 가상 머신 이미지를 검색하거나 찾아봅니다.
5. **Gen 2** VM을 만들려면 **고급** 탭으로 이동하여 **Gen 2** 옵션을 선택합니다.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="1세대 또는 2세대를 선택":::합니다.

6. 배포할 VM의 크기를 선택합니다.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="선택한 이미지에 권장되는 VM 크기를 선택":::합니다.

7. VM을 만드는 데 필요한 다른 세부 정보를 입력합니다.
8. **검토 + 만들기** 를 선택하여 선택 사항을 검토합니다. **유효성 검사 통과** 메시지가 나타나면 **만들기** 를 선택합니다.

Azure에서 지정한 가상 머신의 프로비저닝을 시작합니다. 왼쪽 메뉴의 **가상 머신** 탭을 선택하여 진행 상황을 추적합니다. 생성이 완료되면 가상 머신의 상태가 **실행 중** 으로 변경됩니다.

## <a name="connect-to-your-vm"></a>VM에 연결

[Windows](../virtual-machines/windows/connect-logon.md) 또는 [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM에 연결하려면 다음 문서를 참조하세요.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>다음 단계

- [VM 이미지를 테스트](azure-vm-image-test.md)하여 Azure Marketplace 게시 요구 사항을 충족하는지 확인합니다. 선택 사항입니다.
- VM 이미지를 테스트하지 않으려면 [파트너 센터](https://partner.microsoft.com/)에 로그인하여 SIG 이미지(옵션 #1)를 게시합니다.
- #2 또는 #3 옵션을 수행한 경우 [SAS URI를 생성](azure-vm-get-sas-uri.md)합니다.
- 새 Azure 기반 VHD를 만드는 데 어려움이 있는 경우에는 [Azure Marketplace용 VM FAQ](azure-vm-create-faq.md)를 참조하세요.
