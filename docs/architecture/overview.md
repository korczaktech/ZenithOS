# Visão geral da arquitetura

## Plataforma

- Arquitetura inicial: x86_64.
- Firmware: UEFI.
- Kernel: monolítico modular.
- Níveis de privilégio: Ring 0 e Ring 3.
- Imagem de disco de desenvolvimento: RAW.
- Ambiente inicial de validação: QEMU.

## Componentes principais

ZBL, Zenith Kernel, ZMM, ZScheduler, ZPM, ZIM, ZTS, ZIPC, ZenithFS, ZDF, ZSF, Zenith Network Stack, Zenith Desktop e Zenith System API.

## Princípios

Implementar componentes próprios quando houver viabilidade técnica e benefício justificável. Interfaces de hardware e protocolos deverão seguir suas especificações oficiais. Desempenho e segurança serão demonstrados por testes, não presumidos.

## Dependência da linguagem

O compilador Nebula precisa suportar compilação freestanding para x86_64, geração de executáveis UEFI PE32+ e kernel ELF64, ABI, intrínsecas, atomics e operações privilegiadas.
