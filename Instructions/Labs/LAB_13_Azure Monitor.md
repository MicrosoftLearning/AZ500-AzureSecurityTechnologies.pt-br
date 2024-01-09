---
lab:
  title: '[Azure Monitor][3]'
  module: Module 04 - Manage security operations
---

# Laboratório &#58; Azure Monitor

# Manual do aluno

## Cenário do laboratório

Coletar eventos e contadores de desempenho de máquinas virtuais com o agente do Azure Monitor

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Implantar uma máquina virtual do Azure
- 2. Criar um espaço de trabalho do Log Analytics
- Exercício – Criar uma conta de armazenamento do Azure
- Criar uma regra de coleta de dados
  
## Instruções

### Implantar uma máquina virtual do Azure

### Tempo do exercício: 10 minutos

Neste exercício, você realizará as seguintes tarefas: 

- Implantar uma máquina virtual do Azure 

#### Implantar uma máquina virtual do Azure

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, selecione **PowerShell** e **Criar armazenamento**.

3. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

4. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar um grupo de recursos que será usado neste laboratório:
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**Nota**: Este grupo de recursos será usado para os laboratórios 13, 14 e 15. 

5. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para criar uma nova máquina virtual do Azure. 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
    ```
    
6.  Quando solicitado a fornecer credenciais, use estas:

    |Configuração|Valor|
    |---|---|
    |Usuário |localadmin|
    |Senha|**Use sua senha pessoal criada no Laboratório 04 > Exercício 1 > Tarefa 1 > Etapa 9.**|

    >Aguarde até que a implantação seja concluída. 

7. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para confirmar se a máquina virtual chamada **myVM** foi criada e seu **ProvisioningState** foi **bem-sucedido**.

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. Feche o painel do Cloud Shell. 

### Criar um workspace do Azure Log Analytics.

### Tempo do exercício: 10 minutos

Neste exercício, você realizará as seguintes tarefas: 

- Tarefa 1: Crie um workspace do Log Analytics.

#### Tarefa 1: Crie um workspace do Log Analytics.

Nesta tarefa, você criará um espaço de trabalho do Log Analytics. 

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Espaços de trabalho do** Log Analytics e pressione a **tecla Enter**.

2. Na folha Espaços de trabalho do **** Log Analytics, clique em ** + Criar**.

3. Na guia **Noções básicas** da folha **Criar conta de armazenamento**, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB131415**|
    |Nome|qualquer nome globalmente exclusivo|
    |Region|**Leste dos EUA**|

4. Selecione **Examinar + criar**.

5. Na guia **Examinar + criar** da folha **Criar Aplicativo de Funções**, selecione **Criar**.

### Exercício – Criar uma conta de armazenamento do Azure

### Tempo estimado: 10 minutos

Neste exercício, você realizará as seguintes tarefas:

- 3 – Criar uma conta de armazenamento do Azure

#### 3 – Criar uma conta de armazenamento do Azure

Nesta tarefa, você criará uma nova conta de armazenamento.

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Contas** de armazenamento e pressione a **tecla Enter**.

2. No portal do Azure, navegue até a página **Contas de armazenamento** e clique no botão **Criar** para criar nova conta.

    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/73eb9241-d642-455a-a1ff-b504670395c0)

3. Na guia **Noções básicas** da folha **Criar conta de armazenamento**, defina as seguintes configurações (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB131415**|
    |Nome da conta de armazenamento|Insira qualquer nome global exclusivo de 3 a 24 caracteres composto por letras e dígitos e começando com uma letra.|
    |Location|**(EUA) Leste dos EUA**|
    |Desempenho|Contas para uso geral v2 Standard|
    |Redundância|**LRS (armazenamento com redundância local)**|

4. Na guia **Básico** da folha **Criar conta de armazenamento**, selecione **Examinar + Criar**, aguarde a conclusão do processo de validação e selecione **Criar**.

     ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d443821c-2ddf-4794-87fa-bfc092980eba)

    >Aguarde até a conta de armazenamento ser criado. Isso deverá levar cerca de dois minutos.

### Crie uma Regra de Coleta de Dados.

### Tempo estimado: 15 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 2: Criar regra de coleta de dados.

#### Tarefa 2: Criar regra de coleta de dados.

Nesta tarefa, você criará uma regra de coleta de dados.

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Monitor** e pressione a **tecla Enter**.

2. Na folha Configurações** do **Monitor, clique em **Regras de Coleta de Dados.**

  ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d43e8f94-efb2-4255-9320-210c976fd45e)


3. **Na guia Noções Básicas** da folha Criar Regra de Coleta de **Dados**, especifique as seguintes configurações:
  
    |Configuração|Valor|
    |---|---|
    |Detalhes da regra|
    |Nome da Regra|**DCR1**|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB131415**|
    |Region|**Leste dos EUA**|
    |Tipo de Plataforma|**Windows**|
    |Ponto de extremidade da coleta de dados|*Deixar em branco*|

    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/9b58c4ce-b7a8-4acf-8289-d95b270a6083)


4. Clique no botão Avançar **: Recursos >** para prosseguir.

5. Na guia Recursos, selecione Habilitar Pontos de Extremidade de Coleta de Dados.

    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/c8388619-c254-4c80-a1ff-dde2f35ed350)

6. Clique no botão Avançar **: Coletar e entregar >** para prosseguir.

7. Clique em **+ Adicionar fonte de dados e, na **página Adicionar fonte de dados, altere o **menu suspenso Tipo** de fonte**** de dados para exibir **Contadores de Desempenho.** Deixar as configurações padrão

    |Configuração|Valor|
    |---|---|
    |Contador de desempenho|Taxa de amostragem (segundos)|
    |CPU|60|
    |Memória|60|
    |Disco|60|
    |Rede|60|

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/a24e44ad-1d10-4533-80e2-bae1b3f6564d)

8. Clique no botão Avançar **: >** de destino para prosseguir.
  
9. Altere o menu suspenso Tipo de destino** para exibir **os Logs do **Azure Monitor.** **Na janela Assinatura, verifique se sua *Assinatura*** é exibida e altere o menu suspenso Conta ou namespace** para refletir o **Espaço de Trabalho do Log Analytics criado anteriormente.

   ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/481843f5-94c4-4a8f-bf51-a10d49130bf8)

10. Clique em **Adicionar fonte** de dados na parte inferior da página.
    
    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/964091e7-bbbc-4ca8-8383-bb2871a1e7f0)

13. Clique em **Revisar + Criar**.

    ![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/50dd8407-a106-4540-9e14-ae40a3c04830)

14. Clique em **Criar**.

> Resultados: você implantou uma máquina virtual do Azure, um espaço de trabalho do Log Analytics, uma conta de armazenamento do Azure e uma regra de coleta de dados para coletar eventos e contadores de desempenho de máquinas virtuais com o Azure Monitor Agent.

>**Observação**: não remova os recursos deste laboratório, pois eles são necessários para o laboratório do Microsoft Defender for Cloud e o laboratório do Microsoft Sentinel.
 
