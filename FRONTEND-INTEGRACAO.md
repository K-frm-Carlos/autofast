# AutoFast — preparação do Front-end para integração

## O que foi alterado

- A tela do mecânico consulta `GET /api/orcamentos` e monta as colunas com os status existentes no Back-end (`PENDENTE`, `EM_ANALISE`, `APROVADO`, `RECUSADO`).
- O formulário de novo orçamento consulta clientes e veículos, cadastra a peça em `POST /api/pecas` e depois cria o orçamento em `POST /api/orcamentos`.
- O detalhe do orçamento exibe os dados retornados pela API e permite editar valores/prazo e alterar o status (`PUT /api/orcamentos/{id}`), além de excluir (`DELETE /api/orcamentos/{id}`).
- O chat não exibe conversas demonstrativas; consulta as mensagens do orçamento quando recebe um ID.
- Perfil e listagem do cliente não exibem mais dados pessoais demonstrativos.
- O cliente Axios está centralizado em `src/services/api.ts`.

## Configuração

1. Instale as dependências: `npm install`.
2. Copie `.env.example` para `.env`.
3. Ajuste `VITE_API_URL` para a URL do Spring Boot (padrão: `http://localhost:8080/api`).
4. Inicie o Back-end e depois execute `npm run dev`.

## Atenção: contrato atual do Back-end

- Para criar um orçamento, o Back-end exige IDs existentes de cliente e veículo, além de um registro de peça. Por isso o formulário usa clientes e veículos já cadastrados e cria a peça antes do orçamento.
- `Veiculo` possui `@JsonIgnore` no relacionamento com `Cliente`, então a listagem atual não informa o `cliente.id` associado ao veículo. Por enquanto, o formulário apresenta todos os veículos disponíveis depois que um cliente é selecionado; idealmente, o Back-end deve expor um DTO com `clienteId` e implementar `GET /api/veiculos/cliente/{id}` sem serialização recursiva.
- A entidade `Peca` exige `fotoUrl` não nulo. Quando o usuário não fornece URL, o Front-end envia uma string em branco para satisfazer a restrição atual. Recomenda-se tornar esse campo opcional no Back-end ou implementar upload.
- O chat só consegue listar mensagens por orçamento. Enviar mensagens exige os IDs de remetente e destinatário, que dependem da autenticação/usuário logado. O envio ainda não foi habilitado para não criar mensagens sem autoria válida.
- O formulário de criação está preparado para o perfil mecânico. Login, cadastro, seleção de perfil e carregamento do perfil real dependem da integração de autenticação, que ainda não existe no contrato de endpoints enviado.

## Endpoints utilizados

| Método | Endpoint | Uso |
|---|---|---|
| GET | `/api/orcamentos` | Listar orçamentos |
| POST | `/api/pecas` | Criar peça vinculada ao orçamento |
| POST | `/api/orcamentos` | Criar orçamento |
| PUT | `/api/orcamentos/{id}` | Atualizar status/dados do orçamento |
| DELETE | `/api/orcamentos/{id}` | Excluir orçamento |
| GET | `/api/clientes` | Carregar clientes |
| GET | `/api/veiculos` | Carregar veículos |
| GET | `/api/mensagens/orcamento/{id}` | Listar mensagens do orçamento |

Se o navegador bloquear chamadas por CORS, será necessário liberar a origem do Vite no Spring Boot (normalmente `http://localhost:5173`).
