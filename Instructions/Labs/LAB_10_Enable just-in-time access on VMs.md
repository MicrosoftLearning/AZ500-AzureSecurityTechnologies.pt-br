---
lab:
  title: 10 – Habilitar o acesso just-in-time em VMs
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# Laboratório 10: Habilitar o acesso just-in-time em VMs

# Manual de laboratório do aluno

## Cenário do laboratório

Como Engenheiro de Segurança do Azure em uma empresa de serviços financeiros, você é responsável por proteger os recursos do Azure, incluindo VMs (máquinas virtuais) que hospedam aplicativos críticos. A equipe de segurança identificou que o acesso aberto contínuo às VMs aumenta o risco de ataques de força bruta e acesso não autorizado. Para atenuar isso, o CISO (Diretor de Segurança da Informação) solicitou que você habilite o acesso à VM JIT (just-in-time) em uma VM específica do Azure usada para processar transações financeiras.

## Objetivos do laboratório

Neste laboratório, você completará os seguintes exercícios:

- Exercício 1: Habilitar o JIT em suas VMs pelo portal do Azure.

- Exercício 2: Solicitar acesso a uma VM que tenha o JIT habilitado no portal do Azure.

## Instruções para o exercício 

### Exercício 1: Habilitar o JIT em suas VMs a partir de máquinas virtuais do Azure

>**Observação**: você pode habilitar o JIT em uma VM nas páginas de máquinas virtuais do Azure do portal do Azure.

1. Na caixa de pesquisa na parte superior do portal, insira **máquinas virtuais**. Selecione **Máquinas virtuais** nos resultados da pesquisa.

2. Selecione **myVM**.
 
3. Selecione **Configuração** na seção **Configurações** de **myVM.**
   
4. Em **Acesso à VM Just-In-Time** selecione **Habilitar Just-In-Time**.

5. Em **Acesso à VM just-in-time,** clique no link que diz **Abrir Microsoft Defender para Nuvem.**

6. Por padrão, o acesso just-in-time para a VM usa estas configurações:

   - Máquinas do Windows
   
     - Porta RDP: 3389
     - Acesso máximo permitido: três horas
     - Endereços IP de origem permitidos: todos

   - Máquinas do Linux
     - Porta SSH: 22
     - Acesso máximo permitido: três horas
     - Endereços IP de origem permitidos: todos
   
7. Por padrão, o acesso just-in-time para a VM usa estas configurações:

   - Na guia **configurada**, clique com o botão direito do mouse na VM à qual você deseja adicionar uma porta e selecione Editar.

   ![Captura da tela de configuração de acesso just-in-time para a VM.](../media/just-in-time-vm-access.png)
   
   - Em **Configuração de acesso JIT à VM**, você pode editar as configurações existentes de uma porta já protegida ou pode adicionar uma nova porta personalizada.
   - Ao terminar de editar as portas, selecione **Salvar**.   

### Exercício 2: Solicitar acesso a uma VM habilitada para JIT na página de conexão da máquina virtual do Azure.

>**Observação**: quando uma VM tem um JIT habilitado, você precisa solicitar acesso para se conectar a ele. Você pode solicitar acesso em qualquer uma das maneiras com suporte, independentemente de como você habilitou o JIT.
   
1. No portal do Azure, abra a página de máquinas virtuais.

2. Selecione a VM à qual você deseja se conectar e abra a página **conectar**.

   - O Azure verifica se o JIT está habilitado nessa VM.

        - Se o JIT não estiver habilitado para a VM, você será solicitado a habilitá-lo.
    
        - Se o JIT estiver habilitado, selecione **Solicitar acesso** para passar uma solicitação de acesso com o IP solicitante, o intervalo de tempo e as portas que foram configuradas para essa VM.
    
   ![Captura de tela de Conectar a uma máquina virtual usando JIT.](../media/vm-connection-for-vm1.png)

> **Resultados**: você explorou vários métodos sobre como habilitar o JIT em suas VMs e como solicitar acesso a VMs que têm o JIT habilitado no Microsoft Defender para Nuvem.
