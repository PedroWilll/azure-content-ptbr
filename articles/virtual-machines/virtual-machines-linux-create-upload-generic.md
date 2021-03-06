<properties
	pageTitle="Criar e carregar um VHD Linux no Azure"
	description="Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="szark"/>

# Informações para as distribuições não endossadas #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Importante**: o SLA (contrato de nível de serviço) da plataforma do Azure aplica-se a máquinas virtuais com o sistema operacional Linux somente quando uma das [distribuições endossadas é usada](virtual-machines-linux-endorsed-distros.md). Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.

- [Linux no Azure - Distribuições endossadas](virtual-machines-linux-endorsed-distros.md)
- [Suporte para imagens Linux no Microsoft Azure](http://support2.microsoft.com/kb/2941892)

Todas as distribuições em execução no Azure precisam atender a diversos pré-requisitos para ter a chance de serem executadas corretamente na plataforma. Este artigo não é conclusivo, já que cada distribuição é diferente. Dessa forma, mesmo que você atenda a todos os critérios abaixo, talvez seja necessário ajustar significativamente o seu sistema Linux para garantir que ele seja executado corretamente na plataforma.

Por isso, recomendamos que você inicie com uma das nossas [distribuições endossadas do Linux no Azure](virtual-machines-linux-endorsed-distros.md) sempre que possível. Os artigos a seguir guiam você pela preparação das diversas distribuições endossadas do Linux que têm suporte no Azure:

- **[Distribuições com base em CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES e openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

O restante deste artigo traz orientações gerais para execução da sua distribuição do Linux no Azure.


## Notas de instalação gerais do Linux ##

- O formato VHDX não tem suporte no Azure, somente o **VHD fixo**. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd. Se você estiver usando o VirtualBox, isso significará selecionar **Tamanho fixo** em vez do padrão alocado dinamicamente durante a criação do disco.

- Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, poderá usar [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) em discos de dados.

- Não há suporte para NUMA para tamanhos de máquinas virtuais maiores devido a um bug nas versões do kernel Linux abaixo de 2.6.37. Esse problema afeta principalmente distribuições que usam o kernel upstream do Red Hat 2.6.32. A instalação manual do agente Linux do Azure (waagent) desabilita a NUMA automaticamente na configuração do GRUB para o kernel do Linux.

- Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações a esse respeito.

- Todos os VHDs devem ter tamanhos que sejam múltiplos de 1 MB.


### Instalação do Linux sem Hyper-V ###

Em alguns casos, os instaladores do Linux não incluem os drivers Hyper-v no ramdisk inicial (initrd ou initramfs), a menos que ele detecte que está executando um ambiente Hyper-V. Ao usar um sistema de virtualização diferente (ou seja, Virtualbox, KVM, etc.) para preparar a imagem do Linux, talvez seja necessário recompilar o initrd para garantir que pelo menos os módulos kernel `hv_vmbus` e `hv_storvsc` estejam disponíveis no ramdisk inicial. Isso é um problema conhecido pelo menos em sistemas com base na distribuição Red Hat upstream.

O mecanismo para recriar a imagem initrd ou initramfs pode variar dependendo da distribuição. Consulte a documentação da distribuição ou suporte para o procedimento adequado. Aqui está um exemplo de como recompilar o initrd usando o utilitário `mkinitrd`:

Primeiro, faça backup da imagem initrd existente:

	# cd /boot
	# sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Em seguida, recompile o initrd com os módulos kernel `hv_vmbus` e `hv_storvsc`:

	# sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### Redimensionando VHDs ###

As imagens de VHD no Azure devem ter um tamanho virtual alinhado para 1MB. Normalmente, os VHDs criados usando o Hyper-V já foram alinhados corretamente. Se o VHD não estiver alinhado corretamente, você receberá uma mensagem de erro semelhante à seguinte quando tentar criar uma *imagem* de seu VHD:

	"The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Para corrigir isso, você pode redimensionar a VM usando o console do Gerenciador do Hyper-V ou o cmdlet do Powershell [Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx). Se você não estiver executando em um ambiente Windows, então é recomendável usar qemu-img para converter (se necessário) e redimensionar o VHD.

> [AZURE.NOTE] Há um bug conhecido nas versões qemu-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema será corrigido em uma versão futura do qemu- img. Por ora é recomendado usar a versão 2.2.0 ou inferior do qemu-img. Referência: https://bugs.launchpad.net/qemu/+bug/1490611


 1. Redimensionar o VHD diretamente, usando ferramentas como `qemu-img` ou `vbox-manage` pode resultar em um VHD incapaz de ser inicializado. Então convém primeiro converter o VHD para uma imagem de disco bruta. Se a imagem VM já foi criada como imagem de disco bruta (o padrão para alguns Hypervisors como KVM), você pode ignorar esta etapa:

		# qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Calcule o tamanho necessário da imagem do disco para garantir que o tamanho virtual esteja alinhado para 1MB. O seguinte script de bash shell pode ajudar com isso. O script usa "`qemu-img info`" para determinar o tamanho virtual da imagem do disco e, em seguida, calcula o tamanho para o próximo 1 MB:

		rawdisk="MyLinuxVM.raw"
		vhddisk="MyLinuxVM.vhd"

		MB=$((1024*1024))
		size=$(qemu-img info -f raw --output json "$rawdisk" | \
		       gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

		rounded_size=$((($size/$MB + 1)*$MB))
		echo "Rounded Size = $rounded_size"

 3. Redimensione o disco bruto usando $rounded\_size conforme definido no script acima:

		# qemu-img resize MyLinuxVM.raw $rounded_size

 4. Agora, converta o disco bruto em um VHD de tamanho fixo:

		# qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## Requisitos do kernel do Linux ##

Os drivers LIS (Serviços de Integração do Linux) para Hyper-V e Azure são obtidos diretamente no kernel upstream do Linux. Muitas distribuições que contam com uma versão recente do kernel do Linux (por exemplo, versões 3.x) já terão esses drivers ou fornecerão versões revertidas desses drivers com seus kernels. Os drivers são atualizados constantemente no kernel upstream com reparos e recursos. Por isso, recomendamos que você execute, sempre que possível, uma [distribuição endossada](virtual-machines-linux-endorsed-distros.md) que inclua esses reparos e essas atualizações.

Se você estiver executando uma variante das versões de **6.0 a 6.3** do Red Hat Enterprise Linux, será necessário instalar os drivers LIS mais recentes para o Hyper-V Os drivers podem ser encontrados [neste local](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Os drivers LIS com o kernel já estão incluídos nas versões **6.4 e posteriores** do RHEL (e derivados). Por isso, não é necessário ter outros pacotes de instalação para executar esses sistemas no Azure.

Se for necessário usar um kernel personalizado, recomendamos que você use uma versão mais recente (**3.8 ou posterior**). No caso das distribuições ou dos fornecedores que mantêm seus próprios kernels, será necessário um pouco de esforço para a reversão dos drivers LIS do kernel upstream para seu kernel personalizado. Recomendamos que você acompanhe os reparos upstream nos drivers LIS e faça as reversões necessárias, mesmo se estiver executando uma versão relativamente recente do kernel. A localização dos arquivos de origem do driver do LIS consta no arquivo [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS), na árvore do kernel do Linux:

	F:	arch/x86/include/asm/mshyperv.h
	F:	arch/x86/include/uapi/asm/hyperv.h
	F:	arch/x86/kernel/cpu/mshyperv.c
	F:	drivers/hid/hid-hyperv.c
	F:	drivers/hv/
	F:	drivers/input/serio/hyperv-keyboard.c
	F:	drivers/net/hyperv/
	F:	drivers/scsi/storvsc_drv.c
	F:	drivers/video/fbdev/hyperv_fb.c
	F:	include/linux/hyperv.h
	F:	tools/hv/

Sabemos que a ausência dos patches a seguir resulta em problemas no Azure. Por isso, eles devem constar no kernel. De forma alguma essa lista pode ser considerada completa para todas as distribuições:

- [ata\_piix: adie os discos para os drivers Hyper-V por padrão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: conta para pacotes em trânsito no caminho RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc: evite o uso de WRITE\_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc: desabilite WRITE SAME para RAID e drivers do adaptador de host virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc: correção de desreferência de ponteiro NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc: as falhas do buffer de anéis podem resultar em congelamento de E/S](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)


## O agente Linux do Azure ##

O [agente Linux do Azure](virtual-machines-linux-agent-user-guide.md) (waagent) é necessário para garantir o provisionamento correto da máquina virtual Linux no Azure. Você pode obter a versão mais recente, verificar os problemas com arquivos ou enviar solicitações pull no [repositório GitHub do agente Linux](https://github.com/Azure/WALinuxAgent).

- O agente Linux consta na licença do Apache 2.0. Diversas distribuições já fornecem pacotes RPM ou DEB para o agente. Assim, em alguns casos, é possível instalá-los e atualizá-los sem dificuldades.

- O agente Linux do Azure requer Python v2.6+.

- Além disso, o agente requer o módulo python-pyasn1. A maioria das distribuições fornece esse módulo como uma pacote autônomo instalável.

- Em alguns casos, é possível que o agente Linux do Azure não seja compatível com o NetworkManager. Muitos pacotes RPM/DEB fornecidos pelas distribuições configuram o NetworkManager como um conflito para o pacote do waagent e, portanto, desinstalam o NetworkManager quando você instala o pacote do agente Linux.


## Requisitos gerais do sistema Linux ##

- Modifique a linha de inicialização do kernel no GRUB ou GRUB2 para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.

	Além disso, recomendamos que você *remova* os seguintes parâmetros, se eles existirem:

		rhgb quiet crashkernel=auto

	As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.

	Você pode deixar configurada a opção `crashkernel`, mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

- Instalando o agente Linux do Azure

	O agente Linux do Azure é necessário para garantir o provisionamento de uma imagem Linux no Azure. Muitas distribuições fornecem o agente como um pacote RPM ou DEB (o pacote geralmente recebe o nome de "WALinuxAgent" ou "walinuxagent"). Você também pode seguir as etapas descritas no [Guia do agente Linux](virtual-machines-linux-agent-user-guide.md) para instalar o agente manualmente.

- Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão.

- Não crie espaço swap no disco do sistema operacional

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Em "/etc/sudoers", exclua o comentário ou remova as seguintes linhas, se elas estiverem presentes:

		Defaults targetpw
		ALL    ALL=(ALL) ALL

- Por fim, execute os comandos a seguir para desprovisionar a máquina virtual:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

	>[AZURE.NOTE] No Virtualbox, você pode ver o seguinte erro após executar 'waagent -force -deprovision': `[Errno 5] Input/output error`. Essa mensagem de erro não é crítica e pode ser ignorada.

- Em seguida, você deverá desligar a máquina virtual e carregar o VHD no Azure.

<!---HONumber=AcomDC_0518_2016-->