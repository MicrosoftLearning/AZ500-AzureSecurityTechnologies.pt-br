---
lab:
  title: Laboratório &#58; Proteger o banco de dados SQL do Azure
  module: Module 03 - Secure data and applications
---

# Laboratório &#58; Proteger o banco de dados SQL do Azure
# Manual do aluno

## Cenário do laboratório

Você foi solicitado a revisar os recursos de segurança do Banco de Dados SQL do Azure. Especificamente, você está interessado em:

- Proteção contra ataques como injeção de SQL e exfiltração de dados. 
- Capacidade de descobrir e classificar informações do banco de dados em categorias como Confidencial. 
- Capacidade de auditar consultas de servidor de banco de dados e banco de dados e eventos de log. 

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Exercício 1: Implementar recursos de segurança do Banco de dados SQL

## Laboratório: Protegendo o banco de dados SQL do Azure

![imagem](https://user-images.githubusercontent.com/91347931/157533836-7250fa58-a109-4882-a55b-d3fa3baf34ab.png)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\11\\azuredeploy.json**

### Exercício 1: Implementar recursos de segurança do Banco de dados SQL

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Implantar um Banco de Dados SQL do Azure
- Tarefa 2: Configurar a Proteção Avançada de Dados
- Configurar a classificação de dados
- Tarefa 4: Configurar auditoria

#### Implantar um Banco de Dados SQL do Azure

Nesta tarefa, você usará um modelo para implantar a infraestrutura de laboratório. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Implantar um modelo** personalizado e pressione a **tecla Enter**.

3. Na **página Implantação personalizada**, clique em **Criar seu modelo no editor**.

4. **Na folha Editar modelo**, clique em Carregar arquivo, localize o\\** arquivo** azuredeploy.json** do Allfiles\\Labs\\11\\e clique em **** Abrir**.

    >**Observação**: revise o conteúdo do modelo e observe que ele implanta um banco de dados SQL do Azure.

5. Na folha Editar **modelo** , clique em **Salvar**.

6. **Na folha Implantação** personalizada, verifique se as seguintes configurações estão definidas (deixe outras com seus valores padrão):

   |Configuração|Valor|
   |---|---|
   |Subscription|O nome da assinatura do Azure que você usará neste laboratório|
   |Grupo de recursos|clique em **Criar novo** e digite o nome **AZ500LAB11**|
   |Location|**(EUA) Leste dos EUA**|

7. Clique em **Examinar + Criar** e em **Criar**.

    >Aguarde até que a implantação seja concluída. 

#### Tarefa 2: Configurar a Proteção Avançada de Dados

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Grupos** de recursos e pressione a **tecla Enter**.

2. **Na folha Grupos de recursos, na lista de grupos de recursos**, clique na **entrada AZ500LAB11**.

3. Na folha AZ500LAB11****, clique na entrada que representa o SQL Server recém-criado.

4. Na folha SQL Server, na **seção Segurança**, clique em **Microsoft Defender para Nuvem**, selecione **Habilitar Microsoft Defender para SQL.**

      >**Observação**: aguarde até que a notificação indique que o Azure Defender para SQL foi habilitado com êxito.

5. Na folha SQL Server, na seção Segurança **, na **** página Microsoft Defender para Nuvem**, no parâmetro Microsoft Defender para SQL: Habilitado no **nível de assinatura (Configurar**), clique em **(configurar).**  

      >**Nota**: Atualize o navegador se **(configurar)** não estiver sendo exibido.
    
6. Na folha Configurações do **Servidor, revise as informações sobre preços e o período de avaliação, **CONFIGURAÇÕES DE AVALIAÇÃO DE VULNERABILIDADE e **CONFIGURAÇÕES AVANÇADAS DE** PROTEÇÃO CONTRA AMEAÇAS.****

7. De volta à **folha Microsoft Defender para Cloud** , revise **Recomendações** e **alertas** de segurança.

      >**Observação**: pode levar de 10 a 15 minutos para que as recomendações apareçam na folha Microsoft **Defender for Cloud** . Em vez de esperar, prossiga para a próxima tarefa, mas considere retornar a essa folha depois de concluir todas as tarefas restantes.


#### Configurar a classificação de dados

Nesta tarefa, você descobrirá e classificará informações no banco de dados SQL para conformidade com GPDR e proteção de dados.

1. Na folha SQL Server, na seção Configurações **, clique em **Bancos de Dados SQL**.**

2. Na lista de bancos de dados, selecione a **entrada AZ500LabDb** . 

3. Na folha do banco de dados SQL do AZ500LabDb **, na **seção Segurança**, clique em **Classificação de Descoberta de Dados e Classificação**.**

4. **Na folha Descoberta de Dados & Classificação, clique na **guia Classificação****.

    >O mecanismo de classificação verifica o banco de dados em busca de colunas que contenham dados possivelmente confidenciais e mostra uma lista de classificações de coluna recomendadas.

5. Clique na mensagem **de texto Encontramos 15 colunas com recomendações** de classificação exibidas na barra azul na parte superior da folha.

6. Revise as colunas listadas e o rótulo de sensibilidade recomendado. 

7. Habilite a **caixa de seleção Selecionar tudo** e clique em **Aceitar recomendações selecionadas**.

    >**Nota**: Como alternativa, você pode selecionar apenas determinadas colunas e descartar outras. 

    >**Nota**: Você tem a opção de alterar o tipo de informação e o rótulo de sensibilidade. 

8. Depois de concluir a revisão, clique em **Salvar**. 

    >Para concluir a classificação e rotular (marcar) de maneira persistente as colunas do banco de dados com os novos metadados de classificação, selecione Salvar na barra de menus superior. 

9. De volta à guia Visão geral da **folha Descoberta de Dados e Classificação** , observe que ela foi atualizada para levar em conta as informações de **classificação mais** recentes. 

#### Tarefa 4: Configurar auditoria 

Nesta tarefa, você primeiro configurará a auditoria no nível do servidor e, em seguida, configurará a auditoria no nível do banco de dados. 

1. Navegue até o recurso do SQL Server no portal do Azure.

2. Na folha SQL Server, na **seção Segurança** , clique em **Auditoria**.

    >**Nota**: Esta é a auditoria no nível do servidor. A política de auditoria padrão inclui o seguinte conjunto de grupos de ações, que faz a auditoria de todas as consultas e todos os procedimentos armazenados executados no banco de dados, bem como os logons com falha e bem-sucedidos:

3. Defina a opção Habilitar Auditoria** SQL do Azure como **ATIVADA** para habilitar a **auditoria. 

4. Marque a caixa de seleção Armazenamento e as **caixas de** entrada para **Conta de Assinatura** e **Armazenamento** serão exibidas.

5. Escolha sua assinatura na lista suspensa.

6. Escolha uma conta de armazenamento ou crie uma nova.

7. **Na folha Criar conta** de armazenamento, na **caixa Nome**, digite um nome globalmente exclusivo que consista entre 3 e 24 letras minúsculas e dígitos, clique em **OK,** 

    >**Observação**: talvez seja necessário atualizar o navegador antes que a conta de armazenamento fique disponível.

8. De volta à **folha Auditoria, em **Propriedades**** avançadas, defina **Retenção (dias)** como **5**. 

9. Na folha Auditoria, clique em **Salvar** para salvar as configurações de auditoria. 

    >**Nota**: Se você receber uma mensagem de erro sobre o caminho do contêiner de armazenamento inválido, talvez a conta de armazenamento ainda não tenha sido provisionada. Aguarde alguns minutos, clique em Conta** de armazenamento, na folha Escolher conta de armazenamento, selecione a conta** de armazenamento recém-criada e, novamente, na folha Auditoria, clique em ******Salvar**.

10. Na folha do servidor, na **seção Configurações** , clique em **Bancos de Dados SQL**.

11. Na lista de bancos de dados, selecione a **entrada AZ500LabDb** . 

12. Na folha do banco de dados SQL do **AZ500LabDb** , na **seção Segurança** , clique em **Auditoria**. 

    >**Nota**: Esta é a auditoria em nível de banco de dados. A auditoria no nível do servidor já está habilitada. 
  
    >**Observação**: as auditorias podem ser gravadas em uma conta de armazenamento do Azure, em um espaço de trabalho do Log Analytics ou no Hub de Eventos. Você pode selecionar qualquer combinação das três opções abaixo:

    >Se a Auditoria de Blob estiver habilitada no servidor, ela sempre se aplicará ao banco de dados, independentemente das configurações de banco de dados.
13. Na página Visão geral do banco de dados SQL no portal do Azure, selecione Editor de consultas (versão prévia) no menu à esquerda. Tente entrar, você pode falhar na senha, regra de firewall para o seu endereço IP, tudo é auditado. Tente login bem-sucedido também, execute a consulta e você pode encontrar mais detalhes nos logs de auditoria

14. volte para DB, Auditoria e clique em **Exibir logs** de auditoria.

15. **Na folha Registros de** auditoria, observe que você pode alternar entre Auditoria do servidor e Auditoria do banco de dados. 

> Resultados: Você criou um SQL Server e um banco de dados, configurou a classificação de dados e a auditoria. 


**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados. 

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

2. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB11" -Force -AsJob
    ```
4. Feche o painel do **Cloud Shell**. 
