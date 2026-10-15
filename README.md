# Treino de Ouvido

Aplicação web offline-first para treinar o reconhecimento de notas musicais. O jogador escolhe um instrumento e uma fase, ouve uma nota e precisa selecionar a resposta correta em até dois segundos.

## Funcionalidades

- 20 notas por fase.
- Limite de 2 segundos para cada resposta.
- Fases progressivas com notas dentro e fora da progressão.
- 21 instrumentos com samples reais:
  piano, baixo elétrico, fagote, violoncelo, clarinete, contrabaixo, flauta,
  trompa, violão acústico, guitarra elétrica, violão nylon, harmônio, harpa,
  órgão, saxofone, trombone, trompete, tuba, violino e xilofone.
- Roda de respostas centralizada, com botões distribuídos pela circunferência.
- Indicador circular de tempo que transita gradualmente de verde para amarelo e vermelho.
- Shepard tone de preparação com nota-base aleatória e sem repetição imediata.
- Som de clique ao iniciar e ao selecionar uma opção.
- Som de level up e confetes ao atingir pelo menos 18 acertos.
- Resumo final com acertos, total e porcentagem.
- Tema claro/escuro.
- Interface em português do Brasil.
- Cache local para reduzir downloads e atrasos de áudio.
- Aplicação instalável como PWA quando executada por HTTP/HTTPS.

## Como executar

O projeto é estático e não possui etapa de build ou dependências npm.

### Servidor local recomendado

Alguns recursos, como `fetch`, IndexedDB e Service Worker, funcionam melhor em um servidor local:

```bash
python3 -m http.server 8000
```

Depois, abra:

```text
http://localhost:8000/Pitch.html
```

Também é possível usar qualquer servidor estático equivalente.

> Abrir `Pitch.html` diretamente com `file://` não é recomendado. O próprio jogo bloqueia o carregamento dos samples nesse modo para evitar falhas silenciosas.

## Como jogar

1. Escolha um instrumento.
2. Escolha uma fase desbloqueada.
3. Clique em **Iniciar fase**.
4. Aguarde o clique, o atraso de preparação e o Shepard tone.
5. Ouça a nota.
6. Selecione a resposta em menos de dois segundos.
7. Repita até completar as 20 notas.

São necessários pelo menos 18 acertos para concluir uma fase e desbloquear a próxima quando aplicável.

## Organização do projeto

```text
.
├── Pitch.html              # Interface, estilos e lógica principal
├── Samples/                # Samples WAV por instrumento e efeitos MP3
│   ├── piano/
│   ├── violin/
│   ├── ...
│   ├── Click.mp3
│   └── LevelUp.mp3
├── favicon.svg             # Ícone da aplicação
├── manifest.webmanifest    # Metadados da PWA
├── sw.js                   # Cache offline via Service Worker
├── README.md
└── SECURITY.md
```

## Como o áudio funciona

### Samples de instrumentos

O objeto `SAMPLES` em `Pitch.html` lista os arquivos disponíveis para cada instrumento. Quando uma nota precisa ser tocada, o jogo escolhe o sample mais próximo e ajusta o `playbackRate` para alcançar a altura desejada.

Os arquivos são carregados, convertidos para mono e decodificados pelo Web Audio API. Depois, ficam em `audioBuffers` durante a sessão.

### Efeitos

- `Samples/Click.mp3`: início da fase e seleção de instrumento/fase.
- `Samples/LevelUp.mp3`: conclusão com 18 ou mais acertos.

O clique é pré-carregado quando possível para reduzir latência.

### Cache persistente

O jogo usa duas camadas:

1. **IndexedDB**: guarda os bytes dos arquivos de áudio no navegador.
2. **Cache Storage/Service Worker**: mantém recursos e samples disponíveis offline quando a aplicação é servida por HTTP/HTTPS.

O IndexedDB usa:

```text
Banco: absolute-pitch-audio
Store: files
Versão de cache: v1
```

O jogo tenta solicitar armazenamento persistente com `navigator.storage.persist()`. A decisão final pertence ao navegador; o usuário ainda pode apagar os dados do site ou o sistema pode negar a persistência.

Quando uma fase começa, `preloadPhaseAudio()` identifica e decodifica antecipadamente os samples necessários. Assim, a nota não deve depender de um download no instante da resposta.

Na primeira execução de uma combinação de instrumento/fase pode haver carregamento inicial. Nas seguintes, o áudio deve vir do cache local.

## Fases e progressão

As progressões base estão no objeto `PHASES` em `Pitch.html`. Cada entrada possui:

```javascript
[notasDaProgressao, notasForaDoTom]
```

`buildPhases()` combina essas progressões com as oitavas disponíveis e filtra combinações sem samples compatíveis com o instrumento.

O progresso é salvo no `localStorage`, separado por instrumento:

```text
absolute-pitch-highest-phase-piano
absolute-pitch-highest-phase-violin
absolute-pitch-highest-phase-flute
```

O valor representa o índice da maior fase desbloqueada. A preferência de tema usa a chave `absolute-pitch-theme`.

## Aleatoriedade

Os sorteios do jogo usam helpers baseados em `crypto.getRandomValues()`:

- embaralhamento da sequência;
- escolha da oitava e da nota;
- nota-base do Shepard tone;
- propriedades do confete.

`randomInt()` usa rejeição para evitar viés de distribuição ao converter números de 32 bits para intervalos menores. O Shepard tone também evita repetir a mesma nota-base duas vezes seguidas.

## Acessibilidade e interação

- Botões possuem rótulos acessíveis.
- As respostas podem ser acionadas por teclado.
- A tecla `F` inicia uma fase quando nenhum controle de formulário está em foco.
- O tema escuro adapta o contraste dos componentes.
- O indicador de tempo é visual; a instrução informa explicitamente o limite de dois segundos.

## Atualizando samples

1. Coloque o arquivo no diretório correto dentro de `Samples/`.
2. Adicione o nome à entrada correspondente em `SAMPLES`.
3. Verifique se o nome é compatível com o formato usado pelo código, como `C4`, `Cs4` ou `As3`.
4. Incremente `AUDIO_CACHE_VERSION` se um arquivo já existente tiver sido substituído e usuários precisarem receber a nova versão.
5. Atualize `CACHE_VERSION` em `sw.js` quando o cache do Service Worker precisar ser invalidado.

## Limitações conhecidas

- O armazenamento persistente depende do navegador e da permissão concedida.
- Limpar os dados do site remove progresso e cache local.
- O progresso não é sincronizado entre dispositivos.
- Áudio depende de suporte ao Web Audio API e de uma interação do usuário em navegadores que bloqueiam reprodução automática.
- A primeira carga de muitos samples pode levar tempo em uma conexão lenta.
- Não há servidor, banco de dados remoto, conta de usuário ou sincronização online.

## Tecnologias

- HTML5
- CSS3
- JavaScript sem framework
- Web Audio API
- IndexedDB
- Cache API
- Service Worker
- Web App Manifest

## Desenvolvimento e validação

Não há dependências para instalar. Para verificar alterações básicas:

```bash
git diff --check
```

Teste manualmente em um navegador servido por HTTP/HTTPS, especialmente:

- primeira carga com rede lenta;
- segunda carga offline;
- seleção de instrumento e fase;
- fases com muitas notas;
- modo escuro;
- resposta dentro e fora do tempo;
- conclusão com 18 ou mais acertos.

Consulte [SECURITY.md](./SECURITY.md) para reportar vulnerabilidades.
