# Digivice API

**API REST desenvolvida com Java 17 e Spring Boot 3.1.5 para consulta de informações sobre Digimon.**

A **Digivice API** é um projeto complementar ao [frontend Digivice](https://github.com/cesarrafaeljunior/digivice-frontend). Seu objetivo é consumir uma API pública de Digimon, disponibilizando dados por meio de um endpoint próprio com filtros por nome ou nível evolutivo.

**[Frontend Angular](https://github.com/cesarrafaeljunior/digivice-frontend)** · **[API no GitHub](https://github.com/cesarrafaeljunior/digivice-api)**

## Funcionalidades

- Consulta à API externa `https://digimon-api.vercel.app/api/digimon`.
- Listagem geral de Digimon.
- Consulta por nome.
- Consulta por nível evolutivo.
- Padronização da representação dos dados com `DigimonDTO`.
- Conversão de determinados erros HTTP da fonte externa em respostas próprias da API.

## Tecnologias

- **Java 17**
- **Spring Boot 3.1.5**
- **Spring Web**
- **RestTemplate** para requisições HTTP à fonte externa
- **Maven** e Maven Wrapper
- **Docker** (Dockerfile presente no repositório)

## Arquitetura e organização

```text
src/main/java/br/com/digivice/
├── controller/
│   └── DigimonController.java
├── service/
│   └── DigimonService.java
├── DTO/
│   └── DigimonDTO.java
├── exceptions/
│   ├── CustomExceptionHandler.java
│   ├── EntityNotFoundException.java
│   └── errors/
│       └── StandardError.java
└── DigiviceApplication.java
```

O controller recebe os parâmetros de consulta e delega ao serviço, responsável por recuperar dados externos. O DTO representa o contrato de resposta; o tratamento de exceções fornece respostas HTTP para erros conhecidos da fonte consultada.

## Executando localmente

**Pré-requisitos:** JDK 17 e acesso à internet. O Maven Wrapper acompanha o repositório.

```bash
git clone https://github.com/cesarrafaeljunior/digivice-api.git
cd digivice-api
./mvnw spring-boot:run
```

No Windows:

```powershell
.\mvnw.cmd spring-boot:run
```

Por padrão, o Spring Boot disponibiliza a API em `http://localhost:8080`.

### Executando com Docker

```bash
docker build -t digivice-api .
docker run --rm -p 8080:8080 digivice-api
```

Esses comandos utilizam o `Dockerfile` existente no repositório. O acesso à API pública externa continua necessário para que a consulta retorne informações.

## Endpoints

O controller disponibiliza um endpoint HTTP `GET /digimons` com parâmetros opcionais:

| Requisição | Finalidade |
| --- | --- |
| `GET /digimons` | Listar todos os Digimon |
| `GET /digimons?name=Koromon` | Buscar pelo nome |
| `GET /digimons?level=Ultimate` | Filtrar por nível |

Exemplos:

```bash
curl 'http://localhost:8080/digimons'
curl 'http://localhost:8080/digimons?name=Koromon'
curl 'http://localhost:8080/digimons?level=Ultimate'
```

Quando `name` e `level` são enviados simultaneamente, **a implementação atual dá prioridade a `name`**; os filtros não são combinados.

### Estrutura básica da resposta

```json
[
  {
    "name": "Koromon",
    "img": "https://digimon.shadowsmith.com/img/koromon.jpg",
    "level": "In Training"
  }
]
```

O exemplo acima ilustra o contrato presente no projeto. Os dados e endereços de imagem efetivamente retornados dependem da API externa.

### Tratamento de erros

A API contém um `@ControllerAdvice` para tratar `EntityNotFoundException`, incluindo dados como instante, status HTTP, mensagem de erro e caminho da requisição. Falhas de rede e indisponibilidade do serviço externo podem exigir tratamento adicional.

## Relação com o frontend

O frontend Angular está disponível em:

**[cesarrafaeljunior/digivice-frontend](https://github.com/cesarrafaeljunior/digivice-frontend)**

**Nota de integração:** a versão atual do frontend consulta a API pública `digimon-api.vercel.app` diretamente. Portanto, executar esta API Spring Boot não altera automaticamente a fonte de dados do site. Para integrar os projetos, é necessário configurar a URL de consulta no frontend e validar o contrato de dados, CORS e tratamento de falhas.

## Pontos de evolução técnica

- Externalizar a URL da API de terceiros para configuração por ambiente.
- Evitar a modificação de um campo compartilhado de URL no serviço durante consultas. Em acessos concorrentes, essa abordagem pode gerar requisições incorretas; construir URLs como variáveis locais é mais seguro.
- Melhorar o tratamento de indisponibilidade, timeout e erros inesperados da fonte externa.
- Adicionar testes automatizados específicos dos endpoints e do serviço de integração.

## Aviso

Projeto **não oficial**, desenvolvido com finalidade educacional. Digimon e as imagens das criaturas pertencem aos respectivos titulares de direitos. Não há vínculo com os detentores da marca.

---

**Desenvolvido por [César Rafael](https://github.com/cesarrafaeljunior).**
