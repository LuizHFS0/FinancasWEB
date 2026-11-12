# FinançasWEB

**Gestão financeira simples e organizada para MEIs e microempresas.**

O FinançasWEB é um SaaS de gestão financeira geral para pequenos negócios. A plataforma reúne receitas, despesas, contas a pagar, contas a receber, contas financeiras e relatórios em um único ambiente, com suporte a múltiplas empresas por conta e múltiplos usuários por empresa.

> **Versão:** 1.0 — especificação inicial  
> **Status:** Em planejamento e desenvolvimento

## Objetivos

- Facilitar o controle financeiro de pequenos negócios.
- Substituir controles dispersos em planilhas e anotações.
- Apresentar saldos, receitas, despesas e pendências com clareza.
- Permitir que equipes trabalhem na mesma empresa com permissões distintas.
- Oferecer uma solução web responsiva por assinatura.

## Público-alvo

- MEIs e microempresas.
- Pequenos comércios e lojas.
- Agropecuárias.
- Prestadores de serviços.
- Oficinas e assistências técnicas.
- Pequenos negócios de diferentes segmentos.

## Funcionalidades previstas — versão 1.0

- Cadastro, autenticação e recuperação de senha.
- Cadastro e gerenciamento de múltiplas empresas por conta.
- Convites e permissões de usuários por empresa.
- Dashboard financeiro.
- Cadastro de receitas e despesas.
- Contas a pagar e contas a receber.
- Contas financeiras (caixa e contas bancárias).
- Pagamentos e recebimentos parciais.
- Transferências entre contas próprias.
- Categorias financeiras.
- Relatórios e exportações CSV/PDF.
- Teste gratuito de 7 dias.
- Assinaturas mensais e anuais com gateway de pagamento.
- Bloqueio de acesso aos módulos financeiros após o fim do período contratado, sem apagar os dados.

## Tecnologias

| Camada | Tecnologia |
|---|---|
| Frontend | React, JavaScript, HTML5, CSS3 e Vite |
| Backend | Python, FastAPI e Pydantic |
| ORM | SQLAlchemy |
| Migrações | Alembic |
| Banco de dados | PostgreSQL |
| Servidor | Ubuntu Server |
| Proxy web | Nginx |
| Acesso externo | Cloudflare Tunnel |

A aplicação será inicialmente um **monólito modular**, evitando a complexidade de microserviços na primeira versão.

## Arquitetura resumida

1. O navegador carrega a aplicação React.
2. O React consome a API HTTP do FastAPI.
3. O backend autentica o usuário e valida sua associação e permissões na empresa selecionada.
4. O SQLAlchemy acessa o PostgreSQL.
5. As regras financeiras são aplicadas no backend; o frontend não acessa o banco diretamente.

## Multiempresa e isolamento de dados

Uma conta poderá administrar várias empresas. Cada empresa terá seus próprios dados financeiros, membros, configurações e assinatura.

As tabelas pertencentes ao contexto de uma empresa deverão possuir uma referência `empresa_id`. Toda operação deverá validar no backend se o usuário possui acesso à empresa e ao recurso solicitado. O isolamento entre empresas é um requisito obrigatório, não apenas uma regra de interface.

## Perfis iniciais

| Perfil | Acesso |
|---|---|
| Proprietário | Controle completo da empresa |
| Administrador | Gerenciamento da empresa e dos usuários |
| Financeiro | Operações financeiras |
| Visualizador | Consulta de informações e relatórios |

As permissões serão aplicadas por empresa; o mesmo usuário poderá ter perfis diferentes em empresas distintas.

## Regras financeiras essenciais

- Lançamentos pendentes não alteram o saldo realizado.
- Pagamentos confirmados reduzem o saldo da conta correspondente.
- Recebimentos confirmados aumentam o saldo da conta correspondente.
- Pagamentos e recebimentos parciais atualizam somente os valores efetivamente liquidados.
- Transferências entre contas próprias não são receitas nem despesas.
- O sistema diferencia valores previstos e realizados.
- Datas de vencimento, competência e liquidação serão tratadas separadamente.
- Valores monetários serão armazenados em unidades inteiras de centavos, evitando erros de ponto flutuante.

## Assinaturas

- Teste gratuito de 7 dias por empresa.
- Planos com cobrança mensal e anual.
- Integração com gateway de pagamento.
- Webhooks autenticados e processamento idempotente.
- Assinatura vinculada à empresa.
- Bloqueio após expiração conforme as regras comerciais, preservando os dados para eventual reativação e atendimento de obrigações legais.

O provedor de pagamento será escolhido durante a implementação.

## Segurança e privacidade

- Hash seguro de senhas.
- Autenticação e sessões protegidas.
- Autorização validada no backend em todas as operações.
- Isolamento rigoroso entre empresas.
- HTTPS e proteção de credenciais.
- Auditoria de operações relevantes.
- Backups e testes de restauração.
- Política de privacidade, retenção e exclusão de dados.
- Adequação à LGPD.

## Estrutura inicial do repositório

```text
FinancasWEB/
├── backend/
│   ├── app/
│   │   ├── core/
│   │   ├── database/
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── api/
│   │   ├── services/
│   │   ├── repositories/
│   │   └── integrations/
│   ├── migrations/
│   ├── tests/
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── assets/
│   │   ├── components/
│   │   ├── layouts/
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── contexts/
│   │   ├── routes/
│   │   └── styles/
│   └── package.json
├── documentacao/
└── README.md
```

## Roadmap

1. **Fundação:** estrutura do projeto, PostgreSQL, migrações, cadastro, autenticação, empresas, membros e isolamento.
2. **Gestão financeira:** contas, categorias, receitas, despesas, pagamentos, recebimentos e transferências.
3. **Dashboard e relatórios:** indicadores, fluxo de caixa, relatórios e exportações.
4. **SaaS:** planos, teste gratuito, assinaturas, gateway e webhooks.
5. **Lançamento:** segurança, auditoria, LGPD, backups, restauração, testes e implantação.

## Critérios para lançamento

A versão 1.0 deverá ter autenticação funcional, isolamento multiempresa testado, permissões, operações financeiras consistentes, pagamentos parciais, transferências corretas, relatórios confiáveis, ciclo de teste e assinatura integrado, bloqueio por expiração, backups restauráveis e testes de segurança.

## Observações

- O FinançasWEB é um projeto independente do FazendaWEB.
- A aplicação não deve ser apresentada como substituta de serviços contábeis ou fiscais.
- Integrações bancárias/Open Finance, inteligência artificial e aplicativo mobile ficam fora do escopo inicial.
- Decisões técnicas e comerciais ainda não especificadas serão registradas durante o desenvolvimento.

---

**FinançasWEB — Gestão financeira para pequenos negócios.**
