---
lab:
  title: 04 – Implementar a Sincronização de diretório
  module: Module 01 - Manage Identity and Access
---

# Laboratório 04: Implementar a Sincronização de diretório
# Manual de laboratório do aluno

## Cenário do laboratório

Você recebeu uma solicitação para criar uma prova de conceito demonstrando como integrar o ambiente local dos Serviços de domínio do Microsoft Entra com um locatário do Microsoft Entra. Especificamente, você precisa:

- Implementar uma floresta de domínio único do Microsoft Entra Domain Services implantando uma VM do Azure que hospeda um controlador de domínio do Microsoft Entra Domain Services
- Criar e configurar um locatário do Microsoft Entra
- Sincronizar a floresta dos Serviços de domínio Microsoft Entra com o locatário do Microsoft Entra

> Para todos os recursos neste laboratório, estamos usando a região **Leste dos EUA**. Verifique com seu instrutor se esta é a região para ser usada na aula. 

## Objetivos do laboratório

Neste laboratório, você completará os seguintes exercícios:

- Exercício 1: implantar uma VM do Azure hospedando um controlador de domínio do Microsoft Entra ID
- Exercício 2: criar e configurar um locatário do Microsoft Entra
- Exercício 3: sincronizar a floresta do Microsoft Entra ID com um locatário do Microsoft Entra

## Implementar a Sincronização de diretório

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/5d9cc4c7-7dcd-4d88-920d-9c97d5a482a2)

## Instruções

### Exercício 1: implantar uma VM do Azure hospedando um controlador de domínio do Microsoft Entra ID

### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: identificar um nome DNS disponível para uma implantação de VM do Azure
- Tarefa 2: usar um modelo do ARM para implantar uma VM do Azure que hospeda um controlador de domínio do Microsoft Entra ID

#### Tarefa 1: identificar um nome DNS disponível para uma implantação de VM do Azure

Nesta tarefa, você identificará um nome DNS para sua implantação de VM do Azure. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. Abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

3. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

4. Na seção do PowerShell, no painel do Cloud Shell, execute o seguinte para identificar um nome DNS disponível que você pode usar para uma implantação de VM do Azure na próxima tarefa deste exercício:

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**Observação**: substitua o `<custom-label>`espaço reservado por um nome DNS válido que provavelmente seja exclusivo globalmente. Substitua o espaço reservado `<location>` pelo nome da região na qual você deseja implantar a VM do Azure que hospedará o controlador de domínio do Active Directory que você usará neste laboratório.

    >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

5. Verifique se o comando retornou **Verdadeiro**. Caso contrário, execute novamente o mesmo comando com um valor diferente do `<custom-label>` até que o comando retorne **True**.

6. Registre o valor do `<custom-label>` que resultou no resultado bem-sucedido. Isso será necessário na próxima tarefa.

7. Feche o Cloud Shell.

#### Tarefa 2: usar um modelo do ARM para implantar uma VM do Azure que hospeda um controlador de domínio do Microsoft Entra ID

Nesta tarefa, você implantará uma VM do Azure que hospedará um controlador de domínio do Microsoft Entra ID

1. Abra uma outra guia na mesma janela do navegador e acesse **https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain**.

2. Na página **Criar uma VM do Azure com uma nova Floresta do AD**, clique em **Implantar no Azure**. Isso redirecionará automaticamente o navegador para a folha **Criar uma VM do Azure com uma nova Floresta do AD** no portal do Azure. 

3. Na folha **Criar uma VM do Azure com uma nova Floresta do AD**, clique em **Editar parâmetros**.

4. Na folha **Editar parâmetros**, clique em **Carregar arquivo**, na caixa de diálogo **Abrir**, navegue até a pasta **\\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json**, clique em **Abrir** e, em seguida, **Salvar**.

5. Na folha **Criar uma VM do Azure com uma nova Floresta do AD**, especifique as seguintes configurações (deixe as outras com seus valores existentes):

   |Configuração|Valor|
   |---|---|
   |Subscription|o nome da sua assinatura do Azure|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB06**|
   |Region|a região do Azure que você identificou na tarefa anterior|
   |Nome de Usuário do Administrador|**Aluno**|
   |Senha do Administrador|**Use sua senha pessoal criada no Laboratório 02 > Exercício 1 > Tarefa 1 > Etapa 9.**|
   |Nome do Domínio|**adatum.com**|
   |Prefixo do DNS|o nome do host DNS que você identificou na tarefa anterior|
   |Tamanho da VM|**Standard_D2s_v3**|

6. Na folha **Criar uma VM do Azure com uma nova Floresta do AD**, clique em **Revisar + Criar** e, em seguida, clique em **Criar**.

    >**Observação**: não espere a conclusão da implantação, prossiga para o próximo exercício. A implantação pode levar até 15 minutos. Você usará a máquina virtual implantada nesta tarefa no terceiro exercício deste laboratório.

> Resultado: depois de concluir esse exercício, você iniciou a implantação de uma VM do Azure que hospedará um controlador de domínio do Microsoft Entra ID usando um modelo do Resource Manager do Azure


### Exercício 2: criar e configurar um locatário do Microsoft Entra 

### Tempo estimado: 20 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar um locatário do Microsoft Entra
- Tarefa 2: adicionar um nome DNS personalizado ao novo locatário do Microsoft Entra
- Tarefa 3:criar um usuário do Microsoft Entra ID com a função Administrador Global

#### Tarefa 1: criar um locatário do Microsoft Entra

Nesta tarefa, você criará um novo locatário do Microsoft Entra para usar neste laboratório. 

1. No portal do Azure, a caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Entra ID** e pressione a tecla **Enter**.

2. Na folha que exibe **Visão geral** do locatário atual do Microsoft Entra, clique em **Gerenciar locatários** e, na próxima tela, clique em **+ Criar**.

3. Na guia **Noções básicas** da folha **Criar um locatário**, verifique se a opção **Microsoft Entra ID** está selecionada e clique em **Avançar: Configuração >**.

4. Na guia **Configuração** da folha **Criar um diretório**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome da organização|**AdatumSync**|
   |Nome de domínio inicial|um nome exclusivo que consiste de uma combinação de letras e dígitos|
   |País ou Região|**Estados Unidos**|

    >**Observação**: registre o nome de domínio inicial. Você precisará disso nesse laboratório.

    >**Observação**: a marca de verificação verde na caixa de texto **Nome de domínio inicial** indicará se o nome de domínio digitado é válido e exclusivo. (Registre seu nome de domínio inicial para uso posterior).

5. Clique em **Revisar + criar** e em **Criar**.

    >**Observação**: aguarde até que o novo locatário seja criado. Use o ícone de **Notificação** para monitorar o status da implantação. 

#### Tarefa 2: adicionar um nome DNS personalizado ao novo locatário do Azure AD

Nessa tarefa, você adicionará seu nome DNS personalizado ao novo locatário do Azure AD. 

1. No portal do Azure, na barra de ferramentas, clique no ícone **Diretório + assinatura**, localizado à direita do ícone do Cloud Shell. 

2. Na folha **Diretório + assinatura**, selecione a linha **AdatumSync** do locatário recém-criado e clique no botão **Alternar**.

    >**Observação**: talvez seja necessário atualizar a janela do navegador se a entrada **AdatumSync** não aparecer na lista de filtros do **Diretório + assinatura**.

3. Na folha do **Microsoft Entra ID do AdatumSync\|**, na seção **Gerenciar**, clique em **Nomes de domínio personalizados**.

4. Na folha **Nomes de domínio personalizados do AdatumSync\|**, clique em **+ Adicionar domínio personalizado**.

5. Na folha **Nome de domínio personalizado**, na caixa de texto **Nome de domínio personalizado**, digite **adatum.com** e clique em **Adicionar domínio**.

6. Na folha **adatum.com**, revise as informações necessárias para executar a verificação do nome de domínio do Microsoft Entra e selecione **Excluir** duas vezes. 

    >**Observação**: você não poderá concluir o processo de validação porque não possui o nome de domínio DNS **adatum.com**. Isso não impedirá que você sincronize o domínio **adatum.com** dos Serviços de Domínio do Microsoft Entra com o locatário do Microsoft Entra. Você usará para essa finalidade o nome DNS inicial do locatário do Microsoft Entra (o nome que termina com o sufixo **onmicrosoft.com** ), que você identificou na tarefa anterior. No entanto, lembre-se de que, como resultado, o nome de domínio DNS do domínio dos Serviços de Domínio Microsoft Entra e o nome DNS do locatário do Microsoft Entra serão diferentes. Isso significa que os usuários do Adatum precisarão usar nomes diferentes ao entrar no domínio dos Serviços de Domínio Microsoft Entra e ao entrar no locatário do Microsoft Entra.

#### Tarefa 3: criar um usuário do Microsoft Entra ID com a função Administrador Global

Nessa tarefa, você adicionará um novo usuário do Microsoft Entra ID e o atribuirá à função de Administrador Global. 

1. Na folha do locatário **AdatumSync** do Microsoft Entra, na seção **Gerenciar**, selecione **Usuários**.

2. Na folha **Usuários | Todos os usuários**, clique em **+ Novo usuário** e, em seguida, em **Criar novo usuário**.

3. Na folha **Novo usuário**, verifique se a opção **Criar usuário** está selecionada, especifique as seguintes configurações na guia Noções básicas (deixando as demais com seus valores padrão) e clique em **Próximo: Propriedades >**:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**syncadmin**|
   |Nome|**syncadmin**|
   |Senha|verifique se a opção **Gerar senha automaticamente** está selecionada e clique em **Mostrar senha**|

    >**Observação**: registre o nome de usuário completo. Você pode copiar o valor clicando no botão **Copiar para a área de transferência**, no lado direito da lista suspensa que exibe o nome de domínio, e colando-o em um documento do bloco de notas. Você precisará disso mais tarde neste laboratório.

    >**Observação**: registre a senha do usuário clicando no botão **Copiar para a área de transferência**, no lado direito da caixa de texto Senha, e colando-a em um documento do bloco de notas. Você precisará disso mais tarde neste laboratório.

4. Na guia **Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todos os outros com seus valores padrão) e clique em **Avançar: Atribuições >**.

5. Na guia **Atribuições**, clique em **+ Adicionar função**, procure e selecione **Administrador Global** e clique em **Selecionar**. Clique em **Revisar + criar** e em **Criar**.
   
    >**Observação**: um usuário do Azure AD com a função de Administrador Global é necessário para implementar o Microsoft Entra Connect.

6. Abra uma janela do navegador InPrivate.

7. Navegue até o portal do Azure em **`https://portal.azure.com/`** e entre usando a **conta de usuário syncadmin**. Quando solicitado, altere a senha registrada anteriormente nessa tarefa para sua própria senha que atenda aos requisitos de complexidade e registre-a para referência futura. Será solicitado que você forneça a senha nas tarefas futuras.

    >**Observação**: para entrar, você precisará fornecer um nome totalmente qualificado da conta de usuário **syncadmin**, incluindo o nome de domínio DNS do locatário do Microsoft Entra, que você registrou anteriormente nessa tarefa. Esse nome de usuário está no formato syncadmin@`<your_tenant_name>`.onmicrosoft.com, em que `<your_tenant_name>` é o espaço reservado que  representa seu nome de locatário exclusivo do Microsoft Entra. 

8. Saia como **syncadmin** e feche a janela do navegador InPrivate.

> **Resultado**: depois de concluir esse exercício, você criou um locatário do Microsoft Entra, viu como adicionar um nome DNS personalizado ao novo locatário do Microsoft Entra e criou um usuário do Azure AD com a função de Administrador Global.


### Exercício 3: sincronizar a floresta do Microsoft Entra ID com um locatário do Microsoft Entra

### Tempo estimado: 20 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: preparar os Serviços de Domínio Microsoft Entra para a sincronização de diretório
- Tarefa 2: instalar o Microsoft Entra Connect
- Tarefa 3: verificar a sincronização de diretório

#### Tarefa 1: preparar os Serviços de Domínio Microsoft Entra para a sincronização de diretório

Nessa tarefa, você se conectará à VM do Azure que executa o controlador de domínio dos Serviços de Domínio Microsoft Entra e criará uma conta de sincronização de diretório. 

   > Antes de iniciar essa tarefa, verifique se a implantação do modelo iniciada no primeiro exercício deste laboratório foi concluída.

1. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra associado à assinatura do Azure, na qual você implantou a VM do Azure no primeiro exercício deste laboratório.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior do portal da Azure, digite **Máquinas virtuais** e pressione a tecla **Enter**.

3. Na folha **Máquinas Virtuais**, clique na entrada **adVM**. 

4. Na folha **adVM**, clique em **Conectar** e, no menu suspenso, clique em **RDP**. 

5. Na lista suspensa **Endereço IP**, selecione **Endereço IP público do balanceador de carga**, clique em **Baixar Arquivo RDP** e use-o para se conectar à VM do Azure **adVM** por meio da Área de Trabalho Remota. Quando a autenticação for solicitada, forneça as seguintes credenciais:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 02 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Observação**: aguarde até que a sessão da Área de Trabalho Remota e o **Gerenciador do Servidor** sejam carregados.  

    >**Observação**: as etapas a seguir são executadas na sessão da Área de Trabalho Remota para a VM do Azure **adVM**.

    >**Observação**: se o **Endereço IP público do balanceador de carga** não estiver disponível na lista suspensa **Endereço IP** da folha RDP, na pesquisa do Portal do Azure por **Endereços IP públicos**, selecione **adPublicIP** e anote o endereço IP. Clique no botão Iniciar, digite **MSTSC** e pressione **Enter** para iniciar o cliente de Área de Trabalho Remota. Digite o endereço IP público do balanceador de carga na caixa de texto **Computador:** e clique em **Conectar**.

6. No **Gerenciador do Servidor**, clique em **Ferramentas** e, no menu suspenso, clique em **Centro Administrativo do Microsoft Entra ID**.

7. No **Centro de administração do Microsoft Entra**, clique em **adatum (local)** no painel **Tarefas**; no nome de domínio **adatum (local)**, clique em **Novo** e, no menu em cascata, clique em **Unidade Organizacional**.

8. Na janela **Criar Unidade Organizacional**, na caixa de texto **Nome**, digite **ToSync** e clique em **OK**.

9. Clique duas vezes na unidade organizacional **ToSync** recém-criada, de modo que seu conteúdo apareça no painel de detalhes do console do Centro Administrativo do Microsoft Entra ID. 

10. No painel **Tarefas**, na seção **ToSync**, clique em **Novo** e, no menu em cascata, clique em **Usuário**.

11. Na janela **Criar usuário**, crie uma nova conta de usuário com as seguintes configurações (deixe as outras com seus valores existentes) e clique em **OK**:
    
    |Configuração|Valor|
    |---|---|
    |Nome completo|**aduser1**|
    |Logon UPN do usuário|**aduser1**|
    |Logon SamAccountName do usuário|**aduser1**|
    |Senha e Confirmar Senha|**Use sua senha pessoal criada no Laboratório 02 > Exercício 1 > Tarefa 1 > Etapa 9.**|
    |Outras opções de senha|**A senha nunca expira**|


#### Tarefa 2: instalar o Microsoft Entra Connect

Nesta tarefa, você instalará o Microsoft Entra Connect na máquina virtual. 

1. Na sessão da Área de Trabalho Remota para **adVM**, use o Microsoft Edge para navegar até o portal do Azure em **https://portal.azure.com** e entre usando a conta de usuário **syncadmin** que você criou no exercício anterior. Quando solicitado, especifique o nome UPN completo e a senha que você registrou no exercício anterior.

2. No portal do Azure, a caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Entra ID** e pressione a tecla **Enter**.

3. No portal do Azure, na folha **Visão Geral do AdatumSync \|**, no painel de navegação esquerdo, em **Gerenciar**, clique em **Microsoft Entra Connect**.

4. Na folha **Introdução ao Microsoft Entra Connect\|**, clique em **Connect Sync** no painel de navegação esquerdo e, em seguida, clique no link **Baixar Microsoft Entra Connect**. Você será redirecionado para a página de download do **Microsoft Entra Connect**.

5. Na página de download do **Microsoft Entra Connect** , clique em **Download**.

6. Quando solicitado, clique em **Executar** para iniciar o assistente do **Microsoft Entra Connect**.

7. Na página **Bem-vindo ao Microsoft Entra Connect** do assistente do **Microsoft Entra Connect**, clique na caixa de seleção **Concordo com os termos da licença e o aviso de privacidade** e clique em **Continuar**.

8. Na página **Configurações Expressas** do assistente do **Microsoft Entra Connect**, clique na opção **Personalizar**.

9. Na página **Instalar componentes necessários**, deixe todas as opções de configuração opcionais desmarcadas e clique em **Instalar**.

10. Na página **Entrada do usuário**, verifique se somente a **Sincronização de Hash de Senha** está habilitada e clique em **Avançar**.

11. Na página **Conectar ao Microsoft Entra ID**, autentique-se usando as credenciais da conta de usuário **syncadmin** criada no exercício anterior e clique em **Avançar**. 

12. Na página **Conectar seus diretórios**, clique no botão **Adicionar diretório** à direita da entrada **adatum.com** da floresta.

13. Na janela da **conta de floresta do AD**, verifique se a opção **Criar nova conta do Microsoft Entra ID** está selecionada, especifique as seguintes credenciais e clique em **OK**:

    |Configuração|Valor|
    |---|---|
    |Nome do Usuário|**Aluno ADATUM\\**|
    |Senha|**Por favor, use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 2**|

14. De volta à página **Conectar seus diretórios**, verifique se a entrada **adatum.com** aparece como um diretório configurado e clique em **Avançar**

15. Na página de **configuração de entrada do Microsoft Entra ID**, observe o aviso informando que **Usuários não poderão entrar no Microsoft Entra ID com credenciais locais se o sufixo UPN não corresponder a um nome de domínio verificado**, habilite a caixa de seleção **Continuar sem correspondência de todos os sufixos UPN ao domínio verificado** e clique em **Avançar**.

    >**Observação**: como explicado anteriormente, isso é esperado, já que você não conseguiu verificar o domínio DNS personalizado do Microsoft Entra ID **adatum.com**.

16. Na página **Filtragem de domínio e UO**, clique na opção **Sincronizar domínios e UOs selecionados** e desmarque a caixa de seleção ao lado do nome de domínio **adatum.com**. Clique para expandir **adatum.com**, marque somente a caixa de seleção ao lado da UO **ToSync** e, em seguida, clique em **Avançar**.

17. Na página **Identificando seus usuários exclusivamente**, aceite as configurações padrão e clique em **Avançar**.

18. Na página **Filtrar usuários e dispositivos**, aceite as configurações padrão e clique em **Avançar**.

19. Na página **Recursos opcionais**, aceite as configurações padrão e, em seguida, clique em **Avançar**.

20. Na página **Pronto para configurar**, verifique se a caixa de seleção **Iniciar o processo de sincronização quando a configuração for concluída** está marcada e clique em **Instalar**.

    >**Observação**: a instalação deve levar cerca de 2 minutos.

21. Revise as informações na página **Configuração concluída** e clique em **Sair** para fechar a janela do **Microsoft Entra Connect**.


#### Tarefa 3: verificar a sincronização de diretório

Nesta tarefa, você verificará se a sincronização de diretórios está funcionando. 

1. Na sessão da Área de Trabalho Remota para **adVM**, na janela do Microsoft Edge que exibe o portal do Azure, navegue até a folha **Usuários - Todos os usuários (Pré-visualização)** do locatário do Adatum Lab AMicrosoft Entra ID.

2. Na folha **Usuários \| Todos os usuários**, observe que a lista de objetos de usuário inclui a conta **aduser1**. 

   >**Observação**: talvez seja necessário aguardar alguns minutos e selecionar **Atualizar** para que a conta de usuário **aduser1** apareça.

3. Clique na conta **aduser1** e selecione a guia **Propriedades**. Role para baixo até a seção **Local** e observe que o atributo **Sincronização local habilitada** está definido como **Sim**.

4. Na folha **aduser1**, na seção **Informações do Trabalho**, observe que o atributo **Departamento** não está definido.

5. Na sessão da Área de Trabalho Remota para **adVM**, alterne para o **centro de administração do Microsoft Entra**, selecione a entrada **aduser1** na lista de objetos na UO **ToSync** e, no painel **Tarefas**, na seção **aduser1**, selecione **Propriedades**.

6. Na janela **aduser1**, na seção **Organização**, na caixa de texto **Departamento**, digite **Vendas** e selecione **OK**.

7. Na sessão da Área de Trabalho Remota da **adVM**, inicie o **Windows PowerShell**.

8. No console **Administrador: Windows PowerShell**, execute o seguinte para iniciar a sincronização delta do Microsoft Entra Connect:

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

9. Alterne para a janela do Microsoft Edge que exibe a folha **aduser1**, atualize a página e observe que a propriedade Department está definida como Sales.

    >**Observação**: talvez seja necessário aguardar até três minutos e atualizar a página novamente se o atributo **Department** permanecer não definido.

> **Resultado**: após concluir este exercício, você terá preparado o Microsoft Entra Domain Services para sincronização de diretórios, instalado o Microsoft Entra Connect e verificado a sincronização de diretórios.


**Limpar recursos**

>**Observação**: comece desabilitando a sincronização do Microsoft Entra ID

1. Na sessão de Área de Trabalho Remota para **adVM**, inicie o Windows PowerShell como Administrador.

2. No console do Windows PowerShell, instale o módulo PowerShell do MsOnline executando o seguinte (quando solicitado, na caixa de diálogo É necessário provedor NuGet para continuar, digite **Sim** e pressione Enter.):

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

3. No console do Windows PowerShell, conecte-se ao locatário do AdatumSync Microsoft Entra executando o seguinte (quando solicitado, entre com as credenciais **syncadmin**):

    ```powershell
    Connect-MsolService
    ```

4. No console do Windows PowerShell, desabilite a sincronização do Microsoft Entra Connect executando o seguinte:

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

5. No console do Windows PowerShell, verifique se a operação teve êxito executando o seguinte:

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```
    >**Observação**: o resultado deve ser `False`. Se esse não for o caso, aguarde um minuto e execute novamente o comando.

    >**Observação**: em seguida, remova os recursos do Azure
6. Feche a sessão da Área de Trabalho Remota.

7. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra associado à assinatura do Azure na qual você implantou a VM do Azure **adVM**.

8. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. 

9. No menu suspenso no canto superior esquerdo do painel do Cloud Shell, selecione **PowerShell** e, quando solicitado, clique em **Confirmar**. 

10. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
11. Feche o painel do **Cloud Shell**.

    >**Observação**: por fim, remova o locatário do Microsoft Entra
    
    >**Nota 2**: excluir um locatário precisa ser um processo muito difícil, para que nunca ocorra acidentalmente ou maliciosamente.  Isso significa que remover o locatário como parte desse laboratório muitas vezes não funciona.  Embora tenhamos as etapas aqui para excluir o locatário, elas não são necessárias para a conclusão deste laboratório. Se você tiver a necessidade de remover um locatário no mundo real, há artigos em DOCS.Microsoft.com para ajudá-lo.

12. De volta ao portal do Azure, use o filtro **Diretório + assinatura** para alternar para o locatário do **AdatumSync** Microsoft Entra.

13. No portal do Azure, navegue até a folha **Usuários – Todos os usuários**, selecione a entrada que representa a conta de usuário **syncadmin**, na folha **syncadmin – Perfil** escolha **Excluir** e, quando solicitado a confirmar, selecione **Sim**.

14. Repita a mesma sequência de etapas para excluir a conta de usuário **aduser1** e a **Conta do Serviço de Sincronização de Diretórios Locais**.

15. Navegue até a folha **AdatumSync – Visão geral** do locatário do Microsoft Entra, selecione **Gerenciar locatários** e marque a caixa de seleção do diretório **AdatumSync**, escolha **Excluir**, na folha **Excluir locatário "AdatumSync"**, selecione o link **Obter permissão para excluir recursos do Azure**, na folha **Propriedades** do Microsoft Entra, defina **Gerenciamento de acesso para recursos do Azure** como **Sim** e selecione **Salvar**.

    >**Nota**: se você receber qualquer sinal de aviso durante a exclusão, como **Excluir todos os usuários**, exclua os usuários que você criou. Se o sinal de aviso disser **Excluir aplicativos do LinkedIn**, selecione a mensagem de aviso e confirme a exclusão dos aplicativos do LinkedIn. Todos os avisos precisam ser resolvidos para concluir a exclusão do locatário.

16. Saia do portal do Azure e entre novamente. 

17. Navegue de volta para a folha **Excluir locatário "AdatumSync"** e selecione **Excluir**.

> Para obter informações adicionais sobre essa tarefa, consulte [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)
