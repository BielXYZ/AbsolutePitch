# Treino de Ouvido

Um jogo para treinar o reconhecimento de notas musicais. Escolha um
instrumento, ouça a nota e responda em até dois segundos. Com 18 acertos em
20, você desbloqueia a próxima fase.

## Por que experimentar?

- 21 instrumentos com samples reais.
- Fases progressivas, do básico às notas fora da progressão.
- Respostas por toque, clique ou teclado.
- Tema claro e escuro.
- Funciona offline depois do primeiro carregamento.
- Pode ser instalado como aplicativo (PWA).
- Sem conta, anúncios ou servidor próprio.

## Comece em dois minutos

O projeto é estático: não precisa de build nem de dependências npm.

```bash
python3 -m http.server 8000
```

Abra [http://localhost:8000/Pitch.html](http://localhost:8000/Pitch.html) no
navegador. Usar HTTP/HTTPS é importante para áudio, cache e modo offline.

### Como jogar

1. Escolha o instrumento.
2. Selecione uma fase desbloqueada.
3. Clique em **Iniciar fase**.
4. Ouça a nota e escolha a resposta em até dois segundos.
5. Faça pelo menos 18 acertos para avançar.

Também é possível iniciar com `F` e responder usando as teclas indicadas nos
botões.

## Áudio e funcionamento offline

O jogo usa a Web Audio API e carrega os samples da pasta `Samples/`. Na primeira
vez, alguns arquivos podem demorar para baixar; depois, eles ficam disponíveis
no cache do navegador.

Cada nota usa um ataque de volume curto antes do decaimento do sample, evitando
estalos causados por uma entrada abrupta do áudio.

O áudio é iniciado somente após uma ação do usuário, pois alguns navegadores
bloqueiam reprodução automática. Se o navegador não oferecer Web Audio API ou
mantiver o áudio bloqueado, a tela informa o problema.

O cache usa IndexedDB, Cache Storage e Service Worker. A aplicação tenta pedir
armazenamento persistente, mas essa decisão pertence ao navegador. Mesmo que a
solicitação seja recusada, o jogo continua funcionando; apenas existe o risco
de o navegador apagar o cache ou o progresso.

> Não abra o arquivo diretamente com `file://`. Use um servidor local ou uma
> hospedagem HTTPS; caso contrário, os samples e o modo offline podem falhar.

## O que é salvo?

Somente no navegador:

- progresso das fases, separado por instrumento;
- preferência de tema;
- cache dos arquivos de áudio e da aplicação.

Nada é sincronizado entre dispositivos e o projeto não envia esses dados para
um servidor. Limpar os dados do site apaga o progresso e o cache.

## Estrutura do projeto

```text
Pitch.html              # Interface e lógica do jogo
Samples/                # Samples WAV e efeitos MP3
Worker.js               # Cache offline
App.webmanifest         # Instalação como PWA
Icon.svg                # Ícone
SECURITY.md             # Política de segurança
```

Para trocar um sample, atualize o arquivo correspondente e incremente
`AUDIO_CACHE_VERSION` em `Pitch.html`. Se necessário, incremente também
`CACHE_VERSION` em `Worker.js`.

## Limitações importantes

- É necessário um navegador com Web Audio API.
- A primeira interação do usuário pode ser necessária antes de qualquer som.
- Armazenamento persistente não é garantido pelo navegador.
- O cache inicial pode ser grande e levar tempo em conexões lentas.
- O progresso é local e não tem backup automático.

## Validação

Não há dependências para instalar. Para uma verificação rápida:

```bash
git diff --check
```

Consulte [SECURITY.md](./SECURITY.md) para entender os cuidados de segurança
e saber como reportar um problema de forma responsável.
