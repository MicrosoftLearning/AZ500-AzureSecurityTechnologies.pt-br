---
lab:
  title: 05 - Proteção do Banco de Dados SQL do Microsoft Azure
  module: Module 02 - Plan and implement security for Azure SQL Database and Azure SQL Managed Instance
---

# Laboratório 05: Banco de Dados SQL do Microsoft Azure
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a analisar os recursos de segurança do banco de dados SQL do Azure. Especificamente, você tem interesse em:

- Proteção contra ataques como injeção de SQL e exfiltração dos dados. 
- Capacidade de descobrir e classificar informações do banco de dados em categorias como Confidencial. 
- Capacidade de auditar consultas de servidor de banco de dados e banco de dados e eventos de log. 

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório você realizará os seguintes exercícios:

- Exercício 1: implementar recursos de segurança do banco de dados SQL

## Diagrama de proteção do Banco de Dados SQL do Azure

![imagem](https://user-images.githubusercontent.com/91347931/157533836-7250fa58-a109-4882-a55b-d3fa3baf34ab.png)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\11\\azuredeploy.json**

### Exercício 1: implementar recursos de segurança do banco de dados SQL

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: implantar um banco de dados SQL do Azure
- Tarefa 2: configurar a proteção avançada dos dados
- Tarefa 3: configurar a classificação de dados
- Tarefa 4: configurar auditoria

#### Tarefa 1: implantar um banco de dados SQL do Azure

Nesta tarefa, você usará um modelo para implantar a infraestrutura do laboratório. 

1. Entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. Na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Implantar um modelo personalizado** e pressione a tecla **Enter**.

3. No painel **Implantação personalizada**, clique na opção **Criar seu próprio modelo no editor**.

4. No painel **Editar modelo**, clique em **Carregar arquivo**, localize o arquivo **\\Allfiles\\Lab\\11\\azuredeploy.json** e clique em **Abrir**.

    >**Observação**: revise o conteúdo do modelo e observe que ele implanta um banco de dados SQL do Azure.

5. No painel **Editar modelo**, clique em **Salvar**.

6. No painel **Implantação personalizada**, verifique se as seguintes configurações estão definidas (deixe outras com os valores padrão):

   |Configuração|Valor|
   |---|---|
   |Assinatura|o nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB11**|
   |Location|**(EUA) Leste dos EUA**|

7. Clique em **Examinar + Criar** e em **Criar**.

    >**Observação**: aguarde até que a implantação seja concluída. 

#### Tarefa 2: configurar a proteção avançada dos dados

1. No portal do Azure, na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de recursos** e pressione a tecla **Enter**.

2. No painel **Grupos de recurso**, na lista do grupo de recursos, clique na entrada **AZ500LAB11**.

3. No painel **AZ500LAB11**, clique na entrada que representa o SQL Server recém-criado.

4. No painel SQL Server, na seção **Segurança**, clique em **Microsoft Defender para Nuvem**, selecione **Habilitar Microsoft Defender para SQL**.

      >**Observação**: aguarde até que a notificação indique que o Azure Defender para SQL foi habilitado com êxito.

5. No painel do SQL Server, na seção **Segurança**, na página **Microsoft Defender para Nuvem**, no **Microsoft Defender para SQL: habilitado no parâmetro de nível de assinatura (Configurar)**, clique em **(configurar)**.  

      >**Observação**: atualize o navegador se **(configurar)** não estiver sendo exibido.
    
6. No painel **Configurações do Servidor**, revise as informações sobre os preços e o período de avaliação, **CONFIGURAÇÕES DE AVALIAÇÃO DE VULNERABILIDADE** e **CONFIGURAÇÕES DA PROTEÇÃO AVANÇADA CONTRA AMEAÇAS**.

7. Volte para o painel **Microsoft Defender para Nuvem**, revise as **Recomendações** e os **alertas de Segurança**.

      >**Observação**: pode levar de 10 a 15 minutos para que as recomendações apareçam na folha **Microsoft Defender para Nuvem**. Em vez de esperar, prossiga para a próxima tarefa, mas considere retornar a essa folha depois de concluir todas as tarefas restantes.


#### Tarefa 3: configurar a classificação de dados

Nesta tarefa, você descobrirá e classificará informações no banco de dados SQL para conformidade com GPDR e proteção de dados.

1. No painel do SQL Server, na seção **Configurações**, clique em **Bancos de Dados SQL**.

2. Na lista de bancos de dados, selecione a entrada **AZ500LabDb**. 

3. No painel do banco de dados SQL do **AZ500LabDb**, na seção **Segurança**, clique em **Descoberta e Classificação de Dados**.

4. No painel **Descoberta e Classificação de Dados**, clique no guia **Classificação**.

    >**Observação**: o mecanismo de classificação verifica o banco de dados em busca de colunas que contenham dados possivelmente confidenciais e fornece uma lista de classificações de colunas recomendadas.

5. Clique na mensagem de texto **Encontramos 15 colunas com recomendações de classificação** exibidas na barra azul na parte superior do painel.

6. Revise as colunas listadas e o rótulo de confidencialidade recomendado. 

7. Habilite a caixa de seleção **Selecionar tudo** e clique em **Aceitar recomendações selecionadas**.

    >**Observação**: como alternativa, você pode selecionar apenas determinadas colunas e descartar outras. 

    >**Observação**: você tem a opção de alterar o tipo de informação e o rótulo de confidencialidade. 

8. Depois de concluir a revisão, clique em **Salvar**. 

    >**Observação**: isto concluirá a classificação e rotulará de maneira persistente as colunas do banco de dados com os novos metadados de classificação. 

9. De volta à guia da **Visão geral** do painel **Descoberta e Classificação de Dados**, observe que ela foi atualizada para levar em conta as informações de classificação mais recentes. 

#### Tarefa 4: configurar auditoria 

Nesta tarefa, você configurará primeiro a auditoria no nível do servidor e, em seguida, configurará a auditoria no nível do banco de dados. 

1. No portal do Azure, navegue de volta para o painel do SQL Server.

2. No painel do SQL Server, na seção **Segurança** , clique em **Auditoria**.

    >**Observação**: esta é a auditoria no nível do servidor. As configurações de auditoria padrão incluem todas as consultas e procedimentos armazenados executados no banco de dados, bem como os logons com falha e bem-sucedidos.

3. Defina a opção **Habilitar Auditoria do SQL do Azure**, e alterne para **ATIVADA** para habilitar a auditoria. 

4. Marque a caixa de seleção **Armazenamento** e as caixas de entrada para **Assinatura** e para **Conta de Armazenamento** serão exibidas.

5. Escolha sua **Assinatura** na lista suspensa.

6. Clique em **Conta de armazenamento** e escolha **Criar nova**.

7. No painel **Criar conta de armazenamento**, na caixa **Nome**, digite um nome globalmente exclusivo que consista entre 3 e 24 letras minúsculas e dígitos, clique em **OK**, 

    >**Observação**: talvez seja necessário atualizar o navegador antes que a conta de armazenamento fique disponível.

8. De volta ao painel **Auditoria**, em **Propriedades avançadas**, defina **Retenção (dias)** como **5**. 

9. No painel Auditoria, clique em **Salvar** para salvar as configurações de auditoria. 

    >**Observação**: se você receber uma mensagem de erro sobre o caminho do contêiner de armazenamento inválido, talvez a conta de armazenamento ainda não tenha sido provisionada. Aguarde alguns minutos, clique em **Conta de armazenamento**, na folha **Escolher conta de armazenamento**, selecione a conta de armazenamento recém-criada e, novamente, no painel Auditoria, clique em **Salvar**.

10. No painel do servidor, na seção **Configurações**, clique em **Bancos de Dados SQL**.

11. Na lista de bancos de dados, selecione a entrada **AZ500LabDb**. 

12. No painel banco de dados SQL do **AZ500LabDb**, na seção **Segurança**, clique em **Auditoria**. 

    >**Observação**: esta é a auditoria em nível de banco de dados. A auditoria no nível do servidor já está habilitada. 
  
    >**Observação**: as auditorias podem ser gravadas em uma conta de armazenamento do Azure, em um Workspace do Log Analytics ou no Hub de Eventos. Você pode configurar qualquer combinação destas opções.

    >**Observação**: se a auditoria baseada em armazenamento estiver habilitada no servidor, ela sempre se aplicará ao banco de dados, independentemente das configurações de banco de dados.
13. Na página Visão geral do banco de dados SQL no portal do Azure, selecione **Editor de Consultas (versão prévia)** no menu à esquerda. Tente entrar, você talvez falhe na senha, regra de firewall para o seu endereço IP, tudo é auditado. Tente também fazer login com êxito, execute a consulta e você poderá encontrar mais detalhes nos logs de auditoria

14. volte para o BD, Auditoria e clique em **Exibir logs de auditoria**.

15. No painel **Registros de auditoria**, observe que você pode alternar entre Auditoria do servidor e Auditoria do banco de dados. 

> Resultados: você criou um SQL Server e um banco de dados, configurou a classificação de dados e a auditoria. 


**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. A remoção de recursos não utilizados garante que não haverá custos inesperados. 

1. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

2. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB11" -Force -AsJob
    ```
4. Feche o painel do **Cloud Shell**. 
