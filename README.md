# versionamento
Repositório para Materiais da disciplina Versionamento Seduc-SP 

Link para Script instalação
https://lab.inetz.com.br/devmaker/hotel/seduc/config_ambiente_python.ps1

📑 Guia de Aula: Versionamento com Git
Tópicos: Comandos Essenciais, Estudo de Caso e Prática de Conflitos
1. Comandos Fundamentais (Resumo)
git init: Inicia um repositório local.
git clone [url]: Copia um repositório remoto.
git status: Verifica o estado atual dos arquivos (modificados, staged, etc).
git add [arquivo]: Adiciona alterações à área de preparação (staging area).
git commit -m "mensagem": Salva as alterações no histórico com uma descrição.
git branch [nome]: Cria uma nova linha de desenvolvimento.
git checkout [nome]: Alterna entre branches.
git merge [nome]: Une o histórico de outra branch à branch atual.
git log --oneline: Visualiza o histórico de commits de forma simplificada.
2. Estudo de Caso: O Mistério do Menu Sumido
Cenário: Na startup DevMarket, o menu da Home desapareceu após o merge de ontem. A desenvolvedora Rafa reportou o erro no Slack.
Resolução de Problemas:
Como descobrir o culpado? Use git log --oneline para ver os últimos commits e git diff [hash] para examinar as mudanças feitas no merge suspeito.
Add vs Commit: O git add é a preparação (escolher o que vai na caixa), enquanto o git commit é o envio definitivo (lacrar a caixa e registrar no sistema).
Boa prática de correção:
git add index.html
git commit -m "fix: corrige fechamento de tag <nav> que ocultava o menu"
3. Teste Prático: Simulando um Conflito
Siga os comandos abaixo no terminal para reproduzir um erro de merge real:
Parte A: Preparação
bash
mkdir aula-git && cd aula-git
git init
echo "<h1>DevMarket</h1><nav>Menu Original</nav>" > index.html
git add .
git commit -m "feat: estrutura inicial"
Use o código com cuidado.

Parte B: Criando a Divergência
Branch Rafa:
bash
git checkout -b branch-rafa
# Edite o index.html para: <nav>Menu da Rafa</nav>
git add .
git commit -m "style: novo design da rafa"
git checkout main
Use o código com cuidado.

Branch Marketing:
bash
git checkout -b branch-mkt
# Edite o MESMO index.html para: <nav>Menu Promocional</nav>
git add .
git commit -m "feat: texto do marketing"
git checkout main
Use o código com cuidado.

Parte C: O Conflito
bash
git merge branch-rafa  # (Sucesso!)
git merge branch-mkt   # (CONFLITO!)
Use o código com cuidado.

Tarefa do Aluno:
Abra o arquivo index.html.
Remova as marcas <<<<, ==== e >>>>.
Escolha a versão final do texto.
Finalize com git add index.html e git commit -m "fix: resolve conflito no menu".


1. Instalação do GitHub CLI (gh)
Se você estiver no seu servidor Ubuntu/Debian, rode estes comandos:

Bash

# Adiciona o repositório oficial do GitHub
type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null

# Instala o gh
sudo apt update
sudo apt install gh -y

# Autenticação (Siga as instruções na tela)
# Escolha: GitHub.com > HTTPS > Yes > Login with a web browser
gh auth login
2. Script de Automação do Projeto
Em vez de digitar um por um, vamos criar um padrão. Substitua NOME_DA_ORG pelo nome da sua organização ou usuário no GitHub.

Passo A: Criar o Repositório
Bash

# Define o nome do repositório baseado no RA
export REPO_NAME="projeto-inetz-2026-RA12345"

gh repo create inetz-solucoes/$REPO_NAME --private --add-readme
Passo B: Atribuir Variáveis de Ambiente (Variables)
As variáveis comuns (não sensíveis) ficam visíveis nas configurações do Actions.

Bash

# Variável para identificar o aluno e o projeto
gh variable set ALUNO_RA --body "12345" --repo inetz-solucoes/$REPO_NAME
gh variable set ALUNO_PROJETO --body "Projeto_Backend_V1" --repo inetz-solucoes/$REPO_NAME
gh variable set SSH_HOST --body "200.xxx.xxx.xxx" --repo inetz-solucoes/$REPO_NAME
Passo C: Atribuir Segredos (Secrets)
Aqui entra a chave que permite ao GitHub "entrar" no seu servidor.

Bash

# Envia sua chave privada (id_rsa) para o segredo SSH_KEY
gh secret set SSH_KEY < ~/.ssh/id_rsa --repo inetz-solucoes/$REPO_NAME
3. Adicionando Colaboradores
Para que o aluno (e outros professores como o Marcelo Réu) tenham acesso ao código:

Bash

# Adiciona o aluno como colaborador (permissão de escrita)
gh api -X PUT /repos/inetz-solucoes/$REPO_NAME/collaborators/USUARIO_DO_ALUNO -f permission=push

# Adiciona um colega professor como Admin
gh api -X PUT /repos/inetz-solucoes/$REPO_NAME/collaborators/marcelo-reu -f permission=admin
4. Resumo da Estrutura Final
Após rodar esses comandos, o repositório do aluno estará blindado e pronto para o deploy:

Tipo	Nome	Conteúdo
Variable	ALUNO_RA	Identificador único para a pasta /var/inetpub/...
Variable	SSH_HOST	IP do seu servidor Docker Swarm
Secret	SSH_KEY	Chave RSA Privada (Invisível para o aluno)
Access	Colaborador	O GitHub do aluno com acesso ao push

🛠️ Script de Automação: CriarProjetoAluno.ps1
Copie o código abaixo e salve-o como CriarProjetoAluno.ps1.

PowerShell

# --- Configurações Mestres do Inetz ---
$ORG_NAME = "inetz-solucoes"
$SSH_HOST = "200.xxx.xxx.xxx"  # Altere para o IP do seu srv-docker-master
$SSH_USER = "root"             # Usuário de acesso ao servidor
$SSH_PORT = "22"               # Porta SSH padrão

# --- Entrada de Dados ---
$ALUNO_RA = Read-Host "Digite o RA do Aluno (ex: 12345)"
$GH_USER = Read-Host "Digite o Usuário GitHub do Aluno"
$PROJETO_NOME = "projeto-backend-v1"

$REPO_NAME = "projeto-inetz-$ALUNO_RA"

Write-Host "`n🚀 Iniciando criação do ecossistema para: $REPO_NAME" -ForegroundColor Cyan

# 1. Criar o Repositório Privado
gh repo create "$ORG_NAME/$REPO_NAME" --private --add-readme

# 2. Configurar Variáveis de Ambiente (Variables)
Write-Host "📦 Definindo variáveis de ambiente..." -ForegroundColor Yellow
gh variable set ALUNO_RA --body "$ALUNO_RA" --repo "$ORG_NAME/$REPO_NAME"
gh variable set ALUNO_PROJETO --body "$PROJETO_NOME" --repo "$ORG_NAME/$REPO_NAME"
gh variable set SSH_HOST --body "$SSH_HOST" --repo "$ORG_NAME/$REPO_NAME"
gh variable set SSH_USER --body "$SSH_USER" --repo "$ORG_NAME/$REPO_NAME"
gh variable set SSH_PORT --body "$SSH_PORT" --repo "$ORG_NAME/$REPO_NAME"

# 3. Configurar Segredos (Secrets)
Write-Host "🔐 Injetando chaves de segurança..." -ForegroundColor Yellow
# Assume que sua chave está no caminho padrão do Windows
$KEY_PATH = "$HOME\.ssh\id_rsa"
if (Test-Path $KEY_PATH) {
    gh secret set SSH_KEY --repo "$ORG_NAME/$REPO_NAME" < $KEY_PATH
} else {
    Write-Host "❌ Erro: Chave SSH não encontrada em $KEY_PATH" -ForegroundColor Red
}

# 4. Adicionar Colaboradores
Write-Host "👥 Adicionando Aluno e Professores..." -ForegroundColor Yellow
# Aluno como "push" (escreve mas não deleta o repo)
gh api -X PUT "/repos/$ORG_NAME/$REPO_NAME/collaborators/$GH_USER" -f permission=push
# Marcelo Réu como Admin
gh api -X PUT "/repos/$ORG_NAME/$REPO_NAME/collaborators/marcelo-reu" -f permission=admin

Write-Host "`n✅ Ecossistema do Aluno $ALUNO_RA criado com sucesso!" -ForegroundColor Green
Write-Host "🔗 Link: https://github.com/$ORG_NAME/$REPO_NAME" -ForegroundColor Cyan
📝 O que este script faz por você:
Identidade Única: Usa o ALUNO_RA para batizar o repositório e as pastas internas.

Conexão Blindada: Seta SSH_HOST, SSH_USER e SSH_PORT para que o GitHub saiba onde o seu Docker Swarm está ouvindo.

Chave Mestra: Pega a sua id_rsa do Windows e a transforma no segredo SSH_KEY dentro do GitHub (o aluno nunca verá o conteúdo dessa chave).

Gestão de Equipe: Já coloca o aluno no projeto e garante que o Marcelo Réu tenha acesso administrativo para te ajudar na correção.

💡 Como executar no Windows:
Abra o PowerShell na pasta onde salvou o arquivo.

Se o Windows bloquear a execução de scripts, rode uma vez:
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

Execute o script:
.\CriarProjetoAluno.ps1
