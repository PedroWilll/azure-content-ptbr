<!--author=alkohli last changed: 03/17/16-->

#### Para baixar os hotfixes

Execute as etapas a seguir para baixar a atualização do software do Catálogo do Microsoft Update.

1. Inicie o Internet Explorer e acesse [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Caso esta seja a primeira vez que estiver usando o Catálogo do Microsoft Update neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.![Instalar o catálogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Na caixa de pesquisa do Catálogo do Microsoft Update, insira o número da KB (Base de Dados de Conhecimento) do hotfix que deseja baixar, por exemplo **3121901**, e clique em **Pesquisar**.

    A listagem de hotfixes aparece, por exemplo, **Atualização de Pacote Cumulativo de Software 2.0 para a série 8000 do StorSimple **.

    ![Pesquisar o catálogo](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Clique em **Adicionar**. A atualização é adicionada ao carrinho de compras.

5. Pesquise quaisquer hotfixes adicionais listados na tabela acima (**3121900**, **3080728**, **3090322** e **3121899**) e adicione cada um deles ao carrinho de compras.

5. Clique em **Exibir carrinho**.

6. Clique em **Download**. Especifique ou **Procure** o local em que deseja que o download apareça. As atualizações são baixadas para o local especificado e colocadas em uma subpasta com o mesmo nome que a atualização. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.

>   [AZURE.NOTE]
Os hotfixes devem estar acessíveis nos dois controladores para detectar mensagens de erro potenciais do controlador em par.

#### Para instalar e verificar os hotfixes do modo normal

Siga as etapas abaixo para instalar e verificar os hotfixes do modo normal. Caso você já os tenha instalado usando o Portal do Azure, ignore esta etapa e vá para [Instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes).

1. Para instalar os hotfixes, acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Siga as instruções detalhadas em [Usar o PuTTy para se conectar ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). No prompt de comando, pressione **Enter**.

4. Selecione a **Opção 1** para fazer logon no dispositivo com acesso completo.

5. Para instalar o hotfix, no prompt de comando, digite:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Use o IP em vez do DNS no caminho de compartilhamento no comando acima. O parâmetro credential é usado somente para acessar um compartilhamento autenticado.

	É recomendável usar o parâmetro de credencial para acessar compartilhamentos. Até mesmo os compartilhamentos abertos para "todos" geralmente não são abertos para usuários não autenticados.

    Um exemplo de saída é mostrado abaixo.

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John

        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````

6. Digite **Y** quando solicitado a confirmar a instalação do hotfix.

7. Monitore a atualização usando o cmdlet `Get-HcsUpdateStatus`.

    A saída de exemplo a seguir mostra a atualização em andamento. O `RunInprogress` será `True` quando a atualização estiver em andamento.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 12/21/2015 10:36:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :
        ````

     A saída de exemplo a seguir indica que a atualização foi concluída. O `RunInProgress` será `False` quando a atualização estiver concluída.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 12/21/2015 10:59:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````

	> [AZURE.NOTE] Ocasionalmente, o cmdlet relatará `False` quando a atualização ainda estiver em andamento. Para garantir que o hotfix seja concluído, aguarde alguns minutos, execute esse comando novamente e verifique se `RunInProgress` é `False`. Em caso positivo, o hotfix foi concluído.

8. Após a conclusão da atualização de software, repita as etapas de 3 a 5 para instalar e monitorar o agente SaaS e o agente do MDS. Certifique-se de que `all-hcsmdssoftwareupdate_0b438ddf0d5b686aada2378b754fac8c7f2160e9.exe` seja instalado antes de `all-cismdsagentupdatebundle_f98e62f4d56c79e2a6644d027af7a2393a93827a.exe`.

9. Verifique as versões de software do sistema. Tipo:

    `Get-HcsSystem`

    Você deverá ver as seguintes versões:

    - HcsSoftwareVersion: 6.3.9600.17673
    - CisAgentVersion: 1.0.9150.0
    - MdsAgentVersion: 30.0.4698.13

	Se os números de versão não mudarem após a aplicação da atualização, isso indica que houve falha na aplicação do hotfix. Caso isso aconteça, entre em contato com o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter mais ajuda.

9. Repita as etapas de 3 a 5 para instalar os hotfixes restantes do modo normal.

	- O driver LSI - KB3121900
	- A atualização de Storport - KB3080728
	- A atualização de Spaceport - KB3090322

#### Para instalar e verificar hotfixes do modo de manutenção

Use o KB3121899 para instalar atualizações de firmware de disco. Estas são atualizações com interrupção e levam cerca de 30 minutos para ser concluídas. Você pode optar por instalá-las em uma janela de manutenção planejada conectando-se ao console serial do dispositivo.

Observe que, se o firmware de disco já estiver atualizado, você não precisará instalar essas atualizações. Execute o cmdlet `Get-HcsUpdateAvailability` no console serial do dispositivo para verificar se as atualizações estão disponíveis e se elas são interruptivas (modo de manutenção) ou não interruptivas (modo normal).

Para instalar as atualizações de firmware de disco, siga as instruções abaixo.

1. Coloque o dispositivo no Modo de manutenção. Observe que você não deve usar a comunicação remota do Windows PowerShell ao se conectar a um dispositivo no Modo de manutenção. Em vez disso, execute esse cmdlet no controlador do dispositivo quando conectado por meio do console serial do dispositivo. Tipo:

	`Enter-HcsMaintenanceMode`

	Um exemplo de saída é mostrado abaixo.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17664
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------

		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	Em seguida, ambos os controladores são reiniciados no modo de manutenção.

3. Para instalar a atualização de firmware de disco, digite:

	`Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

	Um exemplo de saída é mostrado abaixo.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
		Enter Password:
		WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
		Confirm
		This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
		[Y] Yes [N] No (Default is "Y"): Y
		WARNING: Installation is currently in progress. This operation can take several minutes to complete.

1.  Monitore o andamento da instalação usando o comando `Get-HcsUpdateStatus`. A atualização é concluída quando `RunInProgress` muda para `False`.

2.  Depois que a instalação for concluída, o controlador no qual o hotfix do modo de manutenção foi instalado será reiniciado. Faça logon como opção 1 com acesso completo e verifique a versão de firmware de disco. Tipo:

	`Get-HcsFirmwareVersion`

	As versões de firmware de disco esperados são:

	`XMGG, XGEG, KZ50, F6C2, VR08`

	Um exemplo de saída é mostrado abaixo.

        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17664
    	Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    	You are connected to Controller1
    	---------------------------------------------------------------

    	Controller1>Get-HcsFirmwareVersion

    	Controller0 : TalladegaFirmware
    	  ActiveBIOS:0.45.0006
    	  BackupBIOS:0.45.0008
    	  MainCPLD:17.0.0005
    	  ActiveBMCRoot:2.0.000E
    	  BackupBMCRoot:2.0.000E
    	  BMCBoot:2.0.0001
    	  LsiFirmware:19.00.00.00
    	  LsiBios:07.37.00.00
    	  Battery1Firmware:06.29
    	  Battery2Firmware:06.29
    	  DomFirmware:X231600
    	  CanisterFirmware:3.5.0.32
    	  CanisterBootloader:5.03
    	  CanisterConfigCRC:0xD1B030A4
    	  CanisterVPDStructure:0x06
    	  CanisterGEMCPLD:0x17
    	  CanisterVPDCRC:0xEE3504B4
    	  MidplaneVPDStructure:0x0C
    	  MidplaneVPDCRC:0xA6BD4F64
    	  MidplaneCPLD:0x10
    	  PCM1Firmware:1.00|1.05
    	  PCM1VPDStructure:0x05
    	  PCM1VPDCRC:0x41BEF99C
    	  PCM2Firmware:1.00|1.05
    	  PCM2VPDStructure:0x05
    	  PCM2VPDCRC:0x41BEF99C

    	  DisksFirmware
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG

	 Execute o comando `Get-HcsFirmwareVersion` no segundo controlador para verificar se a versão do software foi atualizada. Em seguida, você pode sair do modo de manutenção. Para isso, digite o comando a seguir para cada controlador de dispositivo:

    `Exit-HcsMaintenanceMode`

1. Os controladores são reiniciados quando você sai do modo de manutenção. Depois que as atualizações do firmware de disco forem aplicadas com êxito e o dispositivo tiver saído do modo de manutenção, retorne ao portal clássico do Azure. Observe que, por 24 horas, o portal poderá não mostrar que as atualizações do modo de manutenção foram instaladas.

<!---HONumber=AcomDC_0323_2016-->