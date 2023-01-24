# Contributing

How to contribute.

## Pre-Requisites
---
Run: `npm install`

---

## Building The Language Server

This project currently supports building a standalone language server through [pkg](https://github.com/vercel/pkg). pkg takes the project and packages it into an executable alongside a node binary.

In order to build the standalone language server first install the pkg cli
```bash
npm install pkg -g
```

Now you have the ability to create standalone language server executables depending on the arguments you pass into pkg.

If you want to create windows-x64, macos-x64, linux-x64 binaries you can use:
```bash
pkg .
```

to create a standalone executable for node16 for windows on arm you can do
```bash
pkg --targets node16-windows-arm64 .
```

to ensure the standalone language server is compressed even more you can do:
```bash
pkg --compress GZip .
```

## Testing
---
### Running Tests
**In VSCode**

In the `Run & Debug` menu are options to separately
run unit or integration tests. Additionally, you
can test the file that is currently open.

**On CLI**

How to test using the CLI.

Unit Testing:
```bash
npm test
```

Integration Testing:
```bash
npm run testInt
```
---
### Writing Tests

* The modules used in testing are:
    * `mocha`: Testing framework
    * `chai`: For assertions
    * `sinon`: stub/mock/spy

* The design of the source code is written with [dependency injection](https://en.wikipedia.org/wiki/Dependency_injection)
in mind.
    * An object or function receives other objects or functions
    for functionality that it depends on, instead of all functionality
    existing statically in one place.
    * This simplifies testing and influences how tests are to be written.

#### How To Use `sinon`

`sinon` is a module that allows you to create spies, stubs and mocks.
These are the core components that complement the testing of a dependency injection designed project.

The following is a quick summary of how to use `sinon` in this project.

**Summary:**
```typescript
// Use to stub interfaces (must explicitly set type in declaration)
stubInterface()

stub(new MyClass()) // Use to stub objects

stub(myFunc) // Use to stub functions

// Explicitly typing
let myClassStub: SinonStubbedInstance<MyClass>
// vs
let myClassStub: MyClass
// will have an impact on compilation and method completion
```

**Imports:**
```typescript
import { stubInterface } from 'ts-sinon' // Only use this module for `stubInterface`
import { SinonStubbedInstance, SinonStubbedMember, createStubInstance, stub } from 'sinon'
```



**Object Instance Stub:**
```typescript
let myClassStub: SinonStubbedInstance<MyClass> = stub(new MyClass())
// Do this if you want myFunc() to execute its actual functionality
myClassStub.myFunc.callThrough()

// Or if you plan to only explicitly stub return values
// it is safer/easier to do the following.
// Note we are not creating a new instance of MyClass here.
let myClassStub: SinonStubbedInstance<MyClass> = createStubInstance(MyClass)
myClassStub.myFunc.returns(3)
```

**Interface Stub:**
```typescript
// Note the need for `ts-sinon.stubInterface()` to stub interfaces.
// `sinon` does not provide the ability to stub interfaces.
let myInterfaceStub: SinonStubbedInstance<MyInterface> = stubInterface()

myInterfaceStub.someFunctionItDefined.returns('my value')
```

**Function Stub:**
```typescript
interface myFuncInterface {
	(x: string): string
}
myFunc: myFuncInterface = (x: string) => { return x}

// Note `SinonStubedMember` instead of `SinonStubbedInstance` for functions
const myFuncStub: SinonStubbedMember<myFuncI> = stub(myFunc)

// Must explicitly type with `SinonStubbedMember` on assignment for this to pass linting
myFuncStub.callThrough() 
```

**Resetting `callThrough()`:**

If you use `callThrough()` on a stubbed object and then want to have it return
a custom value with `returns()`. You must first call `resetBehaviour()`, then `returns()` will work.

```typescript
const myStubbedFunc = stub()
myStubbedFunc.callThrough()
myStubbedFunc.resetBehaviour()
myStubbedFunc.returns()
```
---