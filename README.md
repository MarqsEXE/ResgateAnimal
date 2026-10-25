# ResgateAnimal

## Sobre o projeto

O **ResgateAnimal** foi desenvolvido como um projeto acadêmico durante a faculdade, com o objetivo de criar uma solução para apoiar o trabalho de uma ONG de proteção e resgate de animais. A proposta é facilitar a organização dos animais resgatados, dos adotantes, dos funcionários e dos processos de adoção, contribuindo para que a ONG acompanhe essas informações de forma mais simples e centralizada.

Trata-se de uma aplicação desktop desenvolvida em Java, com interface gráfica Swing e persistência de dados em MySQL.

## Funcionalidades

- **Animais**
  - Cadastro, listagem, atualização e remoção.
  - Registro de nome, espécie, raça, idade em meses e situação de castração.
  - Filtro de animais disponíveis por castrados, não castrados ou ambos.
  - Animais já adotados deixam de aparecer na lista de disponíveis.
- **Adotantes**
  - Cadastro, listagem, atualização e remoção por CPF.
  - Validação de idade mínima de 18 anos.
  - Registro de endereço e telefone.
- **Funcionários**
  - Cadastro, listagem, atualização e remoção.
  - Consulta por ID.
- **Adoções**
  - Registro de uma adoção relacionando animal, adotante e funcionário responsável.
  - Listagem de animais disponíveis.
  - Consulta do histórico de adoções, incluindo a data e os dados dos envolvidos.

## Tecnologias

- Java
- Java Swing
- JDBC
- MySQL
- Maven

## Arquitetura

O código é organizado por domínio e segue uma separação simples entre modelo, serviço e persistência:

```text
src/main/java/com/projeto/programacao/
├── Main.java
├── animal/
│   ├── Animal.java
│   ├── AnimalRepository.java
│   └── AnimalService.java
├── adotante/
│   ├── Adotante.java
│   ├── AdotanteRepository.java
│   └── AdotanteService.java
├── funcionario/
│   ├── Funcionario.java
│   ├── FuncionarioRepository.java
│   └── FuncionarioService.java
└── adocao/
    ├── Adocao.java
    ├── AdocaoRepository.java
    └── AdocaoService.java
```

- As classes `*` representam as entidades do sistema.
- As classes `*Repository` executam as operações JDBC no MySQL.
- As classes `*Service` centralizam o acesso aos repositórios.
- `Main.java` inicializa a conexão com o banco e a interface Swing.

## Requisitos

- JDK 8 ou superior.
- Maven.
- MySQL Server em execução.
- Banco de dados chamado `adocao`.

## Configuração do banco de dados

Crie o banco e as tabelas abaixo antes de iniciar a aplicação:

```sql
CREATE DATABASE IF NOT EXISTS adocao;
USE adocao;

CREATE TABLE IF NOT EXISTS animal (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    especie VARCHAR(50) NOT NULL,
    raca VARCHAR(100) NOT NULL,
    idade INT NOT NULL,
    castrado BOOLEAN NOT NULL
);

CREATE TABLE IF NOT EXISTS adotante (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(150) NOT NULL,
    idade INT NOT NULL,
    endereco VARCHAR(255) NOT NULL,
    cpf VARCHAR(20) NOT NULL UNIQUE,
    telefone VARCHAR(30) NOT NULL
);

CREATE TABLE IF NOT EXISTS funcionario (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(150) NOT NULL,
    idade INT NOT NULL,
    endereco VARCHAR(255) NOT NULL,
    cpf VARCHAR(20) NOT NULL,
    telefone VARCHAR(30) NOT NULL
);

CREATE TABLE IF NOT EXISTS adocao (
    id INT AUTO_INCREMENT PRIMARY KEY,
    funcionario_id INT NOT NULL,
    adotante_id INT NOT NULL,
    animal_id INT NOT NULL,
    data_adocao TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_adocao_funcionario
        FOREIGN KEY (funcionario_id) REFERENCES funcionario (id),
    CONSTRAINT fk_adocao_adotante
        FOREIGN KEY (adotante_id) REFERENCES adotante (id),
    CONSTRAINT fk_adocao_animal
        FOREIGN KEY (animal_id) REFERENCES animal (id)
);
```

### Credenciais da conexão

Atualmente, a conexão é criada em `Main.java` usando:

```text
URL:      jdbc:mysql://localhost:3306/adocao
Usuário:  root
Senha:    vazia
```

Se o seu MySQL utilizar credenciais diferentes, atualize os quatro construtores de repositório inicializados em `Main.java`:

- `AnimalRepository`
- `AdotanteRepository`
- `FuncionarioRepository`
- `AdocaoRepository`

## Como executar

1. Clone ou copie este projeto.
2. Inicie o MySQL.
3. Execute o script de criação do banco apresentado acima.
4. Ajuste as credenciais no `Main.java`, se necessário.
5. Abra o projeto como um projeto Maven na IDE ou execute:

   ```bash
   mvn clean package
   ```

6. Execute a classe principal:

   ```text
   com.projeto.programacao.Main
   ```

   Também é possível executar `Main.java` diretamente pela IDE.

Ao iniciar, a aplicação exibe o menu principal com as opções de gerenciamento de animais, adotantes, funcionários e adoções.

## Fluxo de uma adoção

1. Cadastre um funcionário.
2. Cadastre um adotante com idade igual ou superior a 18 anos.
3. Cadastre um animal.
4. Acesse **Gerenciar Adoções** e escolha **Realizar Adoção**.
5. Informe os identificadores solicitados para o animal, o adotante e o funcionário.
6. Consulte o registro em **Ver Histórico de Adoções**.

## Estrutura de persistência

O driver MySQL Connector/J está declarado no `pom.xml`:

```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>9.1.0</version>
</dependency>
```

As consultas utilizam `PreparedStatement`, e os relacionamentos da tabela `adocao` permitem recuperar o histórico completo da operação.

## Observações

- A idade dos animais é armazenada em **meses**.
- A idade mínima de 18 anos é validada para adotantes tanto no cadastro quanto na atualização.
- A aplicação depende de uma sessão gráfica para executar o Swing.
- As credenciais do banco estão atualmente no código-fonte. Em um ambiente de produção, recomenda-se movê-las para variáveis de ambiente ou um arquivo de configuração fora do controle de versão.