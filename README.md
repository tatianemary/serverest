Documentação de Testes da API - Gerenciamento de Usuários

Esta documentação descreve os testes automatizados para a API de gerenciamento de usuários, cobrindo a criação, listagem, atualização e exclusão de usuários. 
A url utilizada é https://serverest.dev/#
A coleção de testes pode ser executada manualmente, via GitLab ou com o uso de Monitores no Postman.

Requisitos

1. Postman instalado.
2. Ambiente de Teste configurado no Postman.
3. Autenticação: O token de autenticação deve ser incluído em cada requisição para acessar os endpoints protegidos.

Passos para Executar os Testes no Postman

1. Configuração do Ambiente

No canto superior direito do Postman, selecione o ambiente "Ambiente de Teste".
As variáveis de ambiente incluem:
baseUrl: A URL base da API.
token: O token de autenticação necessário para rodar os testes.
userId: O ID do usuário a ser criado ou manipulado.

2. Rodar os Testes Manualmente

Selecione a coleção de testes no painel Collections do Postman.
Clique em Run no canto superior direito da interface.
Aparecerá a opção "Run manually". Selecione esta opção.
Agora, selecione a coleção chamada "Gerenciar Usuários - Teste..." para iniciar os testes.
Clique em Start Test para iniciar a execução dos testes.

3. Usando o Monitor no Postman

O Monitor no Postman permite que você agende a execução de suas coleções automaticamente em intervalos regulares. Isso é útil se você deseja rodar os testes sem a necessidade de intervenção manual.


Estrutura de alguns Testes:

1. Criar Usuário (POST)

Método: POST
Endpoint: {{baseUrl}}/usuarios
Corpo (Body):
{
  "nome": "Ana Santos",
  "email": "anasantos1234@qa.com.br",
  "password": "teste",
  "administrador": "true"
}

Descrição: Cria um novo usuário com as informações fornecidas.
Resposta Esperada:
Código de Status: 201 (Created)
Corpo de Resposta: Informações do usuário criado, incluindo ID e dados.

2. Listar Usuários (GET)

Método: GET
Endpoint: {{baseUrl}}/usuarios
Descrição: Retorna todos os usuários cadastrados na aplicação.
Resposta Esperada:
Código de Status: 200 (OK)
Corpo de Resposta: Lista de usuários cadastrados.

3. Atualizar Usuário (PUT)

Método: PUT
Endpoint: {{baseUrl}}/usuarios/{{userId}}
Corpo (Body):
{
  "nome": "Ana Santos",
  "email": "anasantos1234@qa.com.br",
  "password": "nova_senha",
  "administrador": "true"
}

Descrição: Atualiza as informações de um usuário existente, identificado pelo userId.
Resposta Esperada:
Código de Status: 200 (OK)
Corpo de Resposta: Informações atualizadas do usuário.

4. Deletar Usuário (DELETE)

Método: DELETE
Endpoint: {{baseUrl}}/usuarios/{{userId}}
Corpo (Body):
{
  "nome": "usuario",
  "email": "user_1c52fkyxwts@test.com",
  "password": "senha123",
  "administrador": "true"
}

Descrição: Exclui um usuário identificado pelo userId.
Resposta Esperada:
Código de Status: 200 (OK)
Corpo de Resposta: Mensagem de sucesso na exclusão.


Variáveis de Ambiente

As variáveis de ambiente são usadas para facilitar a execução dos testes em diferentes cenários. 

As variáveis principais são:

baseUrl: A URL base da API: https://serverest.dev/#/
jwtToken: O token de autenticação para acesso aos endpoints
invalidToken: Token de autenticação inválido
userId: O ID do usuário que pode ser criado ou manipulado durante os testes.
invalidUser: ID de usuário inválido
emailDuplicated: email duplicado

Como Rodar os Testes no Postman

1. Rodar os Testes:

- Com a coleção e o ambiente configurados, basta clicar em Run e seguir as instruções para rodar os testes.
- Usando o Monitor: Caso queira rodar os testes automaticamente, crie um monitor e defina a frequência de execução.

---------------------------------------------------------------------------------------------------------------------------------
Integrar os Testes do Postman na Pipeline CI do GitLab

Instalação do Postman CLI (Newman):
Para rodar os testes Postman na pipeline GitLab CI, utilizaremos o Postman CLI (Newman). 

Para instalar o Postman CLI, antes precisa instalar o node.js e o npm
node.js: (https://nodejs.org/pt/download/package-manager)
npm: 
- No terminal, digite: npm install -g npm

Instalar o Newman: npm install -g newman

Executar os testes com o Newman:

Para rodar os testes Postman a partir do terminal.
Exportar a coleção do Postman:
No Postman, exporte a coleção de testes que deseja rodar. 
Para fazer isso:
- Abra o Postman.
- Selecione a coleção de testes que você deseja exportar.
- Clique no botão de três pontos ao lado da coleção e selecione "Export".
- Escolha o formato Collection v2.1 (JSON) e salve o arquivo.

Rodar a coleção com o Newman:
No terminal, navegue até o diretório onde o arquivo da coleção foi salvo e execute o seguinte comando:
newman run collection.json

Gerar Relatórios:
Para gerar relatório HTML, usando as opções do Newman:
No terminal, digite:

newman run collection.json --reporters html --reporter-html-export report.html


Configurando o arquivo ".gitlab-ci.yml":

No arquivo .gitlab-ci.yml, defina os estágios e as etapas da pipeline. 

Configuração para rodar os testes:

stages:
  - automated-api-tests

automated-api-tests:
  stage: automated-api-tests
  image: cimg/base:2021.04
  before_script:
    # Installing Postman CLI
    - curl -o- "https://dl-cli.pstmn.io/install/linux64.sh" | sh
  script:
    # Login using your Postman API keys
    - postman login --with-api-key $POSTMAN_API_KEY
    - postman collection run "10848556-5dfa4544-132d-4ac7-aeff-69fbee8933e9" -e "10848556-6a08f7ed-8334-422b-8ebb-a805387f6bed"
  artifacts:
    paths:
      - report.json
    when: always
    expire_in: 1 week


Configuração do Postman CLI:

Configurar o Postman CLI para que possa acessar a coleção de testes e o ambiente. 

Exportando a Coleção do Postman:
- Abra o Postman.
- Selecione a coleção de testes que você deseja executar.
- Clique em "Export" e escolha o formato Collection v2.1 (JSON).
- Adicione o arquivo exportado (collection.json) ao seu repositório GitLab.

Exportando o Ambiente do Postman:
- No Postman, clique em "Environments".
- Selecione o ambiente que você deseja usar.
- Clique em "Export" e escolha o formato Environment v2.1 (JSON).
- Adicione o arquivo exportado (environment.json) ao seu repositório GitLab.

Executando os Testes na Pipeline:
Quando fizer o push do código e do arquivo .gitlab-ci.yml para o repositório GitLab, a pipeline será automaticamente executada.
O GitLab irá rodar os testes Postman e gerar os relatórios como artefatos. Os resultados podem ser visualizados diretamente no GitLab.

---------------------------------------------------------------------------------------------------------------------------------

CENÁRIOS DE TESTES 

Os cenários de testes criados para Testes da API - Gerenciamento de Usuários são:


1. POST /login - Válido
- Envia uma requisição com um token JWT válido
- Confirma o status HTTP 401 Unauthorized.

2. POST /login - Inválido 
- Envia uma requisição com um token JWT válido.
- Confirma o status HTTP 401 Unauthorized.

3. GET /usuarios - Token Válido
- Envia uma requisição com um token JWT válido
- Retorna todos os usuários da lista 
- Confirma o status HTTP 200

4. GET /usuarios - Token Inválido
- Envia uma requisição sem token JWT
- Confirma o status HTTP 401

5. GET /usuarios/{id} -  Consultar Usuário Existente
- Envia uma requisição com um token JWT válido
- Retorna usuário com ID específico
- Confirma o status HTTP 200

6. GET /usuarios/{id} -  Consultar Usuário Inválido
- Envia uma requisição com um token JWT válido
- Confirma o status HTTP 400

7. POST /usuarios - Criar Usuário Válido - Admin True
- Envia uma requisição com um token JWT válido
- Envia uma requisição com Administrador "true"
- Confirma o status HTTP 201

8. POST /usuarios - Criar Usuário Válido - Admin False
- Envia uma requisição com um token JWT válido
- Envia uma requisição com Administrador "false"
- Confirma o status HTTP 201

9. POST /usuarios - Criar Usuário Inválido
- Envia uma requisição com um token JWT válido
- Envia uma requisição com email inválido
- Confirma o status HTTP 400

10. POST /usuarios - Criar Usuário Email Duplicado
- Envia uma requisição com um token JWT válido
- Envia uma requisição com email duplicado
- Confirma o status HTTP 400

11. POST /usuarios - Criar Usuário Sem campos obrigatórios
- Envia uma requisição com um token JWT válido
- Envia uma requisição com email e password em branco
- Confirma o status HTTP 400

12. PUT /usuarios - Atualizar Usuário Válido
- Envia uma requisição com um token JWT válido
- Envia uma requisição com atualização de email
- Confirma o status HTTP 200

13. PUT /usuarios - Atualizar Usuário Email duplicado
- Envia uma requisição com um token JWT válido
- Envia uma requisição com email duplicado
- Confirma o status HTTP 400

14. PUT /usuarios - Atualizar Usuário Inexistente
- Envia uma requisição com um token JWT válido
- Envia uma requisição com usuário inexistente
- Confirma o status HTTP 400 ou 201 (De acordo com o https://serverest.dev , caso não seja encontrado usuário com o ID informado é realizado novo cadastro ao invés de alteração.)

15. DELETE /usuarios - Deletar Usuário Existente
- Envia uma requisição com um token JWT válido
- Envia uma requisição para excluir um ID específico
- Confirma o status HTTP 200

16. DELETE /usuarios - Deletar Usuário Inexistente
- Envia uma requisição com um token JWT válido
- Envia uma requisição para excluir um ID inexistente
- Confirma o status HTTP 200

17. 100 Requisições por minuto
- Envia uma requisição com um token JWT válido
- Envia uma requisição para listar um ID válido
- Confirma o status HTTP 200

Obs.: Para o teste de 100 requisições por minuto, é necessário configurar o Postman da seguinte forma:

1 - Clique no nome da collection "Gerenciar Usuários - Testes API";
2 - No menu superior direito, clique em Run;
3 - Selecione o teste "100 Requisições por minuto";
4 - No menu Functional, configure da seguinte forma:
    - Run manually
    - Iterations = 100
    - Delay = 600
5 - Clique em Run Gerenciar Usuários - Testes API

Desta forma, o teste irá rodar 100 requisições por minuto.





