# ads2-senac
Atividade CRUD em JAVA+SPRING BOOT


Passo 1: Configuração do Projeto
Você pode começar criando um novo projeto Spring Boot usando o Spring Initializr (https://start.spring.io/). Escolha as seguintes opções:

Project: Maven Project
Language: Java
Spring Boot: 3.x (ou a versão mais recente)
Dependencies: Spring Web, Spring Data JPA, H2 Database (para um banco de dados em memória, pode substituir por MySQL ou outro banco em produção)
Clique em "Generate" e baixe o projeto. Depois, descompacte o arquivo e abra o projeto em sua IDE favorita.

Passo 2: Criando a Entidade
Vamos começar criando a entidade User.

User.java

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

@Entity: Indica que esta classe é uma entidade JPA. O Spring Data JPA criará uma tabela para esta entidade no banco de dados.
@Id: Define o campo que será a chave primária da entidade.
@GeneratedValue(strategy = GenerationType.IDENTITY): Indica que o valor da chave primária será gerado automaticamente pelo banco de dados.

Passo 3: Criando o Repositório
Crie um repositório para a entidade User.

UserRepository.java

package com.example.demo.repository;

import com.example.demo.model.User;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
}

JpaRepository: Interface fornecida pelo Spring Data JPA que contém métodos prontos para operações CRUD. Você não precisa implementar esses métodos; o Spring Data JPA faz isso por você.

Passo 4: Criando o Serviço
O serviço conterá a lógica de negócios para as operações CRUD.

UserService.java

package com.example.demo.service;

import com.example.demo.model.User;
import com.example.demo.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    public Optional<User> getUserById(Long id) {
        return userRepository.findById(id);
    }

    public User saveUser(User user) {
        return userRepository.save(user);
    }

    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
}


Explicação das Anotações:

@Service: Indica que esta classe é um serviço, que contém a lógica de negócios e pode ser injetada em outras partes da aplicação.
@Autowired: Injeta uma dependência automaticamente. Aqui, o repositório UserRepository é injetado no serviço.


Passo 5: Criando o Controlador
O controlador expõe endpoints REST para a aplicação.

UserController.java


package com.example.demo.controller;

import com.example.demo.model.User;
import com.example.demo.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        Optional<User> user = userService.getUserById(id);
        return user.map(ResponseEntity::ok).orElseGet(() -> ResponseEntity.notFound().build());
    }

    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User createdUser = userService.saveUser(user);
        return new ResponseEntity<>(createdUser, HttpStatus.CREATED);
    }

    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User user) {
        if (!userService.getUserById(id).isPresent()) {
            return ResponseEntity.notFound().build();
        }
        user.setId(id);
        User updatedUser = userService.saveUser(user);
        return ResponseEntity.ok(updatedUser);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        if (!userService.getUserById(id).isPresent()) {
            return ResponseEntity.notFound().build();
        }
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}

Explicação das Anotações:

@RestController: Combina @Controller e @ResponseBody. Indica que esta classe é um controlador REST e os métodos retornarão diretamente a resposta no corpo da resposta HTTP.
@RequestMapping("/users"): Define a URL base para todos os endpoints neste controlador.
@GetMapping, @PostMapping, @PutMapping, @DeleteMapping: Mapeiam os métodos HTTP (GET, POST, PUT, DELETE) para métodos específicos no controlador.
@PathVariable: Extrai valores de variáveis de caminho na URL.
@RequestBody: Extrai o corpo da requisição HTTP e o mapeia para um objeto Java.



Passo 6: Configuração do Banco de Dados
Para um banco de dados em memória, o H2 está configurado por padrão. Se você estiver usando outro banco de dados, ajuste o application.properties conforme necessário.

application.properties

# Configurações do H2 Database
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.h2.console.enabled=true

Conclusão
Você agora tem uma aplicação Spring Boot com um CRUD completo para gerenciar usuários. Para executar a aplicação, use a classe principal gerada pelo Spring Boot:

Rodando a aplicação, você pode acessar a API REST para criar, ler, atualizar e excluir usuários. O console H2 pode ser acessado em http://localhost:8080/h2-console para visualizar o banco de dados em memória.

