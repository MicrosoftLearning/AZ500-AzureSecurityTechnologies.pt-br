---
lab:
  title: '00:55 Firewall do Azure'
  module: Module 02 - Implement Platform Protection
---

# Laboratório&#58; Firewall do Azure
# Manual do aluno

## Cenário do laboratório

Você foi solicitado a instalar o Firewall do Azure. Controlar o acesso à rede de saída e de entrada é uma parte importante de um plano de segurança de rede geral. Especificamente, você gostaria de criar e testar os seguintes componentes de infraestrutura:

- Uma rede virtual com uma sub-rede de carga de trabalho e uma sub-rede de host de salto.
- Uma máquina virtual é cada sub-rede. 
- Uma rota personalizada que garante todo o tráfego de carga de trabalho de saída da sub-rede de carga de trabalho deve usar o firewall.
- Regras de aplicativo de firewall que só permitem tráfego de saída para www.bing.com. 
- Regras de rede de firewall que permitem pesquisas de servidor DNS externo.

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Exercício 1: Implantar e testar um Firewall do Azure

## Diagrama do Firewall do Azure.

![imagem](https://user-images.githubusercontent.com/91347931/157529954-a1bc434b-2eca-41c1-b875-1f0c977d5e20.png)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\08\\template.json**

### Exercício 1: Implantar e testar um Firewall do Azure

### Tempo estimado: 40 minutos

> Para todos os recursos deste laboratório, estamos usando a **região Leste (EUA).** Verifique com seu instrutor se esta é a região a ser usada para sua aula. 

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: Use um modelo para implantar o ambiente de laboratório. 
- Implantar um Firewall do Azure
- Tarefa 4: Criar uma rota padrão
- Tarefa 5: Configurar uma regra de aplicativo
- Tarefa 6: Configurar uma regra de rede 
- Tarefa 6: Configurar servidores DNS.
- Tarefa 9: Testar o firewall 

#### Tarefa 1: Use um modelo para implantar o ambiente de laboratório. 

Nesta tarefa, você revisará e implantará o ambiente de laboratório. 

Nesta tarefa, você criará uma máquina virtual usando um modelo ARM. Esta máquina virtual será usada no último exercício para este laboratório. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Implantar um modelo** personalizado e pressione a **tecla Enter**.

3. Na **página Implantação personalizada**, clique em **Criar seu modelo no editor**.

4. **Na folha Editar modelo**, clique em Carregar arquivo, localize o\\** arquivo** template.json** do Allfiles\\Labs\\08\\e clique em **** Abrir**.

    >**Observação**: revise o conteúdo do modelo e observe que ele implanta uma VM do Azure que hospeda o Windows Server 2016 Datacenter.

5. Na folha Editar **modelo** , clique em **Salvar**.

6. **Na folha Implantação** personalizada, verifique se as seguintes configurações estão definidas (deixe outras com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Subscription|O nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB08**|
   |Location|**(EUA) Leste dos EUA**|

    >**Observação**: para identificar as regiões do Azure onde você pode provisionar VMs do Azure, consulte [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

7. Clique em **Revisar + criar** e em **Criar**.

    >Aguarde até que a implantação seja concluída. Isso deverá levar cerca de dois minutos. 

#### Tarefa 2: Implantar o firewall do Azure

Nesta tarefa, você implantará o firewall na rede virtual com uma política de firewall configurada. 

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Firewalls** e pressione a **tecla Enter**.

2. Na página **Firewall**, clique em **Criar**.

3. Na guia **Noções básicas** da folha **Criar máquina virtual**, defina as seguintes configurações (deixe outras com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Resource group|**AZ500LAB08**|
   |Nome|**Test-FW01**|
   |Região|**(EUA) Leste dos EUA**|
   |SKU do Firewall|**Padrão**|
   |Gerenciamento do firewall|**Use regras de Firewall (clássicas) para gerenciar este firewall**|
   |Escolher uma rede virtual|clique na opção Usar existente** e, na **lista suspensa, selecione **Test-FW-VN**|
   |Endereço IP público|clck **Adicionar novo** e digite o nome **TEST-FW-PIP** e clique em **OK**|

4. Clique em **Revisar + criar** e em **Criar**. 

    >Aguarde até que a implantação seja concluída. Isso deverá levar cerca de cinco minutos. 

5. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Grupos** de recursos e pressione a **tecla Enter**.

6. **Na folha Grupos de recursos, na lista de grupos de recursos**, clique na **entrada AZ500LAB08**.

    >**Nota**: Na folha AZ500LAB08 **** grupo de recursos, revise a lista de recursos. Você pode classificar por **tipo**.

7. Na lista de recursos, clique na entrada que representa o **firewall Test-FW01** .

8. **Na folha Test-FW01**, identifique o **endereço IP** privado que foi atribuído ao firewall. 

    >Essas informações serão necessárias na próxima etapa.


#### Tarefa 4: Criar uma rota padrão

Nesta tarefa, você criará uma rota padrão para a **sub-rede Workload-SN** . Essa rota configurará o tráfego de saída através do firewall.

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Tabelas** de rotas e pressione a **tecla Enter**.

2. Na página **Tabela de rotas**, clique em **Criar**.

3. **Na folha Criar tabela** de rotas, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Resource group|**AZ500LAB08**|
   |Region| **Leste dos EUA**|
   |Nome|**Firewall-route**|

4. Clique em **Revisar + criar**, clique em **Criar** e aguarde a conclusão do provisionamento. 

5. **Na folha Tabelas** de rotas, clique em Atualizar** e, na lista de tabelas de rotas, clique **na **entrada Rota** do firewall.

6. Na folha Rota do **firewall, na **seção Configurações**, clique em Sub-redes e, na folha Sub-redes**** da rota \| do firewall, clique em ******+ Associar.****

7. **Na folha Associar sub-rede**, especifique as seguintes configurações:

   |Configuração|Valor|
   |---|---|
   |Rede virtual|**Test-FW-VN**|
   |Sub-rede|**Workload-SN**|

    >Não deixe de selecionar apenas a sub-rede Workload-SN para essa rota, caso contrário, o firewall não funcionará corretamente.

8. Associar a sub-rede à rede virtual 

9. De volta à **folha Rota** do firewall, na **seção Configurações**, clique em Rotas** e, em seguida, clique em ****+ Adicionar**. 

10. Na página **Adicionar pool de nós**, especifique as seguintes configurações:  

   |Configuração|Valor|
   |---|---|
   |Nome da rota|fw-dg|
   |Prefixo de endereço de destino|**Endereço IP**|
   |Intervalos de CIDR /endereço IP de destino|**0.0.0.0/0**
   |Tipo do próximo salto|**Solução de virtualização**|
   |Endereço do próximo salto|o endereço IP privado do firewall que você identificou na tarefa anterior|

    >O Firewall do Azure é, na verdade, um serviço gerenciado, mas a solução de virtualização funciona nessa situação.
    
11.  Clique em **+ Adicionar** para adicionar a primeira rota. 


#### Tarefa 5: Configurar uma regra de aplicativo

Nesta tarefa, você adicionará uma regra de aplicativo que permite o acesso de saída a www.google.com.

1. No portal do Azure, navegue de volta para o **firewall Test-FW01** .

2. **Na folha Test-FW01**, na **seção Configurações**, clique em **Regras (clássico).**

3. **Na folha Test-FW01 \| Rules (classic),** clique na guia Application rule collection (Coleção de regras de **aplicativo e, em seguida, clique em **+ Add application rule collection****).

4. Na folha Adicionar coleção** de regras de **aplicativo, especifique as seguintes configurações (deixe outras pessoas com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Nome|**App-Coll01**|
   |Prioridade|**200**|
   |Ação|**Permitir**|

5. **Na folha Adicionar coleção** de regras de aplicativo, crie uma nova entrada na **seção FQDNs** de destino com as seguintes configurações (deixe outras pessoas com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |name|**PermitirGH**|
   |Tipo de origem|**Endereço IP**|
   |Origem|**10.0.2.0/24**|
   |Protocolo/Porta|http:80,https:443|
   |FQDNs de destino|**www.bing.com**|

6. Clique em **Adicionar** para adicionar a regra de aplicativo baseada em FQDNs de destino.

    >O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. 

#### Tarefa 6: Configurar uma regra de rede

Nesta tarefa, você adicionará uma regra de rede que permite o acesso de saída a dois endereços IP na porta 53 (DNS).

1. No portal do Azure, navegue de volta para a **folha Test-FW01 \| Rules (classic).**

2. **Na folha Test-FW01 \| Rules (classic),** clique na guia Coleção** de regras de rede e clique em **+ Adicionar coleção** de **regras de rede.

3. Na folha Novo Contêiner, especifique as seguintes configurações (deixe as demais com seus valores padrão) e selecione OK:

   |Configuração|Valor|
   |---|---|
   |Nome|**Net-Coll01**|
   |Prioridade|**200**|
   |Ação|**Permitir**|

4. **Na folha Adicionar coleção** de regras de rede, crie uma nova entrada na **seção Endereços IP** com as seguintes configurações (deixe outras pessoas com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Nome|**AllowDns**|
   |Protocolo|**UDP**|
   |Tipo de origem|**Endereço IP**|
   |Endereços de Origem|**10.0.2.0/24**|
   |Tipo de destino|**Endereço IP**|
   |Endereço de destino|**209.244.0.3,209.244.0.4**|
   |Portas de Destino|**53**|

5. Clique em **Adicionar** para adicionar a regra.

    >**Nota**: Os endereços de destino usados nesse caso são servidores DNS públicos conhecidos. 

#### Tarefa 6: Configurar os servidores DNS da máquina virtual

Nesta tarefa, você configurará os endereços DNS primário e secundário para a máquina virtual. Isso não é um requisito geral do Firewall do Azure. 

1. Volte para o grupo de recursos no **portal do Azure**.

2. Na folha AZ500LAB08****, na lista de recursos, clique na **máquina virtual Srv-Work**.

3. **Na folha Srv-Work**, na **seção Configurações**, clique em **Rede**.

4. **Na folha Rede de Trabalho** Srv, clique no link ao lado da entrada Interface **de \| rede**.

5. Na folha da interface de rede, na seção Configurações, clique em Servidores** DNS, selecione a **opção Personalizado, adicione os dois servidores DNS mencionados na **regra de rede: **209.244.0.3** e 209.244.0.4** e **clique em ****Salvar**** para salvar a alteração.**

6. Retorne à página da **máquina virtual Srv-Work** .

    >Aguarde a conclusão da atualização

    >**Nota**: A atualização dos servidores DNS para uma interface de rede reiniciará automaticamente a máquina virtual à qual essa interface está conectada e, se aplicável, quaisquer outras máquinas virtuais no mesmo conjunto de disponibilidade.

#### Tarefa 9: Testar o firewall

Nesta tarefa, você testará a regra de rede para confirmar que ela funciona conforme o esperado.

1. Volte para o grupo de recursos no **portal do Azure**.

2. Na folha AZ500LAB08****, na lista de recursos, clique na **máquina virtual Srv-Jump**.

3. **Na folha Srv-Jump, clique em Conectar**** e, no menu suspenso, clique em ****RDP**. 

4. Clique em **Baixar Arquivo** RDP e use-o **para se conectar à VM do Azure Srv-Jump** via Área de Trabalho Remota. Quando solicitado a autenticar, forneça os seguintes credntials:

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|localadmin|
   |Senha|**Pa55w.rd1234**|

    >**Observação**: as etapas a seguir são executadas na sessão de Área de Trabalho Remota para a **VM do Azure Srv-Jump** . 

    >**Nota**: Você se conectará à **máquina virtual Srv-Work** . Isso está sendo feito para que possamos testar a capacidade de acessar o site da bing.com.  

5. Na sessão de Área de Trabalho Remota para Srv-Jump, clique com o botão direito do mouse em Iniciar **, no menu do botão direito do mouse, clique **em **Executar e, na caixa de diálogo Executar****, execute o **seguinte para **se conectar ao **Srv-Work**.** 

    ```
    mstsc /v:Srv-Work
    ```

6. Quando a autenticação for solicitada, forneça suas credenciais do Azure.

   |Configuração|Valor|
   |---|---|
   |Nome de usuário|localadmin|
   |Senha|**Pa55w.rd1234**|

    >**Nota**: Aguarde até que a sessão de Área de Trabalho Remota seja estabelecida e a interface do Gerenciador do Servidor seja carregada.

7. Na sessão de Área de Trabalho Remota para **Srv-Work**, no **Gerenciador do Servidor, clique em Servidor** Local e em **Configuração de Segurança Reforçada** do**** IE.

8. Na caixa de diálogo Configuração** de Segurança Reforçada do **Internet Explorer, defina as duas opções como **Desativado** e clique em **OK.**

9. Na sessão de Área de Trabalho Remota para Srv-Work **, inicie o Internet Explorer e navegue até****`https://www.bing.com` .** 

    >**Nota**: O site deve ser exibido com êxito. O firewall permite o acesso.

10. Navegue para **`http://www.microsoft.com/`**

    >**Nota**: Dentro da página do navegador, você deve receber uma mensagem com texto semelhante ao seguinte: `HTTP request from 10.0.2.4:xxxxx to microsoft.com:80. Action: Deny. No rule matched. Proceeding with default action.` Isso é esperado, uma vez que o firewall bloqueia o acesso a este site. 

11. Encerre ambas as sessões da Área de Trabalho Remota.

> Resultado: você configurou e testou com êxito o Firewall do Azure.

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados. 

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

2. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB08" -Force -AsJob
    ```
4. Feche o painel do **Cloud Shell**. 
