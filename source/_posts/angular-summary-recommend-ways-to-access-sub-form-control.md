---
title: "Angular summary: recommend using FormGroup.controls to access sub form control"
categories: js
tags: TBD
comments: true
date: 2025-04-20 16:58:39
---

In Angular we at least have 2 ways to access sub form control, `FormGroup.get('controlPath')` or `FormGroup.controls.controlPath`. So far, I choose to use the latter one for the sake of type safety.

Considering the below code

```ts
@Component({
  selector: 'app-root',
  templateUrl: './app/playground.component.html',
  imports: [FormsModule, ReactiveFormsModule],
})
export class PlaygroundComponent {
  formGroup = this.formBuilder.group({
    booker: new FormControl('123', {validators: [Validators.required]}),
    bookNames: this.formBuilder.array<string>([]),
    bookerConf: this.formBuilder.group({language: ''})
  });
  constructor(private formBuilder: FormBuilder) {
    const bookerFromControl = this.formGroup.controls.booker; // FormControl<string | null>
    const bookerFromGetPath = this.formGroup.get('booker'); // AbstractControl<string | null, string | null> | null
    const bookerFromGetPaths = this.formGroup.get(['booker']); // AbstractControl<any, any> | null
    const bookerFromGetPathsWithConst = this.formGroup.get(['booker'] as const); // AbstractControl<string | null, string | null> | null
    
    const bookNameFromControls = this.formGroup.controls.bookNames; // FormArray<FormControl<string | null>>
    const bookNamesFromPath = this.formGroup.get('bookNames'); // AbstractControl<(string | null)[], (string | null)[]> | null
    const bookNamesFromPaths = this.formGroup.get(['bookNames']); // AbstractControl<any, any> | null
    const bookNamesFromPathsWithConst = this.formGroup.get(['bookNames'] as const); // AbstractControl<(string | null)[], (string | null)[]> | null
  }
}
```

As you can see `formGroup.get` at least has 2 issues

- can not recognize if there is `booker` field or not, thus the type of `bookerFromGetPath` can be `null`
  - There will be no errors when typing the wrong form control name, like `formGroup.get('booker1')`.
  - Also, the VSCode refactoring function will not work for `formGroup.get('booker')` when you want to change all `booker` field names together.
  - And type becomes even worse with a path array argument like `formGroup.get(['booker'])`. But for this case we can use it with `as const` like `formGroup.get(['booker'] as const)` to make it not that bad.
- `bookerFromGetPath` returns the wrong type, `AbstractControl` not `FormControl`.
  - It is not a big issue since `AbstractControl` is similar to `FormControl`.
  - But for `bookNameFromControls` the differences between `FormArray` and `AbstractControl` is big.

There's an open issue [Strictly typed FormGroup should have stictly-typed "get" accessor](https://github.com/angular/angular/issues/58697) opened on Nov 16, 2024.

As of 2025/4/20, the issue is still not fixed. And the issue only focuses on the first problem.

Anyway, that's the reason for my choice.

[**Issue**](https://github.com/xianshenglu/blog/issues/)

[**Source**](https://github.com/xianshenglu/blog/blob/master/source/_posts/angular-summary-recommend-ways-to-access-sub-form-control.md)

## Reference
