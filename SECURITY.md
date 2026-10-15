# Política de segurança

## Escopo

Esta política se aplica ao código e aos recursos deste repositório, incluindo:

- `Pitch.html`;
- `sw.js`;
- `manifest.webmanifest`;
- arquivos de áudio e imagens distribuídos em `Samples/` e na raiz.

O projeto é uma aplicação estática executada no navegador. Atualmente não possui backend próprio, autenticação, banco de dados remoto, API privada ou coleta intencional de dados pessoais.

## Versões suportadas

Como o projeto é mantido como uma aplicação em desenvolvimento, a versão publicada na branch padrão é a referência suportada. Correções de segurança devem ser aplicadas ao estado mais recente do repositório.

| Versão | Suporte |
| --- | --- |
| Branch padrão / versão mais recente | Suportada |
| Versões antigas ou cópias locais | Sem garantia de suporte |

## Como reportar uma vulnerabilidade

Não publique detalhes de uma vulnerabilidade em issue pública, pull request público ou comentário público antes de uma correção estar disponível.

Prefira o recurso **Security Advisories** do GitHub, quando disponível para o repositório. Caso não esteja disponível, abra uma comunicação privada aos mantenedores pelos canais privados configurados na página do repositório.

O relatório deve incluir:

1. Descrição clara do problema.
2. Arquivo e trecho afetado.
3. Passos reproduzíveis.
4. Impacto esperado.
5. Versão, navegador e sistema operacional usados.
6. Prova de conceito mínima, quando necessária.
7. Sugestão de correção, se houver.

Não envie senhas, tokens, dados pessoais ou arquivos confidenciais no relatório.

## Prazo de resposta

Tentaremos:

- confirmar o recebimento em até 7 dias;
- avaliar impacto e reproduzibilidade;
- manter o reporte privado durante a investigação;
- informar o andamento quando houver uma correção ou mitigação;
- publicar um aviso somente quando for seguro fazê-lo.

Esses prazos são objetivos de manutenção, não uma garantia de SLA.

## Escala de severidade

A avaliação considera explorabilidade, impacto e pré-condições:

- **Crítica**: execução remota, comprometimento amplo ou exposição grave de dados.
- **Alta**: impacto relevante com exploração relativamente simples.
- **Média**: impacto limitado, exigindo condições específicas ou interação.
- **Baixa**: impacto pequeno, defesa em profundidade ou problema difícil de explorar.

Como a aplicação roda localmente no navegador e não possui backend, muitos problemas terão impacto limitado ao próprio navegador, à origem do site ou aos dados locais do usuário.

## Práticas de segurança do projeto

- Não guardar segredos, tokens ou credenciais no código.
- Usar caminhos relativos para recursos locais.
- Validar respostas de `fetch` antes de processar áudio.
- Mostrar erros de carregamento em vez de tratar falhas como sucesso.
- Manter o Service Worker limitado à própria origem.
- Usar `crypto.getRandomValues()` para decisões aleatórias do jogo.
- Versionar caches para permitir invalidação controlada.
- Evitar inserir conteúdo fornecido pelo usuário como HTML.
- Manter dependências externas fora do caminho crítico sempre que possível.

## Dados locais e privacidade

O projeto salva localmente:

- progresso de fases no `localStorage`;
- preferência de tema no `localStorage`;
- bytes de áudio em IndexedDB;
- recursos de aplicação no Cache Storage quando o Service Worker está ativo.

Esses dados não são enviados para um servidor pelo código do projeto. O navegador pode aplicar suas próprias políticas de armazenamento, limpeza e persistência.

Para remover os dados, o usuário pode limpar os dados do site nas configurações do navegador. Em ambiente de desenvolvimento, também é possível apagar o armazenamento da origem pelo painel de Application/Storage das ferramentas do navegador.

## Divulgação responsável

Ao reportar um problema, aguarde a orientação dos mantenedores antes de divulgar detalhes técnicos. Solicitações de teste devem ocorrer apenas em instalações próprias ou explicitamente autorizadas.

Atividades como phishing, exploração de terceiros, acesso não autorizado, destruição de dados, exfiltração de informações ou testes contra usuários reais não são autorizadas por esta política.

## Mudanças de segurança

Correções devem ser pequenas e revisáveis, atualizar a documentação relacionada e incluir validação apropriada. Quando necessário, a versão do cache do Service Worker e `AUDIO_CACHE_VERSION` devem ser incrementadas para evitar que usuários recebam recursos antigos.
