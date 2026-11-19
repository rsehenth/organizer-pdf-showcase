[English](README.md) | [Português (Brasil)](README.pt-BR.md)

# Organizer PDF

Um conjunto de ferramentas de PDF para Windows, para organizar materiais escolares e executar tarefas comuns com PDFs: montar, dividir, extrair e otimizar documentos, tudo localmente e sem alterar os arquivos originais.

<!-- SCREENSHOT_PENDENTE: ainda não há screenshot aprovado e atual. Adicione aqui como assets/screenshots/<nome>.png depois de capturar e revisar uma. -->

## Visão geral

O Organizer PDF nasceu como ferramenta para professores que recebem PDFs grandes formados por vários materiais separados e precisam separar, nomear e exportar cada um. Hoje é uma bancada geral de PDF com quatro módulos, histórico local e recuperação de trabalho em andamento.

Princípios de projeto:

- **Local primeiro.** Leitura, organização, montagem, divisão, extração, otimização, histórico e presets funcionam sem internet. A etapa opcional de IA só roda com chave explícita e consentimento a cada análise.
- **Originais nunca são alterados.** Toda operação trabalha sobre uma cópia privada verificada, identificada por SHA-256, e os resultados são publicados sem sobrescrever arquivos existentes.
- **Resultado verificado.** Antes de publicar, o resultado é conferido contra a origem (quantidade de páginas, ordem, dimensões, rotação, texto e, quando aplicável, aparência renderizada).

## Principais recursos

- **Organização pedagógica.** Abrir um PDF grande, analisá-lo localmente (texto, regras e OCR), revisar as fronteiras sugeridas e então criar, editar, unir, dividir, renomear, confirmar e exportar materiais.
- **Montagem de múltiplas origens.** Montar um PDF novo com páginas de vários arquivos, na ordem final, com suporte a arrastar e soltar.
- **Edição de páginas.** Reordenar, duplicar, girar 90°, remover e desfazer (até 200 passos), com pré-visualização do resultado final.
- **Dividir e extrair.** Dividir por intervalos, por blocos de N páginas ou uma página por arquivo; extrair uma seleção ordenada em um arquivo, um arquivo por página ou um por sequência contígua.
- **Otimização.** Três perfis explícitos (sem perdas, equilibrado, compacto). Um resultado que não fica menor que o original não é mantido, a menos que o usuário peça.
- **Operações em lote.** Processamento em fila, em série, com verificação de cancelamento entre etapas e arquivos.
- **Histórico e presets.** Cada operação registra seu desfecho (páginas, arquivos produzidos, destino, origens com SHA-256, avisos). Repetir uma operação ou aplicar um preset apenas preenche o painel; nada é executado sem confirmação.
- **Persistência e recuperação.** O trabalho de montagem é salvo automaticamente como rascunho e oferecido para recuperação após um encerramento inesperado; cada origem é revalidada por existência, SHA-256 e quantidade de páginas antes de reabrir.
- **Interface nativa para Windows** desenvolvida com PySide6.

## Módulos do aplicativo

| Módulo | Finalidade |
|---|---|
| Organizar Materiais | Analisar um PDF grande, revisar fronteiras, confirmar e exportar materiais individuais. |
| Montar PDF | Compor um documento novo com páginas de um ou mais PDFs. |
| Dividir, Extrair e Otimizar | Três operações independentes que compartilham o mesmo fluxo de trabalho verificado. |
| Histórico e Presets | Consultar operações anteriores, reabrir configurações, repetir operações e gerenciar presets. |

## Tecnologias

| Área | Tecnologia |
|---|---|
| Linguagem | Python 3.12+ |
| Interface | PySide6 (Qt for Python) |
| Motor de PDF | PyMuPDF |
| Persistência | SQLite |
| OCR (opcional, PDFs formados por imagens) | Tesseract OCR com dados do idioma português |
| IA (opcional) | API da OpenAI, apenas com chave e consentimento explícitos |
| Testes | pytest |
| Empacotamento (interno) | PyInstaller (pasta única) e Inno Setup |

## Visão da arquitetura

```text
 Interface (PySide6)   janela principal · montador · ferramentas · histórico · diálogos
        │
 Serviços              montagem · lote · otimização · exportação · pipeline de análise
        │
 Modelos de domínio    composição e planejamento de intervalos (sem Qt, sem E/S de PDF)
        │
 Armazenamento (SQLite) documentos e grupos · montagens · histórico e presets
        │
 Camada de PDF         cópias privadas verificadas · publicação exclusiva · PyMuPDF
```

- Os modelos de composição e de planejamento de intervalos independem de Qt e de E/S de PDF, o que os mantém diretamente testáveis.
- Toda escrita passa por um único pipeline de trabalho e por um único mecanismo de cópia verificada; não existe uma segunda forma de gravar PDF.
- Os dados do usuário ficam em uma pasta por usuário em `%LOCALAPPDATA%`. O conteúdo dos PDFs não é copiado para o banco; as montagens guardam caminho, SHA-256 e quantidade de páginas de cada origem. O banco não é criptografado em repouso.
- Senhas, tokens e credenciais não são gravados no histórico nem nos presets.

## Validação técnica

A Fase 7A (validação técnica isolada, 11/11/2026) terminou com **936 testes aprovados, 8 ignorados e zero falhas** (944 casos distintos), em ambiente isolado no Windows 11 com Python 3.14.10, PyMuPDF 1.28.2 e PySide6 6.11.2. Os dois testes que dependem de PDFs privados estão entre os ignorados. Todos os PDFs de entrada foram gerados pelos próprios testes.

Trata-se apenas de validação automatizada. **A homologação funcional no Windows (Fase 7B) não foi concluída.**

## Estado atual do projeto

- O desenvolvimento funcional dos módulos planejados está concluído (até a Fase 6.7).
- A validação técnica automatizada (Fase 7A) está concluída, com as ressalvas acima.
- A homologação funcional no Windows está **pendente**. O produto **não está validado para distribuição** e nenhum instalador ou executável é publicado.

Limitações conhecidas: não há exclusão nem renomeação de montagens salvas na interface, importação/exportação de presets, PDF/A, assinatura digital ou criptografia dos PDFs gerados, nem desfazer entre sessões. Documentos assinados, XFA e protegidos por senha são tratados de forma conservadora (recusados ou com aviso, conforme a operação).

## Documentação

A documentação técnica fica no repositório privado de desenvolvimento:

- `docs/DEVELOPER-GUIDE.md` — arquitetura, módulos, persistência e limitações (em português)
- `docs/DEVELOPMENT-CLOSURE-PHASE6.md` e `docs/PHASE7-VALIDATION-PLAN.md`
- `docs/validation/` — relatório e evidências da Fase 7A
- `docs/licensing/` e `packaging/windows/THIRD_PARTY_NOTICES.md` — licenciamento de terceiros

## Licenciamento e disponibilidade

O código-fonte é privado e nenhuma licença sobre ele é concedida aqui. Nenhum executável, instalador ou release é distribuído.

O produto depende de componentes de terceiros, em especial PyMuPDF (AGPL-3.0 ou licença comercial da Artifex) e PySide6/Qt (LGPL-3.0/GPL). A distribuição pública está **bloqueada** até que a questão de licenciamento do PyMuPDF seja resolvida e as obrigações da LGPL do Qt sejam cumpridas. Os avisos de terceiros são preservados no repositório privado.
