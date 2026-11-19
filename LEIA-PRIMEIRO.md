# Naima — pacote completo

## 1. Instalar
Abra o seu agente de código (Claude Code, Codex, Devin ou similar), aponte este repositório — ou arraste `pacote-instalacao-naima-hermes.zip` pra conversa — e aperte Enter.
O agente lê o `LEIA-ME-CLAUDE.md` (de dentro do zip ou da pasta `naima-hermes/`) e instala a Naima na sua VPS, passo a passo, em português.
Você vai precisar de: uma VPS Ubuntu com SSH, sua conta ChatGPT Plus/Pro (OAuth, sem API key), um bot no @BotFather e seu ID do @userinfobot.

## 2. Treinar (já vem instalado — ou pelo Telegram se preferir)
O pacote já instala as 9 skills e as regras completas junto com a Naima: ela nasce com o time de subagentes (Juliana, clone, Paulo, Jonathan) e os métodos (carrossel, landing page, proposta, vídeo sanduíche, Instagram). Não precisa mandar nada.

Se preferir treinar na mão: cada `.md` da pasta `treinamento/` começa com um "PROMPT PARA ENVIAR". Mande o arquivo pra Naima no Telegram com aquela frase.

Ordem sugerida:
1. 02-seguranca-da-naima.md
2. 03-comportamento-e-personalidade.md  (aqui você personaliza: edite antes de mandar, se quiser)
3. 04-regras-naima.md
4. 06-isolamento-de-dados.md
5. 07-onboarding-entrevista-do-dono.md

Subagentes (cria um por vez):
6. 01-juliana-ops.md
7. 02-clone-do-dono-sdr.md
8. 03-paulo-dev.md
9. 04-jonathan-copy.md

Skills / métodos:
10. 05-conectar-clone-no-instagram.md
11. 06-bonus-edicao-sanduiche-economico.md
12. 09-carrossel-instagram-publicacao.md
13. 29-metodo-criacao-landing-pages.md
14. 30-metodo-criacao-propostas.md

## 3. Personalizar
Tudo é texto. Abra qualquer `.md`, mude nome, tom, regras, negócio, e mande de novo pra Naima com "lê este documento e incorpora". Ela atualiza.
