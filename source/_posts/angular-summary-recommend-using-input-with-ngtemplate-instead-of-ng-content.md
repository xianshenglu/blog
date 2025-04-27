---
title: "My Angular summary: Using ngTemplateOutlet instead of ng-content"
categories: js
tags: TBD
comments: true
date: 2025-04-20 21:51:32
---

As we know, we can use `ng-content` to inject content inside a component. But we can also do that with `ngTemplateOutlet`. Finally, the real world cases made me lean towards `ngTemplateOutlet` by default for most cases because of the type safety, simplicity, and extensibility issue in `ng-content`.

Let's take the `card` for example. Let's say we need to support customized `card-title` and `card-body`.

### Type safety and simplicity

I implement `card-title` with `ng-content` and `card-body` with `ngTemplateOutlet`. The type-safe version is like

```ts
const CARD_TITLE_SELECTOR = "card-title";

@Component({
  selector: CARD_TITLE_SELECTOR,
  imports: [],
  template: `<ng-content></ng-content> `,
  styles: ``,
})
export class CardTitleComponent {}

@Component({
  selector: "card",
  imports: [NgTemplateOutlet],
  template: `
    <ng-content [select]="cardTitleSelector"></ng-content>
    <div class="card-divider"></div>
    <ng-container *ngTemplateOutlet="cardBody"></ng-container>
  `,
  styles: ``,
})
export class CardComponent {
  @Input({ required: true }) cardBody!: TemplateRef<any>;
  cardTitleSelector = CARD_TITLE_SELECTOR;
}
```

And the usage is like

```html
<card [cardBody]="cardBody">
  <card-title>card title</card-title>
  <ng-template #cardBody> card body </ng-template>
</card>
```

As you can see, they are really similar.

Regarding the simplicity, the `ngTemplateOutlet` version wins because `ng-content` version looks a little bit verbose because of the declaration of `CardTitleComponent` and `CARD_TITLE_SELECTOR`.

Regarding the type safety, the `ngTemplateOutlet` version wins because you can see errors in the editor if `cardBody` is not provided since it's a required input. The error message is like

> Required input 'cardBody' from component CardComponent must be specified

### Extensibility

#### Conditional content projection

`ng-content` is not recommended in the conditional content projection case, which you can find on the Angular [homepage](https://angular.dev/guide/components/content-projection#).

> You should not conditionally include `<ng-content>` with @if, @for, or @switch. Angular always instantiates and creates DOM nodes for content rendered to a `<ng-content>` placeholder, even if that `<ng-content>` placeholder is hidden. For conditional rendering of component content, see Template fragments.

For example, given the code below

```html
<card [cardBody]="cardBody">
  <card-title>card title</card-title>
  <ng-template #cardBody> card body </ng-template>
</card>
```

```ts
@Component({
  selector: CARD_TITLE_SELECTOR,
  imports: [],
  template: `<ng-content></ng-content> `,
  styles: ``,
})
export class CardTitleComponent {
  constructor() {
    alert("card title");
  }
}

@Component({
  selector: "card",
  imports: [NgTemplateOutlet, NgIf],
  template: `
    <ng-container *ngIf="false">
      <ng-content [select]="cardTitleSelector"></ng-content>
    </ng-container>

    <div class="card-divider"></div>
    <ng-container *ngTemplateOutlet="cardBody"></ng-container>
  `,
  styles: ``,
})
export class CardComponent {
  @Input({ required: true }) cardBody!: TemplateRef<any>;
  cardTitleSelector = CARD_TITLE_SELECTOR;
}

```

Even `card` component has `*ngIf="false"` with `cardTitleSelector` the `card-title` component is still rendered, and you will see the alert.

In real-world cases, we may need to hide or display something completely depends on the inner component state change. In that case, `ng-content` is not recommended, though we can still make it work.

#### Context for complex cases

In the case of interacting with the component state `ngTemplateOutlet` support `context` which is much more useful for complex cases. For example,

```ts
@Component({
  selector: "card",
  imports: [NgTemplateOutlet, NgIf],
  template: `
    <ng-container *ngIf="false">
      <ng-content [select]="cardTitleSelector"></ng-content>
    </ng-container>

    <div class="card-divider"></div>
    <ng-container
      *ngTemplateOutlet="cardBody; context: { isActive: false}"
    ></ng-container>
  `,
  styles: ``,
})
export class CardComponent {
  @Input({ required: true }) cardBody!: TemplateRef<any>;
  cardTitleSelector = CARD_TITLE_SELECTOR;
}
```

```html
<card [cardBody]="cardBody">
  <card-title>card title</card-title>
  <ng-template #cardBody let-isActive="isActive">
    card body is active? {{ isActive }}
  </ng-template>
</card>
```

Sometimes the `context` can be replaced with `Output` event, but sometimes it's not easy to do that. 

Actually, there's an open issue [Scoped Slots (similar to Vue)](https://github.com/angular/angular/issues/52552) for the similar function in `ng-content`.

## Last words

I have to say `ng-content` is more intuitive in my opinion, and that's also my initial choice when using Angular. I thought it's the similar API like [`Slots` in Vue](https://vuejs.org/guide/components/slots.html)(I used Vue before).

However, the above reasons made me lean towards `ngTemplateOutlet` during the development. I don't want to implement with intuitive `ng-content` first and one day switch to `ngTemplateOutlet` because of the `context` or conditional content projection limitation. Or adding some nonintuitive trick to make `ng-content` continue to work is also not what I want.

Actually, regarding 2 different APIs, Vue only provide 1. I really don't know what Angular team is thinking about.

[**Issue**](https://github.com/xianshenglu/blog/issues/)

[**Source**](https://github.com/xianshenglu/blog/blob/master/source/_posts/.md)

## Reference
