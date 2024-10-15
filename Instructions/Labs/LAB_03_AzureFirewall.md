---
lab:
  title: "03 - Firewall\_do Azure"
  module: Module 02 - Plan and implement security for public access to Azure resources
---

# Laboratório 03: Firewall do Azure
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a instalar o Firewall do Azure. Isso ajuda sua organização a controlar o acesso à rede de saída e de entrada, o que é uma parte importante de um plano de segurança de rede geral. Especificamente, você gostaria de criar e testar os seguintes componentes de infraestrutura:

- Uma rede virtual com uma sub-rede de carga de trabalho e uma sub-rede de jump host.
- Uma máquina virtual em cada sub-rede. 
- Uma rota personalizada que garante todo o tráfego de carga de trabalho de saída da sub-rede de carga de trabalho deve usar o firewall.
- Regras de aplicativo de firewall que só permitem tráfego de saída para www.bing.com. 
- Regras de rede de firewall que permitem pesquisas de servidor DNS externo.

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório você realizará os seguintes exercícios:

- Exercício 1: implantar e testar um Firewall do Azure

## Diagrama do Firewall do Azure

![imagem](https://user-images.githubusercontent.com/91347931/157529954-a1bc434b-2eca-41c1-b875-1f0c977d5e20.png)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\08\\template.json**

### Exercício 1: implantar e testar um Firewall do Azure

### Tempo estimado: 40 minutos

> Para todos os recursos deste laboratório, estamos usando a região **Leste (EUA)**. Verifique com seu instrutor se esta é a região a ser usada para sua aula. 

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: usar um modelo para implantar o ambiente de laboratório. 
- Tarefa 2: implantar um Firewall do Azure.
- Tarefa 3: criar uma rota padrão.
- Tarefa 4: configurar uma regra de aplicativo.
- Tarefa 5: configurar uma regra de rede. 
- Tarefa 6: configurar servidores DNS.
- Tarefa 7: testar o firewall. 

#### Tarefa 1: usar um modelo para implantar o ambiente de laboratório. 

Nesta tarefa, você revisará e implantará o ambiente do laboratório. 

Nesta tarefa, você criará uma máquina virtual usando um modelo do ARM. Esta máquina virtual será usada no último exercício deste laboratório. 

1. Entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. Na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Implantar um modelo personalizado** e pressione a tecla **Enter**.

3. No painel **Implantação personalizada**, clique na opção **Criar seu próprio modelo no editor**.

4. No painel **Editar modelo**, clique em **Carregar arquivo**, encontre o arquivo **\\Allfiles\\Labs\\08\\template.json** e clique em **Abrir**.

    >**Observação**: revise o conteúdo do modelo e observe que ele implanta uma VM do Azure que hospeda o datacenter do Windows Server 2016.

5. No painel **Editar modelo**, clique em **Salvar**.

6. No painel **Implantação personalizada**, verifique se as seguintes configurações estão definidas (deixe outras com os valores padrão):

   |Configuração|Valor|
   |---|---|
   |Assinatura|o nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB08**|
   |Location|**(EUA) Leste dos EUA**|
   |adminPassword|Uma senha segura de sua própria escolha para as máquinas virtuais. Lembre-se da senha. Você precisará dele mais tarde para se conectar às VMs.|

    >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

7. Clique em **Revisar + criar** e em **Criar**.

    >**Observação**: aguarde até que a implantação seja concluída. Isso deverá levar cerca de dois minutos. 

#### Tarefa 2: implantar o firewall do Azure

Nesta tarefa, você implantará o Firewall do Azure na rede virtual. 

1. No portal do Azure, na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Firewalls** e pressione a tecla **Enter**.

2. No painel **Firewall**, clique em **+ Criar**.

3. Na guia **Noções básicas** do painel **Criar um firewall**, especifique as seguintes configurações (deixe outras com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Grupo de recursos|**AZ500LAB08**|
   |Nome|**Test-FW01**|
   |Região|**(EUA) Leste dos EUA**|
   |SKU do Firewall|**Standard**|
   |Gerenciamento do firewall|**Use regras de Firewall (clássicas) para gerenciar este firewall**|
   |Escolher uma rede virtual|clique na opção **Usar existente** e, na lista suspensa, selecione **Test-FW-VN**|
   |Endereço IP público|clique em **Adicionar novo** e digite o nome **TEST-FW-PIP** e clique em **OK**|

4. Clique em **Revisar + criar** e em **Criar**. 

    >**Observação**: aguarde até que a implantação seja concluída. Isso deverá levar cerca de cinco minutos. 

5. No portal do Azure, na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de recursos** e pressione a tecla **Enter**.

6. No painel **Grupos de recursos**, na lista de grupos de recursos, clique na entrada **AZ500LAB08**.

    >**Observação**: no painel do grupo de recursos **AZ500LAB08**, revise a lista de recursos. Você pode classificar por **Tipo**.

7. Na lista de recursos, clique na entrada que representa o firewall **Test-FW01**.

8. No painel **Test-FW01**, identifique o endereço **IP Privado** que foi atribuído ao firewall. 

    >**Observação:** essas informações serão necessárias na próxima tarefa.


#### Tarefa 3: criar uma rota padrão

Nessa tarefa, você criará uma rota padrão para a sub-rede **Workload-SN**. Essa rota configurará o tráfego de saída por meio do firewall.

1. No portal do Azure, na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Tabelas de rotas** e pressione a tecla **Enter**.

2. No painel **Tabelas de rotas**, clique em **+ Criar**.

3. Na folha **Criar tabela de rotas**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Grupo de recursos|**AZ500LAB08**|
   |Região| **Leste dos EUA**|
   |Nome|**Firewall-route**|

4. Clique em **Revisar + criar**, clique em **Criar** e aguarde a conclusão do provisionamento. 

5. No painel **Tabelas de rotas**, clique em **Atualizar** e, na lista de tabelas de rotas, clique na entrada **Firewall-route**.

6. No painel **Firewall-route**, na seção **Configurações**, clique em **Sub-redes** e, em seguida, no painel **Firewall-route \| Sub-redes**, clique em **+ Associar**.

7. Na folha **Associar sub-rede**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Rede virtual|**Test-FW-VN**|
   |Sub-rede|**Workload-SN**|

    >**Observação**: verifique se a sub-rede **Workload SN** está selecionada para essa rota, caso contrário, o firewall não funcionará corretamente.

8. Clique em **OK** para associar o firewall à sub-rede da rede virtual. 

9. De volta ao painel **Firewall-route**, na seção **Configurações**, clique em **Rotas** e, em seguida, em **+ Adicionar**. 

10. No painel **Adicionar rota**, especifique as seguintes configurações:  

   |Configuração|Valor|
   |---|---|
   |Nome da rota|**FW-DG**|
   |Prefixo de endereço de destino|**Endereço IP**|
   |Intervalos de CIDR /endereço IP de destino|**0.0.0.0/0**
   |Tipo do próximo salto|**Solução de virtualização**|
   |Endereço do próximo salto|o endereço IP privado do firewall que você identificou na tarefa anterior|

    >**Observação**: o Firewall do Azure é, na verdade, um serviço gerenciado, mas a solução de virtualização funciona nessa situação.
    
11.  Clique em **Adicionar** para adicionar a rota. 


#### Tarefa 4: configurar uma regra de aplicativo

Nessa tarefa, você criará uma regra de aplicativo que permite o acesso de saída a `www.bing.com`.

1. No portal do Azure, navegue de volta para o firewall **Test-FW01**.

2. No painel **Test-FW01**, na seção **Configurações**, clique em **Regras (clássico)**.

3. No painel **Test-FW01 \| Regras (clássico)**, clique na guia **Coleção de regras de aplicativo** e em **+ Adicionar coleção de regras de aplicativo**.

4. No painel **Adicionar coleção de regras de aplicativo**, especifique as seguintes configurações (deixe outras com os valores padrão):

   |Configuração|Valor|
   |---|---|
   |Nome|**App-Coll01**|
   |Prioridade|**200**|
   |Ação|**Permitir**|

5. No painel **Adicionar coleção de regras de aplicativo**, crie uma entrada na seção **FQDNs de destino** com as seguintes configurações (deixe outras com os valores padrão):

   |Configuração|Valor|
   |---|---|
   |name|**AllowGH**|
   |Tipo de origem|**Endereço IP**|
   |Origem|**10.0.2.0/24**|
   |Porta do protocolo|**http:80, https:443**|
   |FQDNs de destino|**www.bing.com**|

6. Clique em **Adicionar** para adicionar a regra de aplicativo baseada em FQDNs de destino.

    >**Observação**: o Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. 

#### Tarefa 5: configurar uma regra de rede

Nessa tarefa, você criará uma regra de rede que permite o acesso de saída a dois endereços IP na porta 53 (DNS).

1. No portal do Azure, navegue de volta para o painel **Test-FW01 \| Regras (clássico).**

2. No painel **Test-FW01 \| Regras (clássico),** clique na guia **Coleção de regras de rede** e clique em **+ Adicionar coleção de regras de rede**.

3. Na folha **Adicionar coleção de regras de rede**, especifique as seguintes configurações (deixe outras com os valores padrão):

   |Configuração|Valor|
   |---|---|
   |Nome|**Net-Coll01**|
   |Prioridade|**200**|
   |Ação|**Permitir**|

4. No painel **Adicionar coleção de regras de rede**, crie uma nova entrada na seção **Endereços IP** com as seguintes configurações (deixe outras com os valores padrão):

   |Configuração|Valor|
   |---|---|
   |Nome|**AllowDNS**|
   |Protocolo|**UDP**|
   |Tipo de origem|**Endereço IP**|
   |Endereços de Origem|**10.0.2.0/24**|
   |Tipo de destino|**Endereço IP**|
   |Endereço de destino|**209.244.0.3,209.244.0.4**|
   |Portas de Destino|**53**|

5. Clique em **Adicionar** para adicionar a regra de rede.

    >**Observação**: os endereços de destino usados nesse caso são servidores DNS públicos conhecidos. 

#### Tarefa 6: configurar os servidores DNS da máquina virtual

Nesta tarefa, você configurará os endereços DNS primário e secundário para a máquina virtual. Isso não é um requisito do firewall. 

1. No portal do Azure, navegue de volta para o grupo de recursos **AZ500LAB08**.

2. No painel **AZ500LAB08**, na lista de recursos, clique na máquina virtual **Srv-Work**.

3. Na folha **Srv-Work**, clique em **Rede**.

4. No painel **Srv-Work \| Rede**, clique no link ao lado da entrada **Adaptador de rede**.

5. No painel da interface de rede, na seção **Configurações**, clique em **Servidores de DNS**, selecione a opção **Personalizado**, adicione os dois servidores DNS mencionados na regra de rede: **209.244.0.3** e ** 209.244.0.4** e clique em **Salvar** para salvar a alteração.

6. Retorne à página da máquina virtual **Srv-Work**.

    >**Observação**: aguarde a conclusão da atualização.

    >**Observação**: a atualização dos servidores DNS para um adaptador de rede reiniciará automaticamente a máquina virtual à qual essa interface está conectada e, se aplicável, quaisquer outras máquinas virtuais no mesmo conjunto de disponibilidade.

#### Tarefa 7: testar o firewall

Nessa tarefa, você testará o firewall para confirmar se ele está funcionando conforme o esperado.

1. No portal do Azure, navegue de volta para o grupo de recursos **AZ500LAB08**.

2. No painel **AZ500LAB08**, na lista de recursos, clique na máquina virtual **Srv-Jump**.

3. No painel **Srv-Jump**, clique em **Conectar** e, no menu suspenso, clique em **RDP**. 

4. Clique em **Baixar Arquivo RDP** e use-o para se conectar à VM do Azure **Srv-Jump** por meio da Área de Trabalho Remota. Quando solicitado a autenticar, forneça as seguintes credenciais:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**localadmin**|
   |Senha|A senha segura escolhida durante a implantação do modelo personalizado na tarefa 1 etapa 6.|

    >**Observação**: as etapas a seguir são executadas na sessão de Área de Trabalho Remota para a VM do Azure **Srv-Jump**. 

    >**Observação**: você se conectará à máquina virtual **Srv-Work**. Isso está sendo feito para que possamos testar a capacidade de acessar o site bing.com.  

5. Na sessão de Área de Trabalho Remota para **Srv-Jump**, clique com o botão direito do mouse em **Iniciar**, no menu do botão direito do mouse, clique em **Executar** e, na caixa de diálogo **Executar**, execute o seguinte para se conectar ao **Srv-Work**. 

    ```
    mstsc /v:Srv-Work
    ```

6. Quando a autenticação for solicitada, forneça esta credenciais do Azure:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|**localadmin**|
   |Senha|A senha segura escolhida durante a implantação do modelo personalizado na tarefa 1 etapa 6.|

    >**Observação**: aguarde até que a sessão de Área de Trabalho Remota seja estabelecida e a interface do Gerenciador do Servidor seja carregada.

7. Na sessão de Área de Trabalho Remota para **Srv-Work**, no **Gerenciador do Servidor**, clique em **Servidor local** e, em seguida, em **Configuração de Segurança Aprimorada do IE**.

8. Na caixa de diálogo **Configuração de Segurança Aprimorada do IE**, defina as duas opções como **Desativado** e clique em **OK.**

9. Na sessão de Área de Trabalho Remota para **Srv-Work**, inicie o Internet Explorer e navegue até **`https://www.bing.com`**. 

    >**Observação**: o site deverá ser exibido com êxito. O firewall permite o acesso.

10. Navegue para **`http://www.microsoft.com/`**

    >**Observação**: na página do navegador, você receberá uma mensagem com um texto semelhante a este: `HTTP request from 10.0.2.4:xxxxx to microsoft.com:80. Action: Deny. No rule matched. Proceeding with default action.` Isso é esperado, uma vez que o firewall bloqueia o acesso a este site. 

11. Encerre ambas as sessões da Área de Trabalho Remota.

> Resultado: você configurou e testou com êxito o Firewall do Azure.

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. A remoção de recursos não utilizados garante que não haverá custos inesperados. 

1. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

2. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB08" -Force -AsJob
    ```
4. Feche o painel do **Cloud Shell**. 
