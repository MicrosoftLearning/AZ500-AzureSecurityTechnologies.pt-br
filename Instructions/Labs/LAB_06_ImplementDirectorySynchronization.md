---
lab:
  title: 06 – Implementar a sincronização de diretório
  module: Module 01 - Manage Identity and Access
---

# Laboratório 06: Implementar a sincronização de diretório
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito demonstrando como integrar o ambiente local dos Serviços de Domínio do Microsoft Entra a um locatário do Microsoft Entra. Você precisa especificamente:

- Implementar uma floresta de domínio único dos Serviços de Domínio do Microsoft Entra implantando uma VM do Azure que hospeda um controlador de domínio dos Serviços de Domínio do Microsoft Entra
- Criar e configurar um locatário do Microsoft Entra
- Sincronizar a floresta dos Serviços de Domínio do Microsoft Entra com o locatário do Microsoft Entra

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório, você realizará os seguintes exercícios:

- Exercício 1: implantar uma VM do Azure que hospeda um controlador de domínio do Microsoft Entra ID
- Exercício 2: criar e configurar um locatário do Microsoft Entra
- Exercício 3: sincronizar a floresta do Microsoft Entra ID com um locatário do Microsoft Entra

## Implementar a sincronização de diretório

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/5d9cc4c7-7dcd-4d88-920d-9c97d5a482a2)

## Instruções

### Exercício 1: implantar uma VM do Azure que hospeda um controlador de domínio do Microsoft Entra ID

### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: identificar um nome DNS disponível para uma implantação de VM do Azure
- Tarefa 2: usar um modelo do ARM para implantar uma VM do Azure que hospeda um controlador de domínio do Microsoft Entra ID

#### Tarefa 1: identificar um nome DNS disponível para uma implantação de VM do Azure

Nesta tarefa, você identificará um nome DNS para sua implantação de VM do Azure. 

1. Entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, abra o Cloud Shell clicando no ícone no canto superior direito. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

3. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para identificar um nome DNS disponível que você pode usar para uma implantação de VM do Azure na próxima tarefa deste exercício:

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**Observação**: substitua o espaço reservado `<custom-label>` por um possível nome DNS válido que seja globalmente exclusivo. Substitua o espaço reservado `<location>` pelo nome da região na qual você deseja implantar a VM do Azure que hospedará o controlador de domínio do Active Directory que você usará neste laboratório.

    >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

5. Verifique se o comando retornou **True**. Caso contrário, execute novamente o mesmo comando com um valor diferente do `<custom-label>` até que o comando retorne **True**.

6. Registre o valor do `<custom-label>` que teve êxito. Você precisará dele na próxima tarefa.

7. Feche o Cloud Shell.

#### Tarefa 2: usar um modelo do ARM para implantar uma VM do Azure que hospeda um controlador de domínio do Microsoft Entra ID

Nesta tarefa, você implantará uma VM do Azure que hospedará um controlador de domínio do Microsoft Entra ID

1. Abra uma guia na mesma janela do navegador e vá para **https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain**.

2. Na página **Criar uma VM do Azure com uma nova Floresta do AD**, clique em **Implantar no Azure**. Isso redirecionará automaticamente o navegador para o painel **Criar uma VM do Azure com uma nova Floresta do AD** no portal do Azure. 

3. No painel **Criar uma VM do Azure com uma nova Floresta do AD**, clique em **Editar parâmetros**.

4. No painel **Editar parâmetros**, clique em **Carregar arquivo** na **caixa de diálogo aberta**, navegue até a pasta **\\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json**, clique em **Abrir** e em **Salvar**.

5. No painel **Criar uma VM do Azure com uma nova Floresta do AD**, especifique as seguintes configurações (deixe as outras com seus valores existentes):

   |Configuração|Valor|
   |---|---|
   |Subscription|o nome da sua assinatura do Azure|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB06**|
   |Region|a região do Azure que você identificou na tarefa anterior|
   |Nome de Usuário do Administrador|**Aluno**|
   |Senha do Administrador|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
   |Nome do Domínio|**adatum.com**|
   |Prefixo do DNS|o nome do host DNS que você identificou na tarefa anterior|
   |Tamanho da VM|**Standard_D2s_v3**|

6. No painel **Criar uma VM do Azure com uma nova Floresta do AD**, clique em **Revisar + criar** e clique em **Criar**.

    >**Observação**: não espere a conclusão da implantação, prossiga para o próximo exercício. A implantação levará cerca de 15 minutos. Você usará a máquina virtual implantada nesta tarefa no terceiro exercício deste laboratório.

> Resultado: depois de concluir esse exercício, você iniciou a implantação de uma VM do Azure que hospedará um controlador de domínio do Microsoft Entra ID usando um modelo do Azure Resource Manager


### Exercício 2: criar e configurar um locatário do Microsoft Entra 

### Tempo estimado: 20 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar um locatário do Microsoft Entra
- Tarefa 2: adicionar um nome DNS personalizado ao novo locatário do Microsoft Entra
- Tarefa 3: criar um usuário do Microsoft Entra ID com a função de Administrador Global

#### Tarefa 1: criar um locatário do Microsoft Entra

Nesta tarefa, você criará um locatário do Microsoft Entra para usar neste laboratório. 

1. No portal do Azure, use a caixa de texto **Pesquisar recursos, serviços e documentos** no início da página do portal do Azure, digite **Microsoft Entra ID** e pressione a tecla **Enter**.

2. No painel que exibe **Visão geral** do locatário atual do Microsoft Entra, clique em Gerenciar locatários** e, na próxima tela, clique em ****+ Criar**.

3. Na guia **Noções básicas** do painel **Criar um locatário**, verifique se a opção ** Microsoft Entra ID** está selecionada e clique em **Avançar: configuração >**.

4. Na guia **Configuração** do painel **Criar um diretório**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Nome da organização|**AdatumSync**|
   |Nome de domínio inicial|um nome exclusivo que consiste em uma combinação de letras e dígitos|
   |País ou Região|**Estados Unidos**|

    >**Observação**: registre o nome de domínio inicial. Você precisará dele posteriormente nesse laboratório.

    >**Observação**: a marca de seleção verde na caixa de texto **Nome de domínio inicial** indicará se o nome de domínio digitado é válido e exclusivo. (Registre seu nome de domínio inicial para usar posteriormente).

5. Clique em **Revisar + criar** e em **Criar**.

    >**Observação**: aguarde até que o novo locatário seja criado. Use o ícone **Notificação** para monitorar o status da implantação. 

#### Tarefa 2: adicionar um nome DNS personalizado ao novo locatário do Azure AD

Nessa tarefa, você adicionará seu nome DNS personalizado ao novo locatário do Azure AD. 

1. No portal do Azure, na barra de ferramentas, clique no ícone **Diretório + assinatura**, localizado à direita do ícone do Cloud Shell. 

2. No painel **Diretório + assinatura**, selecione a linha recém-criada **AdatumSync** do locatário e clique no botão **Alternar**.

    >**Observação**: atualize a janela do navegador se a entrada **AdatumSync** não aparecer na lista de filtros **Diretório + assinatura**.

3. No painel **AdatumSync \| Microsoft Entra ID**, na seção **Gerenciar**, clique em **Nomes de domínios personalizados**.

4. No painel **AdatumSync \| Nomes de domínios personalizados**, clique em **+ Adicionar domínio personalizado**.

5. No painel **Nome de domínio personalizado**, na caixa de texto **Nome de domínio personalizado**, digite **adatum.com** e clique em **Adicionar domínio**.

6. No painel **adatum.com**, examine as informações necessárias para executar a verificação do nome de domínio do Microsoft Entra e selecione **Excluir** duas vezes. 

    >**Observação**: você não poderá concluir o processo de validação porque não possui o nome de domínio DNS **adatum.com**. Isso não impedirá que você sincronize o domínio dos Serviços de Domínio do Microsoft Entra **adatum.com** com o locatário do Microsoft Entra. Você usará para essa finalidade o nome DNS inicial do locatário do Microsoft Entra (o nome que termina com o sufixo **onmicrosoft.com**), que você identificou na tarefa anterior. No entanto, lembre-se de que, como resultado, o nome de domínio DNS do domínio dos Serviços de Domínio Microsoft Entra e o nome DNS do locatário do Microsoft Entra serão diferentes. Isso significa que os usuários do Adatum precisarão usar nomes diferentes ao entrar no domínio dos Serviços de Domínio do Microsoft Entra e ao entrar no locatário do Microsoft Entra.

#### Tarefa 3: criar um usuário do Microsoft Entra ID com a função de Administrador Global

Nessa tarefa, você adicionará um novo usuário do Microsoft Entra ID e atribuirá a ele a função de Administrador Global. 

1. No painel de locatário do Microsoft Entra **AdatumSync**, na seção **Gerenciar**, clique em **Usuários**.

2. No painel **Usuários | Todos os usuários**, clique em **+ Novo usuário** e, em seguida, em **Criar novo usuário**.

3. No painel **Novo usuário**, verifique se a opção **Criar usuário** está selecionada e especifique as seguintes configurações na guia Noções Básicas (deixe as outras com os valores padrão) e clique em **Avançar: propriedades >**:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**syncadmin**|
   |Nome|**syncadmin**|
   |Senha|verifique se a opção **Gerar senha automaticamente** está selecionada e clique em **Mostrar senha**|

    >**Observação**: registre o nome de usuário completo. Copie o valor dele clicando no botão **Copiar para a área de transferência** no lado direito da lista suspensa que exibe o nome de domínio e colando-o em um documento do bloco de notas. Você precisará dele mais tarde neste laboratório.

    >**Observação**: registre a senha do usuário clicando no botão **Copiar para a área de transferência** no lado direito da caixa de texto de Senha e colando-a em um documento do bloco de notas. Você precisará dele mais tarde neste laboratório.

4. Na guia **Propriedades**, role até a parte inferior e especifique o Local de uso: **Estados Unidos** (deixe todos os outros com os valores padrão) e clique em **Avançar: atribuições >**.

5. Na guia **Atribuições**, clique em **+ Adicionar função**, procure e selecione **Administrador Global** e clique em **Selecionar**. Clique em **Revisar + criar** e em **Criar**.
   
    >**Observação**: é necessário um usuário do Azure AD com a função de Administrador Global para implementar o Microsoft Entra Connect.

6. Abra uma janela do navegador InPrivate.

7. Navegue até o portal do Azure em **`https://portal.azure.com/`** e entre usando a conta de usuário **syncadmin**. Quando solicitado, altere a senha registrada anteriormente nesta tarefa para usar sua própria senha que atenda aos requisitos de complexidade e registre-a para referência futura. Essa senha será solicitada em tarefas posteriores.

    >**Observação**: para entrar, você precisará fornecer um nome totalmente qualificado da conta de usuário **syncadmin**, incluindo o nome de domínio DNS do locatário do Microsoft Entra, que você registrou anteriormente nesta tarefa. Esse nome de usuário está no formato syncadmin@`<your_tenant_name>`.onmicrosoft.com, em que `<your_tenant_name>` é o espaço reservado que representa seu nome de locatário exclusivo do Microsoft Entra. 

8. Saia de **syncadmin** e feche a janela do navegador InPrivate.

> **Resultado**: ao final desse exercício, você terá criado um locatário do AMicrosoft Entra, aprendido a adicionar um nome DNS personalizado ao novo locatário do Microsoft Entra e criado um usuário do Azure AD com a função de Administrador Global.


### Exercício 3: sincronizar a floresta do Microsoft Entra ID com um locatário do Microsoft Entra

### Tempo estimado: 20 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: preparar os Serviços de Domínio do Microsoft Entra para a sincronização de diretório
- Tarefa 2: instalar o Microsoft Entra Connect
- Tarefa 3: verificar a sincronização de diretório

#### Tarefa 1: preparar os Serviços de Domínio do Microsoft Entra para a sincronização de diretório

Nessa tarefa, você se conectará à VM do Azure que executa o controlador de domínio dos Serviços de Domínio do Microsoft Entra e criará uma conta de sincronização de diretório. 

   > Antes de iniciar esta tarefa, verifique se a implantação de modelo iniciada no primeiro exercício deste laboratório foi concluída.

1. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra associado à assinatura do Azure na qual você implantou a VM do Azure no primeiro exercício deste laboratório.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Máquinas virtuais** e pressione a tecla **Enter**.

3. No painel **Máquinas virtuais**, clique na entrada **adVM**. 

4. No painel **adVM**, clique em **Conectar** e, no menu suspenso, clique em **RDP**. 

5. No menu suspenso **Endereço IP**, selecione **Endereço IP público do balanceador de carga** e clique em **Baixar Arquivo RDP** e use-o para se conectar à VM do Azure **adVM** por meio da Área de Trabalho Remota. Quando solicitado a autenticar, forneça as seguintes credenciais:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Observação**: aguarde até a sessão da Área de Trabalho Remota e o **Gerenciador do Servidor** serem carregados.  

    >**Observação**: as etapas a seguir são executadas na sessão de Área de Trabalho Remota para a VM do Azure **adVM**.

    >**Observação**: se o **endereço IP público do balanceador de carga** não estiver disponível no menu suspenso **Endereço IP** da folha do RDP, no Portal do Azure pesquise por **endereços IP públicos**, selecione **adPublicIP** e anote o endereço IP. Clique no botão Iniciar, digite **MSTSC** e pressione **Enter** para iniciar o cliente de área de trabalho remota. Digite o endereço IP público do balanceador de carga na caixa de texto **Computador:** e clique em **Conectar**.

6. No **Gerenciador do Servidor**, clique em **Ferramentas** e, no menu suspenso, clique em **Centro Administrativo do Microsoft Entra ID**.

7. No **Centro Administrativo do Microsoft Entra ID**, clique em **adatum (local)**, no painel **Tarefas**, no nome de domínio **adatum (local)** clique em **Novo** e, no menu em cascata, clique em **Unidade Organizacional**.

8. Na janela **Criar unidade organizacional**, na caixa de texto **Nome**, digite **ToSync** e clique em **OK**.

9. Clique duas vezes na unidade organizacional **ToSync** para que seu conteúdo apareça no painel de detalhes do console do Centro Administrativo do Microsoft Entra ID. 

10. No painel **Tarefas**, na seção **ToSync**, clique em **Novo** e, no menu em cascata, clique em **Usuário**.

11. Na janela **Criar usuário**, crie uma conta de usuário com as seguintes configurações (deixe outras com os valores existentes) e clique em **OK**:
    
    |Configuração|Valor|
    |---|---|
    |Nome completo|**aduser1**|
    |Logon UPN do usuário|**aduser1**|
    |Logon SamAccountName do usuário|**aduser1**|
    |Senha e Confirmar Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
    |Outras opções de senha|**A senha nunca expira**|


#### Tarefa 2: instalar o Microsoft Entra Connect

Nesta tarefa, você instalará o Microsoft Entra Connect na máquina virtual. 

1. Na sessão de Área de Trabalho Remota para **adVM**, use o Microsoft Edge para navegar até o portal do Azure em **https://portal.azure.com** e entre usando a conta de usuário **syncadmin** que você criou no exercício anterior. Quando solicitado, especifique o nome UPN e a senha completa do usuário que você registrou no exercício anterior.

2. No portal do Azure, use a caixa de texto **Pesquisar recursos, serviços e documentos** no início da página do portal do Azure, digite **Microsoft Entra ID** e pressione a tecla **Enter**.

3. No portal do Azure, no painel **AdatumSync \| Visão Geral**, no painel de navegação esquerdo, em **Gerenciar**, clique em **Microsoft Entra Connect**.

4. No painel **Microsoft Entra Connect \| Introdução**, clique em **Sincronização do Connect** no painel de navegação esquerdo e clique no link **Baixar o Microsoft Entra Connect**. Você será redirecionado para a página de download do **Microsoft Entra Connect**.

5. Na página de download do **Microsoft Entra Connect**, clique em **Download**.

6. Quando solicitado, clique em **Executar** para iniciar o assistente do **Microsoft Entra Connect**.

7. Na página **Bem-vindo ao Microsoft Entra Connect** do assistente do **Microsoft Entra Connect**, clique na caixa de seleção **Eu concordo com os termos de licença e aviso de privacidade** e clique em **Continuar**.

8. Na página **Configurações expressas** do assistente do **Microsoft Entra Connect**, clique na opção **Personalizar**.

9. Na página **Instalar componentes necessários**, deixe todas as opções de configuração opcionais desmarcadas e clique em **Instalar**.

10. Na página **Entrada do usuário**, verifique se somente a **Sincronização de hash de senha** está habilitada e clique em **Avançar**.

11. Na página **Conectar ao Microsoft Entra ID**, autentique usando as credenciais da conta de usuário **syncadmin** criada no exercício anterior e clique em **Avançar**. 

12. Na página **Conectar seus diretórios**, clique no botão **Adicionar Diretório** à direita da entrada da floresta **adatum.com**.

13. Na janela da **conta da floresta do AD**, verifique se a opção para **Criar uma conta do Microsoft Entra ID** está selecionada, especifique as seguintes credenciais e clique em **OK**:

    |Configuração|Valor|
    |---|---|
    |Nome do Usuário|**ADATUM\\Aluno**|
    |Senha|**Use sua senha pessoal criada no Laboratório 06 > Exercício 1 > Tarefa 2**|

14. De volta à página  **Conectar seus diretórios**, verifique se a entrada **adatum.com** aparece como um diretório configurado e clique em **Avançar**

15. Na página **Configuração de entrada do Microsoft Entra ID**, observe o aviso informando que os **Usuários não poderão entrar no Microsoft Entra ID com credenciais locais se o sufixo UPN não corresponder a um nome de domínio verificado**, habilite a caixa de seleção **Continuar sem corresponder todos os sufixos UPN ao domínio verificado** e clique em **Avançar**

    >**Observação**: como explicado anteriormente, isso é esperado, já que não foi possível verificar o domínio DNS personalizado do Microsoft Entra ID **adatum.com**.

16. Na página **Filtragem de domínio e UO**, clique na opção **Sincronizar domínios e UOs selecionados** e desmarque a caixa de seleção ao lado do nome de domínio **adatum.com**. Clique para expandir **adatum.com**, marque somente a caixa de seleção ao lado da UO **ToSync** e, em seguida, clique em **Avançar** .

17. Na página **Identificação exclusiva dos seus usuários**, aceite as configurações padrão e clique em **Avançar**.

18. Na página **Filtrar usuários e dispositivos**, aceite as configurações padrão e clique em **Avançar**.

19. Na página **Recursos opcionais**, aceite as configurações padrão e, em seguida, clique em **Avançar**.

20. Na página **Pronto para configurar**, verifique se a caixa de seleção **Iniciar o processo de sincronização quando a configuração for concluída** está marcada e clique em **Instalar**.

    >**Observação**: a instalação levará cerca de dois minutos.

21. Revise as informações na página **Configuração concluída** e clique em **Sair** para fechar a janela do **Microsoft Entra Connect**.


#### Tarefa 3: verificar a sincronização de diretório

Nesta tarefa, você verificará se a sincronização de diretório está funcionando. 

1. Na sessão de Área de Trabalho Remota para **adVM**, na janela do Microsoft Edge que exibe o portal do Azure, navegue até o painel **Usuários – Todos os usuários (Visualização)** do locatário do Microsoft Entra ID do Laboratório do Adatum.

2. No painel **Usuários \| Todos os usuários**, observe que a lista de objetos do usuário inclui a conta **aduser1**. 

   >**Observação**: talvez seja necessário aguardar alguns minutos e selecionar **Atualizar** até que a conta de usuário **aduser1** seja exibida.

3. Clique na conta **aduser1** e selecione a guia **Propriedades**. Role para baixo até a seção **Local**, observe que o atributo **Sincronização local habilitada** está definido como **Sim**.

4. No painel **aduser1**, na seção **Informações do trabalho**, observe que o atributo **Departamento** não está definido.

5. Na sessão da Área de Trabalho Remota para **adVM**, alterne para o **Centro Administrativo do Microsoft Entra ID**, selecione a entrada **aduser1** na lista de objetos na UO **ToSync** e no painel **Tarefas**, na seção **aduser1**, selecione **Propriedades**.

6. Na janela **aduser1**, na seção **Organização**, na caixa de texto **Departamento**, digite **Vendas** e selecione **OK**.

7. Na sessão da Área de Trabalho Remota da **adVM**, inicie o **Windows PowerShell**.

8. No console do **Administrador: Windows PowerShell**, execute o seguinte para iniciar a sincronização delta do Microsoft Entra Connect:

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

9. Alterne para a janela do Microsoft Edge que exibe o painel **aduser1**, atualize a página e observe que a propriedade Department está definida como Sales.

    >**Observação**: talvez seja necessário aguardar até três minutos e atualizar a página novamente se o atributo **Departamento** permanecer não definido.

> **Resultado**: ao concluir este exercício, você terá preparado os Microsoft Entra Domain Services para sincronização de diretório, instalado o Microsoft Entra Connect e verificado a sincronização de diretório.


**Limpar recursos**

>**Observação**: comece desabilitando a sincronização do Microsoft Entra ID

1. Na sessão de Área de Trabalho Remota para **adVM**, inicie o Windows PowerShell como Administrador.

2. No console do Windows PowerShell, instale o módulo do PowerShell do MSOnline executando o seguinte (quando solicitado, na caixa de diálogo provedor NuGet é necessário continuar, digite **Sim** e pressione Enter.):

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

3. No console do Windows PowerShell, conecte-se ao locatário do Microsoft Entra do AdatumSync executando o seguinte (quando solicitado, entre com as credenciais de **syncadmin**):

    ```powershell
    Connect-MsolService
    ```

4. No console do Windows PowerShell, desabilite a sincronização do Microsoft Entra Connect executando o seguinte:

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

5. No console do Windows PowerShell, verifique se a operação foi bem-sucedida executando o seguinte comando:

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```
    >**Observação**: o resultado deve ser `False`. Se esse não for o caso, aguarde um minuto e execute novamente o comando.

    >**Observação**: em seguida, remova os recursos do Azure
6. Feche a sessão da Área de Trabalho Remota.

7. No portal do Azure, defina o filtro **Diretório + assinatura** para o locatário do Microsoft Entra associado à assinaturado do Azure na qual você implantou a VM do Azure **adVM**.

8. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. 

9. No menu suspenso no canto superior esquerdo do painel do Cloud Shell, selecione **PowerShell** e, quando solicitado, clique em **Confirmar**. 

10. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
11. Feche o painel do **Cloud Shell**.

    >**Observação**: por fim, remova o locatário do Microsoft Entra
    
    >**Observação 2**: excluir um locatário é um processo muito difícil, por isso nunca ocorrerá de forma acidental ou mal-intencionada.  Isso significa que remover o locatário como parte desse laboratório muitas vezes não funciona.  Embora tenhamos as etapas aqui para excluir o locatário, não é necessário considerar que você concluíu este laboratório. Se você tiver a necessidade de remover um locatário no mundo real, há alguns artigos sobre DOCS.Microsoft.com para ajudar você.

12. De volta ao portal do Azure, use o filtro **Diretório + assinatura** para alternar para o locatário **AdatumSync** do Microsoft Entra.

13. No portal do Azure, navegue até o painel **Usuários – Todos os usuários**, clique na entrada que representa a conta de usuário **syncadmin**, na folha **syncadmin – Perfil** clique em **Excluir** e, quando solicitado a confirmar, clique em **Sim**.

14. Repita a mesma sequência de etapas para excluir a conta de usuário **aduser1** e a **Conta de serviço de sincronização de diretório local**.

15. Navegue até o painel **AdatumSync – Visão geral** do locatário do Microsoft Entra, clique em **Gerenciar locatários** e marque a caixa de seleção do diretório **AdatumSync**, clique em **Excluir**, na folha **Excluir locatário "AdatumSync"**, clique no link **Obter permissão para excluir recursos do Azure**, na folha **Propriedades** do AMicrosoft Entra, defina **Gerenciamento de acesso para recursos do Azure** como **Sim** e clique em **Salvar**.

    >**Observação**: ao excluir, caso você receba qualquer sinal de aviso como **Excluir todos os usuários**, exclua os usuários que você criou ou, caso o sinal de aviso indique **Excluir aplicativos do LinkedIn**, clique na mensagem de aviso e confirme a exclusão do aplicativo LinkedIn, todo o aviso precisa ser endereçado para passar a exclusão do locatário.

16. Saia do portal do Azure e entre novamente. 

17. Navegue de volta para a folha **Excluir o locatário "AdatumSync"** e clique em **Excluir**.

> Para obter informações adicionais sobre essa tarefa, confira [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)
