# Naima — pacote completo

Agente de IA pessoal que mora na sua VPS e conversa com você pelo Telegram, montada pela [Jazz Automations](https://github.com/jazzautomations) sobre o [Hermes Agent](https://github.com/NousResearch/hermes-agent).

Ela executa o que você pede na hora — sem cartão de aprovação, sem frescura de permissão. O piso de segurança é em texto: só conversa com o dono, nunca mostra segredo, não apaga nem manda nada pra fora sem confirmação.

## Instalar

Abra o seu agente de código e diga:

> Instala a Naima pra mim — o pacote está em github.com/jazzautomations/naima-completo

O agente lê o `LEIA-ME-CLAUDE.md` e conduz tudo: Hermes na VPS, OAuth do ChatGPT, bot do Telegram, systemd. Você vai precisar de uma VPS Ubuntu com SSH, conta ChatGPT Plus/Pro, um bot do @BotFather e seu ID do @userinfobot.

Se preferir sem git: baixe `pacote-instalacao-naima-hermes.zip` e arraste pra conversa do agente.

## Treinar

Depois de instalada, mande os `.md` da pasta `treinamento/` pra ela pelo Telegram, um por vez, com a frase de prompt que vem no topo de cada arquivo. Ordem sugerida no `LEIA-PRIMEIRO.md`.

## Conteúdo

| Caminho | O que é |
|---|---|
| `naima-hermes/` | pacote de instalação descompactado (template + roteiro do agente) |
| `pacote-instalacao-naima-hermes.zip` | o mesmo pacote em zip, pra arrastar no chat |
| `treinamento/` | 14 documentos de treinamento (regras, personalidade, subagentes, skills) |
| `LEIA-PRIMEIRO.md` | instruções completas de instalação e treino |

## Licença

MIT — use, modifique e distribua livremente.
