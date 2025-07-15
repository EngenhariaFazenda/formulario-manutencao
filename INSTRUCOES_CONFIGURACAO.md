# Instruções de Configuração - Formulário de Manutenção Predial

## Visão Geral

Este sistema permite que usuários dos edifícios enviem solicitações de manutenção através de um formulário online. As solicitações são automaticamente convertidas em tarefas no Microsoft Planner para avaliação da equipe de engenharia antes de serem inseridas no Citsmart.

## Funcionalidades

- ✅ Formulário web responsivo para solicitações de manutenção
- ✅ Validação de dados e upload de arquivos
- ✅ Geração automática de protocolo único
- ✅ Integração com Microsoft Planner (criação automática de tarefas)
- ✅ API REST para gerenciamento de solicitações
- ✅ Dashboard de estatísticas
- ✅ Sincronização de status entre sistema e Planner

## Pré-requisitos

### 1. Registro de Aplicação no Microsoft Entra ID (Azure AD)

Para que a integração com o Microsoft Planner funcione, você precisa registrar uma aplicação no Azure Portal:

1. **Acesse o Azure Portal**: https://portal.azure.com
2. **Navegue para "Microsoft Entra ID"** (antigo Azure Active Directory)
3. **Clique em "App registrations"** no menu lateral
4. **Clique em "New registration"**
5. **Preencha os dados**:
   - **Name**: "Formulário Manutenção Predial"
   - **Supported account types**: "Accounts in this organizational directory only"
   - **Redirect URI**: Deixe em branco por enquanto
6. **Clique em "Register"**

### 2. Configurar Permissões da Aplicação

Após criar a aplicação:

1. **Vá para "API permissions"**
2. **Clique em "Add a permission"**
3. **Selecione "Microsoft Graph"**
4. **Escolha "Application permissions"**
5. **Adicione as seguintes permissões**:
   - `Tasks.ReadWrite.All` (para criar e gerenciar tarefas no Planner)
   - `Group.Read.All` (para acessar informações dos grupos/planos)
6. **Clique em "Grant admin consent"** (requer permissões de administrador)

### 3. Criar Client Secret

1. **Vá para "Certificates & secrets"**
2. **Clique em "New client secret"**
3. **Adicione uma descrição**: "Formulário Manutenção"
4. **Escolha a validade**: 24 meses (recomendado)
5. **Clique em "Add"**
6. **⚠️ IMPORTANTE**: Copie o valor do secret imediatamente (não será mostrado novamente)

### 4. Obter IDs Necessários

Anote os seguintes valores da aplicação:
- **Application (client) ID**: Encontrado na página "Overview"
- **Directory (tenant) ID**: Encontrado na página "Overview"
- **Client Secret**: Copiado no passo anterior

### 5. Obter Plan ID do Microsoft Planner

Para obter o ID do plano do Planner:

**Opção 1 - Via URL do Planner:**
1. Abra o Microsoft Planner no navegador
2. Navegue até o plano desejado
3. Copie o ID da URL: `https://tasks.office.com/[tenant]/Home/PlanViews/[PLAN_ID]`

**Opção 2 - Via Graph Explorer:**
1. Acesse: https://developer.microsoft.com/en-us/graph/graph-explorer
2. Execute: `GET https://graph.microsoft.com/v1.0/me/planner/plans`
3. Encontre o plano desejado e copie o ID

## Configuração do Sistema

### 1. Configurar Variáveis de Ambiente

Copie o arquivo `.env.example` para `.env`:

```bash
cp .env.example .env
```

Edite o arquivo `.env` com os valores obtidos:

```env
# Configurações do Microsoft Graph API
MICROSOFT_TENANT_ID=seu-tenant-id-aqui
MICROSOFT_CLIENT_ID=seu-client-id-aqui
MICROSOFT_CLIENT_SECRET=seu-client-secret-aqui
MICROSOFT_PLANNER_PLAN_ID=seu-plan-id-aqui

# Configurações opcionais
FLASK_ENV=production
FLASK_DEBUG=False
SECRET_KEY=sua-chave-secreta-segura-aqui
```

### 2. Instalar Dependências

```bash
# Ativar ambiente virtual
source venv/bin/activate

# Instalar dependências
pip install -r requirements.txt
```

### 3. Testar Configuração

Execute o teste de conexão:

```bash
curl http://localhost:5000/api/test-planner
```

Resposta esperada:
```json
{
  "success": true,
  "message": "Conexão com Microsoft Planner estabelecida com sucesso",
  "details": {
    "plan_name": "Nome do seu plano",
    "plan_id": "seu-plan-id"
  }
}
```

## Execução

### Desenvolvimento

```bash
python src/main.py
```

O sistema estará disponível em: http://localhost:5000

### Produção

Para produção, recomenda-se usar um servidor WSGI como Gunicorn:

```bash
pip install gunicorn
gunicorn -w 4 -b 0.0.0.0:5000 src.main:app
```

## Uso do Sistema

### Para Usuários Finais

1. **Acesse o formulário** através da URL fornecida
2. **Preencha todos os campos obrigatórios**:
   - Informações do solicitante (nome, email)
   - Localização (edifício, andar, local específico)
   - Detalhes da manutenção (tipo, prioridade, descrição)
3. **Anexe arquivos** se necessário (fotos, documentos)
4. **Clique em "Enviar Solicitação"**
5. **Anote o protocolo** gerado para acompanhamento

### Para Equipe de Engenharia

1. **Acesse o Microsoft Planner** do seu time
2. **Visualize as novas tarefas** criadas automaticamente
3. **Analise os detalhes** da solicitação na descrição da tarefa
4. **Atualize o status** da tarefa conforme o progresso
5. **Use a API** para consultar estatísticas e relatórios

## API Endpoints

### Solicitações

- `POST /api/solicitacoes` - Criar nova solicitação
- `GET /api/solicitacoes` - Listar solicitações
- `GET /api/solicitacoes/{protocolo}` - Obter solicitação específica
- `PUT /api/solicitacoes/{id}/status` - Atualizar status

### Estatísticas

- `GET /api/estatisticas` - Obter estatísticas gerais

### Testes

- `GET /api/test-planner` - Testar conexão com Planner
- `GET /health` - Verificar saúde da aplicação

## Estrutura de Dados

### Solicitação

```json
{
  "id": 1,
  "protocolo": "MAN202507101234ABCD",
  "nome": "João Silva",
  "email": "joao.silva@empresa.com",
  "telefone": "(11) 99999-9999",
  "departamento": "TI",
  "edificio": "sede",
  "andar": 5,
  "local": "Sala 501",
  "tipo": "eletrica",
  "prioridade": "normal",
  "descricao": "Descrição do problema...",
  "status": "pendente",
  "data_criacao": "2025-07-10T10:30:00",
  "planner_task_id": "task-id-no-planner",
  "tem_planner": true
}
```

## Troubleshooting

### Erro de Autenticação

- Verifique se as credenciais estão corretas no arquivo `.env`
- Confirme se o admin consent foi concedido
- Verifique se o client secret não expirou

### Tarefa não criada no Planner

- Verifique se o Plan ID está correto
- Confirme se a aplicação tem permissões adequadas
- Consulte os logs da aplicação para detalhes do erro

### Formulário não carrega

- Verifique se o servidor está rodando
- Confirme se não há erros de JavaScript no console do navegador
- Verifique se o CORS está configurado corretamente

## Logs

Os logs da aplicação contêm informações importantes sobre:
- Criação de solicitações
- Integração com Planner
- Erros de autenticação
- Performance da aplicação

Para visualizar logs em tempo real:

```bash
tail -f logs/app.log
```

## Segurança

- **Nunca compartilhe** o client secret
- **Use HTTPS** em produção
- **Configure firewall** adequadamente
- **Monitore logs** regularmente
- **Atualize dependências** periodicamente

## Suporte

Para suporte técnico:
1. Consulte os logs da aplicação
2. Verifique a documentação da Microsoft Graph API
3. Teste a conectividade com o endpoint de teste
4. Contate o administrador do sistema se necessário

