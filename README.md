# ads2-senac

## Atividade CRUD em Java + Spring Boot

---

## Passo 1: Configuração do Projeto

Você pode começar criando um novo projeto Spring Boot usando o [Spring Initializr](https://start.spring.io/).

**Escolha as seguintes opções:**
- **Project:** Maven Project
- **Language:** Java
- **Spring Boot:** 3.x (ou a versão mais recente)
- **Dependencies:** Spring Web, Spring Data JPA, H2 Database (para um banco de dados em memória, pode substituir por MySQL ou outro banco em produção)

Clique em "Generate" e baixe o projeto. Depois, descompacte o arquivo e abra o projeto em sua IDE favorita.

---

## Passo 2: Criando a Entidade

Vamos começar criando a entidade `User`.

**User.java**
```java
package com.example.demo.model;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}

Explicação das Anotações:
  - @Entity: Indica que esta classe é uma entidade JPA. O Spring Data JPA criará uma tabela para esta entidade no banco de dados.
  - @Id: Define o campo que será a chave primária da entidade.
  - @GeneratedValue(strategy = GenerationType.IDENTITY): Indica que o valor da chave primária será gerado automaticamente pelo banco de dados.

Passo 3: Criando o Repositório
Crie um repositório para a entidade User.

UserRepository.java

package com.example.demo.repository;

import com.example.demo.model.User;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
}
,0,,,,,,,,,,,,,,,,,,,
