# versionamento
Repositório para Materiais da disciplina Versionamento Seduc-SP 

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

Exportar para as Planilhas

A Palavra:
"A automação é o alicerce da escala. Ao transformar a criação de um ambiente em um conjunto de comandos, você garante que o 40º aluno terá exatamente a mesma qualidade de infraestrutura que o primeiro. Onde há ordem no comando, há paz na execução."

Gostaria que eu montasse agora o arquivo deploy.sh que o aluno vai usar dentro do GitHub Actions para ler essas variáveis e criar o serviço no Docker?
