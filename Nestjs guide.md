## Commands

```bash
# new project
nest new project-name

# run project 
yarn start:dev

# generate module
nest g module users

# generate controller
nest g controller users

# generate service
nest g service users
```

## Beginning of project

1. install config module
2. add mongodb mongoose support 
3. add versioning
4. add validation setup for dtos 

Monorepo 

**Nestjs Checklist**
failures:
- [ ] connect to mongodb using typeorm

- [x] module create
- [x] controller create
- [x] service create 
- [x] validation using dtos
- [x] setup and use .env 
- [x] create basic apis
- [x] connect to mongodb using mongoose
- [x] versioning an api
- [x] rabbitmq message queue setup 

### extras:
1. golevel rabbitmq 
2. uuid + types
3. 

hybrid: search this when you want both req-res and microservices 



## Working on a modules

1. need dtos, interfaces, schemas/entities

## Basic rest api

```typescript
import { Body, Controller, Get, Post } from '@nestjs/common';
import { CreateFlightDto } from './dtos/create-flight.dto';
import { FlightsService } from './flights.service';

@Controller({
    path: 'flights',
    version: '1',
})
export class FlightsController {
    constructor(private flightsService: FlightsService) {}

    @Get()
    async findAll() {
        const data = await this.flightsService.findAll();  
        return data;
    }

    @Post()
    async create(@Body() flightData: CreateFlightDto): Promise<any> {
        const data = await this.flightsService.create(flightData);  
        return { data };
    }

}
```

## Validation setup

```typescript
// in main.ts
app.useGlobalPipes(

    new ValidationPipe({
        transform: true,
        exceptionFactory: (errors) =>
            new BadRequestException({
                status: 'error',
                data: null,
                message: 'Validation failed',
                error: {
                    name: 'ValidationError',
                    errors: errors.map((err) => ({
                        property: err.property,
                        errors: Object.values(err.constraints),
                        children: err.children,    
                    })),
                },
            }),
    }),
);
```

## Service

```typescript
import { Injectable } from '@nestjs/common';
import { InjectModel } from '@nestjs/mongoose';
import { Model } from 'mongoose';
import { CreateFlightDto } from './dtos/create-flight.dto';
import { Flight, FlightDocument } from './schemas/flights.schema';
// import { Flights } from './flights.entity';

// @InjectRepository(Flights)
// private flightsRepository: Repository<Flights>,
@Injectable()
export class FlightsService {
  constructor(
    @InjectModel(Flight.name) private flightModel: Model<FlightDocument>,
  ) {}

  async findAll() {
    const flights = await this.flightModel.find().populate('airline');

    return flights;
  }

  async create(flight: CreateFlightDto) {
    const newFlight = new this.flightModel(flight);

    await newFlight.save();

    return newFlight;
  }
}

```



## Using mongoose

Connecting 

```typescript
// in app module imports
imports: [MongooseModule.forRoot(process.env.MONGODB_LOCAL_URI)]
```

Creating schema

```typescript
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import mongoose, { HydratedDocument } from 'mongoose';
import { Airline } from 'src/airlines/schemas/airlines.schema';
import { v4 as uuidv4 } from 'uuid';

export type FlightDocument = HydratedDocument<Flight>;

@Schema()
export class Flight {

    @Prop() flightNumber: string;

    @Prop({ type: mongoose.Schema.Types.ObjectId, ref: 'Airline' })
    airline: Airline;

    @Prop() departureAirportId: number;

    @Prop({ type: String, default: uuidv4 }) flightId: string;

    @Prop({ unique: true }) cityCode: string;

}

export const FlightSchema = SchemaFactory.createForClass(Flight);
```

Adding schema in module

```typescript
@Module({
    imports: [
        MongooseModule.forFeature([
            { name: Flight.name, schema: FlightSchema }
        ]),
    ],
    // ...
})

export class FlightsModule {}
```

Injecting in service

```typescript
constructor(
    @InjectModel(City.name) private userModel: Model<UserDocument>
) {}
```

## Creating DTOs

```typescript
// create-flight.dto.ts
import { Type } from 'class-transformer';
import { IsInt, IsString, MaxLength, MinLength } from 'class-validator';

export class CreateFlightDto {

    @IsString()
    flightName: string;

    @Type(() => Number)
    @IsInt()
    availableSeats: number;

    @IsString()
    @MinLength(3, { message: 'City code should have at least 3 letters' })
    @MaxLength(3, { message: 'City code can have maximum 3 letters' })
    cityCode: string;

    @IsString()
    @MinLength(1, { message: 'City name should not be empty' })    
    cityName: string;

}
```

## RabbitMQ

packages

```bash
yarn add @nestjs/microservices amqp-connection-manager amqplib @golevelup/nestjs-rabbitmq
```

add the following in module imports

```typescript
RabbitMQModule.forRoot(RabbitMQModule, {
    exchanges: [
        {
            name: 'cities',
            type: 'direct',
        },
    ],
    uri: 'amqp://user:password@localhost:5672',
}),
```

creating subscriber: create a new service file and add it to modules providers. This service will handle the messages only. 

```typescript
@RabbitSubscribe({
    exchange: 'cities',
    routingKey: 'city-created',
    queue: 'city-queue',
})
async handleCityCreatedEvent(message: CityCreatedDto) {
    const newCity = new this.cityModel({
        cityId: message.cityId,
        cityCode: message.cityCode,
        cityName: message.cityName,
    });

    await newCity.save();
}
```
