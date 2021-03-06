<properties
   pageTitle="Como ativar ou desativar uma função | Microsoft Azure"
   description="Aprenda a ativar funções para identidades com privilégios com o aplicativo Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/29/2016"
   ms.author="kgremban"/>

# Como ativar ou desativar funções no Azure AD Privileged Identity Management

O Azure AD (Active Directory) Privileged Identity Management simplifica como as empresas gerenciam as identidades com privilégios e o acesso a recursos no Azure AD e em outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.

Se você tiver se tornado elegível para uma função administrativa, isso significará que você poderá ativar essa função quando precisar executar uma tarefa que a exige. Por exemplo, se você precisa gerenciar o Office 365 apenas algumas vezes, os administradores de função com privilégios da sua organização não desejam tornar um administrador permanente. Em vez disso, eles fazem de você elegível para as funções de Administrador Global ou Administrador do Exchange Online no Azure AD. Então, você solicita uma atribuição de função temporária quando precisar desses privilégios, e você terá controle administrativo para o Office 365 por um período de tempo predeterminado.

Este artigo destina-se a administradores que precisam ativar sua função no Azure AD PIM (Privileged Identity Management). Ele vai orientá-lo pelas etapas para ativar uma função quando você precisar das permissões e desativar a função quando terminar.


## Adicionar o aplicativo Privileged Identity Management

Use o aplicativo do Azure AD Privileged Identity Management no [Portal do Azure](https://portal.azure.com/) para solicitar uma ativação de função mesmo se você for operar em outro portal ou por meio do PowerShell. Se você não tiver o aplicativo Azure AD Privileged Identity Management em seu portal do Azure, siga estas etapas para começar.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Se sua organização tiver mais de um diretório, clique em seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório no qual você vai operar.
3. Selecione **Novo** > **Segurança + Identidade** > **Azure AD Privileged Identity Management**.

    ![Habilitar o PIM no portal][1]

4. Marque **Fixar no painel** e então clique em **Criar**. O aplicativo Privileged Identity Management será aberto.

## Ativar uma função

Quando você precisar assumir uma função, poderá solicitar a ativação usando o botão **Ativar minhas funções** no aplicativo Azure AD Privileged Identity Management.


1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o bloco Azure AD Privileged Identity Management.
2. Selecione **Ativar minhas funções**. Será exibida uma lista das funções que foram atribuídas a você.
3. Selecione a função que você deseja ativar.
4. Selecione **Ativar**. A folha **Solicitar ativação de função** será exibida.
5. Algumas funções exigem MFA (Multi-Factor Authentication) antes que você possa ativar a função. Você só precisa fazer isso uma vez por sessão.

    ![Verifique com o MFA antes da ativação de função - captura de tela][2]

6. Insira o motivo para a solicitação de ativação no campo de texto. Algumas funções exigem que você forneça um número de tíquete de problema.
7. Selecione **OK**. A função agora será ativada e a alteração de função se tornará visível no Microsoft Online Services.

## Desativar uma função

Após uma função ter sido ativada, ela será automaticamente desativada quando seu limite de tempo for atingido.

Se você terminar antes, também poderá desativar uma função manualmente no aplicativo Azure AD Privileged Identity Management. Selecione **Ativar minhas funções**, escolha a função que você terminou de usar e selecione **Desativar**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Caso você esteja interessado em aprender mais sobre o Azure AD Privileged Identity Management, os links a seguir têm mais informações.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png

<!---HONumber=AcomDC_0706_2016-->