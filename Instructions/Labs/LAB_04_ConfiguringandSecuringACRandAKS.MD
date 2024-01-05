---
lab:
  title: 04 – Configuração e proteção do ACR e do AKS
  module: 'Module 01 - Secure compute, storage, and databases'
---

# Laboratório 04: Configuração e proteção do ACR e do AKS
# Manual de laboratório do aluno

## Cenário do laboratório

Você foi solicitado a implantar uma prova de conceito com o Registro de Contêiner do Azure e o Serviço de Kubernetes do Azure. Especificamente, a prova de conceito deve demonstrar:

- Como usar o Dockerfile para criar uma imagem.
- Como usar o Registro de Contêiner do Azure para armazenar imagens.
- Como configurar um Serviço de Kubernetes do Azure.
- Como proteger e acessar aplicativos de contêiner interna e externamente. 

> Para todos os recursos neste laboratório, estamos usando a região  **Leste dos EUA**. Verifique com seu instrutor se esta é a região a ser usada para a aula. 

## Objetivos do laboratório

Neste laboratório você realizará os seguintes exercícios:

- Exercício 1: configurar e proteger o ACR e AKS

## Diagrama Configurar e proteger o ACR e AKS

![imagem](https://user-images.githubusercontent.com/91347931/157532250-1104a829-792a-4b6d-beff-fe976e2d5d4b.png)

## Instruções

## Arquivos de laboratório:

- **\\Allfiles\\Labs\\09\\nginxexternal.yaml**
- **\\Allfiles\\Labs\\09\\nginxinternal.yaml**

### Exercício 1: configurar e proteger o ACR e AKS

### Tempo estimado: 45 minutos

> Para todos os recursos deste laboratório, estamos usando a região **Leste (EUA)**. Verifique com seu instrutor se esta é a região a ser usada para sua aula. 

Neste exercício, você realizará as seguintes tarefas:

- Tarefa 1: criar um Registro de Contêiner do Azure
- Tarefa 2: criar um Dockerfile, criar um contêiner e enviá-lo por push para o Registro de Contêiner do Azure
- Tarefa 3: criar um cluster do Serviço de Kubernetes do Azure
- Tarefa 4: conceder permissões ao cluster do AKS para acessar o ACR
- Tarefa 5: implantar um serviço externo no AKS
- Tarefa 6: verificar se você pode acessar um serviço externo hospedado no AKS
- Tarefa 7: implantar um serviço interno no AKS
- Tarefa 8: verificar se você pode acessar um serviço interno hospedado no AKS

#### Tarefa 1: criar um Registro de Contêiner do Azure

Nesta tarefa, você criará um grupo de recursos do laboratório em um Registro de Contêiner do Azure

1. Entre no portal do Azure **`https://portal.azure.com/`**.

    >**Observação**: entre no portal do Azure usando uma conta que tenha a função de Proprietário ou Colaborador na assinatura do Azure que você está usando para este laboratório e a função Administrador global no locatário do Microsoft Entra associado a essa assinatura.

2. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. Se solicitado, clique em **Bash** e **Criar armazenamento**.

3. Verifique se o **Bash** está selecionado no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

4. Na sessão Bash no painel do Cloud Shell, execute o seguinte para criar um grupo de recursos para este laboratório:

    ```sh
    az group create --name AZ500LAB09 --location eastus
    ```

5. Na sessão Bash no painel do Cloud Shell, execute o seguinte para verificar se o grupo de recursos foi criado:

    ```
    az group list --query "[?name=='AZ500LAB09']" -o table
    ```

6. Na sessão Bash no painel do Cloud Shell, execute o seguinte para criar uma instância do Registro de Contêiner do Azure (ACR) (O nome do ACR deve ser globalmente único): 

    ```sh
    az acr create --resource-group AZ500LAB09 --name az500$RANDOM$RANDOM --sku Basic
    ```

7. Na sessão Bash no painel do Cloud Shell, execute o seguinte para confirmar que o novo ACR foi criado:

    ```sh
    az acr list --resource-group AZ500LAB09
    ```

    >**Observação**: guarde o nome do ACR. Você precisará dele na próxima tarefa.

#### Tarefa 2: criar um Dockerfile, criar um contêiner e enviá-lo por push para o Registro de Contêiner do Azure

Nesta tarefa, você criará um Dockerfile, criará uma imagem do Dockerfile e a implantará no ACR. 

1. Na sessão Bash no painel do Cloud Shell, execute o seguinte para criar um Dockerfile para criar uma imagem baseada em Nginx: 

    ```sh
    echo FROM nginx > Dockerfile
    ```

2. Na sessão Bash no painel do Cloud Shell, execute o seguinte para criar uma imagem do Dockerfile e enviá-la por push para o novo ACR. 

    >**Observação**: o ponto à direita no final da linha de comando é obrigatório. Ele designa o diretório atual como o local do Dockerfile. 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az acr build --resource-group AZ500LAB09 --image sample/nginx:v1 --registry $ACRNAME --file Dockerfile .
    ```

    >**Observação**: aguarde até que o comando seja concluído com êxito. Isso pode levar cerca de dois minutos.

3. Feche o painel do Cloud Shell.

4. No portal do Azure, navegue até o grupo de recursos **AZ500Lab09** e, na lista de recursos, clique na entrada que representa a instância do Registro de Contêiner do Azure provisionada na tarefa anterior.

5. No painel Registro de Contêiner, na seção **Serviços**, clique em **Repositórios**. 

6. Verifique que a lista de repositórios inclui a nova imagem de contêiner chamada **sample/nginx**.

7. Clique na entrada **sample/nginx** e verifique a presença da tag **v1** que identifica a versão da imagem.

8. Clique na entrada **v1** para exibir o manifesto da imagem.

    >**Observação**: o manifesto inclui o hash sha256, a data de criação do manifesto e as entradas da plataforma. 

#### Tarefa 3: criar um cluster do Serviço de Kubernetes do Azure

Nesta tarefa, você criará um Serviço de Kubernetes do Azure e analisará os recursos implantados. 

1. Na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Serviços de Kubernetes** e pressione a tecla **Enter**.

2. No painel **Serviços de Kubernetes**, selecione  **+ Criar** e, no menu suspenso, clique em **+ Criar um cluster de Kubernetes**

3. Na guia **Básico** do painel **Criar cluster de Kubernetes**, selecione **Configuração predefinida do cluster** e **Desenvolvimento/Teste ($)**. Agora especifique as seguintes configurações (deixe outras com os valores padrão):

    |Configuração|Valor|
    |----|----|
    |Assinatura|o nome da assinatura do Azure que você está usando neste laboratório|
    |Grupo de recursos|**AZ500LAB09**|
    |Nome do cluster do Kubernetes|**MyKubernetesCluster**|
    |Região|**(EUA) Leste dos EUA**|
    |Zonas de disponibilidade |**Nenhuma**|
    |Método de dimensionamento|**Manual**|
    |Contagem de nós|**1**|

4. Clique em **Avançar: Pools de Nós >** e, na guia **Pools de Nós** do painel **Criar cluster de Kubernetes**, especifique as seguintes configurações (deixe outras com os valores padrão):

    |Configuração|Valor|
    |----|----|
    |Habilitar nós virtuais|caixa de seleção desmarcada|
    
5. Clique em **Avançar: Acesso >**, na guia **Acesso** do painel **Criar cluster de Kubernetes**, aceite os padrões e clique em **Avançar: Rede >**. 

6. Na guia **Rede** do painel **Criar cluster de Kubernetes**, especifique as seguintes configurações (deixe outras com os valores padrão):

    |Configuração|Valor|
    |----|----|
    |Configuração de rede|**CNI do Azure**|
    |Prefixo do nome DNS|**Deixe o valor padrão**|

    >**Observação**: o AKS pode ser configurado como um cluster privado. Isso atribui um IP privado ao servidor da API para garantir que o tráfego de rede entre seu servidor da API e seus pools de nós permaneça somente na rede privada. Para obter mais informações, confira a página [Criar um cluster privado do Serviço de Kubernetes do Azure](https://docs.microsoft.com/en-us/azure/aks/private-clusters).

7. Clique em **Avançar: Integrações >** e, na guia **Integrações** do painel **Criar cluster do Kubernetes**, defina **Monitoramento de contêiner** como **Desativado**. 

    >**Observação**: em cenários de produção, convém ativar o monitoramento. O monitoramento é desativado neste caso, uma vez que não é coberto no laboratório. 

8. Clique em **Examinar + Criar** e em **Criar**.

    >**Observação**: aguarde até que a implantação seja concluída. Isso pode levar cerca de 10 minutos.

9. Quando a implantação for concluída, no portal do Azure, na caixa de texto **Pesquisar recursos, serviços e documentos** na parte superior da página do portal do Azure, digite **Grupos de recursos** e pressione a tecla **Enter**.

10. Na folha **Grupos de recursos**, na lista de grupos de recursos, observe um novo grupo de recursos chamado **MC_AZ500LAB09_MyKubernetesCluster_eastus** que contém componentes dos nós AKS. Revise os recursos nesse grupo de recursos. 
    
11. Navegue de volta para a folha **Grupos de recursos** e clique na entrada **AZ500LAB09** . 

    >**Observação**: na lista de recursos, observe o AKS Cluster e a rede virtual correspondente.

12. No portal do Azure, abra uma sessão do Bash no Cloud Shell. 

    >**Observação**: verifique se o **Bash** aparece no menu suspenso no canto superior esquerdo do painel do Cloud Shell.

13. Na sessão do Bash no painel do Cloud Shell, execute o seguinte para se conectar ao cluster do Kubernetes:

    ```sh
    az aks get-credentials --resource-group AZ500LAB09 --name MyKubernetesCluster
    ```

14. Na sessão do Bash no painel do Cloud Shell, execute o seguinte para listar os nós do cluster do Kubenetes: 

    ```sh
    kubectl get nodes
    ```

    >**Observação**: verifique se o **Status** do nó do cluster está listado como **Pronto**.

#### Tarefa 4: conceder permissões ao cluster do AKS para acessar o ACR e gerenciar sua rede virtual

Nesta tarefa, você concederá ao cluster do AKS permissão para acessar o ACR e gerenciar sua rede virtual. 

1. Na sessão do Bash no painel do Cloud Shell, execute o seguinte para configurar o cluster do AKS para usar a instância do Registro de Contêiner do Azure criada anteriormente neste laboratório. 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az aks update -n MyKubernetesCluster -g AZ500LAB09 --attach-acr $ACRNAME
    ```

    >**Observação**: este comando concede a atribuição de função 'acrpull' ao ACR. 

    >**Observação**: esse comando pode levar alguns minutos para ser concluído. 

2. Na sessão do Bash no painel do Cloud Shell, execute o seguinte para conceder ao cluster do AKS a função Colaborador para sua rede virtual. 

    ```sh
    RG_AKS=AZ500LAB09

    RG_VNET=MC_AZ500LAB09_MyKubernetesCluster_eastus    

    AKS_VNET_NAME=aks-vnet-30198516
    
    AKS_CLUSTER_NAME=MyKubernetesCluster
    
    AKS_VNET_ID=$(az network vnet show --name $AKS_VNET_NAME --resource-group $RG_VNET --query id -o tsv)
    
    AKS_MANAGED_ID=$(az aks show --name $AKS_CLUSTER_NAME --resource-group $RG_AKS --query identity.principalId -o tsv)
    
    az role assignment create --assignee $AKS_MANAGED_ID --role "Contributor" --scope $AKS_VNET_ID
    ```

#### Tarefa 5: implantar um serviço externo no AKS

Nesta tarefa, você baixará os arquivos de manifesto, editará o arquivo YAML e aplicará suas alterações ao cluster. 

1. Na sessão do Bash no painel do Cloud Shell, clique no ícone **Carregar/Baixar arquivos**, no menu suspenso, clique em **Carregar**, na caixa de diálogo **Abrir**, navegue até o local onde você baixou os arquivos de laboratório, selecione **\\Allfiles\\Labs\\09\\nginxexternal.yaml** e clique em **Abrir**. Em seguida, selecione **\\Allfiles\\Labs\\09\\nginxinternal.yaml** e clique em **Abrir**.

2. Na sessão do Bash no painel do Cloud Shell, execute o seguinte para identificar o nome da instância do Registro de Contêiner do Azure:

    ```sh
    echo $ACRNAME
    ```

    >**Observação**: registre o nome da instância do Registro de Contêiner do Azure. Você precisará dela posteriormente nesta tarefa.

3. Na sessão do Bash no painel do Cloud Shell, execute o seguinte para abrir o arquivo nginxexternal.yaml, para que você possa editar seu conteúdo. 

    ```sh
    code ./nginxexternal.yaml
    ```

    >**Observação**: este é o arquivo yaml *externo*.

4. No painel do editor, role para baixo até a **linha 24** e substitua o espaço reservado **`<ACRUniquename>`** pelo nome do ACR.

5. No painel do editor, no canto superior direito, clique no ícone de **reticências**, clique em ** Salvar** e em **Fechar editor**. 

6. Na sessão do Bash no painel do Cloud Shell, execute o seguinte para aplicar a alteração ao cluster:

    ```sh
    kubectl apply -f nginxexternal.yaml
    ```

7. Na sessão do Bash no painel do Cloud Shell, analise a saída do comando executado na tarefa anterior para verificar se a implantação e o serviço correspondente foram criados. 

    ```
    deployment.apps/nginxexternal created
    service/nginxexternal created
    ```

#### Tarefa 6: verificar se você pode acessar um serviço externo hospedado no AKS

Nesta tarefa, verifique se o contêiner pode ser acessado externamente usando o endereço IP público.

1. Na sessão do Bash no painel do Cloud Shell, execute o seguinte para recuperar informações sobre o serviço nginxexternal, incluindo nome, tipo, endereços IP e portas. 

    ```sh
    kubectl get service nginxexternal
    ```

2. Na sessão do Bash no painel do Cloud Shell, analise a saída e registre o valor na coluna IP externo. Isso será necessário na próxima etapa. 

3. Abra uma nova guia do navegador e acesse o endereço IP que você identificou na etapa anterior.

4. Verifique se a página **Bem-vindo ao nginx!** é exibida. 

#### Tarefa 7: implantar um serviço interno no AKS

Nesta tarefa, você implantará o serviço interno no AKS. 

1. Na sessão de Bash no painel do Cloud Shell, execute o seguinte para abrir o arquivo nginxintenal.yaml, para que você possa editar seu conteúdo. 

    ```sh
    code ./nginxinternal.yaml
    ```

    >**Observação**: Este é o arquivo yaml *interno*.

2. No painel do editor, role para baixo até a linha que contém a referência à imagem do contêiner e substitua o espaço reservado **`<ACRUniquename>`** pelo nome do ACR.

3. No painel do editor, no canto superior direito, clique no ícone de **reticências**, clique em ** Salvar** e em **Fechar editor**. 

4. Na sessão do Bash no painel do Cloud Shell, execute o seguinte para aplicar a alteração ao cluster:

    ```sh
    kubectl apply -f nginxinternal.yaml
    ```

5.  Na sessão Bash no painel do Cloud Shell, analise a saída para verificar se sua implantação e o serviço foram criados:

    ```
    deployment.apps/nginxinternal created
    service/nginxinternal created
    ```

6. Na sessão Bash no painel do Cloud Shell, execute o seguinte para recuperar informações sobre o serviço nginxinternal, incluindo nome, tipo, endereços IP e portas. 

    ```sh
    kubectl get service nginxinternal
    ```

7. Na sessão Bash no painel do Cloud Shell, analise a saída. O External-IP é, neste caso, um endereço IP privado. Se estiver em um estado **Pendente**, execute o comando anterior novamente.

    >**Observação**: guarde esse endereço IP. Você precisará dele na próxima tarefa. 

    >**Observação**: para acessar o ponto de extremidade de serviço interno, você se conectará interativamente a um dos pods em execução no cluster. 

    >**Observação**: como alternativa, você pode usar o endereço CLUSTER-IP.

#### Tarefa 8: verificar se você pode acessar um serviço interno hospedado no AKS

Nesta tarefa, você usará um dos pods em execução no cluster do AKS para acessar o serviço interno. 

1. Na sessão Bash no painel do Cloud Shell, execute o seguinte para listar os pods no namespace padrão no cluster do AKS:

    ```sh
    kubectl get pods
    ```

2. Na lista dos pods, copie a primeira entrada na coluna **NOME**.

    >**Observação**: este é o pod que você usará nas etapas subsequentes.

3. Na sessão Bash no painel do Cloud Shell, execute o seguinte para conectar-se interativamente ao primeiro pod (substitua o espaço reservado `<pod_name>` pelo nome copiado na etapa anterior):

    ```sh
    kubectl exec -it <pod_name> -- /bin/bash
    ```

4. Na sessão Bash no painel do Cloud Shell, execute o seguinte para verificar que o site nginx está disponível por meio do endereço IP privado do serviço (substitua o espaço reservado `<internal_IP>` pelo endereço IP registrado na tarefa anterior):

    ```sh
    curl http://<internal_IP>
    ```

5. Feche o painel do Cloud Shell.

> Resultado: você configurou e protegeu o ACR e o AKS.


**Limpar recursos**

> Lembre-se de remover todos os recursos do Azure que acabam de ser criados e que você não usa mais. A remoção de recursos não utilizados garante que não haverá custos inesperados.

1. No portal do Azure, abra o Cloud Shell clicando no primeiro ícone no canto superior direito do portal do Azure. 

2. No menu suspenso no canto superior esquerdo do painel do Cloud Shell, selecione **PowerShell** e, quando solicitado, clique em **Confirmar**.

3. Na sessão do PowerShell no painel do Cloud Shell, execute o seguinte para remover os grupos de recursos criados neste laboratório:
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB09" -Force -AsJob
    ```

4.  Feche o painel do **Cloud Shell**. 
