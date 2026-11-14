# Gerador de Anexo Fotográfico (v2)

**Versão:** 2.0 (em desenvolvimento) · **Atualizado em:** 2026-11-14

## Objetivo

Aplicação web para engenheiros civis montarem o anexo fotográfico de um laudo técnico: adicionar fotos, organizar a ordem, escrever legendas e exportar um arquivo Word (`.docx`). A tela mostra cada página como ela vai sair no Word. Roda inteira no navegador, sem servidor e sem instalação. Nada é enviado para a internet.

Esta é a v2, um projeto separado do `picture_arrangement` original.

## Como usar

1. Abra `index.html` (ou `sem_senha.html`) no Chrome, Edge ou Firefox.
2. Adicione as fotos (selecione várias ou arraste uma pasta). JPG, PNG e HEIC/HEIF do iPhone são aceitos.
3. Reordene as fotos arrastando. Escreva a legenda de cada uma.
4. Abra a aba **Páginas** para ver a folha A4 ou Carta, como ficará no Word.
5. Escolha quantas fotos vão em cada página (veja abaixo).
6. Clique em **Salvar** para guardar o trabalho em um arquivo `.anexo`.
7. Clique em **Gerar Documento** para baixar o `.docx`.

### Fotos por página

- O padrão é **4 fotos por página**. Ele se troca na barra lateral (1 a 8) e vale para todas as páginas.
- Cada página da aba Páginas tem o seletor **Fotos nesta página** (1 a 8). A quantidade fica presa à página, não à foto: se você reordenar, as fotos mudam de página e cada página mantém a sua quantidade.
- As fotos entram nas páginas na ordem da galeria.
- **Voltar todas para 4 por página** desfaz os ajustes feitos página a página.
- Não existe mais o modo Automático. Quem decide a quantidade é você.

### Uma por página (foto em destaque)

- Marque **Uma por página** no card da foto na galeria, ou no botão ★ sobre a foto na aba Páginas.
- A foto marcada ocupa uma página só dela, no maior tamanho que cabe na folha (cerca de 22,6 cm de altura em foto em pé; foto deitada é limitada pela largura de 17 cm).
- A marcação interrompe o grupo: as fotos antes dela ficam em uma página incompleta e não são completadas com fotos de depois.
- Essa página incompleta não tem seletor próprio (aparece desativado, "cortada por foto em destaque"). Para mudar a quantidade dali em diante, ajuste a página seguinte ao destaque.
- Fotos em destaque não contam na quantidade por página.

### Salvar e continuar depois

- **Salvar** gera um `.anexo` com fotos, legendas, ordem, marcações e quantidades por página. **Abrir** (ou arrastar o `.anexo` para a tela) retoma do ponto salvo.
- Há também um backup automático no navegador (IndexedDB). Se a aba fechar, ao reabrir aparece "Continuar de onde parei".
- **Guarde uma cópia do `.anexo` fora do navegador.** Limpar os dados do navegador apaga o backup automático.
- **Novo projeto** zera as fotos e as quantidades por página e volta o padrão para 4.
- Arquivos `.anexo` antigos abrem normalmente. Os que foram salvos com o modo Automático abrem com 4 por página.

## Regras de layout

| Item | Valor |
|---|---|
| Papel | A4 (padrão) ou Carta |
| Margens (A4) | superior 25 mm, inferior 18 mm, laterais 20 mm |
| Logo no cabeçalho | altura máxima 12 mm |
| Fotos | mantêm a proporção original, sem corte e sem barras cinzas |
| Fotos na mesma linha | têm a mesma altura, qualquer que seja a orientação |
| Borda nas fotos | nenhuma |
| Rodapé | "Página X de Y" |
| Legenda | nunca é cortada |

- Legenda grande: só a página dela encolhe as fotos. O tamanho da legenda é decisão sua.
- Uma página sem legenda grande mantém o tamanho normal; a última página incompleta usa o tamanho máximo do modo.
- Cada página da tela vira uma seção do `.docx`, com as mesmas fotos e os mesmos tamanhos.

## Avisos na tela

- **Âmbar:** a foto ficou abaixo do tamanho mínimo escolhido (Pequeno, Médio ou Grande).
- **Vermelho:** a foto ficou abaixo de 4 cm. Texto: "Foto abaixo de 4 cm. Use menos fotos nesta página ou encurte a legenda."

## Aproveitamento da página

Configuração opcional na barra lateral. Reduz a altura que as fotos podem ocupar em cada página, deixando mais respiro.

- **Valores:** 75%, 80%, 85%, 90%, 95% e **100% (padrão)**. Com 100% nada muda.
- **O que muda:** só o tamanho das fotos. Quantas fotos vão em cada página e a ordem continuam iguais.
- **Foto "Uma por página":** não é afetada. Continua ocupando a folha toda.
- **Só reduz onde a altura é o limite.** Com fotos em pé e deitadas na mesma linha, a largura costuma limitar antes, e a diferença entre os valores pode ser pequena.
- **Salvar e retomar:** o valor vai no `.anexo`. Um arquivo do projeto original, salvo com 85% (o padrão antigo), abre com 85%. Troque para 100% se quiser.
- **"Novo projeto":** mantém o percentual escolhido (tratado como preferência sua).
- **Avisos de tamanho:** o aviso vermelho (abaixo de 4 cm) vale em qualquer percentual. O aviso âmbar com percentual abaixo de 100% está em revisão (ver Limites conhecidos).

Exemplo (10 fotos mistas, A4, 6 por página): maior lado da menor foto de 6,8 cm a 100%, 5,8 cm a 85% e 5,1 cm a 75%.

## Problemas comuns

| Sintoma | O que fazer |
|---|---|
| Aparece o aviso âmbar ou vermelho | Reduza o número de fotos da página ou encurte a legenda. |
| Uma página tem menos fotos do que o seletor indica | Uma foto "Uma por página" cortou a página. Ajuste a página seguinte. |
| Fotos mudaram de página depois de reordenar | É esperado: a quantidade fica presa à página. |
| Perdi o trabalho ao fechar a aba | Reabra e use "Continuar de onde parei". Se limpou os dados do navegador, abra o `.anexo` guardado. |
| O `.docx` não gera | O `docx.js` vem de CDN e precisa ser carregado ao menos uma vez com internet. |
| Foto HEIC não aparece | Use Chrome, Edge ou Firefox atuais. A conversão é embutida e funciona offline. |

## Limites conhecidos

- O `.docx` ainda **não foi aberto no Word real**. A conferência foi feita na estrutura do arquivo. Ao abrir o primeiro arquivo, verifique se há página em branco, se a última linha cabe e se o rodapé aparece.
- As fotos JPEG ficam nomeadas `.png` dentro do `.docx` (limitação do `docx.js` 7.8.2). O conteúdo é JPEG. Não foi confirmado se o Word reclama.
- Fotos muito estreitas ou muito largas (menos de cerca de 1,5 cm) podem sair levemente distorcidas.
- Não foi testado com fotos reais (HEIC, rotação EXIF) nem com logo real.

## Tecnologia

- HTML, CSS e JavaScript puros, em um arquivo só, sem framework e sem build.
- [`docx.js`](https://docx.js.org/) 7.8.2 via CDN, para gerar o `.docx`.
- [`libheif-js`](https://github.com/catdad-experiments/libheif-js) embutido (wasm em base64), para HEIC/HEIF.
- `localStorage` para a logo. `IndexedDB` para o backup automático.

## Arquivos

| Arquivo | Uso |
|---|---|
| `index.html` | Versão com senha de licença (idêntica a `com senha.html`) |
| `com senha.html` | Versão com senha de licença |
| `sem_senha.html` | Versão sem senha |

## Requisitos

Chrome 90+, Edge 90+ ou Firefox 88+.

## Histórico

| Versão | Data | Mudanças |
|---|---|---|
| 2.0 | 2026-11-14 | Projeto novo, separado do original. Quantidade de fotos por página escolhida pelo usuário (padrão 4). Foto "Uma por página". Páginas A4/Letter iguais ao `.docx`. Sem modo Automático. Sem aviso de legenda longa. "Aproveitamento da página" de 75% a 100% (padrão 100%): só reduz o tamanho das fotos, o limite do aviso âmbar sai do tamanho a 100% e uma foto nunca fica maior que a 100%. `.anexo` sem o campo abre a 100%. Estrutura do `.docx` validada pelo QA; Word real ainda não testado. |
