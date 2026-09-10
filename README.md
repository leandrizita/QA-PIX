# QA PIX — Verificação de Chave e Transação Simples

Projeto de portfólio de **QA (Quality Assurance)** com foco em testes
**manuais e automatizados** de um fluxo bancário: verificação de **chave
PIX** e execução de uma **transação simples** entre duas chaves.

> ⚠️ Projeto educacional. A API é um **mock** com dados fictícios em
> memória — não se conecta a nenhum banco real ou ao DICT do Bacen.

## Por que este projeto existe

Serve como demonstração prática de competências de QA aplicadas a um
domínio bancário sensível (PIX):
- Elaboração de **plano de testes**.
- Escrita de **casos de teste manuais** rastreáveis às regras de negócio.
- **Automação de testes de API** com Pytest.
- **Pipeline de CI** no GitHub Actions.

## Estrutura do projeto

```
pix-qa-project/
├── src/api/                    # API mock (Flask) que simula o backend bancário
│   ├── app.py
│   └── requirements.txt
├── tests/automated/             # Testes automatizados (Pytest)
│   ├── conftest.py
│   ├── test_validar_chave.py
│   └── test_transacao_pix.py
├── docs/
│   ├── plano-de-testes.md       # Plano de testes, estratégia e matriz de rastreabilidade
│   └── casos-de-teste-manuais.md# Casos de teste manuais em tabela
├── .github/workflows/ci.yml     # Pipeline de CI
├── requirements-dev.txt
└── README.md
```

## Regras de negócio simuladas
- Tipos de chave suportados: CPF, CNPJ, e-mail, telefone, chave aleatória (UUID).
- Toda chave é validada por **formato** e, em seguida, por **existência/status**.
- Limite de transação simples: **R$ 5.000,00**.
- Não é permitido transferir para a própria chave.
- Transação é recusada se origem ou destino estiverem com a conta bloqueada.

Detalhes completos em [`docs/plano-de-testes.md`](docs/plano-de-testes.md).

## Como rodar localmente

### 1. Instalar dependências
```bash
pip install -r src/api/requirements.txt
pip install -r requirements-dev.txt
```

### 2. Subir a API mock
```bash
python src/api/app.py
# API disponível em http://localhost:5000
```

### 3. Rodar os testes automatizados
```bash
pytest tests/automated -v
```

### 4. Testar manualmente (exemplo com cURL)
```bash
curl -X POST http://localhost:5000/api/pix/validar-chave \
  -H "Content-Type: application/json" \
  -d '{"chave": "12345678900"}'

curl -X POST http://localhost:5000/api/pix/transacao \
  -H "Content-Type: application/json" \
  -d '{"chave_origem": "98765432100", "chave_destino": "12345678900", "valor": 150.00}'
```

Os casos de teste manuais completos (passo a passo, dados e resultado
esperado) estão em
[`docs/casos-de-teste-manuais.md`](docs/casos-de-teste-manuais.md).

## Endpoints da API mock

| Método | Rota | Descrição |
|---|---|---|
| POST | `/api/pix/validar-chave` | Valida formato e existência de uma chave PIX |
| POST | `/api/pix/transacao` | Executa uma transação PIX simples entre duas chaves |
| GET | `/api/pix/transacao/<id>` | Consulta uma transação pelo ID |
| GET | `/api/health` | Health check |

## Chaves cadastradas na massa de teste (mock)

| Chave | Tipo | Titular | Status |
|---|---|---|---|
| `12345678900` | CPF | Maria Souza | Ativa |
| `98765432100` | CPF | João Pereira | Ativa |
| `11222333000181` | CNPJ | Comercio LTDA | Ativa |
| `maria.souza@email.com` | E-mail | Maria Souza | Ativa |
| `+5561999998888` | Telefone | Carlos Lima | Ativa |
| `b6f1c9d4-2e3a-4f5b-9c1d-7a8e6f0a1b2c` | Aleatória | Ana Rocha | Ativa |
| `55555555555` | CPF | Conta Bloqueada | **Bloqueada** |

## Stack utilizada
- **Python 3.11** + **Flask** (API mock)
- **Pytest** (testes automatizados)
- **GitHub Actions** (CI)
- Documentação em **Markdown**

## Próximos passos (roadmap)
- [ ] Testes de contrato (schema da API).
- [ ] Testes de performance com Locust/k6.
- [ ] Automação de UI (Cypress/Playwright) sobre tela simulada de transação.

## Licença
MIT — sinta-se livre para usar como referência de estudo.
