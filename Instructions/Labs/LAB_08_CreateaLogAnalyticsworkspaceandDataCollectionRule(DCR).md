---
lab:
  title: 08 – Criar um workspace do Log Analytics e uma DCR (Regra de Coleta de Dados)
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# Laboratório 08: Criar um workspace do Log Analytics e uma DCR (Regra de Coleta de Dados)

# Manual de laboratório do aluno

## Cenário do laboratório

Como Engenheiro de Segurança do Azure para uma empresa de tecnologia financeira, você tem a tarefa de aprimorar o monitoramento e a visibilidade de segurança em todas as VMs (máquinas virtuais) do Azure usadas para processar transações financeiras e gerenciar dados confidenciais do cliente. A equipe de segurança precisa de logs detalhados e métricas de desempenho dessas VMs para detectar possíveis ameaças e otimizar o desempenho do sistema. O CISO (diretor de segurança de informação) pediu que você implementasse uma solução que coletasse eventos de segurança, logs do sistema e contadores de desempenho. Você ficou responsável por configurar o AMA (Agente do Azure Monitor) e as DCRs (Regras de Coleta de Dados) para centralizar a coleta de logs e o monitoramento de desempenho.



> Para todos os recursos neste laboratório, estamos usando a região **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório, você completará os seguintes exercícios:

- Exercício 1: implantar uma máquina virtual do Azure
- Exercício 2: criar um workspace do Log Analytics
- Exercício 3: criar uma conta de armazenamento do Azure
- Exercício 4: criar uma regra de coleta de dados.
  
## Instruções

### Exercício 1: implantar uma máquina virtual do Azure

### Tempo do exercício: 10 minutos

Neste exercício, você realizará as seguintes tarefas: 

- Tarefa 1: implantar uma máquina virtual do Azure. 

#### Tarefa 1: implantar uma máquina virtual do Azure

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. Abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

3. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

4. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para criar um grupo de recursos que será usado neste laboratório:
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**Observação**: este grupo de recursos será usado para os laboratórios 8, 9 e 10.

5. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para habilitar a criptografia no host (EAH)
   
   ```powershell
    Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace Microsoft.Compute 
    ```

5. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para criar uma nova máquina virtual do Azure. 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
    ```
    
6.  Quando solicitado a fornecer credenciais:

    |Configuração|Valor|
    |---|---|
    |Usuário |**localadmin**|
    |Senha|**Use sua senha pessoal criada no Laboratório 2 > Exercício 2 > Tarefa 1 > Etapa 3.**|

    >**Observação**: aguarde até que a implantação seja concluída. 

7. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para confirmar se a máquina virtual chamada **myVM** foi criada e seu **ProvisioningState** foi **Com êxito**.

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. Feche o painel do Cloud Shell. 

### Exercício 2: criar um workspace do Log Analytics

### Tempo do exercício: 10 minutos

Neste exercício, você realizará as seguintes tarefas: 

- Tarefa 1: Crie um workspace do Log Analytics.

#### Tarefa 1: criar um workspace do Log Analytics

Nesta tarefa, você criará um workspace do Log Analytics. 

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Workspaces do Log Analytics** e pressione a tecla **Enter**.

2. Na folha **Workspaces do Log Analytics**, clique em  **+ Criar**.

3. Na guia **Noções básicas** da folha **Criar workspace do Log Analytics**, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Assinatura|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB131415**|
    |Nome|qualquer nome globalmente exclusivo e válido|
    |Região|**Leste dos EUA**|

4. Selecione **Examinar + criar**.

5. Na guia **Examinar + criar** da folha **Criar workspace do Log Analytics**, escolha **Criar**.

### Exercício 3: criar uma conta de armazenamento do Azure

### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar uma conta de armazenamento do Azure.

#### Tarefa 1: criar uma conta de armazenamento do Azure

Nesta tarefa, você criará uma conta de armazenamento.

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Contas de armazenamento** e pressione a tecla **Enter**.

2. Na folha **Contas de armazenamento** no portal do Azure, clique no botão **+ Criar** para criar uma nova conta de armazenamento.

    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/73eb9241-d642-455a-a1ff-b504670395c0)

3. Na guia **Noções básicas** da folha **Criar conta de armazenamento**, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Assinatura|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB131415**|
    |Nome da conta de armazenamento|qualquer nome globalmente exclusivo de 3 a 24 caracteres composto por letras e dígitos|
    |Location|**(EUA) EastUS**|
    |Desempenho|**Padrão (contas para uso geral v2)**|
    |Redundância|**LRS (armazenamento com redundância local)**|

4. Na guia **Noções básicas** da folha **Criar conta de armazenamento**, clique em **Revisar**, aguarde a conclusão do processo de validação e clique em **Criar**.

     ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d443821c-2ddf-4794-87fa-bfc092980eba)

    >**Observação**: aguarde até a conta de armazenamento ser criada. Isso deverá levar cerca de dois minutos.

### Exercício 3: criar uma regra de coleta de dados

### Tempo estimado: 15 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar uma regra de coleta de dados.

#### Tarefa 1: criar uma regra de coleta de dados.

Nesta tarefa, você criará uma regra de coleta de dados.

1. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Monitor** e pressione a tecla **Enter**.

2. Na folha **Configurações do Monitor**, clique em  **Regras de Coleta de Dados.**

  ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d43e8f94-efb2-4255-9320-210c976fd45e)


3. Clique no botão **Criar** para criar uma nova regra de coleta de dados.

4. Na guia **Noções Básicas** da folha **Criar Regra de Coleta de Dados**, especifique as seguintes configurações:
  
    |Configuração|Valor|
    |---|---|
    |**Detalhes da regra**|
    |Nome da Regra|**DCR1**|
    |Assinatura|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB131415**|
    |Região|**Leste dos EUA**|
    |Tipo de Plataforma|**Windows**|
    |Ponto de extremidade da coleta de dados|*Deixar em branco*|

    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/9b58c4ce-b7a8-4acf-8289-d95b270a6083)


4. Clique no botão **Avançar: recursos >** para prosseguir.
   
6. Na guia Recursos, selecione **+ Adicionar recursos,**, marque **Habilitar pontos de extremidade de coleta de dados**. Em Selecionar um modelo de escopo, marque **AZ500LAB131415,** e clique em **Aplicar.**

    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d4191115-11bc-43ec-9bee-e84b9b95a821)

10. Clique no botão **Avançar: Coletar e entregar >** para prosseguir.

    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/8294d300-f910-4757-ad52-43c7594ac822)

11. Selecione **+ Adicionar fonte de dados** e, na página **Adicionar fonte de dados**, altere o menu suspenso **Tipo de fonte de dados** para exibir **Contadores de desempenho.** Deixe as seguintes configurações padrão:

    |Configuração|Valor|
    |---|---|
    |**Contador de desempenho**|**Taxa de amostragem (segundos)**|
    |CPU|60|
    |Memória|60|
    |Disco|60|
    |Rede|60|

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/a24e44ad-1d10-4533-80e2-bae1b3f6564d)

11. Clique no botão **Avançar: Destino >** para prosseguir.
  
12. Clique em **+ Adicionar destino**, altere o menu suspenso **Tipo de destino** para exibir os **Logs do Azure Monitor**. Na janela **Assinatura**, verifique se sua *Assinatura* está sendo exibida e altere o menu suspenso **Conta ou namespace** para refletir o workspace do Log Analytics criado anteriormente.

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/481843f5-94c4-4a8f-bf51-a10d49130bf8)

11. Selecione **Adicionar fonte de dados** na parte inferior da página.
    
    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/964091e7-bbbc-4ca8-8383-bb2871a1e7f0)

13. Clique em **Revisar + Criar**.

    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/50dd8407-a106-4540-9e14-ae40a3c04830)

14. Clique em **Criar**.

> Resultados: você implantou uma máquina virtual do Azure, um workspace do Log Analytics, uma conta de armazenamento do Azure e uma regra de coleta de dados para coletar eventos e contadores de desempenho de máquinas virtuais com o agente do Azure Monitor.

>**Observação**: não remova os recursos deste laboratório, pois eles são necessários para o laboratório do Microsoft Defender para Nuvem, para o laboratório "Habilitar o acesso just-in-time em VMs" e para o laboratório do Microsoft Sentinel.
 
