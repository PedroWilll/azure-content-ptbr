<properties
	pageTitle="Criar uma VM executando o Windows no portal clássico | Microsoft Azure"
	description="Crie uma máquina virtual do Windows no portal clássico do Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="cynthn"/>

# Criar uma máquina virtual executando o Windows no portal clássico do Azure

> [AZURE.SELECTOR]
- [Portal clássico do Azure](virtual-machines-windows-classic-tutorial.md)
- [PowerShell: Implantação clássica](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Saiba como [executar estas etapas usando o modelo de implantação do Gerenciador de Recursos](virtual-machines-windows-hero-tutorial.md).

Este tutorial mostra como é fácil criar uma VM (máquina virtual) do Azure executando Windows no portal clássico do Azure. Vamos usar uma imagem do Windows Server como exemplo, mas ela é apenas uma das muitas imagens que o Azure oferece. Observe que as opções de imagem dependem de sua assinatura. Por exemplo, imagens da área de trabalho do Windows podem estar disponíveis para assinantes do MSDN.

Esta seção mostra como usar a opção **Da Galeria** no portal clássico do Azure para criar a máquina virtual. Esta opção fornece mais escolhas de configuração que a opção **Criação rápida**. Por exemplo, se desejar entrar em uma máquina virtual em uma rede virtual, será necessário usar a opção **Da galeria**.

Também é possível criar VMs usando [suas próprias imagens](virtual-machines-windows-classic-createupload-vhd.md). Para saber mais sobre esse e outros métodos, consulte [Diferentes maneiras de criar uma máquina virtual do Windows](virtual-machines-windows-creation-choices.md).



## Passo a passo em vídeo

Aqui está um passo a passo deste tutorial.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Criar a máquina virtual

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Próximas etapas

- Faça logon na máquina virtual. Para obter instruções, consulte [Fazer logon em uma máquina virtual que executa o Windows Server](virtual-machines-windows-classic-connect-logon.md).

- Anexe um disco para armazenar dados. Você pode anexar tanto discos vazios como discos que contenham dados. Para obter instruções, consulte [Anexar um disco de dados a uma máquina virtual do Windows criada com o modelo de implantação clássico](virtual-machines-windows-classic-attach-disk.md).

<!---HONumber=AcomDC_0713_2016-->