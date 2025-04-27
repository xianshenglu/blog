---
title: >-
  My Angular summary: Using formControl directive instead of
  formControlName
categories: js
tags: TBD
comments: true
date: 2025-04-20 22:41:06
---

As the title said, I finally chose to use `formControl` directive instead of `formControlName` for the sake of type safety and simplicity.

### Type safety

Given this formGroup

```ts
formGroup = this.formBuilder.group({
  booker: "",
  bookNames: this.formBuilder.array([new FormControl("", Validators.required)]),
});
```

The HTML with `formControlName` could be like this

```html
<div [formGroup]="formGroup">
  <input
    [placeholder]="'please enter the booker name'"
    formControlName="booker"
  />
  <ng-container formArrayName="bookNames">
    <input [placeholder]="'please enter the book name'" formControlName="0" />
  </ng-container>
</div>
```

So far, you will not see errors in the editor if there are typos in field names `booker`, `bookNames` or `0`. You can only see errors at runtime, like `ERROR Error: Cannot find control with name: 'booker1'`.

However, you will see errors like `Property 'booker1' does not exist on type '` immediately in the editor when binding with `formControl` incorrectly because it's typescript.

```html
<div>
  <input
    [placeholder]="'please enter the booker name'"
    [formControl]="formGroup.controls.booker"
  />
  <input
    [placeholder]="'please enter the book name'"
    [formControl]="formGroup.controls.bookNames.at(0)"
  />
</div>
```

The type check will help you a lot, especially when you're renaming the field names.

### Simplicity

Based on the above example, you can see we don't need `formGroup` or `formArrayName` to work with `formControl`. But we do need them when using the `formControlName` directive. It's not a big issue in this example.

Let's say you want to move the `formGroup.controls.bookNames.at(0)` to a child component. With the `formControl` directive, you only need `@Input() formCtrl!: FormControl<string | null>;` to make it work. The complete code is like

```ts
@Component({
  selector: "app-item",
  imports: [FormsModule, ReactiveFormsModule],
  template: `
    <input
      [placeholder]="'please enter the book name'"
      [formControl]="formCtrl"
    />
  `,
  styles: ``,
})
export class ItemComponent {
  @Input() formCtrl!: FormControl<string | null>;
}
```

and

```html
<div>
  <input
    [placeholder]="'please enter the booker name'"
    [formControl]="formGroup.controls.booker"
  />
  <app-item [formCtrl]="formGroup.controls.bookNames.at(0)"> </app-item>
</div>
```
You can see that the effort and changes are small. 

While using `formControlName` you need `@Input() formGroup!: FormGroup<any>;`, `@Input() formArrayName!: string;` and `@Input() formCtrlName!: string;` to make it work. The complete code is like

```ts
@Component({
  selector: "app-item",
  imports: [FormsModule, ReactiveFormsModule],
  template: `
    <ng-container [formGroup]="formGroup">
      <ng-container [formArrayName]="formArrayName">
        <input
          [placeholder]="'please enter the book name'"
          [formControlName]="formCtrlName"
        />
      </ng-container>
    </ng-container>
  `,
  styles: ``,
})
export class ItemComponent {
  @Input() formGroup!: FormGroup<any>;
  @Input() formArrayName!: string;
  @Input() formCtrlName!: string;
}
```

```html
<div [formGroup]="formGroup">
  <input
    [placeholder]="'please enter the booker name'"
    formControlName="booker"
  />
  <app-item
    [formCtrlName]="'0'"
    [formArrayName]="'bookNames'"
    [formGroup]="formGroup"
  >
  </app-item>
</div>
```

As you can see, it doesn't look smart. 

In fact `ItemComponent` can work with just `formControlName`. But in that case `ItemComponent` will need to implement `ControlValueAccessor` which is not so easy and brings another complexity.

[**Issue**](https://github.com/xianshenglu/blog/issues/)

[**Source**](https://github.com/xianshenglu/blog/blob/master/source/_posts/.md)

## Reference
