# Cypress Intercept Examples (cy-intercept)

> Repositório com exemplos práticos de como utilizar o comando `cy.intercept()` no Cypress para monitorar, manipular e validar requisições de rede (HTTP) em testes de ponta a ponta (E2E).

## Objetivo

Este projeto demonstra as capacidades do **Cypress 6+** em lidar com chamadas de rede, substituindo os antigos comandos `cy.server()` e `cy.route()`. Com o `cy.intercept()`, é possível:

- **Espionar (Spying)**: Verificar se uma requisição foi feita e quais dados foram enviados
- **Simular (Stubbing)**: Forçar uma resposta específica (mock) para testar cenários de erro ou estados específicos da interface
- **Modificar**: Alterar cabeçalhos (headers) ou o corpo da requisição/resposta em tempo real

## Tecnologias

- **Cypress** `^13.17.0` - Framework de testes E2E moderno e poderoso
- **Puppeteer** `^21.9.0` - Biblioteca para automação de navegador headless
- **JavaScript/Node.js** - Ambiente de execução

## Estrutura do Projeto

```
cy-intercept/
├── cypress/
│   ├── e2e/                    # Testes de ponta a ponta
│   ├── fixtures/               # Dados JSON para mocks e fixtures
│   ├── support/                # Configurações e utilitários compartilhados
│   │   └── utils/
│   │       └── tabNavigation   # Utilitários para navegação entre abas
│   └── screenshots/            # Screenshots de testes (gerados automaticamente)
├── cypress.config.js           # Configuração do Cypress
├── package.json                # Dependências e scripts
├── package-lock.json           # Lock file das dependências
└── README.md                   # Este arquivo
```

## Instalação

### Pré-requisitos
- Node.js `v14.0.0` ou superior
- npm ou yarn

### Passos

1. **Clone o repositório:**
   ```bash
   git clone https://github.com/Pedro001979/cy-intercept.git
   cd cy-intercept
   ```

2. **Instale as dependências:**
   ```bash
   npm install
   ```

3. **Verifique a instalação:**
   ```bash
   npx cypress --version
   ```

## Como Usar

### Abrir o Cypress Interactive

```bash
npx cypress open
```

Isso abrirá a interface gráfica do Cypress onde você pode:
- Visualizar todos os testes disponíveis
- Executar testes individualmente
- Acompanhar em tempo real o comportamento dos testes
- Acessar o debugger do navegador

### Executar Testes em Headless

```bash
npm test
```

Ou com mais opções:

```bash
npx cypress run --headless --browser chrome
```

## Exemplos de Uso

### 1. Esperando uma Requisição (Spying)

```javascript
describe('Teste de Spying', () => {
  it('deve validar que a requisição foi feita', () => {
    // Define um alias para a requisição
    cy.intercept('GET', '/usuarios').as('getUsuarios');
    
    cy.visit('/dashboard');
    
    // Aguarda a requisição e valida o status
    cy.wait('@getUsuarios')
      .its('response.statusCode')
      .should('eq', 200);
  });
});
```

### 2. Mockando uma Resposta (Stubbing)

```javascript
describe('Teste de Stubbing', () => {
  it('deve retornar uma resposta mockada', () => {
    cy.intercept('GET', '/config', {
      statusCode: 200,
      body: { 
        theme: 'dark', 
        language: 'pt-br' 
      }
    }).as('getConfig');
    
    cy.visit('/settings');
    cy.wait('@getConfig');
    
    // Valide se a interface está usando os valores mockados
    cy.get('[data-testid="theme"]').should('contain', 'dark');
  });
});
```

### 3. Interceptando e Modificando Requisições

```javascript
describe('Teste de Modificação', () => {
  it('deve modificar o corpo da requisição', () => {
    cy.intercept('POST', '/usuarios', (req) => {
      // Modifica o corpo da requisição
      req.body.role = 'admin';
      req.reply();
    }).as('postUsuario');
    
    cy.visit('/new-user');
    cy.get('form').submit();
    
    cy.wait('@postUsuario')
      .its('request.body.role')
      .should('eq', 'admin');
  });
});
```

### 4. Mockando Respostas de Erro

```javascript
describe('Teste de Tratamento de Erro', () => {
  it('deve tratar erros de API com graciosidade', () => {
    cy.intercept('GET', '/dados', {
      statusCode: 500,
      body: { 
        error: 'Internal Server Error' 
      }
    }).as('getDataError');
    
    cy.visit('/data-page');
    cy.wait('@getDataError');
    
    // Valide se a mensagem de erro é exibida
    cy.get('[data-testid="error-message"]')
      .should('be.visible')
      .should('contain', 'Erro ao carregar dados');
  });
});
```

## Recursos Avançados

### Validação de Múltiplas Requisições

```javascript
cy.intercept('GET', '/api/*').as('apiRequests');

cy.visit('/dashboard');

// Aguarda 3 requisições diferentes
cy.wait('@apiRequests').then((intercepts) => {
  expect(intercepts.length).to.equal(3);
});
```

### Delay Simulado

```javascript
cy.intercept('GET', '/usuarios', (req) => {
  req.reply((res) => {
    // Adiciona um delay de 2 segundos
    res.delay(2000);
  });
}).as('getUsuariosComDelay');
```

### Verificação de Headers

```javascript
cy.intercept('POST', '/login', (req) => {
  expect(req.headers['content-type']).to.equal('application/json');
  req.continue();
}).as('postLogin');
```

## Configuração

O arquivo `cypress.config.js` contém as configurações principais:

- **baseUrl**: URL base para os testes (`http://lojaebac.ebaconline.art.br/`)
- **Browser Launch Events**: Configuração especial para Chrome com debugging
- **Tasks**: Utilitários customizados como `tabNavigation`

## Documentação Oficial

- [Documentação Cypress Intercept](https://docs.cypress.io/api/commands/intercept)
- [Cypress Best Practices](https://docs.cypress.io/guides/references/best-practices)
- [Network Requests no Cypress](https://docs.cypress.io/guides/guides/network-requests)

## Contribuindo

Contribuições são bem-vindas! Para contribuir:

1. Faça um fork do projeto
2. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

## Licença

Este projeto está sob a licença **ISC**. Veja mais detalhes em [ISC License](https://opensource.org/licenses/ISC).

## Autor

**Pedro Ricardo**

- GitHub: [@Pedro001979](https://github.com/Pedro001979)
- Repositório: [cy-intercept](https://github.com/Pedro001979/cy-intercept)

## Suporte

Se tiver dúvidas ou encontrar problemas, abra uma issue no repositório.

---

**Última atualização**: 2026-05-05
