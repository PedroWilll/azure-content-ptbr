<properties
	pageTitle="Introdução ao Azure Mobile Engagement para implantação do Unity para Android"
	description="Saiba como usar o Azure Mobile Engagement com Análises e Notificações por Push para aplicativos Unity implantados em dispositivos iOS."
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Introdução ao Azure Mobile Engagement para implantação do Unity para Android

[AZURE.INCLUDE [Alternador de tutorial do Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso de aplicativos e como enviar notificações por push a usuários segmentados de um aplicativo do Unity durante a implantação em um dispositivo Android. Este tutorial usa o tutorial clássico Roll a Ball do Unity como ponto de partida. Você deve seguir as etapas deste [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de prosseguir com a integração do Mobile Engagement que demonstramos no tutorial abaixo.

Este tutorial exige o seguinte:

+ [Editor do Unity](http://unity3d.com/get-unity)
+ [SDK do Unity do Mobile Engagement](https://aka.ms/azmeunitysdk)
+ SDK do Android do Google

> [AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).

##<a id="setup-azme"></a>Configuração do Mobile Engagement para seu aplicativo Android

[AZURE.INCLUDE [Criar Aplicativo de Mobile Engagement no Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

###Importar o pacote do Unity

1. Baixe o [pacote do Unity do Mobile Engagement](https://aka.ms/azmeunitysdk) e salve-o em seu computador local. 

2. Vá para **Ativos -> Importar Pacote -> Pacote Personalizado** e selecione o pacote que você baixou na etapa anterior.

	![][70]

3. Verifique se todos os arquivos estão selecionados e clique no botão **Importar**.

	![][71]

4. Depois que a importação for bem-sucedida, você verá os arquivos importados do SDK em seu projeto.

	![][72]

###Atualizar o EngagementConfiguration

1. Abra o arquivo de script **EngagementConfiguration** na pasta do SDK e atualize **ANDROID\_CONNECTION\_STRING** com a cadeia de conexão que você obteve anteriormente no portal do Azure.  

	![][73]

2. Salve o arquivo

3. Execute **Arquivo -> Envolvimento -> Gerar Manifesto Android**. Esse é o plug-in adicionado pelo SDK do Mobile Engagement, e quando você clicar nele, isso atualizará automaticamente as configurações do projeto.

	![][74]

> [AZURE.IMPORTANT] Execute isso sempre que você atualizar o arquivo **EngagementConfiguration**, caso contrário, suas alterações não serão refletidas no aplicativo.

###Configurar o aplicativo para o acompanhamento básico

1. Abra o script **PlayerController** anexado ao objeto Player para edição. 

2. Adicione a seguinte instrução usando:

		using Microsoft.Azure.Engagement.Unity;

3. Adicione o seguinte ao método `Start()`
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###Implantar e executar o aplicativo
Verifique se você tem o SDK do Android instalado no computador antes de tentar implantar esse aplicativo Unity em seu dispositivo.

1. Conecte um dispositivo Android a seu computador. 

2. Abra **Arquivo -> Configurações de Compilação**

	![][40]

3. Selecione **Android** e clique em **Alternar Plataforma**

	![][51]

	![][52]

4. Clique em **Configurações do jogador** e forneça um Identificador de Pacote válido.

	![][53]

5. Finalmente, clique em **Compilar e Executar**

	![][54]

6. Você poderá ser solicitado a fornecer um nome de pasta para armazenar o pacote do Android.

7. Se tudo correr bem, o pacote será implantado no dispositivo conectado e você verá o jogo Unity em seu telefone!

##<a id="monitor"></a>Conectar o aplicativo com monitoramento em tempo real

[AZURE.INCLUDE [Conectar o aplicativo com monitoramento em tempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo

[AZURE.INCLUDE [Habilitar as mensagens em nuvem do Google](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###Atualizar o EngagementConfiguration

1. Abra o arquivo de script **EngagementConfiguration** na pasta do SDK e atualize **ANDROID\_GOOGLE\_NUMBER** com o **Número do Projeto do Google** obtido anteriormente do portal do Desenvolvedor de Nuvem do Google. Esse é um valor de cadeia de caracteres, assim, coloque-o entre aspas duplas. 

	![][75]

2. Salve o arquivo.

3. Execute **Arquivo -> Envolvimento -> Gerar Manifesto Android**. Esse é o plug-in adicionado pelo SDK do Mobile Engagement, e quando você clicar nele, isso atualizará automaticamente as configurações do projeto.

	![][74]

###Configurar o aplicativo para receber notificações

1. Abra o script **PlayerController** anexado ao objeto Player para edição. 

2. Adicione o seguinte ao método `Start()`

		EngagementReachAgent.Initialize();

3. Agora que o aplicativo está atualizado, implante e execute o aplicativo em um dispositivo de acordo com as instruções abaixo.

[AZURE.INCLUDE [Enviar notificações do portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png

<!---HONumber=AcomDC_0330_2016-->