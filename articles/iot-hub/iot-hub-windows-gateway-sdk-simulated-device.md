<properties
	pageTitle="Simular um dispositivo com o SDK do Gateway | Microsoft Azure"
	description="Passo a passo do SDK do Gateway do Hub IoT do Azure usando o Windows para ilustrar o envio de telemetria de um dispositivo simulado usando o SDK do Gateway do Hub IoT do Azure."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/20/2016"
     ms.author="cstreet"/>


# SDK do Gateway IoT (beta) - enviar mensagens do dispositivo para a nuvem com um dispositivo simulado usando o Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## Criar e executar a amostra

Antes de começar, é necessário:

- [Configure seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Windows.
- [Criar um hub IoT][lnk-create-hub] em sua assinatura do Azure; você precisará do nome do hub para concluir este passo a passo. Se você ainda não tem uma assinatura do Azure, é possível obter uma [conta gratuita][lnk-free-trial].
- Adicione dois dispositivos ao hub IoT e anote as IDs e chaves de dispositivo. É possível usar o [Gerenciador de Dispositivo ou a ferramenta iothub-explorer][lnk-explorer-tools] para adicionar seus dispositivos ao hub IoT criado na etapa anterior e recuperar suas chaves.

Para compilar a amostra:

1. Abra um prompt de comando **Prompt de Comando do Desenvolvedor para VS2015**.
2. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk**.
3. Execute o script **tools\\build.cmd**. Este script cria um arquivo de solução do Visual Studio, compila a solução e executa os testes. É possível encontrar a solução do Visual Studio na pasta **build** na cópia local do repositório **azure-iot-gateway-sdk**.

Para executar a amostra:

Em um editor de texto, abra o arquivo **samples\\simulated\_device\_cloud\_upload\\src\\simulated\_device\_cloud\_upload\_win.json** na cópia local do repositório **azure-iot-gateway-sdk**. Este arquivo configura os módulos no gateway de exemplo:

- O módulo **IoTHub** se conecta ao seu hub IoT. É necessário configurá-lo para enviar dados ao hub IoT. Especificamente, defina o valor de **IoTHubName** como o nome de seu hub IoT e defina o valor de **IoTHubSuffix** como **azure-devices.net**.
- O módulo **mapping** mapeia os endereços MAC dos dispositivos simulados para as IDs de dispositivo do Hub IoT. Verifique se os valores de **deviceId** correspondem às IDs dos dois dispositivos que você adicionou ao hub IoT e se os valores de **deviceKey** contêm as chaves dos dois dispositivos.
- Os módulos **BLE1** e **BLE2** são os dispositivos simulados. Observe como os endereços MAC correspondem ao módulo **mapping**.
- O módulo **Logger** registra a atividade de gateway em um arquivo.
- Os valores de **caminho do módulo** mostrados abaixo pressupõem que você clonou o repositório do SDK do Gateway na raiz da unidade **C:**. Se você o baixou em outro local, precisará ajustar os valores de **caminho do módulo** de acordo.

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\iothubhttp\\Debug\\iothubhttp_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\identitymap\\Debug\\identitymap_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "GW-ble1-demo",
                    "deviceKey"  : "{Device key}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "GW-ble2-demo",
                    "deviceKey"  : "{Device key}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\ble_fake\\Debug\\ble_fake_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\ble_fake\\Debug\\ble_fake_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ]
}
```

Salve todas as alterações feitas no arquivo de configuração.

Para executar a amostra:

1. No prompt de comando, navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk**.
2. Execute o comando a seguir:
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. É possível usar o [Gerenciador de Dispositivo ou a ferramenta iothub-explorer][lnk-explorer-tools] para monitorar as mensagens que o hub IoT recebe do gateway.


## Próximas etapas

Se você quiser obter uma compreensão mais avançada do SDK do Gateway e experimentar alguns exemplos de código, visite os seguintes recursos e tutoriais para desenvolvedores:

- [Enviar mensagens do dispositivo para a nuvem de um dispositivo real com o SDK do Gateway][lnk-physical-device]
- [Gerenciar um dispositivo de gateway][lnk-manage-devices]
- [SDK de Gateway do Azure IoT][lnk-gateway-sdk]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Guia do desenvolvedor][lnk-devguide]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md
[lnk-manage-devices]: iot-hub-gateway-sdk-device-management.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->