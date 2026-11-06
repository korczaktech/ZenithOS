# ZenithOS

**ZenithOS** é o projeto de um sistema operacional independente da Korczak Technologies. Sua proposta é oferecer um ambiente integrado para desenvolvimento, produtividade e experiência de usuário (UX), com serviços, ferramentas e aplicativos do ecossistema Korczak HUB.

Este README é a especificação arquitetural central do ZenithOS. Define identidade, objetivos, componentes, nomes oficiais, contratos entre subsistemas e requisitos de evolução. Não é uma declaração de que todos os recursos descritos já estejam implementados.

| Propriedade | Definição |
|---|---|
| Nome | ZenithOS |
| Versão inicial registrada | 0.0.1 |
| Organização | Korczak Technologies |
| Linguagem de sistema | Nebula |
| Extensão de código | `.nbx` |
| Boot planejado | Zenith Bootloader (ZBL), via UEFI |
| Kernel | Zenith Kernel (ZK), monolítico modular |
| Sistema de arquivos | ZenithFS |
| Arquitetura inicial do repositório | x86-64 |
| Ambientes de integração | Linux, Windows, macOS e navegador |
| Terminal nativo | Zeus |
| Meta de memória | Até 1 GB de RAM para a configuração de referência |
| Foco | Desenvolvimento, produtividade e UX |

---

# 1. Visão do sistema

O ZenithOS deverá ser um sistema integrado, e não apenas uma interface gráfica sobre outro sistema operacional.

Seus objetivos principais são:

- Inicialização, kernel e subsistemas próprios.
- Ambiente gráfico e experiência de usuário consistentes.
- Integração nativa com os aplicativos do Korczak HUB.
- Integração com Aurora EDS e Morok AI.
- Terminal de sistema próprio, chamado Zeus.
- Terminal utilizável também pelo navegador.
- Desenvolvimento de aplicações e do próprio sistema em Nebula.
- Sistemas de baixo nível projetados para o ZenithOS.
- Conectividade por Wi-Fi e Bluetooth.
- Integração com dispositivos, arquivos, rede e periféricos.
- Uso eficiente de memória, armazenamento e energia.
- Compatibilidade e interoperabilidade com ambientes Linux, Windows e macOS.

A experiência deve priorizar inicialização simples, navegação clara, consistência visual, acessibilidade, resposta rápida e ferramentas integradas.

# 2. Princípios obrigatórios

1. **Independência:** o sistema não deve depender de uma instalação de Linux, Windows ou macOS para funcionar como sistema operacional.
2. **Nebula como linguagem do sistema:** código-fonte de kernel, serviços, drivers, aplicativos nativos e ferramentas próprias deve ser escrito em Nebula.
3. **Baixo nível original:** memória, processos, escalonamento, comunicação, drivers, segurança e sistema de arquivos devem possuir implementação e contratos próprios.
4. **Integração:** os aplicativos Korczak devem compartilhar identidade, autenticação, notificações, configurações e interfaces de integração quando aplicável.
5. **Eficiência:** a arquitetura deve reduzir consumo de RAM, CPU, armazenamento e energia.
6. **Segurança:** isolamento entre processos, permissões explícitas, validação de entradas e proteção de recursos.
7. **UX:** interface coerente, acessível, previsível e personalizável.
8. **Portabilidade:** separar o núcleo independente de arquitetura das implementações específicas de hardware.
9. **Transparência:** distinguir funcionalidades implementadas, experimentais e planejadas.
10. **Sem dependência de linguagens externas:** Assembly, C e C++ não devem ser linguagens de implementação do ZenithOS. O compilador Nebula deve gerar o código de máquina necessário.

# 3. Arquitetura em camadas

A arquitetura deve separar componentes de hardware, núcleo, serviços, integração e experiência de usuário.

```text
                  ZENITHOS
                     |
        +------------+------------+
        |                         |
        v                         v
   Zenith Desktop             Zenith Web
        |                         |
        +------------+------------+
                     |
             Zenith UX Layer
                     |
        +------------+------------+
        |            |            |
        v            v            v
   Aplicativos    Serviços    Integrações
        |            |            |
        |            |            +-- Korczak HUB
        |            |            +-- Aurora EDS
        |            |            +-- Morok AI
        |            |
        |            +-- Zeus
        |            +-- Rede
        |            +-- Segurança
        |            +-- Arquivos
        |
        +-------------------------+
                     |
              Zenith System API
                     |
        +------------+------------+
        |            |            |
        v            v            v
      Kernel       Drivers       IPC
        |
        +-- Memória
        +-- Processos e threads
        +-- Escalonador
        +-- Syscalls
        +-- Segurança
        +-- VFS
        +-- Energia
        |
        v
       HAL
        |
        v
      Hardware
```

O **HAL (Hardware Abstraction Layer)** separa as interfaces comuns das particularidades de cada arquitetura e dispositivo. O kernel não deve depender diretamente da interface gráfica ou de aplicativos.

# 4. Inicialização e boot

## 4.1 Zenith Bootloader (ZBL)

O ZBL é o componente responsável pelo processo de inicialização.

Responsabilidades:

- Identificar e validar a plataforma de inicialização.
- Preparar o ambiente mínimo de execução.
- Obter o mapa de memória disponibilizado pelo firmware.
- Carregar o kernel e os módulos essenciais.
- Preparar parâmetros de inicialização.
- Transferir controle ao ponto de entrada do kernel.
- Registrar informações de boot para diagnóstico.

A interface inicial planejada é UEFI. O firmware é uma interface de plataforma, não um sistema operacional hospedeiro.

## 4.2 Sequência de inicialização

1. Firmware inicia o ZBL.
2. ZBL valida os componentes de boot.
3. Memória e parâmetros de inicialização são preparados.
4. Kernel Zenith é carregado.
5. HAL e subsistemas essenciais são inicializados.
6. Gerenciador de memória e escalonador são ativados.
7. Drivers essenciais e sistema de arquivos são preparados.
8. Serviços de sistema são iniciados.
9. Ambiente gráfico é carregado.
10. Sessão do usuário é aberta.

O boot deverá possuir tratamento de falhas, logs, validação de integridade e caminhos de recuperação.

# 5. Kernel e sistemas de baixo nível

## 5.1 Zenith Kernel (ZK)

O ZK é o núcleo monolítico modular do sistema. Componentes essenciais executam em Ring 0 quando aplicável, enquanto aplicações e serviços isoláveis executam em Ring 3.

Responsabilidades:

- Gerenciamento de memória física e virtual.
- Processos, threads e escalonamento.
- Interrupções e exceções.
- Chamadas de sistema.
- Comunicação entre processos.
- Sincronização e operações atômicas.
- Segurança e controle de privilégios.
- Gerenciamento de dispositivos.
- Sistema de arquivos e armazenamento.
- Temporizadores e energia.

A modularidade deve permitir separar responsabilidades e testar subsistemas sem transformar cada componente em um serviço remoto desnecessário.

## 5.2 Gerenciamento de memória

O subsistema de memória deverá oferecer:

- Mapeamento de memória física e virtual.
- Paginação e proteção de páginas.
- Alocadores de kernel e de usuário.
- Regiões compartilhadas controladas.
- Proteção contra acesso fora dos limites.
- Mapeamento de memória de dispositivos.
- Estatísticas de uso e diagnóstico.
- Estratégias de recuperação em pressão de memória.

## 5.3 Processos e escalonamento

O gerenciador de processos deverá controlar criação, execução, suspensão, finalização, permissões e recursos.

O escalonador deverá considerar prioridade, responsividade, justiça, tarefas de sistema, cargas interativas e economia de energia.

## 5.4 Syscalls

As chamadas de sistema serão a fronteira controlada entre Ring 3 e Ring 0. A API deverá validar parâmetros, permissões, endereços e recursos antes de executar operações privilegiadas.

## 5.5 Interprocess Communication (ZIPC)

**ZIPC — Zenith Interprocess Communication** é a camada de comunicação entre processos.

Interfaces previstas:

- Canais de mensagens.
- Filas.
- Eventos e sinais.
- Memória compartilhada com permissões.
- Sincronização.
- Notificações entre serviços.

O protocolo deve validar identidade do processo, direitos de acesso, tamanho das mensagens e ciclo de vida dos recursos.

# 6. Hardware e drivers

## 6.1 Zenith Driver Framework (ZDF)

**ZDF — Zenith Driver Framework** é o framework próprio de drivers.

Deverá definir:

- Registro e descoberta de dispositivos.
- Identificadores e classes de hardware.
- Ciclo de vida dos drivers.
- Comunicação com barramentos.
- Interrupções.
- DMA quando suportado.
- Gerenciamento de energia.
- Isolamento e validação de operações.
- Interfaces estáveis entre drivers e kernel.

## 6.2 Arquiteturas

O repositório inicia com foco em x86-64. A arquitetura deverá ser preparada para separar código independente de CPU das implementações específicas.

ARM64 poderá ser adicionado mediante HAL, boot, interrupções, memória, ABI, drivers e testes próprios. Nenhuma arquitetura será declarada suportada apenas por constar no planejamento.

## 6.3 Wi-Fi

O subsistema de rede sem fio deverá contemplar:

- Descoberta de interfaces.
- Varredura de redes.
- Autenticação e associação.
- Gerenciamento de credenciais protegidas.
- Configuração de IP e DNS.
- Reconexão e monitoramento de sinal.
- Indicadores de estado na interface.
- Suporte progressivo a padrões e dispositivos.

A compatibilidade dependerá de drivers e firmware adequados. O sistema não deve presumir que todo adaptador Wi-Fi possui o mesmo protocolo ou capacidades.

## 6.4 Bluetooth

O subsistema Bluetooth deverá oferecer:

- Descoberta e pareamento.
- Gerenciamento de dispositivos confiáveis.
- Conexão e desconexão.
- Serviços e perfis suportados.
- Áudio e periféricos quando houver drivers correspondentes.
- Controle de permissões e visibilidade.
- Gestão de energia.

## 6.5 Outros dispositivos

A arquitetura deverá permitir suporte progressivo a:

- USB.
- Teclado e mouse.
- Áudio.
- Vídeo e GPU.
- Armazenamento.
- Câmeras.
- Impressoras.
- Touchpad e telas sensíveis ao toque.
- Sensores e dispositivos embarcados.

# 7. Armazenamento e ZenithFS

**ZenithFS** é o sistema de arquivos nativo planejado para o ZenithOS.

Responsabilidades:

- Arquivos e diretórios.
- Metadados e permissões.
- Operações de leitura e escrita.
- Journaling ou mecanismo equivalente de recuperação.
- Verificação de integridade.
- Controle de espaço.
- Cache e sincronização.
- Montagem de volumes.
- Recuperação após desligamento inesperado.

O **VFS (Virtual File System)** fornecerá uma interface comum para ZenithFS e formatos externos suportados.

A interoperabilidade com FAT32 e outros formatos deverá ocorrer por implementações específicas, sem tornar esses formatos dependências do sistema.

# 8. Rede e comunicação

A pilha de rede própria deverá ser organizada em camadas:

- Interfaces físicas e drivers.
- Ethernet e Wi-Fi.
- Endereçamento e configuração de rede.
- IPv4 e IPv6.
- TCP e UDP.
- DNS.
- Interfaces de sockets.
- TLS e validação de certificados.
- Firewall e controle de tráfego.

O sistema deverá oferecer APIs para aplicativos sem permitir acesso irrestrito a interfaces de baixo nível.

# 9. Segurança — ZSF

**ZSF — Zenith Security Framework** é a estrutura de segurança do sistema.

Princípios e recursos:

- Separação entre kernel e espaço de usuário.
- Identidade de usuários e grupos.
- Permissões por recurso.
- Isolamento de processos.
- Proteção de memória.
- Gerenciamento seguro de credenciais.
- Armazenamento protegido de segredos.
- Verificação de integridade de boot e atualizações.
- Política de execução de aplicativos.
- Logs de auditoria.
- Controle de rede.
- Proteção contra acesso não autorizado.

Aplicativos devem solicitar permissões de forma compreensível. A interface deverá informar o usuário sobre acesso a arquivos, microfone, câmera, rede, Bluetooth e outros recursos sensíveis.

# 10. Ambiente gráfico e UX

## 10.1 Zenith Desktop

O ambiente gráfico deverá ser próprio, integrado ao sistema e desenvolvido com foco em produtividade e consistência.

Componentes:

- Gerenciador de janelas.
- Compositor gráfico.
- Área de trabalho.
- Barra de tarefas e navegação.
- Menu de aplicativos.
- Central de notificações.
- Configurações.
- Gerenciador de arquivos.
- Gerenciador de tarefas.
- Gerenciador de dispositivos.
- Temas, fontes e acessibilidade.

## 10.2 Diretrizes de UX

- Interface clara e consistente.
- Navegação previsível.
- Baixa latência de interação.
- Atalhos configuráveis.
- Escala e densidade adaptáveis.
- Acessibilidade por teclado.
- Suporte a leitores de tela quando disponível.
- Temas claro e escuro.
- Feedback de operações e erros.
- Configurações centralizadas.
- Experiência coerente entre aplicativos.

## 10.3 Zenith Web

A experiência no navegador deverá oferecer acesso aos serviços e ferramentas que possam funcionar com segurança em ambiente web.

Não se deve confundir a versão web com o kernel inicializado no hardware: o navegador não concede acesso irrestrito ao sistema de arquivos, dispositivos ou privilégios do computador.

# 11. Integração com o Korczak HUB

O ZenithOS deverá vir com integração nativa ao ecossistema Korczak HUB. Os aplicativos devem compartilhar padrões de identidade, navegação, notificações e permissões.

A integração não significa que todos os serviços remotos sejam executados localmente. Recursos que dependam de infraestrutura externa deverão indicar conexão, disponibilidade e permissões necessárias.

## 11.1 Aplicativos Korczak

A arquitetura deverá prever integração com os aplicativos do HUB:

| Nome oficial | Responsabilidade no ecossistema |
|---|---|
| Kortex | Ambiente de edição e criação |
| Zetta | Aplicativo do ecossistema |
| Omega | Cofre e armazenamento |
| Eta | Aplicativo do ecossistema |
| Gamma | Aplicativo do ecossistema |
| Epsilon | Formulários e fluxos |
| Beta | Aplicativo do ecossistema |
| Iota | Comunicação e reuniões |
| Kash | Pessoas e gestão de usuários |
| Delta | Navegação e web |
| Zunder | Anotações |
| Bionder | Gestão de atividades |
| Munchen | Aplicativo do ecossistema |
| Theta | Aplicativo do ecossistema |
| Morok | Inteligência artificial |

Os nomes acima identificam produtos do ecossistema. As responsabilidades específicas devem respeitar as APIs e especificações de cada aplicativo, sem inventar funções não documentadas.

## 11.2 Aurora EDS

O **Aurora EDS** será integrado como estação de desenvolvimento de sistemas.

A integração deverá permitir:

- Acesso aos projetos autorizados.
- Edição de código Nebula.
- Integração com compilador e ferramentas Nebula.
- Acesso ao terminal Zeus.
- Navegação de arquivos e projetos.
- Execução e depuração quando suportadas.
- Comunicação com os serviços do ZenithOS por APIs controladas.

Aurora permanece um produto próprio, com identidade e ciclo de evolução independentes.

## 11.3 Morok AI

O **Morok AI** será a camada de inteligência artificial integrada ao ambiente.

Possibilidades de integração:

- Assistência durante o desenvolvimento.
- Pesquisa e consulta contextual.
- Ajuda para entender erros e documentação.
- Apoio à organização de tarefas.
- Integração com aplicativos autorizados.
- Automação mediante confirmação e permissões.
- Interface de conversa integrada à UX.

A execução de modelos poderá depender de serviço local ou remoto. O sistema deve informar quando houver processamento externo, consumo de recursos ou envio de dados, respeitando as configurações de privacidade.

# 12. Terminal nativo — Zeus

**Zeus** é o nome oficial do terminal do ZenithOS.

O Zeus deverá ser mais que uma interface de texto: será a ferramenta de administração, automação e desenvolvimento do sistema.

## 12.1 Responsabilidades

- Interpretar comandos.
- Executar programas Nebula.
- Gerenciar arquivos e diretórios.
- Consultar processos e recursos.
- Administrar serviços e dispositivos.
- Gerenciar rede e conexões.
- Consultar logs.
- Executar tarefas de desenvolvimento.
- Integrar-se ao Aurora EDS.
- Expor operações administrativas com controle de privilégios.

## 12.2 Linguagem de comandos

A interface deverá possuir comandos próprios e documentação consistente. Comandos administrativos devem validar permissões e apresentar confirmação quando houver risco de perda de dados ou alteração crítica.

O Zeus não deverá depender do Bash, PowerShell ou de outro shell para executar suas funções centrais.

## 12.3 Terminal no navegador

O Zeus deverá possuir uma interface web para uso pelo navegador.

Arquitetura proposta:

```text
Navegador
   |
   v
Interface Web Zeus
   |
   v
Canal autenticado
   |
   v
Serviço de sessões Zeus
   |
   v
Validação de identidade e permissões
   |
   v
Executor de comandos autorizado
   |
   v
Serviços do ZenithOS
```

A versão web deverá suportar sessões isoladas, entrada e saída interativas, encerramento de sessão, limites de recursos e auditoria.

Quando executada em navegador sem o ZenithOS hospedeiro, deverá operar em ambiente restrito, como WebAssembly ou serviço remoto autorizado. Uma página web não pode obter privilégios de kernel por conta própria.

# 13. Linguagem de implementação: Nebula

O objetivo é que o código-fonte próprio do ZenithOS seja **100% Nebula**.

Isso abrange:

- Bootloader.
- Kernel.
- HAL.
- Gerenciadores de memória e processos.
- Escalonador.
- Syscalls.
- Drivers.
- Sistema de arquivos.
- Pilha de rede.
- Segurança.
- Serviços.
- Interface gráfica.
- Aplicativos nativos.
- Zeus e ferramentas do sistema.

O compilador Nebula é desenvolvido em Rust no seu próprio repositório. Isso é uma ferramenta de construção, não uma dependência de execução do ZenithOS.

O ZenithOS não deverá exigir Assembly, C ou C++ como linguagens de implementação. Instruções de CPU e transições exigidas pela arquitetura deverão ser geradas pelo backend Nebula por meio de intrínsecos, primitivas tipadas e geração de código nativo. O formato de máquina e as interfaces de firmware são requisitos técnicos da plataforma, não linguagens-fonte do sistema.

Ferramentas externas poderão ser usadas na máquina de desenvolvimento para compilar, testar e depurar. Elas não deverão ser necessárias para inicializar ou operar o sistema instalado.

# 14. Integração com Linux, Windows e macOS

O ZenithOS deverá oferecer caminhos de interoperabilidade com as três famílias de sistemas.

É importante separar três objetivos:

1. **Executar ZenithOS:** inicializar o sistema em hardware ou máquina virtual sem depender de outro sistema operacional.
2. **Desenvolver ZenithOS:** permitir que ferramentas e fluxos de desenvolvimento sejam usados a partir de Linux, Windows e macOS.
3. **Integrar ambientes:** trocar arquivos, conectar serviços, acessar recursos de rede e interoperar com dispositivos e aplicações autorizadas.

## 14.1 Linux

A integração deverá contemplar distribuições Linux, sem exigir uma distribuição específica como base do ZenithOS.

Possibilidades:

- Transferência e interoperabilidade de arquivos.
- Ferramentas de desenvolvimento.
- Conexão de rede e serviços.
- Execução em máquinas virtuais.
- Integração de dispositivos suportados.

## 14.2 Windows

A integração deverá contemplar:

- Fluxo de desenvolvimento e validação.
- Imagens de máquina virtual.
- Compartilhamento de arquivos.
- Comunicação por rede.
- Ferramentas de instalação e recuperação compatíveis.

## 14.3 macOS

A integração deverá contemplar:

- Fluxo de desenvolvimento e validação.
- Execução em virtualização compatível com o hardware.
- Compartilhamento de arquivos.
- Comunicação por rede.
- Ferramentas de instalação e recuperação compatíveis.

A compatibilidade de virtualização, boot e dispositivos depende da arquitetura física e das capacidades do host. Nenhum dos três sistemas será uma dependência do kernel Zenith.

# 15. Meta de consumo de memória

A meta de projeto é operar com **no máximo 1 GB de RAM** na configuração de referência.

Esse limite deverá ser tratado como orçamento mensurável, não como garantia para qualquer hardware ou carga de trabalho.

O orçamento deverá separar:

- Kernel e estruturas essenciais.
- Drivers e buffers.
- Serviços de sistema.
- Ambiente gráfico e compositor.
- Aplicativos em segundo plano.
- Cache de arquivos.
- Integrações e sessões do Zeus.

Requisitos de eficiência:

- Carregamento sob demanda.
- Serviços iniciados apenas quando necessários.
- Liberação de recursos ociosos.
- Limites por processo.
- Cache adaptativo.
- Monitoramento de memória.
- Pressão de memória e recuperação controlada.
- Benchmarks com ambiente e carga documentados.

A meta de 1 GB deve especificar quais aplicativos estão abertos, resolução gráfica, dispositivos ativos e serviços habilitados. A integração com Morok AI ou aplicativos pesados não pode ser presumida dentro desse orçamento sem medições.

# 16. Energia e gerenciamento térmico

O sistema deverá oferecer:

- Estados de energia.
- Suspensão e retomada, conforme suporte da plataforma.
- Controle de brilho e dispositivos.
- Monitoramento térmico quando disponível.
- Políticas de desempenho e economia.
- Gerenciamento de bateria.
- Encerramento seguro.

# 17. Aplicativos e serviços de sistema

Os aplicativos nativos deverão usar APIs estáveis do ZenithOS, respeitar permissões e integrar-se ao ambiente gráfico.

A estrutura inicial prevê:

**Aplicativos de sistema**
- Gerenciador de arquivos.
- Configurações.
- Informações do sistema.
- Gerenciador de tarefas.
- Terminal Zeus.

**Utilitários**
- Gerenciador de arquivos compactados.
- Calculadora.
- Editor de texto.

**Serviços**
- Gerenciador de aplicativos.
- Gerenciador de sessões.
- Notificações.
- Rede.
- Segurança.
- Atualização.
- Indexação e pesquisa, quando habilitadas.

# 18. Atualizações e recuperação

O sistema deverá possuir mecanismo próprio de atualização, com:

- Verificação de origem e integridade.
- Assinatura de pacotes.
- Compatibilidade entre versões.
- Aplicação controlada de atualizações.
- Recuperação em caso de falha.
- Registro de alterações.
- Proteção contra downgrade não autorizado quando aplicável.

O processo de atualização não deve deixar o sistema sem inicialização após interrupção inesperada.

# 19. SDK e desenvolvimento

O **Zenith SDK** deverá fornecer interfaces e ferramentas para criação de aplicativos e componentes nativos.

Deverá incluir:

- APIs de sistema documentadas.
- Bibliotecas Nebula.
- Ferramentas de compilação e validação.
- Templates de projeto.
- Ferramentas de diagnóstico.
- Interfaces de drivers.
- Documentação de ABI e syscalls.
- Ambiente de testes e simulação.

Aurora EDS deverá ser uma das principais formas de desenvolver projetos para o ZenithOS, sem ser uma dependência obrigatória do kernel.

# 20. Testes, validação e desempenho

O projeto deverá possuir testes separados por subsistema:

| Área | Validação necessária |
|---|---|
| Boot | Inicialização e recuperação |
| Kernel | Memória, processos, interrupções e syscalls |
| Drivers | Descoberta, ciclo de vida e falhas |
| ZenithFS | Integridade, leitura, escrita e recuperação |
| Rede | Protocolos, conectividade e segurança |
| Wi-Fi | Descoberta, autenticação e reconexão |
| Bluetooth | Descoberta, pareamento e comunicação |
| ZSF | Isolamento, permissões e auditoria |
| ZIPC | Comunicação e isolamento |
| Gráficos | Renderização e responsividade |
| UX | Acessibilidade e consistência |
| Zeus | Comandos, permissões e sessões |
| Integrações | Autenticação, APIs e falhas de conexão |
| Memória | Consumo em cenários definidos |

QEMU será usado para validação inicial sempre que adequado. Testes em hardware real serão necessários para validar drivers, conectividade, energia, desempenho e compatibilidade de dispositivos.

Benchmarks deverão registrar hardware, versão, configuração, carga e método de medição.

# 21. Estrutura do repositório

A estrutura arquitetural do projeto está organizada por subsistema:

| Diretório | Responsabilidade |
|---|---|
| `boot/` | Zenith Bootloader e inicialização |
| `kernel/` | Núcleo, arquitetura, memória, processos e escalonador |
| `drivers/` | Zenith Driver Framework e drivers |
| `filesystem/` | ZenithFS, VFS e formatos externos |
| `ipc/` | Zenith Interprocess Communication |
| `security/` | Zenith Security Framework |
| `network/` | Rede e protocolos |
| `power/` | Energia e gerenciamento térmico |
| `graphics/` | Compositor e renderização |
| `desktop/` | Ambiente de trabalho |
| `ui/` | Componentes e experiência de usuário |
| `services/` | Serviços de sistema |
| `applications/` | Aplicativos nativos |
| `app_manager/` | Gerenciamento de aplicativos |
| `sdk/` | Ferramentas e APIs para desenvolvimento |
| `assets/` | Fontes, ícones, imagens e temas |
| `tests/` | Testes do sistema |
| `benchmarks/` | Medições de desempenho |

Diretórios podem existir antes de seus componentes estarem implementados. A estrutura do repositório não é, por si só, prova de funcionalidade.

# 22. Glossário oficial

| Nome | Significado |
|---|---|
| ZenithOS | Sistema operacional |
| ZBL | Zenith Bootloader |
| ZK | Zenith Kernel |
| HAL | Hardware Abstraction Layer |
| ZDF | Zenith Driver Framework |
| ZIPC | Zenith Interprocess Communication |
| ZSF | Zenith Security Framework |
| ZenithFS | Sistema de arquivos nativo |
| VFS | Virtual File System |
| Zenith Desktop | Ambiente gráfico |
| Zenith Web | Experiência de acesso pelo navegador |
| Zeus | Terminal do sistema |
| Zenith SDK | Kit de desenvolvimento |
| Nebula | Linguagem de implementação do sistema |
| Aurora EDS | Estação de Desenvolvimento de Sistemas integrada |
| Morok AI | Plataforma de inteligência artificial integrada |
| Korczak HUB | Ecossistema de aplicativos e serviços integrados |

Os nomes são contratos de identidade do projeto. Novos componentes deverão receber nomes documentados neste glossário antes de serem tratados como interfaces oficiais.

# 23. Estado de implementação

A versão inicial do repositório é um esqueleto arquitetural. A documentação define objetivos e contratos, não substitui código, testes ou validação em hardware.

Cada recurso deverá ser identificado em uma destas categorias:

- **Implementado:** código integrado e testes correspondentes aprovados.
- **Experimental:** existe implementação, mas há limitações conhecidas ou validação incompleta.
- **Planejado:** requisito arquitetural ainda sem implementação funcional.
- **Não suportado:** recurso explicitamente fora do escopo da versão atual.

A situação de cada subsistema deve ser atualizada conforme o código e os testes evoluírem. Não declarar suporte a Wi-Fi, Bluetooth, compatibilidade multiplataforma, limite de RAM ou execução web sem evidências verificáveis.

# 24. Critérios de conclusão do ZenithOS

O sistema só deverá ser considerado operacional quando, no mínimo:

1. Inicializar por meio do ZBL em uma plataforma suportada.
2. Iniciar o ZK e seus subsistemas essenciais.
3. Gerenciar memória física e virtual com proteção.
4. Executar processos isolados.
5. Oferecer syscalls estáveis e verificadas.
6. Gerenciar dispositivos por interfaces documentadas.
7. Ler e gravar volumes com integridade.
8. Oferecer rede e segurança funcionais.
9. Iniciar ambiente gráfico utilizável.
10. Executar o Zeus com permissões adequadas.
11. Executar aplicativos nativos Nebula.
12. Integrar os aplicativos Korczak por interfaces documentadas.
13. Possuir mecanismos de atualização e recuperação.
14. Passar por testes automatizados e testes em ambientes suportados.
15. Demonstrar consumo de memória dentro do orçamento definido para a configuração de referência.

---

**ZenithOS — sistema operacional independente, integrado e desenvolvido em Nebula.**
