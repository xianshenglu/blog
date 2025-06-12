---
title: "My Angular Summary: Some NGRX Behaviors I'm surprised"
categories: js
tags: TBD
comments: true
date: 2025-06-02 18:26:27
---

I'm not a fan of [NGRX](https://ngrx.io/guide/store) but indeed there are many Angular developers using it. I also have some experience. So I want to share some strange behaviors I've encountered.

### `ROUTER_NAVIGATION` is by default dispatched before any guards or resolvers run

Let's say you are using `selectUrl` from `@ngrx/router-store` in _app.component.ts_, and you are accessing `http://localhost:4200/#/login`.

```ts
export class AppComponent {
  private store = inject(Store);
  private router = inject(Router);

  ngOnInit(): void {
    this.router.events
      .pipe(
        filter((event) => event instanceof NavigationEnd),
        map((event) => event.url),
        startWith(this.router.url)
      )
      .subscribe((url) => {
        console.log("router events url ", url);
      });
    this.store.select(selectUrl).subscribe((url) => {
      console.log("selectUrl ", url);
    });
  }
}
```

And we have a `canActivate` guard for `login` route.

```ts
export const routes: Routes = [
  //....
  {
    path: "login",
    loadComponent: () =>
      import("./features/login/login.component").then(
        (module) => module.LoginComponent
      ),
    canActivate: [canActivateLogin],
  },
];
```

```ts
export const canActivateLogin: CanActivateFn = (route, state) => {
  console.log("canActivateLogin start");
  return inject(AuthService)
    .login()
    .then((isLogin) => {
      console.log("canActivateLogin end");
      return true;
    });
};
```

What will you see in the logs?

The logs you will see are

> router events url /
>
> selectUrl undefined
>
> selectUrl /login
>
> canActivateLogin start
>
> canActivateLogin end
>
> router events url /login

As we can see from the logs, `selectUrl` gives you a future url when the route is navigating.

If we cancel the navigation in the guard like

```ts
export const canActivateLogin: CanActivateFn = (route, state) => {
  console.log("canActivateLogin start");
  return inject(AuthService)
    .login()
    .then((isLogin) => {
      console.log("canActivateLogin end");
      return false;
    });
};
```

The logs are

> router events url /
>
> selectUrl undefined
>
> selectUrl /login
>
> canActivateLogin start
>
> canActivateLogin end
>
> selectUrl

As you can see, the ui page always stays in `/` while `selectUrl` gives you the wrong url during the navigation. Also, the `undefined` and `''` value from `selectUrl` is weird.

If you have some logic depending on the `selectUrl` you may receive bugs because **`ROUTER_NAVIGATION` is by default dispatched before any guards or resolvers run**

But luckily, we can change that by configuration

```ts
export const appConfig: ApplicationConfig = {
  providers: [
    // ...
    provideRouterStore({
      navigationActionTiming: NavigationActionTiming.PostActivation,
    }),
  ],
};
```

After that, the logs for succeeded `/login` navigation will be

> router events url /
>
> selectUrl undefined
>
> canActivateLogin start
>
> canActivateLogin end
>
> selectUrl /login
>
> router events url /login

For cancelled `/login` navigation will be

> router events url /
>
> selectUrl undefined
>
> canActivateLogin start
>
> canActivateLogin end
>
> selectUrl

If we treat `undefined` and `''` from `selectUrl` as `/` we will have correct url now. Because now `ROUTER_NAVIGATION` will run after guards and resolvers.

### Preloading modules will initialize `effects`

Let's say I'm trying to lazy load `LoginModule` for better performance.

```ts
import { Routes } from "@angular/router";
export const routes: Routes = [
  //...
  {
    path: "login",
    loadChildren: () =>
      import("./features/login/login.module").then(
        (module) => module.LoginModule
      ),
  },
];
```

```ts
const loginRoutes: Routes = [
  {
    path: "",
    loadComponent: () =>
      import("./login.component").then((module) => module.LoginComponent),
    canActivate: [canActivateLogin],
    providers: [],
  },
];

@NgModule({
  declarations: [],
  imports: [RouterModule.forChild(loginRoutes)],
  providers: [
    provideEffects(UserEffects),
    provideState(userFeatureKey, userReducer),
  ],
})
export class LoginModule {}
```

```ts
@Injectable()
export class UserEffects {
  private actions$ = inject(Actions);
  private userService = inject(UserService);
  getUserInfo$ = createEffect(() => {
    return this.actions$.pipe(
      ofType(UserActions.loadUsers, PageHeaderActions.refreshButtonClicked),
      switchMap(() => {
        return this.userService
          .getUserInfo()
          .pipe(map((user) => UserActions.loadUsersSuccess({ user })));
      })
    );
  });
}
```

The `UserEffects` is provided to the `LoginModule`. The `UserEffects.getUserInfo$` can be triggered by `PageHeaderActions.refreshButtonClicked` action.

If you haven't accessed `/login` route the `LoginModule` will not be loaded, and nothing will happen if you dispatch `PageHeaderActions.refreshButtonClicked` action.

However, if you enabled preloading for this route, for example, with `PreloadAllModules`

```ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(
      routes,
      withHashLocation()
      withPreloading(PreloadAllModules)
    ),
  ],
};
```

**The `UserEffects` will be initialized after preloading even though you haven't accessed `/login` page!**

If you dispatch `PageHeaderActions.refreshButtonClicked` action `userService.getUserInfo` will be called **even though you haven't accessed `/login` route**! You may receive bugs because some prerequisites might not be met since the route hasn't been activated. Subsequent calls may cause issues, such as displaying an error popup.

This behavior is quite a shock to me. **I merely enabled preloading, how did this end up causing new bugs!**

### `ROOT_EFFECTS_INIT` is dispatched before `provideAppInitializer`

This issue may be related to the one above. As you know, Angular provides [`provideAppInitializer`](https://angular.dev/api/core/provideAppInitializer),

> The function is executed during the application bootstrap process, and the needed data is available on startup.
>
> If the function returns a Promise or an Observable, initialization does not complete until the Promise is resolved or the Observable is completed.

If you have code relying on `ROOT_EFFECTS_INIT`, for example

```ts
@Injectable()
export class UserEffects {
  private actions$ = inject(Actions);
  private userService = inject(UserService);
  getUserInfo$ = createEffect(() => {
    return this.actions$.pipe(
      ofType(ROOT_EFFECTS_INIT),
      switchMap(() => {
        console.log("effects getUserInfo");
        return this.userService
          .getUserInfo()
          .pipe(map((user) => UserActions.loadUsersSuccess({ user })));
      })
    );
  });
}
```

```ts
function initializeApp() {
  const authService = inject(AuthService);
  const router = inject(Router);
  console.log("initializeApp");
}
export const appConfig: ApplicationConfig = {
  providers: [
    provideAppInitializer(initializeApp),
    provideStore({
      router: routerReducer,
      [userFeatureKey]: userReducer,
    }),
    provideEffects(UserEffects),
  ],
};
```

The effects will be triggered and executed before `provideAppInitializer`. Based on the code above you will see logs

> effects getUserInfo
>
> initializeApp

This is also a surprise to me.

If you want to change it, you can create another action, for example `INIT_ROOT_EFFECTS_AFTER_APP_INITIALIZED` to replace `ROOT_EFFECTS_INIT` and dispatch `INIT_ROOT_EFFECTS_AFTER_APP_INITIALIZED` in the end of `provideAppInitializer` function.

## Last words

There're many things to talk about regarding `NGRX` and I know it's controversial. But basically that's the pitfalls I met in my limited experience regarding the API design of `NGRX` itself.

In the future, I may summarize some controversial parts regarding how to use `NGRX` in projects, and those parts may be more opinionated.

Hope to see you soon.

[**Issue**](https://github.com/xianshenglu/blog/issues/)

[**Source**](https://github.com/xianshenglu/blog/blob/master/source/_posts/.md)

## Reference
