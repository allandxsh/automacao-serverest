# Automação de Testes de API - Desafio ServeRest 🚀

Este repositório contém o projeto de automação de testes para a API RESTful ServeRest. O objetivo principal é garantir **100% de cobertura** das operações de usuários, abordando validações de contrato, regras de negócio, fluxos de exceção, segurança e testes de performance (Rate Limit).

## 🛠️ Stack Tecnológica
* **Postman:** Estruturação, scripts de pré-requisição e testes (assertions).
* **Newman:** Execução da suíte via linha de comando (CLI).
* **Newman HTML Extra:** Geração de relatórios visuais detalhados.
* **GitHub Actions:** Integração Contínua (CI) e geração de artefatos.

## ⚙️ Como Executar Localmente

### Pré-requisitos
* [Node.js](https://nodejs.org/) instalado.
* Instalar o Newman e o reporter globalmente:
  ```bash
  npm install -g newman newman-reporter-htmlextra
  ```

### Rodando os Testes
* Na raiz do projeto, execute os comandos abaixo:

1. Testes Funcionais (CRUD e Regras de Negócio):
```
newman run postman/ServeRest_Collection.json -e postman/ServeRest_Environment.json --folder "Testes Funcionais" -r "cli,htmlextra" --reporter-htmlextra-export relatorio-funcional.html
```
2. Teste de Performance (Rate Limit - 101 requests):
```
newman run postman/ServeRest_Collection.json -e postman/ServeRest_Environment.json --folder "Performance" -n 101 -r "cli,htmlextra" --reporter-htmlextra-export relatorio-performance.html
```

## 🧪 Cobertura de Testes (Cenários Implementados)
A suíte foi dividida estrategicamente para cobrir todos os comportamentos mapeados na documentação da API:

* ✅ Autenticação (Login): Geração dinâmica de JWT, validação de sucesso e falha (401).
* ✅ CRUD (Caminho Feliz): Criação, Leitura, Atualização (incluindo Upsert) e Exclusão de usuários.
* ✅ Regras de Negócio (400): Bloqueio de e-mails duplicados, formatos de e-mail inválidos e campos obrigatórios ausentes.
* ✅ Segurança e Autorização (401 / 403): Validação de tokens inválidos/ausentes e tentativa de ações em rotas restritas sem perfil de Administrador.
* ✅ Performance (429): Teste de stress validando o bloqueio de requisições após o limite de 100 chamadas por minuto.

### ⚠️ Bugs e Divergências Identificados

Segurança na Rota de Usuários: O requisito do desafio estipula que "A autenticação é feita via token JWT". Contudo, identificou-se que as rotas de Criação e Exclusão (/usuarios) estão abertas na API do ServeRest, permitindo manipulação sem o cabeçalho de Authorization. Os testes de exclusão foram ajustados para refletir o comportamento real (200 OK sem token), mas isso caracteriza uma vulnerabilidade em relação ao requisito original.

Ausência de Rate Limit (Requisito Não-Funcional):** O desafio exige que a API possua limitação de 100 requisições por minuto. O teste de stress (101 requisições sequenciais) foi executado em aproximadamente 25 segundos, porém a API não retornou o status `429 Too Many Requests`, processando todas as chamadas com sucesso (`200 OK`). O teste foi ajustado para atuar como um *Known Issue* (passando a pipeline, mas evidenciando o bug no relatório).
