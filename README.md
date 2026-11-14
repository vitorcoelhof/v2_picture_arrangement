# Gerador de Anexo Fotográfico

Aplicação web para engenheiros civis gerarem anexos fotográficos formatados (`.docx`) para laudos técnicos. Roda inteiramente no navegador — sem servidor, sem instalação.

## Como usar

Abra o arquivo `index.html` diretamente no Chrome, Edge ou Firefox. Nenhum build step, nenhum servidor necessário. Funciona offline.

## Funcionalidades

- **Salvar e continuar depois** — o botão **Salvar** gera um arquivo `.anexo` com fotos, legendas, ordem e configurações; **Abrir** (ou arrastar o `.anexo` na tela) retoma exatamente de onde parou, pronto para incluir mais fotos ou reordenar. Além disso, o trabalho atual tem backup automático no navegador: se a aba fechar, ao reabrir aparece "Continuar de onde parei"
- **Upload em lote** — selecione múltiplas fotos ou arraste uma pasta inteira
- **Todos os formatos de imagem** — JPG, PNG e também `.heic/.heif` do iPhone (inclusive fotos HDR), convertidos automaticamente sem precisar de app externo
- **Ordenação por drag-and-drop** — reorganize as fotos antes de gerar o documento
- **Legendas editáveis** — cada foto tem um campo de texto individual
- **Numeração automática** — sequencial, com prefixo configurável (Foto / Figura / Imagem) e **número inicial ajustável**
- **Logo e marca d'água** — faça upload uma vez; a logo é reutilizada como marca d'água em baixa opacidade
- **Persistência local** — a logo fica salva no `localStorage` entre sessões
- **Layout 2×2** — 4 fotos por página, com legenda abaixo de cada imagem
- **Saída em `.docx`** — compatível com Microsoft Word 2016+, download direto no navegador
- **Nome do arquivo configurável** — defina o nome antes de gerar
- **Tamanho do papel** — A4 (padrão) ou Carta

## Layout do documento gerado

```
┌─────────────┬─────────────┐
│   Foto 1    │   Foto 2    │
│  [imagem]   │  [imagem]   │
│  Legenda    │  Legenda    │
├─────────────┼─────────────┤
│   Foto 3    │   Foto 4    │
│  [imagem]   │  [imagem]   │
│  Legenda    │  Legenda    │
└─────────────┴─────────────┘
```

Cabeçalho com logo da empresa em todas as páginas. Marca d'água ao fundo.

## Tecnologia

- HTML + CSS + JavaScript puros — arquivo único, sem framework
- [`docx.js`](https://docx.js.org/) via CDN para geração do `.docx` no cliente
- [`libheif-js`](https://github.com/catdad-experiments/libheif-js) embutido no próprio arquivo (wasm em base64) para decodificar `.heic/.heif` — funciona offline, sem CDN
- `localStorage` para persistência da logo entre sessões
- `IndexedDB` para o backup automático do trabalho em andamento
- Sem dependências instaladas, sem npm, sem build

## Requisitos

- Navegador moderno (Chrome 90+, Edge 90+, Firefox 88+)
- A conversão de `.heic` funciona totalmente offline (a biblioteca vem embutida). A geração do `.docx` usa o `docx.js` via CDN, que é carregado uma vez e fica em cache
