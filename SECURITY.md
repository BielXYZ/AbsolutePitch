# Segurança e privacidade

## Em poucas palavras

O Treino de Ouvido roda no navegador e não possui backend, login, banco de
dados remoto ou coleta intencional de dados pessoais. Ele não usa microfone,
não grava áudio e não envia respostas ou samples para um servidor.

O progresso, o tema e os caches ficam no armazenamento local do navegador.
Por isso, não salve senhas, tokens ou outras informações confidenciais na
aplicação.

## Dados e armazenamento

O projeto pode usar:

- `localStorage` para progresso e tema;
- IndexedDB para bytes dos arquivos de áudio;
- Cache Storage e Service Worker para funcionamento offline.

Esses dados pertencem à origem do site. Qualquer código executado na mesma
origem pode, em princípio, acessar esse armazenamento. O navegador também pode
limpá-lo, especialmente em modo privado, com pouco espaço disponível ou sem
armazenamento persistente autorizado.

A aplicação tenta solicitar persistência, mas não pode obrigar o navegador a
concedê-la. Portanto, a retenção do progresso nunca deve ser considerada um
backup. Para apagar os dados, limpe o armazenamento do site nas configurações
do navegador.

## Áudio e origem

O áudio é local e usa Web Audio API. Para reduzir riscos de reprodução
automática, o contexto de áudio só é criado ou retomado durante uma ação do
usuário. O navegador pode continuar exigindo um clique em **Iniciar fase**.

Execute a aplicação por HTTP/HTTPS. Abrir `Pitch.html` com `file://` não é um
modo suportado para carregar os samples nem para ativar o Service Worker.

O Service Worker aceita apenas requisições da própria origem e os recursos são
referenciados por caminhos relativos.

## Práticas do projeto

- Não há segredos ou credenciais no código.
- Respostas de `fetch` são verificadas antes do uso.
- Falhas de áudio e armazenamento são informadas, não tratadas como sucesso.
- A aleatoriedade usa `crypto.getRandomValues()`.
- Caches possuem versão para permitir invalidação.
- O projeto evita inserir conteúdo fornecido pelo usuário como HTML.

## Como reportar um problema

Não publique uma vulnerabilidade em issue ou pull request público antes de uma
correção. Use o recurso **Security Advisories** do GitHub, quando disponível,
ou um canal privado dos mantenedores.

Inclua:

1. descrição do problema e impacto;
2. arquivo e passos para reproduzir;
3. navegador, versão e sistema operacional;
4. prova de conceito mínima, se necessária.

Não envie senhas, tokens, dados pessoais ou arquivos confidenciais.

Tentaremos confirmar o recebimento em até sete dias e manter o reporte privado
durante a investigação. Solicite autorização antes de testar qualquer
instalação que não seja sua.
