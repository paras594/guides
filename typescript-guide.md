# Typescript guide

[toc]



---

### Install typescript

```bash
npm install -g typescript

# check version
tsc --version
```



### Compiling

```bash
tsc hello_typescript.ts
```



### Typescript config

```bash
tsc --init
```



**Config**

```json
{
    "compilerOptions": {
        "target": "ES3",
        "module": "commonjs",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true,
        "sourceMap": true
    }
}
```



With config in project, we can compile all typescript files with `tsc` command only.

```bash
# compile all files
tsc

# watch files
tsc -w
```



### Basic Types

```typescript
let mybool : boolean = true;
let mynum : number = 123;
let stringArr : string[] = [`one`, `two`];

// inferred type
let str = "string here";
str = 1; // type error. Not assignable to string

// duck typing for objects properties
let a = {name: "paras", id: 1}
a = {name: "arjun", id: 2} // works
a = {name: "jai"} // error

// other types
let a: any = {id: 1}
a = {id: 2, name: "paras"} // works

function printObj(obj: string | number) {
    console.log(obj);
}

// type guards
function addWithTypeGuard(
    arg1: string | number,
    arg2: string | number
) {
    if (typeof arg1 === "string") {
        // arg 1 is treated as a string
        console.log(`arg1 is of type string`);
        return arg1 + arg2;
    }
    if (typeof arg1 === "number" && typeof arg2 === "number") {
    	// both are numbers
	    console.log(`arg1 and arg2 are numbers`);
    	return arg1 + arg2;
    }
    console.log(`default return treat both as strings`)
    return arg1.toString() + arg2.toString();
}

// named type
type StringOrNumber = string | number;
function addWithTypeAlias(arg1: StringOrNumber, arg2: StringOrNumber) 

// enum type
enum DoorState {
    Open,
    Closed
}
function checkDoorState(state: DoorState){}

enum Test {
    one = 234,
    two = 2398
}

enum DoorStateString {
    OPEN = "Open",
    CLOSED = "Closed"
}

// undefined type
function checkAndPrintElement(arrElement: string | undefined) {}

// null
function printValues(a: number | null) {}

// object type
let obj: object = {
    name: "myobj",
    props: {
        id: 1,
        type: "an_obj"
    }
}

// type casting
let u: unknown = 'an unknown';
u = 1;
let aNumber2: number = <number>u;

// tuple
let tuple1: [string, boolean];
tuple1 = ["test", true];
tuple1 = ["test"]; // fails

let tupleOptional: [string, boolean?]; // optional
tuple1 = ["test", true];
tuple1 = ["test"]; // works

// literals
// literal will limit the allowed values to a set of values specified.
type AllowedStringValues = "one" | "two" | "three";
type AllowedNumericValues = 1 | 20 | 65535;
```



**Function types**

```typescript
// function with return type number
function calculate(a: number, b: number: c: number): number {
    return (a * b) + c;
}

// function without return value
function printString(a: string): void {
    console.log(a);
}

// optional params
function concatValues(a: string, b?: string) {}

// default
function concatWithDefault(a: string, b: string = "default") {}

// function with callback, or, function signature as param
function myCallback(text: string): void {
	console.log(`myCallback called with ${text}`);
}
function withCallbackArg(
    message: string,
    callbackFn: (text: string) => void
) {}


```



### Interfaces

````typescript
interface IIdName {
    id: number;
    name: string;
}

let idObject: IIdNameObject = {
    id: 2 // gives error for name key
}

// optional properties
interface IOptional {
    id: number;
    name?: string;
}
````

