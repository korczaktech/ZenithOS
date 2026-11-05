# ZenithOS

Sistema operacional independente para x86_64, com firmware UEFI, kernel monolítico modular e código-fonte do sistema escrito em Nebula.

## Objetivos

- Boot próprio por meio do Zenith Bootloader (ZBL).
- Kernel e subsistemas desenvolvidos especificamente para o ZenithOS.
- Execução isolada em Ring 0 e Ring 3.
- Sistema de arquivos nativo ZenithFS.
- Framework próprio de drivers (ZDF), IPC (ZIPC) e segurança (ZSF).
- Desenvolvimento e validação inicial no QEMU.

## Requisitos de engenharia

O compilador Nebula deverá oferecer suporte freestanding/bare-metal, target x86_64, ABI definida, geração de ELF64 e PE32+, intrínsecas de CPU, operações atômicas e inicialização sem runtime de sistema hospedeiro.

O código-fonte do ZenithOS será Nebula. Ferramentas externas poderão ser utilizadas no computador de desenvolvimento para compilar, testar e depurar, mas não serão dependências de execução do sistema.

## Estrutura

- `boot/`: ZBL e inicialização UEFI.
- `kernel/`: núcleo, arquitetura, memória, processos, scheduler e syscalls.
- `drivers/`: ZDF e drivers.
- `filesystem/`: ZenithFS, VFS, FAT32 e armazenamento.
- `ipc/` e `security/`: comunicação e proteção.
- `network/` e `power/`: rede e energia.
- `graphics/`, `desktop/` e `ui/`: ambiente gráfico.
- `services/`, `applications/` e `sdk/`: serviços e ecossistema.
- `tests/` e `benchmarks/`: validação e medição.

## Estado

A estrutura do repositório é um esqueleto arquitetural. A presença de diretórios ou arquivos de documentação não significa que os subsistemas estejam implementados. Cada componente será considerado funcional somente após implementação e testes reproduzíveis.
