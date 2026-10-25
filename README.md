# Assistente de Investimentos com RPA, n8n e IA Generativa

**Data de desenvolvimento:** 25 de outubro de 2026  
**Projeto:** Desafio de Projeto — Digital Innovation One (DIO)  
**Área:** Automação de Processos, Integração de APIs e Inteligência Artificial Generativa  
**Ferramenta principal:** n8n (Self-Hosted)

---

## 1. Apresentação do projeto

Este projeto foi desenvolvido como parte do desafio **Assistente de Investimentos com RPA e n8n**, da Digital Innovation One (DIO).

O objetivo foi construir uma automação capaz de extrair informações de clientes de uma página HTML, consultar uma base de produtos financeiros, relacionar os investimentos disponíveis aos perfis dos clientes e utilizar inteligência artificial generativa para elaborar mensagens personalizadas.

A automação integra **Python, BeautifulSoup, n8n, JavaScript, Google Gemini e Gmail**, permitindo aplicar conceitos de RPA, tratamento de dados, Webhooks, APIs REST e OAuth2.

Todos os dados utilizados são fictícios e destinados exclusivamente à aprendizagem.

## 2. Tecnologias utilizadas

| Tecnologia | Aplicação |
|---|---|
| Python | Extração e envio dos dados |
| BeautifulSoup | Leitura e tratamento do HTML |
| Google Colab | Execução do código Python |
| n8n | Orquestração dos processos |
| Webhook | Recebimento de dados por POST |
| HTTP Request | Consulta à base CSV |
| JavaScript | Tratamento e normalização dos dados |
| JSON | Estruturação e troca de informações |
| Google Gemini | Geração de mensagens com IA |
| Gmail API | Integração para envio de mensagens |
| Google Cloud | Configuração de OAuth2 |
| Cloudflare Tunnel | Comunicação entre Colab e n8n local |
| GitHub | Documentação e versionamento |

## 3. Objetivo da automação

Desenvolver um assistente capaz de:

1. Extrair automaticamente dados fictícios de clientes de uma página HTML.
2. Enviar os registros para o n8n por meio de um Webhook.
3. Consultar uma base CSV contendo produtos de investimento.
4. Relacionar perfil de investidor, saldo e valor mínimo de aplicação.
5. Gerar mensagens personalizadas para cada cliente.
6. Integrar o Google Gemini para aprimorar a comunicação.
7. Normalizar os campos necessários ao envio por e-mail.
8. Integrar o Gmail utilizando autenticação OAuth2.

## 4. Etapa 1 — Extração dos dados com Python

A primeira etapa consistiu em acessar uma página HTML disponibilizada pelo projeto da DIO.

**Fonte dos dados:**

https://digitalinnovationone.github.io/dio-lab-assistente-investimentos-rpa-n8n/

Foi utilizado Python com as bibliotecas `requests` e `BeautifulSoup` para localizar a tabela HTML e extrair os seguintes campos:

- Nome do cliente;
- E-mail;
- Saldo disponível;
- Perfil de investidor.

O código foi executado no Google Colab e identificou **10 registros fictícios**, distribuídos entre os perfis Conservador, Moderado e Arrojado.

## 5. Etapa 2 — Configuração do Webhook no n8n

O n8n foi instalado e executado localmente no Windows.

Foi criado um Webhook utilizando o método HTTP `POST`, com o caminho `Clientes`.

Os registros extraídos pelo Python foram organizados em JSON e enviados ao Webhook.

Como o Google Colab funciona em ambiente remoto e o n8n estava instalado localmente, foi utilizado o **Cloudflare Tunnel** para estabelecer a comunicação HTTPS durante os testes.

Essa integração permitiu transferir os dados dos clientes para o workflow.

## 6. Etapa 3 — Consulta à base de investimentos

Foi adicionado um nó **HTTP Request** para consultar o arquivo CSV de investimentos disponibilizado pela DIO.

O CSV contém informações sobre:

- Perfil de investimento;
- Nome do produto;
- Aplicação mínima;
- Rentabilidade informada.

Em seguida, um nó **Code in JavaScript**, denominado `Normalizando`, foi utilizado para transformar o CSV em objetos JSON.

Essa transformação permitiu que os produtos financeiros fossem utilizados nas etapas seguintes do workflow.

## 7. Etapa 4 — Cruzamento dos dados

O nó **Merge** foi utilizado para combinar os dados recebidos pelo Webhook com a base de investimentos.

No nó `Gera mensagens`, foi implementada uma lógica em JavaScript para identificar os produtos compatíveis com cada cliente, considerando:

- Perfil de investidor;
- Saldo disponível;
- Valor mínimo de aplicação.

O algoritmo também organiza as alternativas encontradas e gera uma mensagem inicial para cada cliente.

## 8. Etapa 5 — Preparação para inteligência artificial

Foi criado o nó `Prep. IA`, responsável por transformar as mensagens em registros individuais e padronizados.

Cada registro contém informações como nome, e-mail, perfil, saldo e mensagem de referência.

Essa etapa também prepara as instruções necessárias para a utilização dos dados por modelos de inteligência artificial.

## 9. Etapa 6 — Integração com Google Gemini

A automação foi integrada à API do **Google Gemini**, utilizando o nó `Message a model`.

O modelo recebe as informações estruturadas e produz textos personalizados destinados à comunicação por e-mail.

O prompt foi configurado para:

- Utilizar linguagem profissional, clara e cordial;
- Personalizar o texto conforme o cliente;
- Apresentar até três alternativas presentes nos dados recebidos;
- Não inventar produtos, valores ou rentabilidades;
- Não prometer resultados financeiros;
- Informar a finalidade educacional dos dados.

A resposta foi solicitada em formato JSON, contendo os campos `assunto` e `corpo`.

## 10. Etapa 7 — Normalização das respostas da IA

Após a geração dos textos pelo Gemini, foi criado o nó **Normalizando IA**.

Esse nó utiliza JavaScript para interpretar as respostas e estruturar os seguintes campos:

| Campo | Finalidade |
|---|---|
| `nome` | Identificação do cliente |
| `para` | Destinatário do e-mail |
| `assunto` | Assunto gerado pela IA |
| `corpo` | Conteúdo da mensagem |
| `perfil` | Perfil do investidor |
| `saldo` | Saldo disponível |
| `status` | Situação do registro |

Essa etapa também valida o formato dos endereços de e-mail e verifica a presença do conteúdo necessário ao envio.

## 11. Etapa 8 — Integração com Gmail

Foi configurada a integração com a Gmail API, por meio do Google Cloud.

A autenticação utilizou **OAuth2**, com a criação de um cliente OAuth do tipo Aplicativo da Web.

O aplicativo foi mantido em modo de teste, com autorização de acesso para a conta Gmail utilizada no laboratório.

No workflow, foi adicionado o nó `Send a message`, configurado para utilizar os campos produzidos pelo nó Normalizando IA:

- Destinatário: `{{ $json.para }}`
- Assunto: `{{ $json.assunto }}`
- Mensagem: `{{ $json.corpo }}`

Também foi utilizado um nó `If` para validar o formato do endereço eletrônico antes da operação de envio.

A integração foi preparada para testes controlados com destinatários autorizados. A validação do formato de e-mail, isoladamente, não garante autorização para envio.

## 12. Estrutura do workflow

O fluxo desenvolvido no n8n contém os seguintes nós:

1. **Cadastro Clientes:** recebe as informações pelo Webhook.
2. **Investimentos:** consulta a base CSV.
3. **Normalizando:** transforma o CSV em JSON.
4. **Merge:** combina os dados.
5. **Gera mensagens:** identifica produtos compatíveis e produz os textos iniciais.
6. **Prep. IA:** prepara os registros para inteligência artificial.
7. **Message a model:** integra o Google Gemini.
8. **Normalizando IA:** padroniza as respostas da IA.
9. **If:** verifica o formato do e-mail.
10. **Send a message:** realiza a operação de envio pelo Gmail.
11. **Respond to Webhook:** devolve a resposta da execução.

## 13. Como reproduzir o projeto

Para reproduzir a automação:

1. Instale o Node.js e o n8n.
2. Inicie o n8n localmente.
3. Importe o arquivo `n8n/workflow.json` disponibilizado neste repositório.
4. Configure a credencial da API Google Gemini.
5. Ative a Gmail API no Google Cloud.
6. Crie um cliente OAuth2 e configure as credenciais do Gmail no n8n.
7. Confira os endereços dos arquivos CSV e da página HTML.
8. Execute o código Python para extrair os clientes.
9. Configure um endereço acessível para o Webhook, caso utilize Google Colab.
10. Execute o workflow em modo de teste e confira os resultados.
11. Realize testes de envio somente com um endereço de e-mail próprio ou expressamente autorizado.

O projeto utiliza credenciais individuais, que devem ser configuradas por cada usuário.

## 14. Segurança e boas práticas

As chaves de API, tokens OAuth, senhas e demais credenciais não devem ser publicados no GitHub.

O Cloudflare Tunnel foi utilizado para testes de conectividade e deve ser encerrado após sua utilização.

Como a automação envolve informações financeiras, o uso de dados reais exigiria medidas adicionais de segurança, privacidade, validação e autorização dos destinatários.

As informações financeiras deste exercício não constituem recomendações individuais de investimento.

## 15. Resultados e aprendizados

O desenvolvimento do projeto permitiu aplicar, de forma prática, conhecimentos de:

- Extração e tratamento de dados com Python;
- Construção e execução de workflows no n8n;
- Integração de serviços por Webhooks e APIs;
- Manipulação de JSON e programação JavaScript;
- Cruzamento de diferentes fontes de dados;
- Aplicação de inteligência artificial generativa;
- Autenticação OAuth2 no Google Cloud;
- Integração entre plataformas em ambientes locais e remotos;
- Organização e documentação de projetos no GitHub.

O principal aprendizado foi compreender como diferentes ferramentas podem ser integradas em um único processo automatizado, desde a coleta dos dados até a preparação de comunicações personalizadas.

## 16. Considerações finais

O desafio proporcionou uma experiência prática de desenvolvimento de automações, combinando RPA, programação, integração de sistemas e inteligência artificial.

A implementação demonstra como o n8n pode funcionar como ferramenta central de orquestração, conectando diferentes tecnologias e permitindo criar processos estruturados, reutilizáveis e escaláveis.

O projeto possui finalidade exclusivamente educacional e integra meu portfólio de atividades desenvolvidas durante os estudos de automação e inteligência artificial.

---

**Desenvolvido em:** 25/10/2026  
**Plataforma:** Digital Innovation One — DIO  
**Repositório:** https://github.com/imaculada-coder/dio-lab-assistente-investimentos-rpa-n8n
