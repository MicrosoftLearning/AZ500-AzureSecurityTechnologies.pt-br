---
lab:
  title: "10 - Microsoft\_Sentinel"
  module: Module 05 - Configure and manage security monitoring and automation solutions
---

# Laboratório 10: Microsoft Sentinel
# Manual de laboratório do aluno

## Cenário do laboratório

**Observação:** o **Azure Sentinel** foi renomeado para **Microsoft Sentinel** 

Você foi solicitado a criar uma prova de conceito de detecção e resposta a ameaças baseadas no Microsoft Sentinel. Você precisa especificamente:

- Comece a coletar dados do Azure Activity e do Microsoft Defender para Nuvem.
- Adicionar alertas integrados e personalizados 
- Analisar como os Guias estratégicos podem ser usados para automatizar uma resposta a um incidente.

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório você realizará os seguintes exercícios:

- Exercício 1: implementar o Microsoft Sentinel

## Diagrama do Microsoft Sentinel

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/509aa70d-de11-4470-a289-877fbfecbc00)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\15\\changeincidentseverity.json**

### Exercício 1: implementar o Microsoft Sentinel

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: integração do Microsoft Sentinel
- Tarefa 2: conectar a Atividade do Azure ao Sentinel
- Tarefa 3: criar uma regra que use o conector de dados de Atividade do Azure. 
- Tarefa 4: criar um guia estratégico
- Tarefa 5: criar um alerta personalizado e configurar o guia estratégico como uma resposta automatizada.
- Tarefa 6: invocar um incidente e analisar as ações associadas.

#### Tarefa 1: integração do Azure Sentinel

Nesta tarefa, você integrará o Microsoft Sentinel e conectará o workspace do Log Analytics. 

1. Entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Sentinel** e pressione a tecla **Enter**.

    >**Observação**: se esta for sua primeira tentativa de ação do Microsoft Sentinel no painel do Azure, conclua as seguintes etapas: no portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Microsoft Sentinel** e pressione a tecla **Enter**. Selecione **Microsoft Sentinel** na visualização **Serviços**.
  
3. No painel **Microsoft Sentinel**, clique em **+ Criar**.

4. No painel **Adicionar Microsoft Sentinel a um espaço de trabalho**, selecione o espaço de trabalho do Log Analytics criado no laboratório do Azure Monitor e clique em **Adicionar**.

    >**Observação**: o Microsoft Sentinel tem requisitos muito específicos para espaços de trabalho. Por exemplo, espaços de trabalho criados pelo Microsoft Defender para Nuvem não podem ser usados. Leia mais em [Início rápido: integração do Azure Sentinel](https://docs.microsoft.com/en-us/azure/sentinel/quickstart-onboard)
    
#### Tarefa 2: configurar o Microsoft Sentinel para usar o conector de dados de Atividade do Azure. 

Nesta tarefa, você configurará o Sentinel para usar o conector de dados de Atividade do Azure.  

1. No portal do Azure, no painel **Microsoft Sentinel \| Visão Geral**, na seção **Gerenciamento de conteúdo**, clique em **Hub de conteúdo**.

2. No painel **Microsoft Sentinel \| Hub de conteúdo**, analise a lista de conteúdo disponível.

3. Digite **Azure** na barra de pesquisa e selecione a entrada que representa a **Atividade do Azure**. Revise sua descrição na extrema direita e clique em **Instalar**.

4. Aguarde a notificação **Instalação com sucesso**. No painel de navegação esquerdo, na seção **Configuração**, clique em **Conectores de dados**.

5. No painel **Microsoft Sentinel\| Conectores de dados**, clique em **Atualizar** e analise a lista de conectores disponíveis. Selecione a entrada que representa o conector de **Atividade do Azure** (oculte a barra de menus à esquerda usando \<< se necessário), revise sua descrição e status na extrema direita e clique em **Abrir página do conector**.

6. No painel **Atividade do Azure**, a guia **Instruções** deve ser selecionada, observe os **Pré-requisitos** e role para baixo até a **Configuração**. Anote as informações que descrevem a atualização do conector. Sua assinatura do Azure Pass nunca usou o método de conexão herdado para que você possa ignorar a etapa 1 (o botão **Desconectar tudo** ficará esmaecido) e prosseguir para a etapa 2.

7. Na etapa 2 **Conectar suas assinaturas por meio do novo pipeline de configurações de diagnóstico**, revise as instruções "Iniciar assistente de Atribuição do Azure Policy e siga as etapas" e clique em **Iniciar assistente de Atribuição do Azure Policy\>**.

8. Na guia **Configurar os logs de atividade do Azure para transmitir para o espaço de trabalho do Log Analytics específico** (página Atribuir política) **Noções Básicas**, clique no botão **Escopo da elipse (...)** Na página **Escopo**, escolha sua assinatura do Azure Pass na lista suspensa de assinaturas e clique no botão **Selecionar** na parte inferior da página.

    >**Observação**: *não* escolha um Grupo de Recursos

9. Clique no botão **Avançar** na parte inferior da guia **Noções Básicas** duas vezes para prosseguir para a guia **Parâmetros**. Na guia **Parâmetros**, clique no botão **Elipse (...) do workspace do Log Analytics principal**. Na página **Workspace do Log Analytics Principal**, verifique se sua assinatura do Azure Pass está selecionada e use a lista suspensa de **workspaces** para selecionar o workspace do Log Analytics que você está usando para o Sentinel. Ao clicar no botão **Selecionar** na parte inferior da página.

10. Clique no botão **Avançar** na parte inferior da guia **Parâmetros** para prosseguir para a guia **Remediação**. Na guia **Remediação**, marque a caixa de seleção **Criar uma tarefa de correção**. Isso habilitará a opção "Configurar logs de atividade do Azure para transmitir para o workspace especificado do Log Analytics" na lista suspensa **Política para correção**. Na lista suspensa **Local de identidade atribuído ao sistema**, selecione a região (Leste dos EUA, por exemplo) selecionada anteriormente para seu workspace do Log Analytics.

11. Clique no botão **Avançar** na parte inferior da guia **Correção** para prosseguir para a guia **Mensagem de não conformidade**. Insira uma mensagem de Não conformidade, se desejar (isso é opcional) e clique no botão **Revisar + Criar** na parte inferior da guia **Mensagem de não conformidade**.

12. Selecione o botão **Criar**. Você deve observar três mensagens de status bem-sucedidas: **Criação de atribuição de política bem-sucedida, Criação de atribuições de função bem-sucedida e Criação de tarefa de correção bem-sucedida**.

    >**Observação**: você pode verificar as notificações, no ícone de sino, para verificar as três tarefas bem-sucedidas.

13. Verifique se o painel **Atividade do Azure** exibe o gráfico **Dados recebidos** (talvez seja necessário atualizar a página do navegador).  

    >**Observação**: pode levar mais de 15 minutos até que o Status mostre "Conectado" e o gráfico exiba os Dados recebidos.

#### Tarefa 3: criar uma regra que use o conector de dados de Atividade do Azure. 

Nesta tarefa, você revisará e criará uma regra que usa o conector de dados de Atividade do Azure. 

1. Na folha **Configuração \| Microsoft Sentinel**, clique em **Analytics**. 

2. Na folha **Análise \|Microsoft Sentinel**, clique na guia **Modelos de regra**. 

    >**Observação**: revise os tipos de regras que você pode criar. Cada regra é associada a um tipo de Fonte de Dados específica.

3. Na listagem de modelos de regra, digite **Suspeito** no formulário da barra de pesquisa e clique na entrada **Número suspeito de criação ou implantação de recursos** associada à fonte de dados de **Atividade do Azure**. E, em seguida, no painel que exibe as propriedades do modelo de regra, clique em **Criar regra** (role para a direita da página, se necessário).

    >**Observação**: esta regra tem a gravidade média. 

4. Na guia **Geral** do **Assistente de regra de análise – Criar um novo painel de Regra agendada**, aceite as configurações padrão e clique em **Avançar: definir lógica de regra >**.

5. Na guia **Definir lógica de regra** do painel **Assistente de regra de análise – Criar uma nova regra agendada**, aceite as configurações padrão e clique em **Avançar: Configurações de incidente (Visualização) >**.

6. Na guia **Configurações de incidente** do painel **Assistente de regra de análise – Criar uma nova Regra agendada**, aceite as configurações padrão e clique em **Avançar: resposta automatizada >**. 

    >**Observação**: é aqui que você pode adicionar um guia estratégico, implementado como um Aplicativo Lógico, a uma regra para automatizar a correção de um problema.

7. Na guia **Resposta automatizada** do painel **Assistente de regra de análise – Criar uma nova Regra agendada**, aceite as configurações padrão e clique em **Avançar: revisar e criar >**. 

8. Na guia **Revisar e criar** do painel **Assistente de regra de análise – Criar uma nova Regra agendada**, clique em **Salvar**.

    >**Observação**: agora você tem uma regra ativa.

#### Tarefa 4: criar um guia estratégico

Nesta tarefa, você criará um guia estratégico. Um guia estratégico de segurança é uma coleção de tarefas que pode ser invocado pelo Microsoft Sentinel em resposta a alertas. 

1. Na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Implantar um modelo personalizado** e pressione a tecla **Enter**.

2. No painel **Implantação personalizada**, clique na opção **Criar seu próprio modelo no editor**.

3. No painel **Editar modelo**, clique em **Carregar arquivo**, localize o **arquivo changeincidentseverity.json** do \\Allfiles\\Labs\\15\\ e clique em **Abrir**.

    >**Observação**: você pode encontrar exemplos de guias estratégicos em [https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks).

4. No painel **Editar modelo**, clique em **Salvar**.

5. No painel **Implantação personalizada**, verifique se as seguintes configurações estão definidas (deixe outras com os valores padrão):

    |Configuração|Valor|
    |---|---|
    |Assinatura|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB131415**|
    |Location|**(EUA) Leste dos EUA**|
    |Nome do guia estratégico|**Change-Incident-Severity**|
    |Nome do usuário|seu endereço de email|

6. Clique em **Revisar + criar** e em **Criar**.

    >**Observação**: aguarde até que a implantação seja concluída.

7. No portal do Azure, na caixa de texto ** Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de recursos** e pressione a tecla **Enter**.

8. No painel **Grupos de recursos**, na lista de grupos de recursos, clique na entrada **AZ500LAB131415**.

9. No painel do grupo de recursos **AZ500LAB131415**, na lista de recursos, clique na entrada que representa o aplicativo lógico recém-criado **Change-Incident-Severity**.

10. No painel **Change-Incidente-Severity**, clique em **Editar**.

    >**Observação**: no painel **Designer de Aplicativos Lógicos**, cada um dos quatro s exibe um aviso. Isso significa que cada um precisa ser revisado e configurado.

11. No painel **Designer de Aplicativos Lógicos**, clique na primeira etapa de ** s**.

12. Clique em **Adicionar novo**, verifique se a entrada na lista suspensa **Locatário** contém seu nome de locatário do Azure AD e clique em **Entrar**.

13. Quando solicitado, entre com uma conta de usuário que tenha a função Proprietário ou Colaborador na função na assinatura do Azure que você está usando neste laboratório.

14. Clique na segunda etapa **s** e, na lista s, selecione a segunda entrada, representando a que você criou na etapa anterior.

15. Repita as etapas anteriores para as duas etapas **s** restantes.

    >**Observação**: certifique-se de que não há avisos exibidos em nenhuma das etapas.

16. No painel **Designer de Aplicativos Lógicos**, clique em **Salvar** para salvar as alterações.

#### Tarefa 5: criar um alerta personalizado e configurar um guia estratégico como uma resposta automatizada

1. No portal do Azure, navegue de volta para a aba **Visão geral** do Microsoft Sentinel\|.

2. No painel **Visão geral** do Microsoft Sentinel\|, na seção **Configuração**, clique em **Analytics**.

3. No painel **Microsoft Sentinel\| Analytics**, clique em **+ Criar** e, no menu suspenso, clique em **Regra de consulta agendada**. 

4. Na guia **Geral** do painel **Assistente de regra de análise – Criar uma nova Regra agendada**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Nome|**Demonstração do Guia Estratégico**|
    |Táticas|**Acesso inicial**|

5. Selecione **Avançar: Definir lógica da regra >**.

6. Na guia **Definir lógica de regra** do painel **Assistente de regra de análise – Criar uma nova Regra agendada**, na caixa de texto **Consulta de regra**, cole a seguinte consulta de regra. 

    ```
    AzureActivity
     | where ResourceProviderValue =~ "Microsoft.Security" 
     | where OperationNameValue =~ "Microsoft.Security/locations/jitNetworkAccessPolicies/delete" 
    ```

    >**Observação**: esta regra identifica a remoção de políticas de acesso a VMs just-in-time.

    >**Observação** Se você receber um erro de análise, o intellisense pode ter adicionado valores à sua consulta. Certifique-se de que a consulta corresponda. Caso contrário, cole a consulta no bloco de notas e, em seguida, do bloco de notas para a consulta de regra. 


7. Na guia **Definir lógica de regra** do painel **Assistente de regras de análise – Criar uma nova Regra agendada**, na seção **Agendamento de consulta**, defina **Executar consulta a cada** **5 minutos**.

8. Na guia **Definir lógica de regra** do painel **Assistente de regra de Análise – Criar uma nova Regra agendada**, aceite os valores padrão das configurações restantes e clique em **Avançar: configurações de Incidente >**.

9. Na guia **Configurações de incidente** do painel **Assistente de regra de análise – Criar uma nova Regra agendada**, aceite as configurações padrão e clique em **Avançar: resposta automatizada >**. 

10. Na guia **Resposta automatizada** do painel **Assistente de regra de análise – Crie uma nova Regra agendada**, em **Regras de automação**, clique em **+ Adicionar novo**.

11. Na janela **Criar nova regra de automação**, insira **Run Change-Severity Playbook** para o **nome da regra de automação**, no campo **Trigger**, clique no menu suspenso e selecione **Quando o alerta é criado**.

12. Na janela **Criar nova regra de automação**, em **Ações**, leia a nota e clique em **Gerenciar permissões do guia estratégico**. Na janela **Gerenciar permissões**, marque a caixa de seleção ao lado do **Grupo de recursos AZ500LAB1314151** criado anteriormente e clique em **Aplicar**.

13.  Na janela **Criar nova regra de automação**, em **Ações**, clique no segundo menu suspenso e selecione o aplicativo lógico **Change-Incident-Severity**. Na janela **Criar nova regra de automação**, clique em **Aplicar**.

14. Na guia **Resposta automatizada** do painel **Assistente de regra analítica – Crie uma nova Regra agendada**, clique em **Avançar: Revisar e criar >** e clique em **Salvar**

    >**Observação**: agora você tem uma nova regra ativa chamada **Demonstração de guia estratégico**. Se ocorrer um evento identificado pela lógica rue, ele resultará em um alerta de gravidade média, que gerará um incidente correspondente.

#### Tarefa 6: invocar um incidente e analisar as ações associadas.

1. No portal do Azure, navegue até **Microsoft Defender para Nuvem\| no painel Visão geral**.

    >**Observação**: verifique sua pontuação segura. Neste momento, já deveria ter sido atualizado. 

2. No painel **Visão geral** do Microsoft Defender para Nuvem\|, clique em **Proteções de carga de trabalho** em **Segurança da nuvem** na navegação à esquerda.

3. No painel **Proteções de carga de trabalho ** do \| Microsoft Defender par Nuvem, role para baixo e clique no bloco **Acesso à VM just-in-time** em **Proteção avançada**.

4. Na folha **Acesso just-in-time a VM**, no lado direito da linha que faz referência à máquina virtual **myVM**, clique no botão de **reticências (...)**, selecione **Remover** e, em seguida, confirme clicando em **Sim**.

    >**Observação:** se a VM não estiver listada nas **VMs Just-in-time**, navegue até o painel **Máquina Virtual** e clique na opção **Configuração**, Clique na opção **Habilitar as VMs Just-in-time** no **acesso da VM Just-in-time**. Repita a etapa acima para navegar de volta para o **Microsoft Defender para Nuvem** e atualizar a página, a VM aparecerá.

5. No portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Log de atividades** e pressione a tecla **Enter**.

6. Navegue até o painel **Log de atividades**, observe uma entrada **Excluir políticas de acesso à rede JIT**. 

    >**Observação**: isso pode levar alguns minutos para aparecer. **Atualize** a página se ela não aparecer.

7. No portal do Azure, navegue de volta para a aba **Visão geral** do Microsoft Sentinel\|.

8. No painel **Visão geral** do Microsoft Sentinel\|, revise o painel e verifique se ele exibe um incidente correspondente à exclusão da política de acesso à VM just-in-time.

    >**Observação**: pode levar até 5 minutos para que os alertas apareçam no painel **Visão geral** do Microsoft Sentinel\|. Se você não estiver vendo um alerta nesse ponto, execute a regra de consulta mencionada na tarefa anterior para verificar se a atividade de exclusão da política de acesso just-in-time foi propagada para o workspace do Log Analytics associado à sua instância do Microsoft Sentinel. Se esse não for o caso, recrie a política de acesso à VM Just-in-time e exclua-a novamente.

9. No painel **Microsoft Sentinel\| Visão geral**, na seção **Gerenciamento de ameaças**, clique em **Incidentes**.

10. Verifique se o painel exibe um incidente com nível de gravidade médio ou alto.

    >**Observação**: pode levar até 5 minutos para que o incidente apareça no painel **Incidentes\| do Microsoft Sentinel**. 

    >**Observação**: revise o painel **Livro estratégico do Microsoft Sentinel\|**. Lá você vai encontrar a contagem de execuções bem-sucedidas e com falha.

    >**Observação**: você tem a opção de atribuir um nível de gravidade e status diferentes a um incidente.

> Resultados: você criou um espaço de trabalho do Microsoft Sentinel, conectou-o aos logs de Atividade do Azure, criou um manual e alertas personalizados que são disparados em resposta à remoção de políticas de acesso de VM just-in-time e verificou se a configuração é válida.

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. A remoção de recursos não utilizados garante que não haverá custos inesperados. 

1. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

2. Verifique se o **PowerShell** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB131415" -Force -AsJob
    ```
4. Feche o painel do **Cloud Shell**.
