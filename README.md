<div align="center">
   <img src="https://github.com/lucasshuan/lucasshuan/assets/78228526/250d306c-c9d7-4b9a-990a-120a8028e881"><br>
   <h1>B2C E-commerce API</h1>
</div>

## About

This project serves as an accessible and extensible example and template for a GraphQL server. Its primary goal is to dismiss the often intricate initial setup involved in configuring **ent** (an entity framework) and **gqlgen** (a GraphQL code generator), since once the setup is complete adding new features becomes remarkably straightforward.

## Prerequisites

- **Go**
- **Docker**
- **make**

## Getting started

1. Clone the repository:

    ```bash
    git clone https://github.com/lucasshuan/go-ent-graphql-example.git
    cd go-ent-graphql-example
    ```

2. Install dependencies:

    ```bash
    make deps
    ```

3. Start Docker containers:

    ```bash
    make docker-up
    ```

4. Start the server:

    ```bash
    make server
    ```

## Usage

Adding new functionality is fairly simple: To create a new database entity (in this example, we are creating an **Item** entity), just run the following command and enter a name when name input is asked:

    ```bash
    make entity
    ```

You will notice that a new file is generated in directory **./ent/schema**. Read more about what Fields and Edges are here.

    ```go
    // User holds the schema definition for the Group entity.
    type Item struct {
        ent.Schema
    }

    // Fields of the User.
    func (Item) Fields() []ent.Field {
        return []ent.Field{
            field.String("name"),
            field.String("description"),
        }
    }

    // Edges of the User are its database relations
    func (Item) Edges() []ent.Edge {
        return nil
    }
    ```
    
The `go generate .` command auto-migrates your database tables for every **./ent/schema/*.go** file, however, at most times, one may want the types generated by ent to be used by the resolvers. This is fairly easy to achieve and also the most important feature of this integration, and the only necessary step is to add an **Annotations** method in your newly created entity file:

    ```go
    func (Item) Annotations() []schema.Annotation {
        return []schema.Annotation{
            entgql.QueryField(),
            entgql.Mutations(entgql.MutationCreate(), entgql.MutationUpdate()),
        }
    }
    ```

The code block adds the default queries and mutations to be added to your **./graphql/schema/ent.graphql** schema when running the command `go generate .`. Resolvers will be created in **./graphql/resolvers** directory for each new file, where you will be able to implement all of your queries and mutations.