# goexpert-graphql



## Como rodar o projeto
``` shell
go mod tidy
sqlite3 cmd/server/data.db 'create table categories (id string, name string, description string);'
sqlite3 cmd/server/data.db 'create table courses (id string, name string, description string, category_id string);'

cd cmd/server
go run server.go
```

## Passos para trabalhar com graphql no go
1. Gerar o arquivo tools.go
``` shell
printf '// +build tools\npackage tools\nimport (_ "github.com/99designs/gqlgen"\n _ "github.com/99designs/gqlgen/graphql/introspection")' | gofmt > tools.go
go mod tidy
```
2. Inicializar o gqlgen
``` shell
go run github.com/99designs/gqlgen init && go mod tidy
```
3. Gerar arquivo de model para cada entidade da aplicação, na pasta `graph/model`, lembrando que não é nessecário colocar o campo 'Foreign Key' no objeto X que depende de Y
4. Definir, separadamente, no arquivo `gqlgen.yml`, os models das entidades da aplicação
5. `go run github.com/99designs/gqlgen generate`
6. Implementar os resolvers no arquivo `graph/schema.resolvers.go`
7. Rodar o server
``` shell
go run cmd/server/server.go
```
9. Acesse: http://localhost:8080
9. Criar as mutations e queries no playground do graphql
``` graphql
mutation createCategory {
  createCategory(input: {name: "Tecnologia", description: "Cursos de Tecnologia"}) {
    id
    name
    description
  }
}

query queryCategories {
  categories {
    id
    name
    description
  }
}

mutation createCourse {
  createCourse(
    input: {name: "Full Cycle", description: "Go Expert", categoryId: "a5936dbe-1a0d-46c2-a53b-79f7f809840f"}
  ) {
    id
    name
    description
  }
}

query queryCourses {
  courses {
    id
    name
    description
  }
}

query queryCategoriesWithCourses {
  categories {
    id
    name
    description
    courses {
      id
      name
      description
    }
  }
}

query queryCoursesWithCategories {
  courses {
    id
    name
    description
    category {
    	id
    	name
    	description
    }
  }
}
```
