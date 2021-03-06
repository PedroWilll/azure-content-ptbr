<properties
   pageTitle="Cenário de aplicativo lógico: crie um gatilho do Barramento de Serviço do Azure Functions | Microsoft Azure"
   description="Saiba como usar as Azure Functions como um gatilho do Barramento de Serviço para um aplicativo lógico"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# Cenário de aplicativo lógico: crie um gatilho do Barramento de Serviço do Azure usando as Azure Functions

Você pode usar as Azure Functions para criar um gatilho para um aplicativo lógico quando você precisa implantar um ouvinte ou uma tarefa de execução longa. Por exemplo, você pode criar uma função que escutaria em uma fila e acionaria imediatamente um aplicativo lógico como um gatilho de envio.

## Compilar o aplicativo lógico

Neste exemplo, você terá uma função em execução para cada aplicativo lógico que precisa ser disparado. Primeiro, crie um aplicativo lógico com um gatilho de solicitação HTTP. A função chama esse ponto de extremidade sempre que uma mensagem da fila é recebida.

1. Abra um novo aplicativo lógico e selecione o gatilho **Manual - Quando uma Solicitação HTTP for Recebida**. Opcionalmente, você pode especificar um esquema JSON para usar com a mensagem da fila usando uma ferramenta como [jsonschema.net](http://jsonschema.net). Cole o esquema no gatilho. Isso permitirá que o designer compreenda a forma dos dados e movimente as propriedades com mais facilidade pelo fluxo de trabalho.
1. Adicione as etapas adicionais que você deseja que ocorram após uma mensagem da fila ser recebida. Por exemplo, envie um email por meio do Office 365.
1. Salve o aplicativo lógico para gerar a URL de retorno de chamada do gatilho para esse aplicativo lógico. A URL aparecerá no cartão do gatilho.

![A URL de retorno de chamada aparece no cartão do gatilho][1]

## Compilar a função

Em seguida, você precisa criar uma função que atuará como o gatilho e ouvirá a fila.

1. Abra o [portal das Azure Functions](https://functions.azure.com/signin) e escolha **Nova Função**, então selecione o modelo **ServiceBusQueueTrigger - C#**.

    ![Portal do Azure Functions][2]

2. Configure a conexão com a fila do Barramento de Serviço (que usará o ouvinte de `OnMessageReceive()` do SDK do Barramento de Serviço).
3. Grave uma função simples para chamar o ponto de extremidade do aplicativo lógico (criado anteriormente) usando a mensagem da fila como gatilho. Aqui está um exemplo completo de uma função. O exemplo usa um tipo de conteúdo de mensagem `application/json`, mas você pode alterar isso se necessário.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";

   public static void Run(string myQueueItem, TraceWriter log)
   {

       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Para testar, adicionar uma mensagem na fila por meio de uma ferramenta como o [Explorer do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer). Veja o aplicativo lógico disparar imediatamente após a função receber a mensagem.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0727_2016-->