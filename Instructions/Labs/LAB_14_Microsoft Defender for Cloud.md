---
lab:
  title: 2 Microsoft Defender para Nuvem
  module: Module 04 - Microsoft Defender for Cloud
---

# Laboratório: explorar o Microsoft Defender para Nuvem
# Manual do aluno

## Cenário do laboratório

Você foi solicitado a criar uma prova de conceito do Microsoft Defender para ambiente baseado em nuvem. Especificamente, você precisa:

- Configure o Microsoft Defender para nuvem para monitorar uma máquina virtual.
- Captura de tela das recomendações de segurança do Microsoft Defender para Nuvem para uma Máquina Virtual.
- Implemente recomendações para configuração de convidado e acesso à VM Just-in-time. 
- Analise como o Secure Score pode ser usado para determinar o progresso na criação de uma infraestrutura mais segura.

> Para todos os recursos neste laboratório, estamos usando a **região Leste dos EUA** . Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste exercício, você realizará as seguintes tarefas:

- Implementar o Microsoft Defender para Nuvem

## Diagrama mostrando o que o Microsoft Defender para Nuvem defende.

![imagem](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/c31055cc-de95-41f6-adef-f09d756a68eb)

## Instruções

### Implementar o Microsoft Defender para Nuvem

Neste exercício, você realizará as seguintes tarefas:

- Configurar o Microsoft Defender para Nuvem
- Revisar as recomendações de segurança do Microsoft Defender para IoT
- Tarefa 3: Implementar a recomendação do Microsoft Defender for Cloud para habilitar o Acesso Just-in-time à VM

#### Configurar o Microsoft Defender para Nuvem

Nesta tarefa, você revisará o Microsoft Defender para Nuvem.

1. Entre no portal do Azure**`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório.

2. No portal do Azure, na caixa de texto Pesquisar recursos, serviços e documentos** na **parte superior da página do portal do Azure, digite **Microsoft Defender for Cloud** e pressione a **tecla Enter**.

3. No menu de navegação esquerdo, escolha **Introdução**. Na folha Introdução ao **Microsoft Defender para Nuvem\|**, clique em **Atualizar**.
     
4. Na folha Introdução ao **Microsoft Defender para Cloud\|, na guia Instalar agentes, role para baixo e clique em **Instalar agentes**.** 

5. Na folha Introdução ao **Microsoft Defender para Cloud\|, na **guia Atualizar, >>role para baixo até que a **seção Selecionar** espaços de trabalho com recursos** de segurança aprimorados fique visível >> ativar o plano** do **Microsoft Defender selecionando seu Espaço de Trabalho do Log Analytics e clique no grande botão Azul de** Atualização.  

    >**Observação**: revise todos os recursos disponíveis como parte dos planos do Microsoft Defender. 

6. Navegue até **Microsoft Defender for Cloud** e, no painel de navegação esquerdo, na seção Gerenciamento, clique em **Configurações do** Ambiente.

7. Na folha Configurações do **Microsoft Defender for Cloud \| Environment** , role para baixo, expanda até que sua assinatura seja exibida e clique na assinatura relevante. 

8. Na folha Planos do **Defensor de Configurações\|, selecione **Habilitar todos os planos** e, se necessário, clique em **Salvar****.

9. Navegue de volta para a folha de configurações** do Microsoft Defender for Cloud \| Environment, expanda até que sua assinatura seja exibida e clique na entrada que representa o espaço de **trabalho do Log Analytics criado no laboratório anterior.

10. Na folha Planos** do **Defensor de Configurações\|, verifique se todas as opções estão "Ativadas". Se necessário, clique em Habilitar todos os planos e, em seguida, clique em ****Salvar**.**

11. Selecione **Coleta de dados** na **folha Planos** do Defensor de configurações\|. Clique em **Todos os Eventos e **Salvar****.

#### Revisar as recomendações de segurança do Microsoft Defender para IoT

Nesta tarefa, você revisará o Microsoft Defender para Nuvem. 

1. No portal do Azure, navegue até Microsoft Defender para Nuvem. 

2. Na folha Visão geral do **Microsoft Defender for Cloud\|, examine o **bloco Pontuação segura****.

    >**Nota**: Registre a pontuação atual, se ela estiver disponível.

3. Navegue de volta para a folha Visão geral do **Microsoft Defender for Cloud\|, clique em **Recursos avaliados**.**

4. **Na folha Inventário**, clique na **entrada myVM**.

    >**Nota**: Talvez seja necessário aguardar alguns minutos e atualizar a página do navegador para que a entrada apareça.
    
5. Na folha Integridade** do **recurso, na **guia Recomendações**, revise a lista de recomendações para **myVM.**

#### Tarefa 3: Implementar a recomendação do Microsoft Defender for Cloud para habilitar o Acesso Just-in-time à VM

Nesta tarefa, você implementará a recomendação do Microsoft Defender for Cloud para habilitar o Acesso Just-in-time à VM na máquina virtual. 

1. No portal do Azure, navegue de volta para a folha Visão geral do **Microsoft Defender for Cloud \| e clique em Proteções de carga de trabalho em ****Segurança** na nuvem no painel de** navegação** esquerdo.

2. Na folha Proteções de** carga de trabalho do **Microsoft Defender for Cloud\|, role para baixo até a **seção Proteção** avançada e clique no bloco Acesso de **VM** just-in-time.

3. Na folha Acesso de **VM just-in-time, na **seção Máquinas** virtuais, selecione **Não configurado** e marque a caixa de seleção da **entrada myVM****.

    >**Nota**: Talvez seja necessário aguardar alguns minutos, atualizar a página do navegador e selecionar **Não configurado** novamente para que a entrada apareça.

4. Clique na opção Habilitar JIT em 1 VM na **extremidade direita da **seção Máquinas virtuais**.**

5. Na folha de configuração** de acesso JIT **VM, na extremidade direita da linha que faz referência à porta **22**, clique no botão de reticências e, em seguida, clique em **Excluir**.

6. Na folha **Configuração de acesso à VM JIT**, selecione **Adicionar**.

    >**Nota**: Monitore o progresso da configuração clicando no **ícone Notificações** na barra de ferramentas e visualizando a **folha Notificações** . 

    >**Nota**: Pode levar algum tempo para que a implementação de recomendações neste laboratório seja refletida pelo Secure Score. Verifique periodicamente o Secure Score para determinar o impacto da implementação desses recursos. 

> Resultados: Você integrou o Microsoft Defender for Cloud e implementou recomendações de máquina virtual. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.
