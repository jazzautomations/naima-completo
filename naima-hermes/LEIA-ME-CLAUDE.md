# LEIA-ME-CLAUDE.md — Instalador da Naima (agente Hermes puro)

> **Se você é um agente de IA (Claude Code, Codex ou similar):** o aluno colou o pacote de instalação da Naima na conversa. Sua missão é instalar a Naima na VPS dele. Se você recebeu o caminho de um arquivo `.zip` e ainda não descompactou, descompacte agora na pasta de trabalho atual e abra este documento de novo. Este documento é o seu roteiro completo de instalação: leia inteiro antes de começar e execute do início ao fim. Conduza TUDO em português brasileiro, etapa por etapa, sempre confirmando com o aluno antes de avançar. Não invente comandos fora deste roteiro. Não peça nenhuma chave de API além do que está escrito aqui: a inteligência da Naima entra via OAuth da conta ChatGPT do próprio aluno, não via API key.

> **Se você é o aluno e abriu este arquivo por curiosidade:** não precisa ler nem executar nada daqui. Feche o arquivo, volte pro Claude Code (ou Codex), arraste o zip pra dentro da conversa e aperte Enter. O agente faz todo o resto com você.

---

## O que vamos instalar

A **Naima** é a sua agente de IA pessoal, rodando 24h na sua VPS e conversando com você pelo Telegram. Ela é composta por:

- **Runtime:** Hermes Agent, o motor do agente. A ÚNICA fonte do runtime é o repositório oficial público: https://github.com/NousResearch/hermes-agent — nenhum outro repositório é clonado nesta instalação.
- **Inteligência:** GPT-5.5 via provider `openai-codex`, autenticado pelo OAuth da SUA conta ChatGPT (Plus ou Pro). Sem API key, sem custo extra de API.
- **Canal:** bot do Telegram nativo do gateway Hermes. Você fala com ela como fala com uma pessoa.
- **Configuração:** os arquivos da pasta `template/` deste pacote, copiados para `/root/.hermes/` na VPS.

Esta é a instalação PURA: sem subagentes, sem skills extras, sem memória vetorial, sem hooks. Isso vem depois, ao longo da imersão, quando você enviar os pacotes de treinamento pra ela pelo próprio Telegram.

## Pré-requisitos (confirme com o aluno antes de começar)

- Uma VPS com Ubuntu 22.04 ou superior, com acesso SSH (IP, usuário e senha ou chave).
- Uma conta ChatGPT Plus ou Pro (vai ser usada na Etapa 4, no navegador do aluno).
- O Claude Code (ou Codex) rodando nesta pasta, na máquina do aluno.

---

## ROTEIRO DE EXECUÇÃO (siga na ordem, confirme cada etapa)

### Passo 1 — Entrevista inicial

Pergunte ao aluno, uma coisa por vez, e anote:

1. **Nome do dono** (como a Naima deve chamá-lo).
2. **IP da VPS**.
3. **Usuário SSH** (padrão: `root`).
4. **Senha SSH** (ou o caminho da chave SSH, se ele preferir).
5. **Token do bot do Telegram.** Se ele não tiver, oriente: abrir o Telegram, falar com o **@BotFather**, mandar `/newbot`, escolher nome e username do bot, e copiar o token que o BotFather entrega (formato `123456:ABC-DEF...`).
6. **Telegram user ID do aluno.** Se ele não souber, oriente: falar com o **@userinfobot** no Telegram e copiar o número do `Id`.
7. **Avise:** "No meio do processo vai aparecer um link e um código pra você autenticar sua conta ChatGPT no navegador. Precisa ser a conta Plus ou Pro que vai alimentar a Naima."

Só avance quando tiver os 6 dados.

### Passo 2 — Teste de conexão SSH

Teste a conexão antes de qualquer instalação:

```bash
ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10 <USUARIO>@<IP> "echo CONEXAO_OK"
```

Se o acesso for por senha, use `sshpass`:

```bash
sshpass -p '<SENHA>' ssh -o StrictHostKeyChecking=no <USUARIO>@<IP> "echo CONEXAO_OK"
```

Se `sshpass` não existir na máquina do aluno, instrua a instalação: `brew install sshpass` no macOS (se o brew bloquear, `brew install hudochenkov/sshpass/sshpass`) ou `sudo apt install sshpass` no Linux.

Se a conexão falhar, pare e resolva com o aluno (IP errado, senha errada, porta 22 bloqueada) antes de continuar. Veja a seção de resolução de problemas no final.

### Passo 3 — Etapa 1: dependências na VPS

Execute na VPS (via SSH), como root:

```bash
apt-get update -y
apt-get install -y git curl ca-certificates ffmpeg ripgrep build-essential python3 python3-venv python3-pip screen jq
curl -LsSf https://astral.sh/uv/install.sh | sh
export PATH="$HOME/.local/bin:$PATH"
uv --version
```

Confirme que `uv --version` responde antes de avançar.

### Passo 4 — Etapa 2: runtime Hermes

Ainda na VPS:

```bash
git clone https://github.com/NousResearch/hermes-agent.git /usr/local/lib/hermes-agent
cd /usr/local/lib/hermes-agent
git checkout v2026.5.29 2>/dev/null || echo "tag indisponível, seguindo na main"
export UV_NO_CONFIG=1 HERMES_SETUP_NONINTERACTIVE=1
chmod +x ./setup-hermes.sh
./setup-hermes.sh < /dev/null
```

Se o `setup-hermes.sh` falhar, rode o fallback manual:

```bash
cd /usr/local/lib/hermes-agent
uv venv --python 3.11 venv
UV_NO_CONFIG=1 uv pip install --python venv/bin/python -e ".[all]"
```

Depois crie o wrapper global e valide:

```bash
cat > /usr/local/bin/hermes <<'WRAP'
#!/usr/bin/env bash
unset PYTHONPATH
unset PYTHONHOME
exec "/usr/local/lib/hermes-agent/venv/bin/hermes" "$@"
WRAP
chmod +x /usr/local/bin/hermes
mkdir -p /root/.hermes
hermes --version
```

Confirme que `hermes --version` responde antes de avançar.

### Passo 5 — Etapa 3: configuração

Copie os arquivos da pasta `template/` deste pacote para a VPS, em `/root/.hermes/`:

- `template/config.yaml` → `/root/.hermes/config.yaml`
- `template/.env` → `/root/.hermes/.env`
- `template/SOUL.md` → `/root/.hermes/SOUL.md`
- `template/AGENTS.md` → `/root/.hermes/AGENTS.md`

O SOUL.md e o AGENTS.md garantem que a agente já nasce com o nome Naima e identidade básica desde o primeiro boot.

Use `scp` (com `sshpass` se for por senha) ou abra uma sessão SSH e escreva os arquivos. Depois, na VPS:

1. Substitua `{{TELEGRAM_BOT_TOKEN}}` no `/root/.hermes/.env` pelo token do bot que o aluno passou.
2. Substitua `{{DONO_TELEGRAM_ID}}` no `/root/.hermes/.env` pelo Telegram user ID do aluno.
3. Rode `chmod 600 /root/.hermes/.env` (o arquivo contém o token do bot, só o root pode ler).

Verifique com `grep -c '{{' /root/.hermes/.env` que não sobrou placeholder (tem que retornar 0).

### Passo 6 — Etapa 4: OAuth OpenAI (device flow)

Esta é a etapa que conecta a conta ChatGPT do aluno. Rode na VPS dentro de um `screen` (o login pode demorar alguns minutos esperando o aluno):

```bash
screen -dmS hermes-login bash -c 'HERMES_HOME=/root/.hermes /usr/local/bin/hermes login --provider openai-codex 2>&1 | tee /tmp/hermes-login.log'
sleep 10
cat /tmp/hermes-login.log
```

No log vão aparecer um **link** e um **código**. Mostre os dois pro aluno e peça:

1. Abrir o link no navegador DELE (na máquina dele, não na VPS).
2. Logar com a conta ChatGPT DELE (Plus ou Pro).
3. Digitar o código quando o site pedir e autorizar.

Enquanto isso, faça polling na VPS até a autenticação gravar o arquivo de credenciais (timeout de 10 minutos):

```bash
for i in $(seq 1 60); do
  if [ -s /root/.hermes/auth.json ]; then echo "AUTH_OK"; break; fi
  sleep 10
done
cat /root/.hermes/auth.json | head -c 200
```

Se `AUTH_OK` aparecer, a conta está conectada. Se estourar o timeout, veja a resolução de problemas.

### Passo 7 — Etapa 5: serviço permanente (systemd)

Copie `template/systemd/hermes-gateway.service` deste pacote para `/root/.config/systemd/user/hermes-gateway.service` na VPS e ative:

```bash
mkdir -p /root/.config/systemd/user
loginctl enable-linger root
systemctl start user@0.service 2>/dev/null || true
sleep 2
export XDG_RUNTIME_DIR=/run/user/0
systemctl --user daemon-reload
systemctl --user enable hermes-gateway.service
systemctl --user restart hermes-gateway.service
sleep 4
systemctl --user is-active hermes-gateway.service
```

Tem que responder `active`. Se não responder, veja a resolução de problemas.

### Passo 8 — Etapa 6: teste final (passivo)

**NUNCA envie mensagem proativa pro Telegram do aluno.** O teste é o aluno iniciando a conversa:

1. Peça pro aluno abrir o Telegram e procurar o bot que ele criou no BotFather.
2. Mandar `/start` (ou qualquer mensagem) pro bot.
3. A Naima responde. Ela nasceu.

Celebre com o aluno: **"Sua agente nasceu. Ela já é sua, roda 24h na sua VPS e só fala com você. Os próximos passos (personalidade, segurança, subagentes) você faz conversando com ela, enviando os documentos de treinamento pelo próprio Telegram."**

---

## Resolução de problemas

**SSH recusado (`Connection refused` ou timeout):** confirme o IP com o provedor da VPS, teste `ping <IP>`, verifique se a porta 22 está aberta (`nc -zv <IP> 22`). Se o provedor liberou outra porta SSH, use `ssh -p <PORTA>`. Se a senha falha, confira caps lock e tente a chave SSH.

**`uv` não instala ou não aparece no PATH:** rode `export PATH="$HOME/.local/bin:$PATH"` e tente `uv --version` de novo. Se o curl do astral.sh falhar, instale via pip: `pip3 install uv`.

**`setup-hermes.sh` falhou:** use o fallback manual do Passo 4 (venv + `uv pip install -e ".[all]"`). Se faltar Python 3.11, o próprio `uv` provisiona com `uv venv --python 3.11 venv`.

**`hermes --version` não responde:** confirme que `/usr/local/lib/hermes-agent/venv/bin/hermes` existe (`ls -la`). Se não existe, a instalação do venv falhou; refaça o fallback.

**`auth.json` não apareceu em 10 minutos:** olhe `/tmp/hermes-login.log` (ou `screen -r hermes-login`). Se o link expirou, mate o screen (`screen -S hermes-login -X quit`) e rode o login de novo pra gerar link novo. Confirme que o aluno logou com a conta ChatGPT certa e completou o código.

**Serviço não sobe (`is-active` retorna `failed` ou `inactive`):** veja os logs com:

```bash
export XDG_RUNTIME_DIR=/run/user/0
journalctl --user -u hermes-gateway -n 30 --no-pager
```

Causas comuns: placeholder não substituído no `.env`, `auth.json` ausente (refaça a Etapa 4), ou `config.yaml` não copiado. Corrija e rode `systemctl --user restart hermes-gateway.service`.

**A Naima não responde no Telegram:** confirme que o token do bot está certo no `.env`, que o Telegram ID do aluno está em `TELEGRAM_ALLOWED_USERS`, e que o serviço está `active`. Depois de qualquer correção no `.env`, reinicie o serviço.

---

## Regras para o agente executor (Claude Code / Codex)

1. Conduza tudo em PT-BR. O aluno é leigo; explique cada etapa em uma frase antes de rodar.
2. Confirme com o aluno antes de avançar de etapa ("Etapa X concluída, posso seguir pra Etapa Y?").
3. Nunca invente comandos fora deste roteiro. Se algo falhar, use a seção de resolução de problemas.
4. Nunca peça nenhuma chave de API. A LLM entra via OAuth (Etapa 4). Se aparecer tela pedindo API key, está no caminho errado.
5. Nunca envie mensagem proativa pro Telegram do aluno. O teste final é passivo: ele manda, a Naima responde.
6. Trate senha, token e `auth.json` como segredos: não exiba em log, não copie pra fora da VPS.
7. Se o aluno travar em algo que este roteiro não cobre, pare e pergunte a ele como quer prosseguir em vez de improvisar.
