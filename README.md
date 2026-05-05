Cypress Intercept Examples (cy-intercept)
Este repositório contém exemplos práticos de como utilizar o comando cy.intercept() no Cypress para monitorar, manipular e validar requisições de rede (HTTP) em testes de ponta a ponta (E2E).

🚀 Objetivo
O objetivo deste projeto é demonstrar as capacidades do Cypress 6+ em lidar com chamadas de rede, substituindo os antigos comandos cy.server() e cy.route(). Com o cy.intercept, é possível:

Espionar (Spying): Verificar se uma requisição foi feita e quais dados foram enviados.

Simular (Stubbing): Forçar uma resposta específica (mock) para testar cenários de erro ou estados específicos da interface.

Modificar: Alterar cabeçalhos (headers) ou o corpo da requisição/resposta em tempo real.

🛠️ Tecnologias
Cypress - Framework de testes E2E.

JavaScript/Node.js - Ambiente de execução.

📁 Estrutura do Projeto
A lógica principal de interceptação geralmente encontra-se em:

cypress/e2e/: Contém os arquivos de especificação (.cy.js ou .spec.js) com os testes.

cypress/fixtures/: Arquivos JSON usados para simular respostas de APIs.

💻 Exemplos de Uso
1. Esperando uma requisição (Spying)
JavaScript
cy.intercept('GET', '/usuarios').as('getUsuarios');
cy.visit('/dashboard');
cy.wait('@getUsuarios').its('response.statusCode').should('eq', 200);
2. Mockando uma resposta (Stubbing)
JavaScript
cy.intercept('GET', '/config', {
  statusCode: 200,
  body: { theme: 'dark', language: 'pt-br' }
}).as('getConfig');
🔧 Como Rodar o Projeto
Clone o repositório:

Bash
git clone https://github.com/Pedro001979/cy-intercept.git
Instale as dependências:

Bash
npm install
Abra o Cypress:

Bash
npx cypress open
