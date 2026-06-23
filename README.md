# Mobix — SaaS para Assistência Técnica   |   Mobix — SaaS for Tech Repair Shops

**Implementado em assistência técnica na cidade do Recife/PE — Brasil**  
**Implemented in a tech repair shop in Recife/PE — Brazil**

---

## Sobre / About

**PT:** Mobix é um sistema SAAS multi-tenant desenvolvido em PHP 8 + MySQL 8 para gestão de assistências técnicas de celulares e eletrônicos. Foi implementado e validado em uma assistência técnica real na cidade do Recife/PE, resolvendo problemas do dia a dia como controle de ordens de serviço, gestão de estoque, vendas e relatórios gerenciais.

**EN:** Mobix is a multi-tenant SAAS system built with PHP 8 + MySQL 8 for electronics repair shop management. It was deployed and validated in a real repair shop in Recife/PE, solving daily operational challenges such as service order tracking, inventory management, sales control, and management reporting.

> ![Interface - Mobix](https://raw.githubusercontent.com/meaeduarda/saas_mobix/main/sistema_mobix.png)

---

## Stack

```
PHP 8.x (strict types)    →   Backend / API
MySQL 8 / MariaDB 10.6+   →   Database (14 tables, utf8mb4, InnoDB)
HTML5 + CSS3 + Vanilla JS →   Frontend (no frameworks)
PHPMailer 7.1              →   Email (2FA, password recovery)
Apache (mod_rewrite)       →   Web server
```

---

## 🗄️ Arquitetura de Dados / Data Architecture

### Visão Geral / Overview

**PT:** Banco de dados MySQL 8 com **14 tabelas normalizadas**, projetado para arquitetura multi-tenant com isolamento por `empresa_id`. Cada tenant (empresa) possui seus próprios dados isolados em nível de linha, sem necessidade de schemas ou bancos separados.

**EN:** MySQL 8 database with **14 normalized tables**, designed for a multi-tenant architecture with row-level isolation via `empresa_id`. Each tenant has its data isolated at row level, without requiring separate schemas or databases.

### Diagrama ER / ER Diagram

> ![Diagrama ER - Mobix](https://raw.githubusercontent.com/meaeduarda/saas_mobix/main/diagramaER.png)

## 🧱 Modelagem / Schema Highlights

### Domínios de Negócio / Business Domains

| Domínio / Domain | Tabelas / Tables | Destaque / Highlight |
|------------------|------------------|----------------------|
| **Tenants** | `empresas` | Slug único por tenant, política de retenção de fotos configurável |
| **Auth & Users** | `usuarios`, `codigos_2fa`, `solicitacoes_recuperacao`, `log_permissoes` | RBAC (admin/tecnico/atendente), 2FA por email, trial automático |
| **Clientes** | `clientes`, `aparelhos_clientes` | CPF único por empresa, histórico de equipamentos por cliente |
| **Ordens de Serviço** | `ordens_servico`, `saidas_os`, `pecas_utilizadas_os` | Ciclo de vida com 6 status, fotos JSON, checklist, garantia |
| **Estoque** | `estoque`, `entradas_estoque` | Categorias, estoque mínimo, custo médio, fornecedor |
| **Vendas** | `vendas`, `itens_venda` | Split 1:N (migração de legado), COGS tracking |

### Decisões de Modelagem / Modeling Decisions

| Decisão | O que foi feito | Por quê |
|---------|----------------|---------|
| **Multi-tenancy** | `empresa_id` em todas as tabelas de negócio + FK | Isolamento de dados sem overhead de schemas separados |
| **ENUMs como VARCHAR** | Status, perfis, categorias armazenados como string | Flexibilidade para evolução sem ALTER TABLE |
| **JSON columns** | `fotos_json`, `checklist_json`, `garantia_json` | Dados semi-estruturados sem criar dezenas de tabelas |
| **Índices compostos** | `(empresa_id, numero_os)`, `(empresa_id, email)` | Performance em queries multi-tenant |
| **Chave única por tenant** | `UNIQUE KEY uk_* (empresa_id, campo)` | Cada tenant tem seu próprio namespace de numeração |
| **ON DELETE RESTRICT** | FKs de `empresa_id` | Impede deleção acidental de tenant com dados |

---

## 🔐 Segurança no Banco de Dados / Database Security

- **Prepared statements** em 100% das queries (sem ORM, PDO puro)
- **Senhas hash** com `password_hash()` + `password_verify()`
- **2FA** com código de 6 dígitos via email, expiração em tempo real
- **CSRF tokens** em todas as ações de escrita
- **Auditoria** via `log_permissoes` com IP e timestamp
- **Status de acesso** com trial, suspensão e expiração automática

---

## 📊 Relatórios e Agregações / Reporting & Aggregations

**PT:** Todo o reporting é feito diretamente nas tabelas transacionais (OLTP) 

**EN:** All reporting runs directly on transactional tables (OLTP) 

```
📈 Dashboard          → Vendas hoje/mês, OS abertas, estoque baixo
💰 Financeiro         → Faturamento, lucro bruto (COGS tracking)
📋 Relatório Mensal   → Filtro por período, método de pagamento
📦 Inventário         → Valor de custo vs. valor de venda
```

---


**Requisitos / Requirements:**

- PHP 8.0+
- MySQL 8.0+ ou MariaDB 10.6+
- Apache com mod_rewrite
- Extensões PHP: PDO, PDO_MySQL, mbstring

---

## 🏗️ Estratégia de Armazenamento / Storage Strategy

**PT:** O sistema iniciou com armazenamento em arquivos JSON e migrou gradualmente para MySQL. Ambas as camadas coexistem com fallback automático — se o MySQL não estiver disponível, o sistema lê dos arquivos JSON.

**EN:** The system started with JSON file storage and gradually migrated to MySQL. Both layers coexist with automatic fallback — if MySQL is unavailable, the system reads from JSON files.

---

**Desenvolvido e implementado em assistência técnica na cidade do Recife/PE**  
_Developed and deployed in a tech repair shop in Recife/PE, Brazil_

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Maria_Eduarda-blue)](https://www.linkedin.com/in/maria-eduarda-acavalcanti/)
[![GitHub](https://img.shields.io/badge/GitHub-saas_mobix-black)](https://github.com/meaeduarda/saas_mobix)

