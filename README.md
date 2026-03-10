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
