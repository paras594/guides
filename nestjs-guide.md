# Nest js Guide



### Install CLI

```bash
npm i -g @nestjs/cli
```



### Create project

```bash
nest new project-name
```



### Run project

````bash
npm run start:dev # starts on 3000
````



### Creating module

```bash
nest generate module users
# or 
nest g module users
```



### Create controller

```bash
nest g controller users
```



### Create Service

```bash
nest g service users
```



## Swagger Setup



**Install**

```bash
npm i @nest/swagger swagger-ui-express
```



**In main.ts**

```typescript
import { NestFactory } from "@nestjs/core";
import { DocumentBuilder, SwaggerModule } from "@nestjs/swagger";
import { AppModule } from "./app.module";

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const config = new DocumentBuilder()
    .setTitle("Nest API")
    .setDescription("the description for Nest API")
    .setVersion("1.0")
    .build();

  const document = SwaggerModule.createDocument(app, config);

  SwaggerModule.setup("/api/docs", app, document);

  await app.listen(3000);
}
bootstrap();
```



### Add a section for controller in swagger docs

in controller of a module

```typescript
@ApiTags("users")
@Controller("users")
```



### Defining the shape of req.body and res

**Where you define types and schema add the following:**

```typescript
import {ApiProperty} from "@nestjs/swagger";

export class CreateUserDto {
  @ApiProperty()
  name: string;
  
  @ApiProperty({required: false})
  age?: number;
}

```



**For response type, do the following in controller**

```typescript
import { ApiCreatedResponse, ApiTags } from "@nestjs/swagger";

@ApiCreatedResponse({type: User}) // User is a class defined by us



```

