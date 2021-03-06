<properties
   pageTitle="Usando o Validador de XML do BizTalk em aplicativos lógicos do Serviço de Aplicativo do Azure | Microsoft Azure"
   description="Validar esquemas usando o Validador de XML do BizTalk em seu aplicativo lógico"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Validador de XML do BizTalk

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Use o conector do Validador de XML do BizTalk em seu aplicativo para validar os dados XML com base em esquemas XML predefinidos. Usuários podem usar esquemas existentes ou gerar esquemas com base em uma instância de arquivo simples, instância JSON ou em conectores existentes.

## Usando o Validador de XML do BizTalk
Para usar o Validador de XML do BizTalk, primeiro crie uma instância do aplicativo de API do Validador de XML do BizTalk. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Validador de XML do BizTalk do Azure Marketplace.

### Configurar o Validador de XML do BizTalk
O Validador de XML do BizTalk usa esquemas como parte de sua configuração. Os usuários podem inicializar a folha de configuração do Aplicativo de API inicializando o Aplicativo de API diretamente do Portal do Azure, ou clicando duas vezes no Aplicativo de API na superfície do designer:

![Configuração do Validador de XML do BizTalk][1]

Na folha Aplicativo de API, os usuários podem configurar esquemas selecionando *Esquemas*:

![Parte Esquemas do Validador de XML do BizTalk][2]

Os usuários podem carregar esquemas do disco ou gerar um com base em uma instância de arquivo simples ou em uma instância JSON:

![Esquemas do Validador de XML do BizTalk][3]


### Usando o Codificador de Arquivo Simples do BizTalk na superfície de design
Depois de configurado, os usuários podem selecionar *->* e escolher uma ação na lista:

![Lista de ações do Validador de XML do BizTalk][4]

#### Validar Xml

A ação Validar Xml valida uma entrada XML específica com base em esquemas pré-configurados:

![Validar Xml no Validador de XML do BizTalk][5]

Parâmetro|Tipo|Descrição do parâmetro
---|---|---
XML de entrada|string|Xml de entrada a ser validado

A ação retorna a saída como um objeto. A saída contém o modelo que representa a resposta do Validador de XML. Ela consiste do resultado, nome do esquema, nó raiz e descrição do erro.


<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG

<!---HONumber=AcomDC_0727_2016-->