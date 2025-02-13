
# 🖥️ Módulo Servidor – Sistema de Gestão Logística

## 🚀 Visão Geral
O **Módulo Servidor** é o **núcleo da operação logística**, responsável por **processar, armazenar e fornecer dados** para o **Coletor** e **Dashboard**.  

Ele gerencia as solicitações, valida informações dos pacotes, controla permissões e **mantém a IA treinada com feedbacks dos usuários**.

---

## 📜 Fluxo de Operação

1️⃣ **Recebimento de Requisições**
   - O servidor escuta **solicitações do Coletor e Dashboard**.
   - Verifica permissões e autenticação antes de processar os dados.

2️⃣ **Validação de Dados**
   - Quando um código de barras é lido pelo Coletor, o servidor verifica se **os dados do pacote já existem no banco**.
   - Caso contrário, **gera um novo registro e sugere um setor de destino**.
   - Caso seja o ultimo, **define um TTL** para correções.

3️⃣ **Inferência e Aprendizado da IA**
   - O sistema sugere um destino para o pacote com base em **dados históricos e regras logísticas**.
   - Caso o usuário corrija a inferência, o servidor **ajusta automaticamente o modelo de IA**.

4️⃣ **Atualização do Banco de Dados**
   - Após a confirmação ou correção, os dados são **salvos no banco NoSQL**.
   - O **ciclo de vida do pacote** é gerenciado para evitar sobrecarga.

5️⃣ **Fornecimento de Dados ao Dashboard**
   - O servidor atende às solicitações do Dashboard para consultas, filtros e impressão de NFs.
   - A LLM integrada no chat do Dashboard **consulta dados relevantes** e responde perguntas sobre pedidos e estoque.

---

## ⚙️ Métodos e Funcionalidades
### Geral
```mermaid
classDiagram
    class Servidor {
        +ouvirRequisicoes()
        +validarDadosPacote(codigo: string)
        +sugerirDestino(dadosPacote: object)
        +processarFeedback(erro: boolean, dadosCorrigidos: object)
        +atualizarModeloIA(erro: boolean)
        +armazenarDados(dadosPacote: object)
        +consultarPedidos(query: object)
        +gerarRelatorio(tipo: string)
        +gerenciarSeguranca(autenticacao: object)
        +interfaces(credencial: object)
    }

    class Coletor {
        +autetificar(credencial: object)
        +lerCodigoBarras(codigo: string)
        +baixarDadosPacote(codigo: string)
        +enviarFeedbackServidor(correto: boolean, dadosCorretos: object)
    }

    class Dashboard {
        +autentificar(credencial: object)
        +buscarPedido(consulta: string)
        +filtrarPedidos(filtro: object)
        +consultarChatIA(pergunta: string)
    }

    Servidor --> Coletor : Retorna Dados do Pacote
    Servidor --> Dashboard : Disponibiliza Informações
    Coletor --> Servidor : Envia Feedback de Correção
    Dashboard --> Servidor : Consulta e Filtra Pedidos
    Dashboard --> Servidor : Envia Perguntas ao Chat IA
```

### Metodo de Treinamento
```mermaid
graph TD;
    
    %% Entrada dos Dados
    Pacote[Pacote Recebido] -->|Lido pelo Coletor| Inferencia[Modulo de Inferência]
    
    %% Inferência Automática
    Inferencia -->|Baseado em histórico e características| DestinoSugerido[Destino Sugerido]
    Inferencia -.->|Registro de decisão| BancoErros[(Banco de Dados de Erros)]

    %% Confirmação ou Correção
    DestinoSugerido -->|Confirmação Humana| Registro[Registro no Servidor]
    DestinoSugerido -->|Correção Manual| Correcao[Erro detectado]
    Correcao -->|Penaliza inferência e reenvia aprendizado| BancoErros

    %% Atualização da Inferência
    BancoErros -->|Treino Contínuo| Inferencia

    %% Conversão para Embeddings e Criação da LLM
    Registro -->|Transformação em Texto| ProcessadorEmbeddings[Conversão para Embeddings]
    ProcessadorEmbeddings -->|Criação do Estado Atual| MotorLLM[LLM Gerada com Inferência Atual]
    MotorLLM -->|Disponibilizado no Dashboard| ChatIA[Chat LLM Integrado]
```
---

## 🎯 Benefícios da Automação no Servidor
✅ **Inferência inteligente** – O servidor **aprende com os erros** e ajusta o destino automaticamente.  
✅ **Redução de carga no banco** – Implementação de **ciclo de vida dos pacotes** para escalabilidade.  
✅ **Interação com IA** – O chat do Dashboard **consulta o servidor** para fornecer respostas baseadas nos dados através de embeddings e vetores.  
✅ **Segurança e Controle** – O sistema **valida permissões** antes de processar requisições.  
