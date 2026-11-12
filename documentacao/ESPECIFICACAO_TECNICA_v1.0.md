# FinançasWEB — Especificação Técnica v1.0

**Status:** Escopo inicial aprovado  
**Produto:** SaaS de gestão financeira para MEIs e microempresas  
**Stack:** React (frontend), FastAPI (backend) e PostgreSQL.

## 1. Visão geral

O FinançasWEB será uma aplicação SaaS de gestão financeira para pequenos negócios de diferentes segmentos. Cada conta poderá administrar várias empresas, e cada empresa poderá ter vários usuários com permissões distintas.

A versão 1.0 prioriza o controle financeiro manual, a segurança dos dados, o isolamento entre empresas e a gestão de assinaturas.

## 2. Decisões confirmadas

| Tema | Decisão |
|---|---|
| Modelo | SaaS multiempresa |
| Empresas por conta | Múltiplas |
| Usuários por empresa | Múltiplos, com permissões |
| Cadastro financeiro | Manual na versão 1.0 |
| Identificação | CPF ou CNPJ |
| Teste gratuito | 7 dias |
| Cobrança | Mensal e anual |
| Pagamento | Gateway integrado |
| Após vencimento | Bloquear acesso conforme regras da assinatura |
| Dados após bloqueio | Preservados, sem exclusão automática |
| Segmentos | Geral |

O provedor de pagamento ainda será escolhido. A integração deverá usar uma camada adaptadora para reduzir o acoplamento ao fornecedor.

## 3. Escopo funcional

### 3.1 Usuários e autenticação
- Cadastro, login e logout.
- Recuperação e redefinição de senha.
- Verificação de e-mail.
- Consulta do usuário autenticado.
- Gerenciamento, expiração e revogação de sessões.
- Proteção contra tentativas abusivas.

### 3.2 Empresas e membros
- Criar, consultar e editar empresas.
- Armazenar CPF/CNPJ, segmento, contato, endereço e configurações.
- Vários negócios por conta de usuário.
- Convites e gerenciamento de membros.
- Assinatura vinculada à empresa.

### 3.3 Dashboard
- Saldo realizado.
- Receitas e despesas do período.
- Contas a pagar e a receber.
- Pendências e vencimentos.
- Fluxo de caixa e movimentações recentes.
- Separação clara entre valores previstos e realizados.

### 3.4 Receitas e despesas
- Cadastro manual, edição e exclusão conforme regras de integridade.
- Descrição, valor, categoria, contraparte e conta financeira.
- Data de competência, vencimento e liquidação.
- Situações pendente, parcial, pago/recebido e vencido.
- Histórico de alterações.

### 3.5 Contas a pagar e receber
- Listagem e filtros por período, situação, categoria e conta.
- Pagamentos e recebimentos parciais.
- Histórico de liquidações.
- Atualização consistente dos saldos.

### 3.6 Contas financeiras e transferências
- Caixa e contas bancárias.
- Saldo inicial, ativação e desativação.
- Consulta de movimentações.
- Transferências entre contas da mesma empresa.

### 3.7 Categorias
- Categorias padrão e personalizadas por empresa.
- Separação entre receitas e despesas.
- Inativar categorias usadas em lançamentos históricos em vez de apagá-las.

### 3.8 Relatórios
- Fluxo de caixa, receitas, despesas e categorias.
- Contas pendentes e vencidas.
- Histórico de movimentações.
- Exportação CSV e PDF.

### 3.9 Perfis e permissões

| Perfil | Permissões gerais |
|---|---|
| Proprietário | Controle completo da empresa |
| Administrador | Gerenciamento da empresa e membros, conforme regras do proprietário |
| Financeiro | Operações financeiras |
| Visualizador | Consulta e relatórios |

Toda permissão será validada no backend. Esconder botões no frontend não constitui autorização.

### 3.10 Assinaturas e administração SaaS
- Teste gratuito de 7 dias.
- Planos mensais e anuais.
- Gateway de pagamento e histórico de cobranças.
- Webhooks autenticados e idempotentes.
- Estados de assinatura: teste, ativa, vencida, inadimplente, cancelada ou bloqueada, conforme política final.
- Bloqueio de funcionalidades após vencimento, preservando os dados.
- Painel SaaS para empresas, usuários, planos, assinaturas, pagamentos, métricas e eventos técnicos.
- O administrador SaaS não terá acesso irrestrito aos dados financeiros dos clientes; acessos excepcionais deverão ser justificados e auditados.

## 4. Regras financeiras

1. Lançamentos pendentes não alteram o saldo realizado.
2. Pagamentos confirmados reduzem o saldo da conta correspondente.
3. Recebimentos confirmados aumentam o saldo da conta correspondente.
4. Liquidações parciais afetam apenas o valor efetivamente liquidado.
5. Transferências entre contas próprias não são receitas nem despesas.
6. Competência, vencimento e liquidação são datas distintas.
7. Relatórios separam valores previstos e realizados.
8. Saldo de caixa não é sinônimo de lucro contábil.
9. Operações financeiras devem ser transacionais e auditáveis.
10. Valores monetários serão armazenados como centavos inteiros ou Decimal de precisão fixa, nunca float binário.

## 5. Arquitetura e tecnologias

| Camada | Tecnologia |
|---|---|
| Frontend | React, JavaScript, HTML5, CSS3 e Vite |
| Backend | Python e FastAPI |
| Validação | Pydantic |
| ORM | SQLAlchemy |
| Migrações | Alembic |
| Banco | PostgreSQL |
| Servidor | Ubuntu Server |
| Proxy | Nginx |
| Acesso externo | Cloudflare Tunnel |
| Serviço | systemd |
| Testes | pytest |

A arquitetura será um **monólito modular**, sem microserviços na versão 1.0. O React será um cliente independente da API. O trabalho de implementação deste repositório será concentrado no backend; o frontend será tratado como cliente/contrato de integração, sem desenvolvimento visual nesta etapa.

## 6. Estrutura proposta

```text
FinancasWEB/
├── backend/
│   ├── app/
│   │   ├── main.py
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
│   ├── requirements.txt
│   └── .env.example
├── frontend/ (cliente React; fora do escopo de implementação atual)
├── documentacao/
└── README.md
```

## 7. Modelo de dados inicial

Entidades previstas:
- usuarios
- empresas
- membros_empresa
- sessoes
- convites_empresa
- contas_financeiras
- categorias
- lancamentos
- pagamentos
- transferencias
- planos
- assinaturas
- pagamentos_assinatura
- eventos_gateway
- logs_auditoria
- notificacoes
- configuracoes_empresa

O modelo definitivo será refinado antes das migrações, com chaves estrangeiras, índices, restrições e regras de exclusão explícitas.

### Isolamento multiempresa
- Entidades de negócio terão vínculo com empresa_id, direto ou indireto.
- O backend validará associação e permissão antes de qualquer leitura ou alteração.
- empresa_id enviado pelo cliente nunca será considerado prova de autorização.
- Consultas, alterações, exclusões, relatórios e exportações serão escopados pela empresa.
- Testes tentarão acessar dados de outra empresa e verificarão a negação.
- PostgreSQL Row Level Security poderá ser avaliado como defesa adicional.

## 8. Rotas iniciais da API

### Autenticação
| Método | Rota | Finalidade |
|---|---|---|
| POST | /api/auth/register | Criar usuário |
| POST | /api/auth/login | Autenticar |
| POST | /api/auth/logout | Encerrar sessão |
| POST | /api/auth/recuperar-senha | Solicitar recuperação |
| POST | /api/auth/redefinir-senha | Redefinir senha |
| GET | /api/auth/me | Consultar usuário/sessão |

### Empresas
| Método | Rota | Finalidade |
|---|---|---|
| GET | /api/empresas | Listar empresas do usuário |
| POST | /api/empresas | Criar empresa |
| GET | /api/empresas/{empresa_id} | Consultar empresa |
| PUT | /api/empresas/{empresa_id} | Atualizar empresa |
| GET | /api/empresas/{empresa_id}/membros | Listar membros |
| POST | /api/empresas/{empresa_id}/convites | Convidar membro |

### Financeiro
| Método | Rota | Finalidade |
|---|---|---|
| GET | /api/empresas/{empresa_id}/dashboard | Indicadores |
| GET | /api/empresas/{empresa_id}/lancamentos | Listar lançamentos |
| POST | /api/empresas/{empresa_id}/lancamentos | Criar lançamento |
| GET | /api/empresas/{empresa_id}/lancamentos/{id} | Consultar lançamento |
| PUT | /api/empresas/{empresa_id}/lancamentos/{id} | Atualizar lançamento |
| DELETE | /api/empresas/{empresa_id}/lancamentos/{id} | Excluir lançamento |
| GET | /api/empresas/{empresa_id}/contas | Listar contas |
| POST | /api/empresas/{empresa_id}/contas | Criar conta |
| PUT | /api/empresas/{empresa_id}/contas/{id} | Atualizar conta |

### Relatórios e assinatura
| Método | Rota | Finalidade |
|---|---|---|
| GET | /api/empresas/{empresa_id}/relatorios/fluxo-caixa | Fluxo de caixa |
| GET | /api/empresas/{empresa_id}/relatorios/receitas | Receitas |
| GET | /api/empresas/{empresa_id}/relatorios/despesas | Despesas |
| GET | /api/empresas/{empresa_id}/assinatura | Consultar assinatura |
| POST | /api/empresas/{empresa_id}/assinatura | Iniciar/alterar contratação |
| POST | /api/webhooks/pagamentos/{provedor} | Receber evento do gateway |

Os contratos definitivos serão documentados no OpenAPI da aplicação.

## 9. Segurança, auditoria e LGPD

- Hash seguro de senhas com salt.
- Sessões protegidas, expiráveis e revogáveis.
- HTTPS em produção.
- Validação de entradas e limites de tamanho.
- Rate limiting em rotas sensíveis.
- Segredos fora do Git, em variáveis de ambiente ou gerenciador apropriado.
- Autorização por empresa e por ação.
- Logs sem senhas, tokens ou dados sensíveis desnecessários.
- Verificação de autenticidade de webhooks e prevenção de duplicidade.
- Política de retenção, exportação e exclusão conforme LGPD.
- Backups automáticos e testes periódicos de restauração.

## 10. Fases de desenvolvimento

1. Fundação: estrutura, PostgreSQL, SQLAlchemy, Alembic, autenticação, empresas, membros e isolamento.
2. Financeiro: contas, categorias, lançamentos, pagamentos, recebimentos e transferências.
3. Consultas: dashboard, filtros, relatórios e exportações.
4. SaaS: planos, teste, assinaturas, gateway, webhooks e bloqueio por vencimento.
5. Produção: testes, segurança, auditoria, LGPD, backups e implantação.

## 11. Critérios mínimos de aceite

- Cadastro, autenticação e recuperação de acesso funcionais.
- Usuário pode administrar várias empresas.
- Membros acessam somente empresas e ações autorizadas.
- Tentativas de acesso cruzado entre empresas são negadas.
- Pagamentos parciais e transferências mantêm saldos corretos.
- Relatórios distinguem previsto e realizado.
- Webhooks não duplicam operações.
- Bloqueio da assinatura não apaga dados.
- Backups podem ser restaurados e testes críticos passam.

## 12. Fora do escopo inicial

- Open Finance e integração bancária automática.
- Emissão fiscal ou contabilidade oficial.
- Aplicativo mobile nativo.
- Microserviços.
- Inteligência artificial.
- Automação de lançamentos bancários.

---

Documento de referência do escopo técnico inicial do FinançasWEB.
