# LocalTV

> Plataforma de sinalização digital para administrar telas, mídias, playlists e conteúdos dinâmicos em tempo real.

![LocalTV — Sinalização Digital, Telas e Conteúdo](https://github.com/betoarts/localTV/raw/main/docs/localtv-cover.jpg)

[![Node.js](https://img.shields.io/badge/Node.js-Express-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=20232a)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-6-646CFF?logo=vite&logoColor=white)](https://vitejs.dev/)
[![SQLite](https://img.shields.io/badge/SQLite-local-003B57?logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

O **LocalTV** transforma TVs, monitores e painéis em canais de comunicação gerenciados centralmente. A plataforma permite organizar mídias, montar playlists, configurar templates e overlays, acompanhar dispositivos conectados e distribuir atualizações em tempo real para uma rede de telas.

> **Status:** em desenvolvimento ativo. Revise autenticação, isolamento de clientes, armazenamento persistente e chaves de IA antes de utilizar em produção.

## Principais recursos

- **Biblioteca de mídia:** upload e organização de imagens, vídeos, HTML e arquivos por cliente.
- **Playlists:** sequência de conteúdos com ordem, duração e programação ajustáveis.
- **Player para telas:** suporte a orientação horizontal/vertical, resolução, transições, volume e reprodução.
- **Controle em tempo real:** atualizações de playlist, comandos e status via Socket.IO.
- **Multi-cliente:** isolamento lógico por `client_id` para clientes, telas, mídias, playlists e templates.
- **Overlays dinâmicos:** textos, imagens, logos, ícones, animações e posicionamento por coordenadas.
- **Templates visuais:** layouts reutilizáveis para conteúdos e campanhas.
- **Clima:** integração com Open-Meteo, geocodificação e cache de 10 minutos.
- **Notícias e RSS:** proxy para feeds RSS ou JSON com cache e normalização dos itens.
- **Assistente de IA:** texto, voz, áudio, memória persistente e fallback entre provedores.
- **Monitoramento:** heartbeat, indicação online/offline e painel de atividade atual.
- **Backup e portabilidade:** exportação e importação da configuração em JSON.
- **PWA e responsividade:** painel administrativo preparado para desktop, tablet e mobile.

## Arquitetura

    localTV/
    ├── backend/
    │   ├── server.js              # API Express e servidor Socket.IO
    │   ├── database.js            # SQLite, schema e migrações
    │   ├── routes/                # Rotas do assistente e configurações
    │   ├── services/              # Serviços auxiliares e integrações
    │   ├── API.md                 # Referência da API e eventos
    │   └── package.json           # Dependências do backend
    ├── frontend/
    │   ├── src/admin/             # Painel administrativo
    │   ├── src/player/            # Player das telas
    │   ├── src/hooks/             # Hooks de comunicação e voz
    │   └── package.json           # Dependências do frontend
    ├── ARCHITECTURE.md            # Decisões e fluxos técnicos
    ├── Dockerfile                 # Build e execução em container
    ├── easypanel.yaml             # Configuração para EasyPanel
    ├── nginx.conf                 # Gateway local
    ├── install.bat                # Instalação no Windows
    └── start.bat                  # Inicialização no Windows

## Stack tecnológica

| Camada | Tecnologia |
| --- | --- |
| Backend | Node.js + Express |
| Banco de dados | SQLite |
| Tempo real | Socket.IO |
| Frontend | React 19 + Vite 6 |
| Estilos | Tailwind CSS 4 |
| Roteamento | React Router 7 |
| Ícones e animações | Lucide React + Lottie |
| Clima | Open-Meteo |
| IA | Gemma/Ollama, Gemini, Groq e OpenAI |
| Infraestrutura | Docker, Nginx e EasyPanel |

## Pré-requisitos

- Node.js 20 ou superior;
- npm;
- Docker Desktop para execução containerizada;
- navegador moderno;
- HTTPS para utilizar microfone fora de `localhost`;
- chaves dos provedores de IA quando os recursos correspondentes forem ativados.

## Execução rápida com Docker

1. Construa a imagem:

       docker build -t localtv .

2. Inicie o container com armazenamento persistente:

       docker run -d --name localtv          -p 3000:3000          -e ADMIN_PASSWORD=troque-esta-senha          -e DATA_DIR=/data          -v localtv_data:/data          localtv

3. Acesse:

       http://localhost:3000

O volume em `/data` preserva o banco `data.db`, as mídias e os arquivos de configuração. Em produção, use uma senha forte e um domínio com HTTPS.

## Execução manual

### Backend

    cd backend
    npm install
    ADMIN_PASSWORD=troque-esta-senha DATA_DIR=./data node server.js

A API e o servidor Socket.IO iniciam na porta definida por `PORT`, com padrão `3000`.

### Frontend

Em outro terminal:

    cd frontend
    npm install
    npm run dev

Durante o desenvolvimento, o Vite normalmente utiliza a porta `5173`. Consulte o arquivo de configuração do frontend para conferir o proxy da API.

No Windows, os scripts `install.bat` e `start.bat` automatizam a instalação e a inicialização local.

## Variáveis de ambiente

### Backend

| Variável | Padrão | Finalidade |
| --- | --- | --- |
| `PORT` | `3000` | Porta HTTP e Socket.IO |
| `ADMIN_PASSWORD` | `admin123` | Senha do painel administrativo |
| `DATA_DIR` | diretório do backend | Diretório persistente do banco e mídias |
| `LOG_REQUESTS` | `0` | Use `1` para habilitar logs HTTP |

### Assistente de IA

| Variável | Finalidade |
| --- | --- |
| `GEMINI_API_KEY` | Chave do Google Gemini |
| `GEMINI_MODEL` | Modelo Gemini utilizado |
| `GROQ_API_KEY` | Chave do Groq |
| `GROQ_MODEL` | Modelo Groq; há um padrão no backend |
| `OPENAI_API_KEY` | Chave da OpenAI |
| `OPENAI_MODEL` | Modelo da OpenAI |
| `OLLAMA_URL` | Endereço do Ollama local |
| `GEMMA_MODEL` | Modelo Gemma no Ollama |

Configure somente os provedores necessários. O backend pode aplicar fallback automático quando um provedor não estiver disponível.

## Painel administrativo e player

- Painel: `/admin`
- Player: rota principal da aplicação
- Assistente standalone: `/assistant`
- Configuração do assistente: `/admin/ai-assistant-config`
- Memória do assistente: `/admin/assistant-memory`

O dispositivo registra sua identidade no Socket.IO, envia heartbeat periódico e recebe comandos de playlist, transição, resolução, volume e reprodução.

## API e eventos

A API HTTP utiliza o prefixo `/api` e o header `x-client-id` para selecionar o cliente ativo. Consulte a [referência completa da API](backend/API.md).

Principais grupos:

| Grupo | Exemplos |
| --- | --- |
| Autenticação | `POST /api/auth/login` |
| Saúde | `GET /health` |
| Clientes | `/api/clients` |
| Dispositivos | `/api/devices` |
| Mídias | `/api/media` |
| Playlists | `/api/playlists` |
| Overlays | `/api/overlays` |
| Clima | `GET /api/weather` |
| Notícias | `GET /api/news` |
| Assistente | `POST /api/chat` |
| Backup | `/api/config/export` e `/api/config/import` |

Eventos Socket.IO relevantes:

| Evento | Direção | Finalidade |
| --- | --- | --- |
| `register_device` | Cliente → servidor | Registra a tela |
| `heartbeat` | Cliente → servidor | Mantém o dispositivo online |
| `now_playing` | Cliente → servidor | Informa a mídia em exibição |
| `playlist:update` | Servidor → cliente | Atualiza a playlist |
| `command_update` | Servidor → cliente | Atualiza configurações do player |
| `overlays_updated` | Servidor → cliente | Recarrega overlays |
| `dashboard_update` | Servidor → cliente | Atualiza o painel administrativo |

## Multi-cliente

O cliente ativo pode ser informado por header, query string ou corpo da requisição:

    x-client-id: cliente-exemplo

Quando não informado, o sistema utiliza `default`. Em uma implantação real, associe o cliente a uma autenticação válida e valide autorização no backend antes de permitir acesso aos recursos.

## Assistente de voz

A entrada por microfone utiliza a Web Speech API do navegador.

| Requisito | Observação |
| --- | --- |
| Chrome ou Edge | Melhor compatibilidade |
| localhost ou HTTPS | Contexto seguro obrigatório |
| Permissão de microfone | Deve ser autorizada pelo usuário |
| `enableVoice: true` | Configuração necessária no assistente |

Fora de `localhost`, publique a aplicação com HTTPS para permitir o uso do microfone.

## Backup e restauração

- Exporte a configuração em `GET /api/config/export`.
- Armazene o JSON em local seguro.
- Importe por `POST /api/config/import` somente após validar o arquivo.
- O processo de importação substitui a configuração existente. Faça backup antes de executar.

O backup lógico não substitui o backup do diretório de mídias nem do banco SQLite.

## Deploy no EasyPanel

1. Crie um serviço a partir do repositório.
2. Utilize o `Dockerfile` ou o `easypanel.yaml`.
3. Configure `PORT=3000` e `DATA_DIR=/data`.
4. Monte um volume persistente em `/data`.
5. Defina `ADMIN_PASSWORD` e as chaves de IA como secrets.
6. Publique com domínio e SSL.

## Segurança

- Troque imediatamente a senha administrativa padrão.
- Não versione `.env`, banco SQLite, tokens ou chaves de IA.
- Restrinja o header `x-client-id` com autenticação e autorização reais.
- Evite `CORS *` em produção; permita apenas origens confiáveis.
- Proteja os endpoints de upload e limite tamanho e tipos de arquivo.
- Restrinja o acesso ao diretório de mídias.
- Faça backup do banco e das mídias periodicamente.
- Monitore o consumo das APIs de IA e feeds externos.
- Trate a importação de configuração como uma operação destrutiva e controlada.

## Scripts úteis

| Comando | Descrição |
| --- | --- |
| `npm install` | Instala dependências |
| `npm run dev` | Inicia o frontend |
| `npm run build` | Gera o build de produção |
| `npm run lint` | Executa a verificação do frontend |
| `node server.js` | Inicia o backend |
| `docker build -t localtv .` | Cria a imagem Docker |
| `docker compose up -d` | Inicia serviços definidos no Compose |
| `docker compose down` | Para os serviços |
| `install.bat` | Instala dependências no Windows |
| `start.bat` | Inicia a aplicação no Windows |

## Contribuição

1. Crie uma branch para sua alteração.
2. Mantenha mudanças de backend, frontend e infraestrutura documentadas.
3. Execute o build e o lint antes de abrir um pull request.
4. Atualize [ARCHITECTURE.md](ARCHITECTURE.md) ou [backend/API.md](backend/API.md) quando alterar contratos.
5. Descreva no pull request os testes e possíveis impactos operacionais.

## Licença

Este projeto está distribuído sob a licença [MIT](LICENSE).

## Autor

Desenvolvido por [betoarts](https://github.com/betoarts).
