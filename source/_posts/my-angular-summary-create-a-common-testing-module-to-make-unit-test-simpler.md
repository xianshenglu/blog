---
title: "My Angular Summary: Create A Common Testing Module to Make Unit Test Simpler"
categories: js
tags: TBD
comments: true
date: 2025-06-01 16:02:16
---

Have you ever felt too much duplicated code when trying to make the `.spec.ts` work without errors? I will show you how to make the unit test work with less duplications.

### Reproduction

Let's start with a demo code

```ts
// app.component.ts
import { Component } from "@angular/core";
import { RouterOutlet } from "@angular/router";
import { UserService } from "./core/services/user.service";

@Component({
  selector: "app-root",
  imports: [RouterOutlet],
  templateUrl: "./app.component.html",
  styleUrl: "./app.component.scss",
})
export class AppComponent {
  title = "angular-playground";
  constructor(private userService: UserService) {
    this.isAdmin();
  }
  isAdmin() {
    return this.userService.isAdmin();
  }
}
```

```ts
// user.service.ts
import { HttpClient } from "@angular/common/http";
import { Injectable } from "@angular/core";

@Injectable({
  providedIn: "root",
})
export class UserService {
  constructor(private httpClient: HttpClient) {}
  isAdmin() {
    return false;
  }
}
```

```ts
// app.component.spec.ts
import { TestBed } from "@angular/core/testing";
import { AppComponent } from "./app.component";

describe("AppComponent", () => {
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [AppComponent],
    }).compileComponents();
  });

  it("should create the app", () => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.componentInstance;
    expect(app).toBeTruthy();
  });
});
```

I used `UserService` in `AppComponent` and call `isAdmin` in the constructor. And `UserService` rely on `HttpClient`. The spec file(_app.component.spec.ts_) was created by Angular without my changes.

When running the unit test I will receive errors like

> NullInjectorError: R3InjectorError(DynamicTestModule)[UserService -> HttpClient -> HttpClient]:
>
> NullInjectorError: No provider for HttpClient!

It happens frequently as long as you have 1 dependency that can not be initialized automatically.

There are many ways to solve that. For sure, in this case, you can just import `HttpClientTestingModule` in the spec file to solve that, and it looks simple. But I will not do that. I will assume a more complicated or realistic case for demo purpose. But in the end, my solution is still simpler compared with import `HttpClientTestingModule` way.

I assume you want to spy `UserService` and use it in the spec. In that case, the code can be

```ts
import { TestBed } from "@angular/core/testing";
import { AppComponent } from "./app.component";
import { UserService } from "./core/services/user.service";

describe("AppComponent", () => {
  let userServiceSpy: jasmine.SpyObj<UserService>;
  beforeEach(() => {
    userServiceSpy = jasmine.createSpyObj(["isAdmin"]);
  });
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      providers: [{ provide: UserService, useValue: userServiceSpy }],
      imports: [AppComponent],
    }).compileComponents();
  });

  it("should create the app", () => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.componentInstance;
    expect(app).toBeTruthy();
  });

  it("should isAdmin return true", () => {
    const fixture = TestBed.createComponent(AppComponent);
    userServiceSpy.isAdmin.and.returnValue(true);
    expect(fixture.componentInstance.isAdmin()).toEqual(true);
  });
});
```

So you can add new tests that depend on different return values from `UserService` API like

```ts
it("should isAdmin return true", () => {
  const fixture = TestBed.createComponent(AppComponent);
  userServiceSpy.isAdmin.and.returnValue(true);
  expect(fixture.componentInstance.isAdmin()).toEqual(true);
});
```

The problem is that **each time we want to mock `UserService` in other spec files we need duplicated initialization code like**

```ts
let userServiceSpy: jasmine.SpyObj<UserService>;

beforeEach(() => {
  userServiceSpy = jasmine.createSpyObj(["isAdmin"]);
});

[{ provide: UserService, useValue: userServiceSpy }];
```

Even if we don't plan to use the spy as long as the test throws error because of the new service dependencies we still need the last line of the above code `{ provide: UserService, useValue: jasmine.createSpyObj(["isAdmin"]) }` to make the initialization work without errors.

It's a little bit annoying.

### Solution

First, create `src/test.ts` since the latest Angular projects don't have it by default. If you already have this file, please modify it according to the code below and your case.

```ts
// src/test.ts
import { NgModule } from "@angular/core";
import { getTestBed } from "@angular/core/testing";
import {
  BrowserDynamicTestingModule,
  platformBrowserDynamicTesting,
} from "@angular/platform-browser-dynamic/testing";
import { UnitTestCommonModule } from "./testing/common.module";

@NgModule({
  providers: [],
  imports: [BrowserDynamicTestingModule],
})
class UnitTestRequiredModule {}

getTestBed().initTestEnvironment(
  [UnitTestRequiredModule, UnitTestCommonModule],
  platformBrowserDynamicTesting()
);
```

```ts
// src/testing/common.module
import { NgModule } from "@angular/core";
import { provideUserServiceSpy } from "./user-service-spy";

@NgModule({
  providers: [provideUserServiceSpy()],
  imports: [],
})
export class UnitTestCommonModule {}
```

```ts
// src/testing/user-service-spy.ts
import { UserService } from "../app/core/services/user.service";

export const userServiceSpy = jasmine.createSpyObj<UserService>(["isAdmin"]);
export const provideUserServiceSpy = () => ({
  provide: UserService,
  useValue: userServiceSpy,
});
```

We provide `userServiceSpy` to `UnitTestCommonModule` and import `UnitTestCommonModule` when `initTestEnvironment`. The flow is like `test.ts-->initTestEnvironment-->UnitTestCommonModule-->userServiceSpy`.

Second, register the `src/test.ts` in the `angular.json` file to let Angular be aware of it. Old Angular projects may already have it.

```json
{
  //...
  "projects": {
    "angular-playground": {
      "architect": {
        //...
        "test": {
          //...
          "options": {
            "main": "src/test.ts"
            //...
          }
        }
      }
    }
  }
}
```

and add `src/test.ts` in the `tsconfig.spec.json` `include` field.

```json
{
  //...
  "include": ["src/test.ts" /*...*/]
}
```

After that, **the initial spec can work without applying any changes**. Even if you want to use the mock, you only need to reference the `userServiceSpy`. For example,

```ts
import { TestBed } from "@angular/core/testing";
import { AppComponent } from "./app.component";
import { userServiceSpy } from "../testing/user-service-spy";

describe("AppComponent", () => {
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [AppComponent],
    }).compileComponents();
  });

  it("should create the app", () => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.componentInstance;
    expect(app).toBeTruthy();
  });
  it("should isAdmin return true", () => {
    const fixture = TestBed.createComponent(AppComponent);
    userServiceSpy.isAdmin.and.returnValue(true);
    expect(fixture.componentInstance.isAdmin()).toEqual(true);
  });
});
```

For other specs that rely on `UserService` you don't need to create the mock either.

For the very beginning idea regarding importing `HttpClientTestingModule` in the spec file, you could add it in the `UnitTestCommonModule` instead. And after that, no spec needs to import `HttpClientTestingModule` any more.

### Last words

The above changes were validated with `@angular/core@19.2.14`.

In the future, the above solution may need to be changed because somehow we're relying on Angular's current support or implementations regarding executing tests. They could change someday.

[**Issue**](https://github.com/xianshenglu/blog/issues/)

[**Source**](https://github.com/xianshenglu/blog/blob/master/source/_posts/.md)

## Reference

[setup-global-providers-for-angular-unit-tests](https://stackoverflow.com/questions/79182192/setup-global-providers-for-angular-unit-tests)
