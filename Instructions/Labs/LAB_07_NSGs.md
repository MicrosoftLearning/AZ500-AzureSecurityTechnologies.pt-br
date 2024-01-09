---
lab:
  title: 07 – Grupos de segurança de rede e grupos de segurança de aplicativo
  module: Module 02 - Implement Platform Protection
---

# Laboratório 07: grupos de segurança de rede e grupos de segurança de aplicativos
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a implementar a infraestrutura de rede virtual da sua organização e testar para garantir que ela esteja funcionando corretamente. Especialmente:

- A organização tem dois grupos de servidores: Servidores Web e Servidores de Gerenciamento.
- Cada grupo de servidores deve estar em seu próprio Grupo de Segurança de Aplicativos. 
- Você deve ser capaz de usar RDP nos Servidores de Gerenciamento, mas não nos Servidores Web.
- Os servidores Web devem exibir a página da Web do IIS quando acessados da Internet. 
- As regras do grupo de segurança de rede devem ser usadas para controlar o acesso à rede. 

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório, você realizará os seguintes exercícios:

- Exercício 1 – Criar a infraestrutura de rede virtual
- Exercício 2: implantar máquinas virtuais e testar os filtros de rede

## Diagrama dos grupos de segurança de rede e de aplicativo

![imagem](https://user-images.githubusercontent.com/91347931/157526438-6da4f68b-db88-4931-a041-8474e66d3fe5.png)

## Instruções

### Exercício 1 – Criar a infraestrutura de rede virtual

### Tempo estimado: 20 minutos

> Para todos os recursos deste laboratório, estamos usando a região **Leste (EUA)**. Verifique com seu instrutor se esta é a região a ser usada para sua aula. 

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar uma rede virtual com uma sub-rede.
- Tarefa 2: criar dois grupos de segurança de aplicativo.
- Tarefa 3: criar um grupo de segurança de rede e associá-lo à sub-rede da rede virtual.
- Tarefa 4: criar regras de segurança NSG de entrada para todo o tráfego para servidores Web e RDP para os servidores de gerenciamento.

#### Tarefa 1: criar uma rede virtual

Nesta tarefa, você criará uma rede virtual para usar com os grupos de segurança de rede e aplicativo. 

1. Entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Redes virtuais** e pressione a tecla **Enter**.

3. No painel **Redes virtuais**, clique em **+ Criar**.

4. Na guia **Noções básicas** da folha **Criar rede virtual**, especifique as seguintes configurações (deixe outras com os valores padrão) e clique em **Avançar: endereços IP**:

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB07**|
    |Nome|**myVirtualNetwork**|
    |Região|**Leste dos EUA**|

5. Na guia **Endereços IP** do painel **Criar rede virtual**, defina o **espaço de endereço IPv4** como **10.0.0.0/16** e se necessário, na coluna **Nome da sub-rede**, clique em **padrão**, no painel **Editar sub-rede**, especifique as seguintes configurações e clique em **Salvar**:

    |Configuração|Valor|
    |---|---|
    |Nome da sub-rede|**default**|
    |Intervalo de endereços da sub-rede|**10.0.0.0/24**|

6. De volta à guia **Endereços IP** do painel **Criar rede virtual**, clique em **Examinar + criar**.

7. Na guia **Examinar + Criar** da folha **Criar rede virtual**, selecione **Criar**.

#### Tarefa 2: criar grupos de segurança do aplicativo

Nesta tarefa, você criará um grupo de segurança de aplicativo.

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de segurança do aplicativo** e pressione a tecla **Enter**.

2. No painel **Grupos de segurança do aplicativo**, clique em **+ Criar**.

3. Na guia **Noções básicas** do painel **Criar um grupo de segurança de aplicativo**, especifique as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |Nome|**myAsgWebServers**|
    |Região|**Leste dos EUA**|

    >**Observação**: este grupo será para os servidores web.

4. Clique em **Revisar + criar** e em **Criar**.

5. Navegue de volta para o painel **Grupos de segurança do aplicativo** e clique em **+ Criar**.

6. Na guia **Noções básicas** do painel **Criar um grupo de segurança de aplicativo**, especifique as seguintes configurações: 

    |Configuração|Valor|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |Nome|**myAsgMgmtServers**|
    |Região|**Leste dos EUA**|

    >**Observação**: este grupo será para os servidores de gerenciamento.

7. Clique em **Revisar + criar** e em **Criar**.

#### Tarefa 3: criar um grupo de segurança de rede e associar o NSG à sub-rede

Nesta tarefa, você criará um grupo de segurança de rede. 

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de segurança de rede** e pressione a tecla **Enter**.

2. No painel **Grupos de segurança de rede**, clique em **+ Criar**.

3. Na guia **Noções básicas**, da folha **Criar grupo de segurança de rede**, execute as seguintes ações: 

    |Configuração|Valor|
    |---|---|
    |Subscription|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB07**|
    |Nome|**myNsg**|
    |Região|**Leste dos EUA**|

4. Clique em **Revisar + criar** e em **Criar**.

5. No portal do Azure, navegue de volta para a folha **Grupos de segurança de rede** e clique na entrada **myNsg**.

6. No painel **myNsg**, na seção **Configurações**, clique em **Sub-redes** e em **+ Associar**. 

7. No painel **Associar sub-rede**, especifique as configurações a seguir e clique em **OK**:

    |Configuração|Valor|
    |---|---|
    |Rede virtual|**myVirtualNetwork**|
    |Sub-rede|**default**|

#### Tarefa 4: criar regras de segurança NSG de entrada para todo o tráfego para servidores Web e RDP para os servidores. 

1. No painel **myNsg**, na seção **Configurações**, clique em **Regras de segurança de entrada**.

2. Revise as regras de segurança de entrada padrão e clique em **+ Adicionar**.

3. No painel **Adicionar regra de segurança de entrada**, especifique as seguintes configurações para permitir as portas TCP 80 e 443 ao grupo de segurança do aplicativo **myAsgWebServers** (deixe todos os outros valores com o padrão): 

    |Configuração|Valor|
    |---|---|
    |Destino|na lista suspensa, selecione **Grupo de segurança do aplicativo** e clique em **myAsgWebServers**|
    |Intervalos de portas de destino|**80,443**|
    |Protocolo|**TCP**|
    |Prioridade|**100**|                                                    
    |Nome|**Allow-Web-All**|

4. Na folha **Adicionar regra de segurança de entrada**, clique em **Adicionar** para criar a nova regra de entrada. 

5. No painel **myNsg**, na seção **Configurações**, clique em **Regras de segurança de entrada** e em **+ Adicionar**.

6. No painel **Adicionar regra de segurança de entrada**, especifique as seguintes configurações para permitir a porta RDP (TCP 3389) para o grupo de segurança do aplicativo **myAsgMgmtServers** (deixe todos os outros valores com o padrão): 

    |Configuração|Valor|
    |---|---|
    |Destino|na lista suspensa, selecione **Grupo de segurança do aplicativo** e clique em **myAsgMgmtServers**|
    |Intervalos de portas de destino|**3389**|
    |Protocolo|**TCP**|
    |Prioridade|**110**|                                                    
    |Nome|**Allow-RDP-All**|

7. Na folha **Adicionar regra de segurança de entrada**, clique em **Adicionar** para criar a nova regra de entrada. 

> Resultado: você implantou uma rede virtual, segurança de rede com regras de segurança de entrada e dois grupos de segurança de aplicativo. 

### Exercício 2: implantar máquinas virtuais e testar filtros de rede

### Tempo estimado: 25 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar uma máquina virtual para usar como um servidor Web.
- Tarefa 2: criar uma máquina virtual para usar como um servidor de gerenciamento. 
- Tarefa 3: associar cada adaptador de rede das máquinas virtuais aos respectivos grupos de segurança de aplicativo.
- Tarefa 4: testar a filtragem de tráfego de rede.

#### Tarefa 1: criar uma máquina virtual para usar como um servidor Web.

Nesta tarefa, você criará uma máquina virtual para usar como um servidor Web.

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Máquinas virtuais** e pressione a tecla **Enter**.

2. No painel **Máquinas virtuais**, clique em **+ Criar** e, na lista suspensa, clique em **+ Máquina virtual do Azure**.

3. Na guia **Noções básicas** do painel **Criar uma máquina virtual**, especifique as seguintes configurações (deixe outras com os valores padrão):

   |Configuração|Valor|
   |---|---|
   |Subscription|o nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|**AZ500LAB07**|
   |Nome da máquina virtual|**myVmWeb**|
   |Region|**(EUA) Leste dos EUA**|
   |Imagem|**Windows Server 2022 Datacenter: Azure Edition- x64 Gen2**|
   |Tamanho|**Standard D2s v3**|
   |Nome de Usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
   |Confirmar senha|**Digite sua senha novamente**|
   |Porta de entrada públicas|**Nenhuma**|
   |Deseja usar uma licença existente do Windows Server |**Não**|

    >**Observação**: para portas de entrada públicas, dependeremos do NSG pré-criado. 

4. Clique em **Avançar: Discos >** e, na guia **Discos** do painel **Criar uma máquina virtual**, defina o **Tipo de disco do sistema operacional** para **HDD Standard** e clique em **Avançar: Rede >**.

5. Na guia **Rede** do painel **Criar uma máquina virtual**, selecione a rede **myVirtualNetwork** criada anteriormente.

6. Em **Grupo de segurança de rede da NIC**, selecione **Nenhum**.

7. Clique em **Avançar: Gerenciamento >** e clique em **Avançar: Monitoramento >**. Na guia **Monitoramento** do painel **Criar uma máquina virtual**, verifique a seguinte configuração:

   |Configuração|Valor|
   |---|---|
   |Diagnóstico de inicialização|**Habilitado com a conta de armazenamento gerenciada (recomendado)**|

8. Clique em **Revisar + criar**, no painel **Revisar + criar**, verifique se a validação foi bem-sucedida e clique em **Criar**.

#### Tarefa 2: criar uma máquina virtual para usar como um servidor de gerenciamento. 

Nesta tarefa, você criará uma máquina virtual para usar como um servidor de gerenciamento.

1. No portal do Azure, navegue de volta para a folha **Máquinas virtuais**, clique em **+ Criar** e, na lista suspensa, clique em **+ máquina virtual do Azure**.

2. Na guia **Noções básicas** do painel **Criar uma máquina virtual**, especifique as seguintes configurações (deixe outras com os valores padrão):

   |Configuração|Valor|
   |---|---|
   |Subscription|o nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|**AZ500LAB07**|
   |Nome da máquina virtual|**myVMMgmt**|
   |Region|(EUA) Leste dos EUA|
   |Imagem|**Windows Server 2022 Datacenter: Azure Edition - x64 Gen2**|
   |Tamanho|**Standard D2s v3**|
   |Nome de Usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|
   |Porta de entrada públicas|**Nenhuma**|
   |Já tem uma licença do Windows Server|**Não**|

    >**Observação**: para portas de entrada públicas, dependeremos do NSG pré-criado. 

3. Clique em **Avançar: Discos >** e, na guia **Discos** do painel **Criar uma máquina virtual**, defina o **Tipo de disco do sistema operacional** para **HDD Standard** e clique em **Avançar: Rede >**.

4. Na guia **Rede** do painel **Criar uma máquina virtual**, selecione a rede **myVirtualNetwork** criada anteriormente.

5. Em **Grupo de segurança de rede da NIC**, selecione **Nenhum**.

6. Clique em **Avançar: Gerenciamento >** e clique em **Avançar: Monitoramento >**. Na guia **Monitoramento** do painel **Criar uma máquina virtual**, verifique a seguinte configuração:

   |Configuração|Valor|
   |---|---|
   |Diagnóstico de inicialização|**Habilitado com a conta de armazenamento gerenciada (recomendado)**|

7. Clique em **Revisar + criar**, no painel **Revisar + criar**, verifique se a validação foi bem-sucedida e clique em **Criar**.

    >**Observação**: aguarde até que ambas as máquinas virtuais sejam provisionadas antes de continuar. 

#### Tarefa 3: associar cada adaptador de rede de máquinas virtuais ao seu grupo de segurança de aplicativo.

Nesta tarefa, você associará cada adaptador de rede de máquinas virtuais ao grupo de segurança de aplicativo correspondente. A interface da máquina virtual myVMWeb será associada ao ASG myAsgWebServers. A interface da máquina virtual myVMMgmt será associada ao ASG myAsgMgmtServers. 

1. No portal do Azure, navegue de volta para o painel **Máquinas virtuais** e verifique se ambas as máquinas virtuais estão listadas com o status **Em execução**.

2. Na lista de máquinas virtuais, clique na entrada **myVMWeb**.

3. No painel **myVMWeb**, na seção **Rede**, clique em **Configurações de rede** e, no painel ** myVMWeb\| Configurações de rede**, clique na guia ** Grupos de segurança de aplicativos**.

4. Clique em + **Adicionar grupos de segurança de aplicativos**, na lista **Grupo de segurança de aplicativos**, selecione **myAsgWebServers** e clique em **Salvar**.

5. Navegue de volta para o painel **Máquinas virtuais** e, na lista de máquinas virtuais, clique na entrada **myVMMgmt**.

6. No painel **myVMMgmt**, na seção **Rede**, clique em **Configurações de rede** e, no painel ** myVMMgmt\| Configurações de rede**, clique na guia **Grupos de segurança de aplicativos**.

7. Clique em + **Adicionar grupos de segurança de aplicativos**, na lista **Grupo de segurança de aplicativos**, selecione **myAsgMgmtServers** e clique em **Salvar**.

#### Tarefa 4: testar a filtragem de tráfego de rede

Nesta tarefa, você testará os filtros de tráfego de rede. Você deve poder executar RDP na máquina virtual myVMMgmnt. Você deve poder se conectar da Internet à máquina virtual myVMWeb e exibir a página da Web padrão do IIS.  

1. Navegue de volta até o painel da Máquina virtual **myVMMgmt**.

2. No painel **myVMMgmt**, clique em **Conectar** e, no menu suspenso, clique em **RDP**. 

3. Clique em **Baixar Arquivo RDP** e use-o para se conectar à VM do Azure **myVMMgmt** via Área de Trabalho Remota. Quando solicitado a autenticar, forneça as seguintes credenciais:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**Aluno**|
   |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >**Observação**: verifique se a conexão de Área de Trabalho Remota foi bem-sucedida. Neste ponto, você confirmou que pode se conectar via Área de Trabalho Remota ao myVMMgmt.

4. No portal do Azure, navegue até o painel de máquinas virtuais **myVMWeb**.

5. No painel **myVMWeb**, na seção **Payload**, clique em ** Executar comando** e, em seguida, clique em **RunPowerShellScript**.

6. No painel **Executar Script de Comando**, execute o seguinte para instalar a função de servidor Web em **myVmWeb**:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

    >**Observação**: aguarde a conclusão da instalação. Isso poderá levar alguns minutos. Nesse ponto, você pode verificar se myVMWeb pode ser acessado via HTTP/HTTPS.

7. No portal do Azure, navegue de volta o painel **myVMWeb**.

8. No painel **myVMWeb**, identifique o **endereço IP público** da VM do Azure myVmWeb.

9. Em outro navegador da Web, navegue até o endereço IP que você identificou na etapa anterior.

    >**Observação**: a página do navegador deve exibir a página de boas-vindas padrão do IIS porque a porta 80 é permitida de entrada da Internet, com base na configuração do grupo de segurança do aplicativo **myAsgWebServers**. O adaptador de rede da VM do Azure myVMWeb está associada a esse grupo de segurança de aplicativo. 

> Resultado: você validou que a configuração de NSG e ASG está funcionando e o tráfego está sendo gerenciado corretamente. 

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. A remoção de recursos não utilizados garante que não haverá custos inesperados. 

1. No portal do Azure, abra o Cloud Shell clicando no ícone no canto superior direito. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

2. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB07" -Force -AsJob
    ```

4.  Feche o painel do **Cloud Shell**. 
