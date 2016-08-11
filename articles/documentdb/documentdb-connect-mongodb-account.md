<properties 
	pageTitle="Conectar-se a uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB | Microsoft Azure" 
	description="Saiba como se conectar a uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB, agora disponível para a versão de visualização. Conectar-se usando a cadeia de conexão do MongoDB." 
	keywords="cadeia de conexão do mongodb"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# Como se conectar a uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB

Saiba como se conectar a uma conta do Banco de Dados de Documentos do Azure com suporte de protocolo para MongoDB usando o formato de URI da cadeia de conexão padrão do MongoDB.

## Obter informações sobre a cadeia de conexão da conta

1. Em uma nova janela, entre no [Portal do Azure](https://portal.azure.com).
2. Na folha **Todas as Configurações**, clique em **Cadeia de Conexão**. Para navegar até **Todas as Configurações**, na barra de navegação rápida, clique em **Procurar**, clique nas contas do **Banco de Dados de Documentos** e selecione a conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.

	![Captura de tela da folha Todas as Configurações](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. A folha **Informações da Cadeia de Conexões** é aberta e tem todas as informações necessárias para se conectar à conta usando um driver para MongoDB, incluindo uma cadeia de conexão pré-construída.

	![Captura de tela da folha de cadeia de conexão](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## Requisitos da cadeia de conexão

É importante observar que o Banco de Dados de Documentos é compatível com o formato de URI da cadeia de conexão padrão do MongoDB, com alguns requisitos específicos: contas do Banco de Dados de Documentos exigem autenticação e comunicação segura via SSL. Sendo assim, o formato da cadeia de conexão é:

	mongodb://username:password@host:port/[database]?ssl=true

Onde os valores dessa cadeia de caracteres estão disponíveis na folha Cadeia de Conexão mostrada acima.

- Nome de usuário (obrigatório)
	- Nome da conta do Banco de Dados de Documentos
- Senha (obrigatória)
	- Senha da conta do Banco de Dados de Documentos
- Host (obrigatório)
	- FQDN da conta do Banco de Dados de Documentos
- Porta (obrigatória)
	- 10250
- Banco de dados (opcional)
	- O banco de dados padrão usado pela conexão
- ssl=true (obrigatório)

Por exemplo, considere a conta mostrada nas Informações da Cadeia de Conexão acima. Uma cadeia de conexão válida é:
	
	mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## Conectando-se com o driver de C# para MongoDB
Como já mencionado, todas as contas do Banco de Dados de Documentos exigem autenticação e comunicação segura via SSL. Embora o formato de URI da cadeia de conexão do MongoDB permita um parâmetro de cadeia de consulta ssl=true, trabalhar com o driver de C# do MongoDB exige o uso do objeto MongoClientSettings na criação de um MongoClient. Considerando as informações de conta acima, o trecho de código a seguir mostra como se conectar à conta e trabalhar com o banco de dados "Tarefas".

	        MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
            
            
## Conectando-se com o driver de JAVA para MongoDB
Assim como na conexão C# em java também exige que autenticação e comunicação ocorram via SSL. Abaixo temos um padrão de uma ConnectionFactory em JAVA.

public class ConnectionFactory {
    
     public static MongoClient getConnection () 
     {
         String databaseURL = "HostName.documents.azure.com:10250";
         String user = "YourUser";
         String password = "YourPass";
         MongoClient mongo = null;
         
     try{
     
         String dbURI = "mongodb://"+user+":"+password+"@"+databaseURL+"/test?ssl=true";
         mongo = new MongoClient(new MongoClientURI(dbURI));        
         return mongo;
    
     }
     
     catch(MongoCommandException e){
         
         System.err.println( e.getClass().getName() + ": " + e.getMessage() );
         return null;
     }
}
}

## Próximas etapas


- Saiba como [usar o MongoChef](documentdb-mongodb-mongochef.md) com uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.
- Explore [amostras](documentdb-mongodb-samples.md) do Banco de Dados de Documentos com suporte de protocolo para MongoDB.

 

<!---HONumber=AcomDC_0720_2016-->
