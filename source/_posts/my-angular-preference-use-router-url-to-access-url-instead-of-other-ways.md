---
title: "My Angular Preference: Use Router.url to Access Url Instead of Other Ways"
categories: js
tags: TBD
comments: true
date: 2025-05-29 21:16:10
---

There're many ways to access url in Angular. I'll show you why I use `Router.url` instead of other ways.

### [`Location.path()`](https://angular.dev/api/common/Location#path)

As the document said, this api

> Normalizes the URL path for this location.

There're several cases you might need to take care.

#### Empty Value for Root Path.

Given url like `http://localhost:4200/#/`, it will return `''` while `Router.url` returns `/`

#### Not Waiting Guard When Location Is Changed Outside of Angular

If you access an unauthorized page with url like `http://localhost:4200/#/unauthorized` in the browser directly and **the guard is checking** it will return `/unauthorized` immediately. `Router.url` will return `/` when the guard is checking and `/unauthorized` when the guard check is passed. I think `Router.url` behavior makes more sense in most cases. **But in some cases, we do need the `Location.path()` behavior.**

A similar use case is when users click the browser `go back` button directly.

Both cases will change the location directly instead of waiting for the guard checking process **because the location is changed by the browser directly, out of the control of Angular.**

In most normal cases, it will have the same behavior as `Router.url` because users are redirected inside the page by Angular and Angular will change the location after the guard checking process.

### [`Router.events` with `NavigationEnd`](https://angular.dev/api/router/Router#events)

The syntax is like below. You can use it inside a component or put it inside a service.

```ts
@Injectable({
  providedIn: "root",
})
export class RouterUtilitiesService {
  url$ = this.router.events.pipe(
    filter((event) => event instanceof NavigationEnd),
    map((event) => event.url)
  );
  constructor(private router: Router) {}
}
```

```ts
@Component({
  template: ``,
})
export class ChildComponent {
  url$ = this.router.events.pipe(
    filter((event) => event instanceof NavigationEnd),
    map((event) => event.url)
  );
  constructor(
    private router: Router,
    private routerUtilitiesService: RouterUtilitiesService,
    private destroyRef: DestroyRef
  ) {
    this.routerUtilitiesService.url$
      .pipe(takeUntilDestroyed(this.destroyRef))
      .subscribe((v) => console.log(`routerUtilitiesService.url$: ${v}`));
    this.url$
      .pipe(takeUntilDestroyed(this.destroyRef))
      .subscribe((v) => console.log(`url$: ${v}`));
  }
  ngOnInit() {}
}

export const routes: Routes = [
  {
    path: "child",
    component: ChildComponent,
    children: [
      {
        path: "grandchild1",
        component: GrandChildComponent,
      },
      {
        path: "grandchild2",
        component: GrandChildComponent,
      },
    ],
  },
];
```

In the above example you will not see logs if you access the url `/child/grandchild1` for the first time because the `NavigationEnd` event was triggered before `ChildComponent` was activated. For sure you will see logs later when navigating inside `ChildComponent`.

To address this, you can add `startWith(this.router.url)` to make it work for the first time.

### [`ActivatedRoute.snapshot.url`](https://angular.dev/api/router/ActivatedRoute#snapshot)

As the document said, `ActivatedRoute`

> Provides access to information about a route associated with a component that is loaded in an outlet.

It doesn't provide a full url, only `a route associated with a component`. Also, you should not use it outside of a component. For example,

```ts
@Injectable({
  providedIn: "root",
})
export class RouterUtilitiesService {
  constructor(public activatedRoute: ActivatedRoute) {}
}

@Component({
  template: ``,
})
export class GrandChildComponent {
  constructor(
    private routerUtilitiesService: RouterUtilitiesService,
    private activatedRoute: ActivatedRoute
  ) {}
  ngOnInit() {
    console.log(
      `activatedRoute.snapshot.url: `,
      this.activatedRoute.snapshot.url.toString()
    );
    console.log(
      `routerUtilitiesService.activatedRoute.snapshot.url: `,
      this.routerUtilitiesService.activatedRoute.snapshot.url.toString()
    );
  }
}

export const routes: Routes = [
  {
    path: "child",
    component: ChildComponent,
    children: [
      {
        path: "grandchild1",
        component: GrandChildComponent,
      },
      {
        path: "grandchild2",
        component: GrandChildComponent,
      },
    ],
  },
];
```

I provide 2 usages of `activatedRoute.snapshot.url`, one is from the component and another is from `routerUtilitiesService.activatedRoute`. When visiting `http://localhost:4200/#/child/grandchild1`, the logs are

> activatedRoute.snapshot.url: grandchild1
> routerUtilitiesService.activatedRoute.snapshot.url:

As you can see, the value from `activatedRoute` is `grandchild1` since we use it in the `GrandChildComponent`. It will be `child` if used in the `ChildComponent`.

And the value from `routerUtilitiesService.activatedRoute` is really weird. If you want to extract a utility function in a service like `isGrandChild1Page`, you need to inject `activatedRoute` in the component each time and pass it to the service method to get the expected value. Something like

```ts
@Injectable({
  providedIn: "root",
})
export class RouterUtilitiesService {
  constructor() {}
  isGrandChild1Page(activatedRoute: ActivatedRoute) {
    return activatedRoute.snapshot.url[0].path === "grandchild1";
  }
}
@Component({
  template: ``,
})
export class GrandChildComponent {
  constructor(
    private routerUtilitiesService: RouterUtilitiesService,
    private activatedRoute: ActivatedRoute
  ) {}
  ngOnInit() {
    console.log(
      this.routerUtilitiesService.isGrandChild1Page(this.activatedRoute)
    );
  }
}
```

A little bit inconvenient. If using `Router.url`, the code is like

```ts
@Injectable({
  providedIn: "root",
})
export class RouterUtilitiesService {
  constructor(private router: Router) {}
  isGrandChild1PageWithRouter() {
    return this.router.url.split("?")[0].endsWith("/grandchild1");
  }
}
@Component({
  template: ``,
})
export class GrandChildComponent {
  constructor(
    private routerUtilitiesService: RouterUtilitiesService,
  ) {}
  ngOnInit() {
    console.log(this.routerUtilitiesService.isGrandChild1PageWithRouter());
  }
}
```

### [`Router.routerState.snapshot.url`](https://angular.dev/api/router/Router#routerState)

As the doc said, `Router.routerState`

> The current state of routing in this NgModule. Represents the state of the router as a tree of activated routes.

`Router.url`

> The current URL.

I'm not sure whether they're totally identical. From my test they are. But I don't think you will use this one since `Router.url` is simpler obviously.

## Conclusion

Ok, I summarized the API I considered before to get the Angular page url. Depending on your use case you may have different choices or other APIs not listed here. In any case, use it carefully.

So far, I would use `Router.url` by default.

[**Issue**](https://github.com/xianshenglu/blog/issues/)

[**Source**](https://github.com/xianshenglu/blog/blob/master/source/_posts/.md)

## Reference
