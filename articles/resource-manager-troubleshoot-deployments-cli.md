<properties
   pageTitle="Exibir operações de implantação com a CLI do Azure | Microsoft Azure"
   description="Descreve como usar a CLI do Azure para detectar e corrigir problemas de implantação do Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Exibir operações de implantação com a CLI do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI do Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se você recebeu um erro durante a implantação de recursos do Azure, você talvez queira ver mais detalhes sobre as operações de implantação que foram executadas. A CLI do Azure fornece comandos que permitem encontrar os erros e determinar as possíveis correções.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

É possível evitar alguns erros validando o modelo e a infraestrutura antes da implantação. Você também pode registrar informações adicionais sobre solicitações e respostas durante a implantação que podem ser úteis mais tarde para a solução de problemas. Para obter informações sobre como validar e registrar informações de solicitação e resposta, consulte [Deploy a resource group with Azure Resource Manager template](resource-group-template-deploy-cli.md) (Implantar um grupo de recursos com modelos do Azure Resource Manager).

## Usar os logs de auditoria para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver os erros de uma implantação, use as seguintes etapas:

1. Para ver os logs de auditoria, execute o comando **azure group log show**. É possível incluir a opção **--last-deployment** para recuperar apenas o log da implantação mais recente.

        azure group log show ExampleGroup --last-deployment

2. O comando **azure group log show** pode retornar muitas informações. Na solução de problemas, normalmente você se concentra nas operações que falharam. O script a seguir usa a opção **--json** e o utilitário [jq](https://stedolan.github.io/jq/) do JSON para pesquisar o log em busca de falhas na implantação.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
            mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
            "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
        },
        ...

    Você pode ver que **properties** inclui informações em json sobre a operação que falhou.

    Você pode usar as opções **-verbose** e **- vv** para ver mais informações dos logs. Use a opção **-verbose** para exibir as etapas das operações em `stdout`. Para ver um histórico completo de solicitações, use a opção **- vv**. As mensagens geralmente fornecem pistas importantes sobre a causa de qualquer falha.

3. Para se concentrar na mensagem de status das entradas com falha, use o seguinte comando:

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == "Failed") | .properties.statusMessage"


## Usar operações de implantação para solucionar problemas

1. Obtenha o status geral de uma implantação com o comando **azure group deployment show**. No exemplo abaixo, a implantação falhou.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Para ver a mensagem das operações com falha de uma implantação, use:

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == "Failed") | .properties.statusMessage.Message"


## Próximas etapas

- Para obter ajuda com a resolução de erros de implantação específicos, veja [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para saber mais sobre como usar os logs de auditoria para monitorar outros tipos de ações, consulte [Operações de auditoria com o Resource Manager](resource-group-audit.md).
- Para validar sua implantação antes de executá-la, veja [Implantar um grupo de recursos com um modelo do Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0615_2016-->