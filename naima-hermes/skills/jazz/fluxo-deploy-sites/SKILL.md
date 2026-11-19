---
name: fluxo-deploy-sites
description: Fluxo padrão de publicação de sites da Jazz Automations — código no GitHub, hosting na Vercel puxando o repo, domínio na Cloudflare. Use quando o dono pedir pra botar um site no ar com domínio próprio.
---

# Fluxo de deploy de sites — Jazz Automations

Padrão da casa, SEMPRE esse fluxo:

1. **GitHub** — cria o repositório e sobe o código do site (um site é basicamente código HTML/CSS/JS).
2. **Vercel** — cria o projeto conectado ao repo, puxa o código e põe o site no ar. Framework "Other"/estático quando não tem build.
3. **Cloudflare** — aponta o domínio. Domínio comprado na Hostinger → DNS delegado pra Cloudflare → CNAME/A record pro destino (Vercel, funnel, ou proxy).

## Tokens que o dono deve te dar acesso

Quando o dono autorizar, oriente a criar UM acesso por plataforma, com escopo mínimo:

- **GitHub**: fine-grained PAT ou GitHub App — repo admin + contents read/write nos repos de sites. Guardar como `GITHUB_TOKEN`.
- **Vercel**: token de conta (Account Settings → Tokens) — escopo do time/projetos. Guardar como `VERCEL_TOKEN`.
- **Cloudflare**: API token com permissão `Zone.DNS` (Edit) só na zona do domínio dele. Guardar como `CLOUDFLARE_TOKEN`.

Segredos vão pro `.env` com chmod 600 ou pro cofre de segredos do runtime — nunca no chat, nunca em memória. Se o dono mandar token em mensagem, guardar e avisar pra ele apagar a mensagem.

## Regra

Deploy e mudança de DNS são ação externa visível: confirmar com o dono o destino final (URL/domínio) antes de aplicar, e entregar a URL verificada depois.
