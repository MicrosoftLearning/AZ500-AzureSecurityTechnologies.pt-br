---
lab:
  title: 03 - Configurar seu locatário para ID Verificada do Microsoft Entra
  module: Module 01 - Manage Identity and Access
---
# Laboratório 03 - Configurar seu locatário para ID Verificada do Microsoft Entra
# Manual do laboratório para o aluno

## Cenário do laboratório

# Configurar seu locatário para ID Verificada do Microsoft Entra

>[!Note] 
> Agora a credencial verificável do Azure Active Directory é a ID verificada do Microsoft Entra e parte da família de produtos Microsoft Entra. Saiba mais sobre a [família Microsoft Entra](https://aka.ms/EntraAnnouncement) de soluções de identidade e comece a usar o [centro de administração unificado Microsoft Entra](https://entra.microsoft.com).

A ID Verificada do Microsoft Entra é uma solução de identidade descentralizada que ajuda você a proteger sua organização. Esse serviço permite emitir e verificar credenciais. Os emissores podem usar o serviço de ID Verificada para emitir as próprias credenciais verificáveis personalizadas. Os verificadores podem usar a API REST gratuita do serviço para solicitar e aceitar facilmente credenciais verificáveis em aplicativos e serviços. Em ambos os casos, o locatário do Azure AD precisa estar configurado para emitir suas credenciais verificáveis ou verificar a apresentação das credenciais verificáveis de um usuário emitidas por terceiros. Caso você seja um emissor e um verificador, poderá usar um só locatário do Azure AD para emitir suas credenciais verificáveis e para verificar as de outras pessoas.

Neste tutorial, você saberá como configurar o locatário do Azure AD para que ele possa usar o serviço de credenciais verificáveis.

Especificamente, você aprenderá como:

> - Criar uma instância do Azure Key Vault.
> - Configurar o serviço ID Verificada.
> - Registrar um aplicativo no Azure AD.
> - Verificar a propriedade de domínio do seu Identificador Descentralizado (DID)

O diagrama a seguir ilustra a arquitetura de ID Verificada e o componente que você configura.

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/8d4d01c2-3110-421a-91a8-7b052bc8d793)


## Pré-requisitos

- Verifique se você tem as permissões de administrador global ou de administrador de política de autenticação sobre o diretório que deseja configurar. Se você não for o administrador global, precisará da permissão de administrador do aplicativo para realizar o registro do aplicativo, incluindo a concessão de consentimento de administrador.
- Verifique se você tem a função de colaborador na assinatura ou no grupo de recursos do Azure em que está implantando o Azure Key Vault.

## Criar um cofre de chave

O Azure Key Vault é um serviço de nuvem que permite o armazenamento e o acesso seguros de segredos e chaves. O serviço de ID Verificada armazena chaves públicas e privadas no Azure Key Vault. Essas chaves são usadas para assinar e verificar as credenciais.

### Usa o portal do Azure para criar um Cofre de Chaves do Azure.

1. Abra uma sessão no navegador e entre no [menu do portal do Azure](https://portal.azure.com/).
   
2. Na caixa de pesquisa do portal do Azure, insira **Cofre de Chaves.**

3. Na lista de resultados, escolha **Key Vault.**

4. Na seção Cofre de chaves, escolha **Criar.**

5. Na guia **Básico** de **Criar um cofre de chaves,** insira ou selecione estas informações:
   
   |Configuração|Valor|
   |---|---|
   |**Detalhes do projeto**|
   |Subscription|Selecione sua assinatura.|
   |Resource group|Digite **azure-rg-1.** Selecione **OK**|
   |**Detalhes da instância**|
   |Nome do cofre de chaves|Digite **Contoso-vault2.**|
   |Região|Selecione **Leste dos EUA**|
   |Tipo de preço|Padrão do sistema **Standard**|
   |Dias de retenção dos cofres excluídos|Padrão do sistema **90**|

6. Selecione a **guia Revisar + criar** ou clique no botão azul Revisar + criar na parte inferior da página.
  
7. Selecione **Criar**.

Anote estas duas propriedades:

* **Nome do cofre**: no exemplo, é **Contoso-Vault2**. Você usará esse nome em outras etapas.
* **URI do cofre**: no exemplo, o URI do Cofre é `https://contoso-vault2.vault.azure.net/`. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar operações nesse novo cofre.

>[!NOTE]
>Por padrão, a conta que cria um cofre é a única com acesso. O serviço de ID Verificada precisa ter acesso ao cofre de chaves. Você precisa configurar o seu cofre de chaves com políticas de acesso que permitam que a conta usada durante a configuração crie e exclua chaves. Para que seja possível criar a associação de domínio para Credenciais Verificáveis, a conta usada durante a configuração também exige permissão para a ID Verificada. Se você usar a mesma conta durante o teste, modifique a política padrão para conceder a permissão para assinar à conta, além das permissões padrão concedidas aos criadores do cofre.

### Definir políticas de acesso para o cofre de chaves

Uma Cofre de chaves define se uma entidade de segurança específica principal pode realizar operações em segredos e chaves do Cofre de chaves. Defina políticas de acesso no cofre de chaves para a conta de administrador do serviço de ID Verificada e para a entidade de API do serviço de solicitação que você criou.
Depois de criar o cofre de chaves, as Credenciais Verificáveis geram um conjunto de chaves usadas para fornecer segurança de mensagem. Essas chaves são armazenadas no Key Vault. Você usa um conjunto de chaves para assinatura, atualização e recuperação de credenciais verificáveis.

### Definir políticas de acesso para o usuário administrador de ID Verificada

1. Entre no [portal do Azure](https://portal.azure.com).

2. Vá até o cofre de chaves que você usa para este tutorial.

3. Em **Configurações**, selecione **Políticas de acesso**.

4. Na página **Adicionar políticas de acesso**, em **USUÁRIO**, selecione a conta usada para seguir este tutorial.

5. Para as **Permissões de chave**, verifique se as permissões a seguir estão selecionadas: **Get**, **Criar**, **Excluir** e **Assinar**. Por padrão, **Criar****e Excluir** já estão habilitados. **Assinar** deve ser a única permissão de chave que você precisa atualizar.

      ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/7c8a92ea-24f1-41e6-9656-869e8486af72)


6. Para salvar as alterações, selecione **Salvar**.

## Configurar a ID Verificada

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b4b857a2-24b8-4c3f-9f5c-43d23b58427f)


Para configurar a ID Verificada, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise a *ID Verificada*. Em seguida, selecione **ID Verificada**.

3. No menu à esquerda, selecione **Instalação**.

4. No menu do meio, selecione **Definir configurações da organização**

5. Configure sua organização fornecendo as seguintes informações:

    1. **Nome da organização**: insira um nome para fazer referência à empresa nas ID Verificadas. Seus clientes não veem esse nome.

    1. **Domínio confiável**: insira um domínio que é adicionado a um ponto de extremidade de serviço no seu documento de DID (identidade descentralizada). O domínio é o que associa sua DID a algo tangível que o usuário pode saber sobre a sua empresa. O Microsoft Authenticator e outras carteiras digitais usam essas informações para validar que a sua DID está vinculada ao seu domínio. Se a carteira puder verificar a DID, ela exibirá um símbolo de verificado. Caso contrário, ela informará ao usuário que a credencial foi emitida por uma organização que não pôde ser validada.

        >[!IMPORTANT]
        > O domínio não pode ser um redirecionamento. Caso contrário, a DID e o domínio não podem ser vinculados. Use HTTPS para o domínio. Por exemplo: `https://did.woodgrove.com`.

    1. **Cofre de chaves**: selecione o cofre de chaves que você criou anteriormente.

    1. Em **Avançado**, você pode escolher o **sistema de confiança** que deseja usar para seu locatário. Você pode escolher entre **Web** ou **ION**. Web significa que seu locatário usa [did:web](https://w3c-ccg.github.io/did-method-web/) como método do DID e ION significa que ele usa [did:ion](https://identity.foundation/ion/).

        >[!IMPORTANT]
        > A única maneira de alterar o sistema de confiança é recusar o serviço ID Verificada e refazer a integração.

1. Selecione **Salvar**.  

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b191676e-03e3-423f-aa28-1e3d2887ea07)


### Definir políticas de acesso para as entidades de serviço de ID Verificada

Quando você configura a ID verificada na etapa anterior, as políticas de acesso no Azure Key Vault são atualizadas automaticamente para fornecer as permissões necessárias às entidades de serviço da ID Verificada.  
Se você precisar redefinir as permissões manualmente, a política de acesso deverá ter a aparência abaixo.

| Entidade de Serviço | AppId | Permissões de chave |
| -------- | -------- | -------- |
| Serviço de credenciais verificáveis | bb2a64ee-5d29-4b07-a491-25806dc854d3 | Obter, Assinar |
| Solicitação de serviço de credenciais verificáveis | 3db474b9-6a0c-4840-96ac-1fceb342124f | Assinar |



![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/896b8ea0-b88b-43b8-a93b-4771defedfde)


## Registrar um aplicativo no Azure AD

Seu aplicativo precisará obter tokens de acesso quando quiser chamar ID Verificada do Microsoft Entra para que ele possa emitir ou verificar credenciais. Para obter tokens de acesso, você precisa registrar um aplicativo e conceder permissão de API para o serviço de solicitação de ID Verificada. Por exemplo, use as seguintes etapas para um aplicativo Web:

1. Entre no [portal do Azure](https://portal.azure.com) com sua conta administrativa.

1. Se você tiver acesso a vários locatários, selecione o ícone **Diretório + assinatura**. Então pesquise e selecione o serviço **Azure Active Directory**.

1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.  

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/03ca3d13-5564-4ce2-b42c-f8333bc97fb5)


1. Insira um nome de exibição para o seu aplicativo. Por exemplo, *verifiable-credentials-app*.

1. Para **Tipos de contas com suporte**, selecione **Contas somente neste diretório organizacional (somente o diretório padrão – locatário único)** .

1. Selecione **Registrar** para criar o aplicativo.

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d0a15737-c8a9-4522-990d-1cf6bc12cc1e)


### Conceder permissões para obter tokens de acesso

Nesta etapa, você concede permissões à entidade de serviço de **Solicitação de Serviço de Credenciais Verificáveis**.

Para adicionar as permissões necessárias, siga estas etapas:

1. Fique na página de detalhes do aplicativo **verifiable-credentials-app**. Selecione **Permissões de API** > **Adicionar uma permissão**.

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/adf97022-2081-4273-8d61-f2b53628e989)

1. Selecione **APIs que a minha organização usa**.

1. Pesquise a entidade de serviço **Solicitação de serviço de credenciais verificáveis** e selecione-a.

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/01691eb2-ab7f-4778-9911-342eb9350f99)

1. Escolha **Permissão de Aplicativo** e expanda **VerifiableCredential.Create.All**.

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/3c5e008e-2ba5-417a-90ff-22e8f622ad34)


1. Selecione **Adicionar Permissões**.

1. Selecione **Dar consentimento do administrador para\<your tenant name\>**.

Você pode optar por conceder permissões de emissão e apresentação separadamente se preferir segregar os escopos para aplicativos diferentes.

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/640def45-e666-423e-bf69-598e8980b125)

## Registrar ID descentralizada e verificar a propriedade do domínio

Depois que o Azure Key Vault estiver configurado e o serviço tiver uma chave de assinatura, concluía as etapas 2 e 3 na configuração.

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/40e10177-b025-4d10-b16a-d66c06762c3f)

1. Navegue até o serviço ID Verificada no portal do Azure.  
1. No menu à esquerda, selecione **Instalação**.
1. No menu do meio, selecione **Verificar a propriedade do domínio**.

# Verificar a propriedade de domínio do seu Identificador Descentralizado (DID)

## Verificar a propriedade do domínio e distribuir o arquivo did-configuration.json

O domínio precisa ser um domínio sob seu controle e deve estar no formato `https://www.example.com/`. 

1. No portal do Azure, vá para a página VerifiedID.

1. Selecione **Instalação** e, em seguida, **Verificar a propriedade do domínio** e escolha **Verificar** para o domínio

1. Copie ou baixe o arquivo `did-configuration.json`.

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/342fa12f-2d0d-40cf-a2f9-8796a86f0824)

1. Hospede o arquivo `did-configuration.json` no local especificado. Exemplo: se você especificou o domínio `https://www.example.com`, o arquivo precisará ser hospedado nesta URL `https://www.example.com/.well-known/did-configuration.json`.
   Não pode haver nenhum caminho adicional na URL além do nome `.well-known path`.

1. Quando o `did-configuration.json` estiver disponível publicamente na URL .well-known/did-configuration.json, verifique-o pressionando o botão **Atualizar status de verificação**.

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/49a06251-af56-49b2-9059-0bd4ca678da6)

> Resultado: você criou com êxito uma instância do Cofre de Chaves do Azure, configurou o serviço de ID Verificada, registrou um aplicativo no Azure AD e verificou a propriedade do domínio para seu Identificador Descentralizado (DID).

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que não sejam gerados custos inesperados. 
