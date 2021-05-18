---
title: 가상 머신 및 가상 머신 확장 집합에 대한 Azure Disk Encryption
description: 가상 머신(VMs)과 VM 확장 집합에 대한 Azure Disk encryption에 대해 알아봅니다. Azure Disk encryption는 Linux 및 Windows VM 모두에서 작동합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 21194bf2fe76a7eb0ee034d4a502c20ee3032dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543676"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>가상 머신 및 가상 머신 확장 집합에 대한 Azure Disk Encryption

Azure Disk encryption는 Linux 및 Windows 가상 머신과 가상 머신 확장 집합에 모두 적용할 수 있습니다. 

## <a name="linux-virtual-machines"></a>Linux 가상 머신

다음 문서에서는 Linux 가상 머신을 암호화하기 위한 지침을 제공합니다.

### <a name="current-version-of-azure-disk-encryption"></a>Azure Disk Encryption의 현재 버전

- [Linux 가상 머신용 Azure Disk Encryption 개요](../../virtual-machines/linux/disk-encryption-overview.md)
- [Linux VM에 대한 Azure Disk Encryption 시나리오](../../virtual-machines/linux/disk-encryption-linux.md)
- [Azure CLI를 사용하여 Linux VM 만들기 및 암호화](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Azure Powershell을 사용하여 Linux VM 만들기 및 암호화](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Azure Portal을 사용하여 Linux VM 만들기 및 암호화](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Linux용 Azure Disk Encryption 확장 스키마](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption 샘플 스크립트](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption 문제 해결](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption 질문과 대답](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption with Azure AD (이전 버전)

- [Linux 가상 머신용 Azure Disk Encryption 개요](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Linux VM의 Azure AD 시나리오에 대한 Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-linux.md)
- [Azure AD(이전 릴리스)를 사용하여 Azure Disk Encryption 키 자격 증명 모음 생성 및 구성](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows 가상 머신

다음 문서에서는 Windows 가상 머신을 암호화하기 위한 참고 자료를 제공합니다.

### <a name="current-version-of-azure-disk-encryption"></a>Azure Disk Encryption의 현재 버전

- [Windows 가상 머신용 Azure Disk Encryption 개요](../../virtual-machines/windows/disk-encryption-overview.md)
- [Windows VM에 대한 Azure Disk Encryption 시나리오](../../virtual-machines/windows/disk-encryption-windows.md)
- [Azure CLI를 사용하여 Windows VM 만들기 및 암호화](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Azure PowerShell을 사용하여 Windows VM 만들기 및 암호화](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Azure Portal을 사용하여 Windows VM 만들기 및 암호화](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Windows용 Azure Disk Encryption 확장 스키마](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption 샘플 스크립트](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption 문제 해결](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption 질문과 대답](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption with Azure AD (이전 버전)

- [Windows 가상 머신용 Azure AD를 사용하는 Azure Disk Encryption 개요](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Windows VM의 Azure AD 시나리오에 대한 Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-windows.md)
- [Azure AD(이전 릴리스)를 사용하여 Azure Disk Encryption 키 자격 증명 모음 생성 및 구성](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합

다음 문서에서는 가상 머신 확장 집합을 암호화하기 위한 참고 자료를 제공합니다.

- [가상 머신 확장 집합을 위한 Azure Disk Encryption의 개요](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Azure CLI를 사용하는 가상 머신 확장 집합 암호화](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Azure PowerShell을 사용하는 가상 머신 확장 집합 암호화](../../virtual-machine-scale-sets/disk-encryption-powershell.md)
- [Azure Resource Manager를 사용하는 가상 머신 확장 집합 암호화](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [가상 머신 확장 집합 확장 시퀀싱을 통한 Azure Disk Encryption 사용](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>다음 단계

- [Azure 암호화 개요](encryption-overview.md)
- [저장 데이터 암호화](encryption-atrest.md)
- [데이터 보안 및 암호화 모범 사례](data-encryption-best-practices.md)
