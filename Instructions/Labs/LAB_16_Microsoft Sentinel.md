---
lab:
  title: Microsoft Sentinel –
  module: Module 04 - Manage Security Operations
---

# Laboratório 16: Microsoft Sentinel
# Manual do aluno

## Cenário do laboratório

**Observação:** **o Azure Sentinel foi renomeado para **Microsoft Sentinel**** 

Você foi solicitado a criar uma prova de conceito de detecção e resposta a ameaças baseadas no Microsoft Sentinel. Especificamente, você precisa:

- Comece a coletar dados do Azure Activity e do Microsoft Defender for Cloud.
- Alertas internos e personalizados 
- Analise como os Playbooks podem ser usados para automatizar uma resposta a um incidente.

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Exercício 1: Implementar o Microsoft Sentinel

## Diagrama do Microsoft Sentinel

![imagem](https://user-images.githubusercontent.com/91347931/157538440-4953be73-90be-4edd-bd23-b678326ba637.png)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\15\\changeincidentseverity.json**

### Exercício 1: Implementar o Microsoft Sentinel

### Tempo estimado: 30 minutos

Neste exercício, você realizará as seguintes tarefas:

- Integração do Microsoft Sentinel
- Tarefa 2: Conectar a atividade do Azure ao Sentinel
- Tarefa 3: Criar uma regra que use o conector de dados de Atividade do Azure. 
- Tarefa 4: Criar um manual
- Tarefa 5: Criar um alerta personalizado e configurar o manual como uma resposta automatizada.
- Tarefa 2: Invocar um incidente e examinar as ações associadas

#### Integração do Azure Sentinel

Nesta tarefa, você integrará o Microsoft Sentinel e conectará o espaço de trabalho do Log Analytics. 

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Microsoft Sentinel** e pressione a **tecla Enter**.

    >**Observação**: se esta for sua primeira tentativa de ação do Microsoft Sentinel no painel do Azure, conclua a(s) seguinte(s) etapa(s): No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Microsoft Sentinel** e pressione a **tecla Enter**. Selecione **Microsoft Sentinel** na **visualização Serviços** .
  
3. Na folha Microsoft **Sentinel** , clique em **+ Criar**.

4. Na folha Adicionar Microsoft **Sentinel a um espaço de trabalho, selecione o espaço de trabalho** do Log Analytics que você criou no laboratório do Azure Monitor e clique em **Adicionar**.

    >**Observação**: o Microsoft Sentinel tem requisitos muito específicos para espaços de trabalho. Por exemplo, espaços de trabalho criados pelo Microsoft Defender for Cloud não podem ser usados. Guia de início rápido: integração do Azure Sentinel.
    
#### Tarefa 2: Configurar o Microsoft Sentinel para usar o conector de dados de Atividade do Azure. 

Nesta tarefa, você configurará o Sentinel para usar o conector de dados de Atividade do Azure.  

1. No portal do Azure, na folha Visão Geral do **Microsoft Sentinel\|, na **seção Gerenciamento de conteúdo, clique em **Hub de conteúdo****.**

2. Na folha Hub** de conteúdo do **Microsoft Sentinel\|, examine a lista de conteúdo disponível.

3. Digite **Azure na barra de pesquisa e selecione a entrada que representa **a Atividade** do Azure**. Revise sua descrição na extremidade direita e clique em **Instalar**.

4. Aguarde a notificação de conclusão da instalação. No painel de navegação esquerdo, na **seção Configuração** , clique em **Conectores de dados**.

5. **Na folha Conectores de dados do Microsoft Sentinel\|, clique em **Atualizar** e examine a lista de conectores** disponíveis. Selecione a entrada que representa o **conector de Atividade** do Azure (oculte a barra de menus à esquerda usando \<< se necessário), revise sua descrição e status na extrema direita e clique em **Abrir página** do conector.

6. **Na folha Atividade** do Azure, a guia Instruções** deve ser selecionada, observe os **Pré-requisitos** e role para baixo até a ****Configuração**. Anote as informações que descrevem a atualização do conector. Sua assinatura do Azure Pass nunca usou o método de conexão herdado para que você possa ignorar a etapa 1 (o **botão Desconectar tudo** ficará esmaecido) e prosseguir para a etapa 2.

7. Na etapa 2 **Conectar suas assinaturas por meio do novo pipeline** de configurações de diagnóstico, revise as instruções "Iniciar o assistente de Atribuição de Política do Azure e siga as etapas" e clique em **Iniciar o assistente\>** de Atribuição de Política do Azure.

8. **Na guia Noções básicas Configurar logs de atividade do Azure para transmitir para o espaço de trabalho especificado do Log Analytics (página Atribuir política), **clique no botão elipse de ****** escopo (...) .** **Na página Escopo**, escolha sua assinatura do Azure Pass na lista suspensa de assinaturas e clique no **botão Selecionar** na parte inferior da página.

    >**Nota**: *Não* escolha um Grupo de Recursos

9. Clique no **botão Avançar** na parte inferior da **guia Noções básicas** duas vezes para prosseguir para a **guia Parâmetros**. Na guia Parâmetros **, **clique no botão elipse** (...) do espaço de trabalho do **Primary Log Analytics. Na página de espaço de trabalho do Log Analytics Principal, verifique se sua assinatura do Azure Pass está selecionada e use a **lista suspensa de espaços de **** trabalho para selecionar o espaço de trabalho** do Log Analytics que você está usando para o Sentinel. Selecione o botão **Avançar: Docker** na parte inferior da página.

10. Clique no **botão Avançar** na parte inferior da **guia Parâmetros** para prosseguir para a **guia Remediação**. Na guia Remediação, **marque a caixa de **seleção Criar uma tarefa** de correção**. Isso habilitará a opção "Configurar logs de atividade do Azure para transmitir para o espaço de trabalho especificado do Log Analytics" na **lista suspensa Política para correção** . Na lista suspensa Local **** de identidade atribuído ao sistema, selecione a região (Leste dos EUA, por exemplo) selecionada anteriormente para seu espaço de trabalho do Log Analytics.

11. Clique no **botão Avançar** na parte inferior da **guia Correção** para prosseguir para a **guia Mensagem** de não conformidade.  Insira uma mensagem de Não conformidade, se desejar (isso é opcional) e clique no **botão Revisar + Criar** na parte inferior da  **guia Mensagem** de não conformidade.

12. Selecione o botão **Criar**. Você deve observar três mensagens de status bem-sucedidas: **Criação de atribuição de política bem-sucedida, Criação de atribuições de função bem-sucedida e Criação de tarefa de correção bem-sucedida**.

    >**Nota**: Você pode verificar as notificações, ícone de sino para verificar as três tarefas bem-sucedidas.

13. Verifique se o painel Atividade** do Azure exibe o ****gráfico Dados recebidos** (talvez seja necessário atualizar a página do navegador).  

    >**Nota**: Pode levar mais de 15 minutos até que o Status mostre "Conectado" e o gráfico exiba Dados recebidos.

#### Tarefa 3: Criar uma regra que use o conector de dados de Atividade do Azure. 

Nesta tarefa, você revisará e criará uma regra que usa o conector de dados de Atividade do Azure. 

1. Na folha Configuração** do **Microsoft Sentinel\|, clique em **Analytics**. 

2. **Na folha Microsoft Sentinel \| Analytics**, clique na guia Modelos de **** regra. 

    >Há dois tipos de regras que você pode criar: Cada conjunto de regras de verificação do sistema é associado a um tipo de fonte de dados específico.

3. Na listagem de modelos de regra, digite **Suspeito** no formulário da barra de pesquisa e clique na **entrada Número suspeito de criação ou implantação** de recursos associada à fonte de dados de Atividade** do **Azure. E, em seguida, no painel que exibe as propriedades do modelo de regra, clique em **Criar regra** (role para a direita da página, se necessário).

    >**Nota**: Esta regra tem a gravidade média. 

4. **Na guia Geral** do assistente de regra do Google Analytics - Criar uma nova folha Regra** agendada, aceite as configurações padrão e clique em **Avançar: definir lógica de **regra >**.

5. **Na guia Definir lógica** de regra do assistente de regras do Google Analytics - Criar uma nova folha Regra** agendada, aceite as configurações padrão e clique em **Avançar: Configurações de **incidente (Visualização) >**.

6. **Na guia Configurações de incidente** do assistente de regra do **Google Analytics - Criar uma nova folha Regra** agendada, aceite as configurações padrão e clique em **Avançar: >** de resposta automatizada. 

    >**Nota**: É aqui que você pode adicionar um playbook, implementado como um aplicativo lógico, a uma regra para automatizar a correção de um problema.

7. **Na guia Resposta** automatizada do assistente de regras do **Google Analytics - Criar uma nova folha Regra** agendada, aceite as configurações padrão e clique em **Avançar: revisar e criar >**. 

8. **Na guia Revisar e criar** do assistente de regras do **Google Analytics - Criar uma nova folha Regra** agendada, clique em **Salvar**.

    >**Nota**: Agora você tem uma regra ativa.

#### Tarefa 4: Criar um manual

Nesta tarefa, você criará um playbook. Um guia estratégico do Microsoft Sentinel é uma coleção de procedimentos de segurança que você pode executar em resposta a alertas. 

1. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Implantar um modelo** personalizado e pressione a **tecla Enter**.

2. Na **página Implantação personalizada**, clique em **Criar seu modelo no editor**.

3. **Na folha Editar modelo**, clique em Carregar arquivo, localize o\\** arquivo** changeincidentseverity.json** do Allfiles\\Labs\\15\\e clique em **** Abrir**.

    >**Nota**: Você pode encontrar exemplos de playbooks emhttps://github.com/Azure/Azure-Sentinel/tree/master/Playbooks[](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) .

4. Na folha Editar **modelo** , clique em **Salvar**.

5. **Na folha Implantação** personalizada, verifique se as seguintes configurações estão definidas (deixe outras com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Subscription|Nome da assinatura que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB131415**|
    |Location|**(EUA) Leste dos EUA**|
    |Nome do guia estratégico|Alterar gravidade do incidente|
    |Nome do Usuário|Seu endereço de email|

6. Clique em **Revisar + criar** e em **Criar**.

    >Aguarde até que a implantação seja concluída.

7. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Grupos** de recursos e pressione a **tecla Enter**.

8. **Na folha Grupos de recursos, na lista de grupos de recursos**, clique na **entrada AZ500LAB131415**.

9. Na folha AZ500LAB131415 **** grupo de recursos, na lista de recursos, clique na entrada que representa o aplicativo lógico Change-Incident-Severity** recém-criado**.

10. Na folha Alterar-Incidente-Severidade****, clique em **Editar**.

    >**Nota**: Na **folha Designer de Aplicativos Lógicos** , cada um dos quatro s exibe um aviso. Isso significa que cada um precisa ser revisado e configurado.

11. Na folha Designer de **Aplicativos Lógicos****, clique na primeira **etapa.

12. Clique em **Adicionar novo**, verifique se a **entrada na lista suspensa Locatário contém seu nome de locatário** do Azure AD e clique em **** Entrar.

13. Quando solicitado, entre com uma conta de usuário que tenha a função Proprietário na assinatura do Azure que você usará neste laboratório.

14. Clique na segunda etapa s e, na lista de s **, selecione a segunda **entrada, representando a que você criou na etapa anterior.

15. Repita as etapas anteriores para os nós restantes.

    >**Nota**: Certifique-se de que não há avisos exibidos em nenhuma das etapas.

16. Na folha Designer de **Aplicativos Lógicos, clique em **Salvar**** para salvar as alterações.

#### Tarefa 5: Criar um alerta personalizado e configurar um manual como uma resposta automatizada

1. No portal do Azure, navegue de volta para a folha Visão geral** do **Microsoft Sentinel\|.

2. Na folha Visão geral** do **Microsoft Sentinel\|, na **seção Configuração**, clique em **Analytics**.

3. Na folha Microsoft Sentinel \| Analytics **, clique em + Criar** e, no menu suspenso, clique em **** Regra** de **consulta agendada. 

4. **Na guia Geral** do assistente de regra do **Google Analytics - Criar uma nova folha Regra** agendada, especifique as seguintes configurações (deixe outras pessoas com seus valores padrão):

    |Configuração|Valor|
    |---|---|
    |Nome|**Demonstração do Playbook**|
    |Táticas|**Acesso inicial**|

5. Selecione **Avançar: Definir lógica da regra >**.

6. **Na guia Definir lógica** de regra do assistente de regra do Google Analytics - Criar uma nova folha Regra** agendada, na caixa de texto Consulta** de regra, cole a seguinte consulta de **** regra. 

    ```
    AzureActivity
     | where ResourceProviderValue =~ "Microsoft.Security" 
     | where OperationNameValue =~ "Microsoft.Security/locations/jitNetworkAccessPolicies/delete" 
    ```

    >**Observação**: esta regra identifica a remoção de políticas de acesso a VMs just-in-time.

    >**Observação** Se você receber um erro de análise, o intellisense pode ter adicionado valores à sua consulta. Certifique-se de que a consulta corresponda caso contrário, cole a consulta no bloco de notas e, em seguida, do bloco de notas para a consulta de regra. 


7. **Na guia Definir lógica** de regra do assistente de regras do Google Analytics - Criar uma nova folha Regra** agendada, na **seção Agendamento de **** consulta, defina a **página Executar consulta a **cada** 5 minutos**.

8. **Na guia Definir lógica** de regra do assistente de regras do Google Analytics - Criar uma nova folha Regra** agendada, aceite os valores padrão das configurações restantes e clique em **Avançar: configurações de **incidente >**.

9. **Na guia Configurações de incidente** do assistente de regra do **Google Analytics - Criar uma nova folha Regra** agendada, aceite as configurações padrão e clique em **Avançar: >** de resposta automatizada. 

10. **Na guia Resposta** automatizada do assistente de regra analítica - Criar uma nova folha Regra** agendada, em Regras** de **automação, clique em ****+ Adicionar novo**.

11. **Na janela Criar nova regra de automação, insira **Run Change-Severity Playbook** para o nome** da regra** de automação, no **campo Trigger**, clique no menu suspenso e selecione **When alert **is created**.

12. **Na janela Criar nova regra** de automação, em Ações **, leia a nota e clique em ****Gerenciar permissões** de playbook. **Na janela Gerenciar permissões**, marque a caixa de seleção ao lado da AZ500LAB1314151** do grupo de recursos criada **anteriormente e clique em **Aplicar**.

13.  **Na janela Criar nova regra** de automação, em **Ações**, clique no segundo menu suspenso e selecione o aplicativo lógico Alterar-Incidente-Severidade****. **Na janela Criar nova regra de** automação, clique em **Aplicar**.

14. **Na guia Resposta** automatizada do assistente de **regra analítica - Criar uma nova folha Regra** agendada, clique em Avançar: Revisar e criar >** e clique em ****Salvar**

    >**Nota**: Agora você tem uma nova regra ativa chamada **Playbook Demo**. Se ocorrer um evento identificado pela lógica rue, ele resultará em um alerta de gravidade média, que gerará um incidente correspondente.

#### Tarefa 2: Invocar um incidente e examinar as ações associadas

1. No portal do Azure, navegue até Microsoft Defender para Nuvem.

    >**Nota**: Verifique sua pontuação segura. A essa altura, já deveria ter sido atualizado. 

2. Na folha Visão geral do **Microsoft Defender for Cloud\|, clique em Proteções de carga de** trabalho em ****Segurança** na nuvem na navegação à** esquerda.

3. Na folha Proteções de** carga de trabalho do **Microsoft Defender for Cloud\|, role para baixo e clique no **bloco Acesso à VM** just-in-time em **Proteção** avançada.

4. Na folha Acesso à VM** just-in-time, no lado direito da linha que faz referência à ****máquina virtual myVM**, clique no **botão de reticências (...**), clique em Remover** e, em seguida, clique em ****Sim.**

    >**Nota:** Se a VM não estiver listada nas VMs Just-in-time, navegue até **a **folha Virutal Machine** e clique na **opção Configuração**, Clique na **opção Habilitar as VMs**** Just-in-time no acesso** da **VM Just-in-time. Repita a etapa acima para navegar de volta para o **Microsoft Defender for Cloud** e atualizar a página, a VM aparecerá.

5. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Log** de atividades e pressione a **tecla Enter**.

6. Navegue até a folha Log de** **atividades, observe uma **entrada Excluir diretivas** de acesso à rede JIT. 

    >Observe que isso pode levar alguns minutos para aparecer. **Atualize** a página se ela não aparecer.

7. No portal do Azure, navegue de volta para a folha Visão geral** do **Microsoft Sentinel\|.

8. Na folha Visão geral** do **Microsoft Sentinel\|, revise o painel e verifique se ele exibe um incidente correspondente à exclusão da política de acesso à VM just-in-time.

    >**Observação**: pode levar até 5 minutos para que os alertas apareçam na folha Visão geral** do **Microsoft Sentinel\|. Se você não estiver vendo um alerta nesse ponto, execute a regra de consulta mencionada na tarefa anterior para verificar se a atividade de exclusão da política de acesso just-in-time foi propagada para o espaço de trabalho do Log Analytics associado à sua instância do Microsoft Sentinel. Se esse não for o caso, recrie a política de acesso à VM Just-in-time e exclua-a novamente.

9. Na página **Microsoft Sentinel | Visão geral\|, na barra de menus da seção **Gerenciamento de ameaças **, selecione **Incidentes.

10. Verifique se a lâmina exibe um incidente com nível de gravidade médio ou alto.

    >**Observação**: pode levar até 5 minutos para que o incidente apareça na folha Incidentes** do **Microsoft Sentinel\|. 

    >**Observação**: revise a folha Playbooks** do **Microsoft Sentinel\|. Você vai encontrar lá a contagem de execuções bem-sucedidas e fracassadas.

    >**Nota**: Você tem a opção de atribuir um nível de gravidade e status diferentes a um incidente.

> Resultados: Você criou um espaço de trabalho do Microsoft Sentinel, conectou-o aos logs de Atividade do Azure, criou um manual e alertas personalizados que são disparados em resposta à remoção das políticas de acesso de VM just-in-time e verificou se a configuração é válida.

**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. Remover recursos não utilizados garante que você não veja encargos inesperados. 

1. No portal do Azure, abra o Azure Cloud Shell clicando no ícone no canto superior direito do portal do Azure. Se solicitado, clique em **PowerShell** e **Criar armazenamento**.

2. Verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

3. Na sessão do PowerShell no painel Cloud Shell, execute o seguinte para remover o grupo de recursos criado neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB131415" -Force -AsJob
    ```
4. Feche o painel do **Cloud Shell**.
