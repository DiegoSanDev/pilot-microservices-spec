# Projeto Piloto – Pagamentos com Microserviços (MVC)

## Visão Geral
Este projeto é um **piloto de onboarding técnico** que simula o fluxo de **pagamentos de pedidos** utilizando **microserviços** e arquitetura **MVC**.  

O objetivo é praticar conceitos de **orquestração, persistência, documentação de APIs e testes unitários**, aplicando boas práticas de desenvolvimento com **separação de responsabilidades**.  

---

## Arquitetura
O sistema é composto por **três microserviços**, todos organizados seguindo o **padrão MVC**:  

### 1. atom-pagamento (persistência de pagamentos)
- Responsável por salvar e consultar pagamentos.  
- Endpoints RESTful documentados com **OpenAPI**.  
- Uso de **JDBI** para acesso ao banco de dados.  
- **MVC**:  
  - `controller` - Recebe requisições REST.  
  - `service` - Lógica de negócio e validações.  
  - `repository` - Persistência com JDBI.  
  - `model` - Entidades.  

### 2. orch-pagamento (orquestração Camel)
- Serviço de orquestração que gerencia o fluxo do pagamento.  
- Expõe endpoint **POST /v1/payments**.  
- **MVC**:  
  - `controller` - Recebe requisições do cliente.  
  - `service` - Orquestração do fluxo (valida, chama atom-pagamento, atualiza status, publica eventos).
  - `model` - Entidades.   
  - `route` - Rotas do Apache Camel (REST - atom-pagamento - eventos).  

### 3. atom-notificacao (notificação)
- Consome eventos de pagamento aprovado.  
- Simula envio de notificação (log ou console).  
- **MVC**:  
  - `consumer` → Recebe eventos assíncronos.  
  - `service` → Processa evento e envia notificação.
  - `repository` - Persistência com JDBI.   
  - `model` → Representa dados do evento.  

---

## Fluxo do Pagamento (MVC)
1. O **cliente** envia uma requisição `POST /v1/payments` para o **orch-pagamento**. 
2. O **controller** encaminha para o **service** do orch-pagamento, que:  
   - Valida dados do pagamento.  
   - Chama o `atom-pagamento` para persistência.  
   - Atualiza o status do pagamento (`PENDING - APPROVED`).  
   - Publica um evento assíncrono de pagamento aprovado.  
3. O **atom-notificacao** consome o evento (`consumer - service`) e processa a notificação.  

Com MVC, cada camada tem responsabilidades claras, mantendo **separação de responsabilidades**.  

---

## Tecnologias Utilizadas
- **Java 21+**  
- **Apache Camel**  
- **JDBI**  
- **OpenAPI / Swagger**  
- **JUnit 5**  
- **Maven**  
- **Docker Compose**  

---

## Estrutura de Pastas

### atom-pagamento
/atom-pagamento
  - controller        # Controladores REST
  - model             # Entidades e DTOs
  - service           # Regras de negócio
  - repository        # Persistência via JDBI
  - test              # Testes unitários
  
### orch-pagamento
/orch-pagamento
  - controller        # Controladores REST
  - model             # Entidades
  - service           # Regras de negócio
  - route             # Rotas do Apache Camel
  - test              # Testes unitários
  
### atom-notificacao
/orch-pagamento
  - consumer          # Consumers de eventos
  - model             # Entidades
  - service           # Regras de negócio
  - test              # Testes unitários
  

