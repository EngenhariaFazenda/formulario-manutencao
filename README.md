# 📋 Formulário de Manutenção Predial

Sistema web para recebimento e gerenciamento de solicitações de manutenção predial, com integração ao Microsoft Planner.

## 🌐 Deploy no Render

Este projeto está configurado para deploy automático no Render.

### Pré-requisitos
- Conta no GitHub
- Conta no Render (gratuita)

### Deploy Rápido
1. Faça fork/clone deste repositório
2. Conecte ao Render
3. Configure as variáveis de ambiente
4. Deploy automático!

### Variáveis de Ambiente Necessárias
```
FLASK_ENV=production
SECRET_KEY=sua-chave-secreta-forte
DATABASE_URL=sqlite:///app.db
PORT=5000
```

### Para Integração com Microsoft Planner
```
MICROSOFT_CLIENT_ID=seu-client-id
MICROSOFT_CLIENT_SECRET=seu-client-secret
MICROSOFT_TENANT_ID=seu-tenant-id
PLANNER_PLAN_ID=seu-plan-id
```

## 🚀 Funcionalidades

- ✅ Formulário responsivo para solicitações
- ✅ Validação de dados em tempo real
- ✅ Upload de arquivos/fotos
- ✅ Geração automática de protocolos
- ✅ Dashboard com estatísticas
- ✅ API REST completa
- ✅ Integração Microsoft Planner (opcional)

## 📊 Endpoints da API

- `GET /` - Formulário principal
- `POST /api/solicitacoes` - Criar solicitação
- `GET /api/solicitacoes` - Listar solicitações
- `GET /api/estatisticas` - Estatísticas do sistema
- `GET /health` - Health check

## 🛠️ Desenvolvimento Local

```bash
# Instalar dependências
pip install -r requirements.txt

# Configurar variáveis de ambiente
cp .env.example .env

# Executar aplicação
python src/main.py
```

## 📞 Suporte

Para dúvidas sobre configuração ou uso do sistema, consulte a documentação completa incluída no projeto.

