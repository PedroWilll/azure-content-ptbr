<properties
	pageTitle="Backup Automatizado para Máquinas Virtuais do SQL Server (Clássico) | Microsoft Azure"
	description="Explica o recurso de Backup Automatizado para o SQL Server com Máquinas Virtuais do Azure usando o Resource Manager. "
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/14/2016"
	ms.author="jroth" />

# Backup automatizado para SQL Server em Máquinas Virtuais do Azure (Clássico)

> [AZURE.SELECTOR]
- [Gerenciador de Recursos](virtual-machines-windows-sql-automated-backup.md)
- [Clássico](virtual-machines-windows-classic-sql-automated-backup.md)

O backup automatizado configura automaticamente o [Backup Gerenciado do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os bancos de dados novos e existentes em uma VM do Azure executando o SQL Server 2014 Standard ou Enterprise. Isso permite que você configure backups regulares do banco de dados que utilizam o durável armazenamento de Blobs do Azure. O Backup Automatizado depende da [Extensão do agente IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
Para exibir a versão do Resource Manager deste artigo, consulte [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure do Resource Manager](virtual-machines-windows-sql-automated-backup.md).

## Pré-requisitos

Para usar o Backup Automatizado, considere os seguintes pré-requisitos:

**Sistema operacional**:

- Windows Server 2012
- Windows Server 2012 R2

**Versão/edição do SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

**Configuração do banco de dados**:

- Os bancos de dados de destino devem usar o modelo de recuperação completa

**Azure PowerShell**:

- [Instalar os comandos mais recentes do Azure PowerShell](../powershell-install-configure.md) se você planeja configurar o Backup Automatizado com o PowerShell.

>[AZURE.NOTE] O Backup Automatizado conta com a Extensão do agente IaaS do SQL Server. As imagens atuais da galeria da máquina virtual do SQL adicionam essa extensão por padrão. Para obter mais informações, consulte [Extensão do agente IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## Configurações

A tabela a seguir descreve as opções que podem ser configuradas para Backup Automatizado. As etapas de configuração reais variam dependendo de se você usar os comandos do Portal do Azure ou do Azure Windows PowerShell.

|Configuração|Intervalo (Padrão)|Descrição|
|---|---|---|
|**Backup Automatizado**|Habilitar/desabilitar (Desabilitado)|Habilita ou desabilita o Backup Automatizado de uma VM do Azure que executa o SQL Server 2014 Standard ou Enterprise.|
|**Período de retenção**|Um a 30 dias (30 dias)|O número de dias para manter um backup.|
|**Conta de armazenamento**|Conta de armazenamento do Azure (a conta de armazenamento criada para a VM especificada)|Uma conta de armazenamento do Azure a ser usada para armazenar arquivos de Backup Automatizado no armazenamento de blobs. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A convenção de nomenclatura do arquivo de backup inclui a data, hora e nome do computador.|
|**Criptografia**|Habilitar/desabilitar (Desabilitado)|Habilita ou desabilita a criptografia. Quando a criptografia está habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificado no mesmo contêiner automaticbackup usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado será gerado com essa senha, mas o certificado antigo permanece para restaurar backups anteriores.|
|**Senha**|Texto da senha (nenhum)|Uma senha para as chaves de criptografia. Isso só é necessário se a criptografia estiver habilitada. Para restaurar um backup criptografado, você deverá ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito.|

## Configuração no Portal
Você pode usar o Portal do Azure para configurar o Backup Automatizado durante o provisionamento ou para VMs existentes.

### Novas VMs
Use o portal do Azure para configurar o Backup Automatizado quando criar uma nova Máquina Virtual do SQL Server 2014 no modelo de implantação clássico.

A captura de tela do portal do Azure a seguir mostra essas opções em **CONFIGURAÇÃO OPCIONAL** | **BACKUP AUTOMATIZADO DO SQL**.

![Configuração de Backup Automático do SQL no portal do Azure](./media/virtual-machines-windows-classic-sql-automated-backup/IC778483.jpg)

### VMs existentes
Para as máquinas virtuais existentes do SQL Server 2014, selecione as configurações de **Backup automático** na seção **Configuração** das propriedades da máquina virtual. Na janela **Backup automatizado**, você pode habilitar o recurso, definir o período de retenção, escolher a conta de armazenamento e definir a criptografia. Isso é mostrado na captura de tela a seguir.

![Configuração de Backup Automatizado no portal do Azure](./media/virtual-machines-windows-classic-sql-automated-backup/IC792133.jpg)

>[AZURE.NOTE] Quando você habilita o Backup Automatizado pela primeira vez, o Azure configura o Agente IaaS do SQL Server em segundo plano. Durante esse tempo, o portal do Azure pode não mostrar que o Backup Automatizado está configurado. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal do Azure refletirá as novas configurações.

## Configuração com o PowerShell

No exemplo do PowerShell a seguir, o Backup Automatizado é configurado para uma VM existente do SQL Server 2014. O comando **New-AzureVMSqlServerAutoBackupConfig** define as configurações de Backup Automatizado para armazenar backups na conta de armazenamento do Azure especificada pela variável $storageaccount. Esses backups serão mantidos por 10 dias. O comando **Set-AzureVMSqlServerExtension** atualiza a VM do Azure especificada com essas configurações.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server.

Para habilitar a criptografia, modifique o script anterior para passar o parâmetro EnableEncryption e uma senha (cadeia de caracteres segura) para o parâmetro CertificatePassword. O script a seguir habilita as configurações de Backup Automatizado no exemplo anterior e adiciona a criptografia.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para desabilitar o backup automático, execute o mesmo script sem o parâmetro **-Enable** para **New-AzureVMSqlServerAutoBackupConfig**. Assim como acontece com a instalação, pode demorar vários minutos para desabilitar o Backup Automatizado.

>[AZURE.NOTE] Desabilitar e desinstalar o Agente IaaS do SQL Server não remove as configurações de Backup Gerenciado previamente configuradas. Você deve desabilitar o Backup Automatizado antes de desabilitar ou desinstalar o Agente IaaS do SQL Server.

## Próximas etapas

O Backup Automatizado configura o Backup Gerenciado em VMs do Azure. Portanto, é importante [ler a documentação do Backup Gerenciado](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e suas implicações.

Você pode encontrar outras orientações de backup e de restauração para o SQL Server em VMs do Azure no seguinte tópico: [Backup e restauração do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0720_2016-->