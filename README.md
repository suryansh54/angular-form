# Angular Forms

- **Reactive forms** are more robust: they're more scalable, reusable, and testable. If forms are a key part of your application, or you're already using reactive patterns for building your application, use reactive forms.
- **Template-driven forms** are useful for adding a simple form to an app, such as an email list signup form. They're easy to add to an app, but they don't scale as well as reactive forms. If you have very basic form requirements and logic that can be managed solely in the template, use template-driven forms.

|  | REACTIVE | TEMPLATE-DRIVEN |
| --- | --- | --- |
| Setup (form model) | More explicit, created in component class | 	Less explicit, created by directives |
| Data model | Structured | Unstructured |
| Predictability | Synchronous | Asynchronous |
| Form validation | Functions | Directives |
| Mutability | Immutable | Mutable |
| Scalability | Low-level API access | Abstraction on top of APIs |

Both reactive and template-driven forms share underlying building blocks.

- **FormControl** tracks the value and validation status of an individual form control.
- **FormGroup** tracks the same values and status for a collection of form controls.
- **FormArray** tracks the same values and status for an array of form controls.
- **ControlValueAccessor** creates a bridge between Angular FormControl instances and native DOM elements.

## Reactive forms

**Step 1:** Registering the reactive forms module

```javascript
// app.module.ts
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [
    // other imports ...
    ReactiveFormsModule
  ],
})
export class AppModule { }
```

**Step 2:** Generating and importing a new form control

```javascript
// name-editor.component.ts
import { Component } from '@angular/core';
import { FormControl } from '@angular/forms';

@Component({
  selector: 'app-name-editor',
  templateUrl: './name-editor.component.html',
  styleUrls: ['./name-editor.component.css']
})
export class NameEditorComponent {
  name = new FormControl('');
}
```

**Step 3:** Registering the control in the template

```html
<!--name-editor.component.html-->
<label>
  Name:
  <input type="text" [formControl]="name">
</label>

<p>
  Value: {{ name.value }}
</p>
```

#### Replacing a form control value
```javascript
// name-editor.component.ts
updateName() {
  this.name.setValue('Nancy');
}
```

```html
<!--name-editor.component.html-->
<p>
  <button (click)="updateName()">Update Name</button>
</p>
```

### Grouping form controls
**Step 1:** Creating a FormGroup instance

```javascript
// profile-editor.component.ts
import { Component } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

@Component({
  selector: 'app-profile-editor',
  templateUrl: './profile-editor.component.html',
  styleUrls: ['./profile-editor.component.css']
})
export class ProfileEditorComponent {
  profileForm = new FormGroup({
    firstName: new FormControl(''),
    lastName: new FormControl(''),
  });
  
  onSubmit() {
    console.warn(this.profileForm.value);
  }
}
```
**Step 2:** Associating the FormGroup model and view
```html
<!--profile-editor.component.html-->
<form [formGroup]="profileForm" (ngSubmit)="onSubmit()">
  
  <label>
    First Name:
    <input type="text" formControlName="firstName">
  </label>

  <label>
    Last Name:
    <input type="text" formControlName="lastName">
  </label>

</form>
<button type="submit" [disabled]="!profileForm.valid">Submit</button>
```

### Creating nested form groups

**Step 1:** Creating a nested group
```javascript
// profile-editor.component.ts
import { Component } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

@Component({
  selector: 'app-profile-editor',
  templateUrl: './profile-editor.component.html',
  styleUrls: ['./profile-editor.component.css']
})
export class ProfileEditorComponent {
  profileForm = new FormGroup({
    firstName: new FormControl(''),
    lastName: new FormControl(''),
    address: new FormGroup({
      street: new FormControl(''),
      city: new FormControl(''),
      state: new FormControl(''),
      zip: new FormControl('')
    })
  });
}
```

**Step 2:** Grouping the nested form in the template
```html
<!--profile-editor.component.html-->
<div formGroupName="address">
  <h3>Address</h3>

  <label>
    Street:
    <input type="text" formControlName="street">
  </label>

  <label>
    City:
    <input type="text" formControlName="city">
  </label>
  
  <label>
    State:
    <input type="text" formControlName="state">
  </label>

  <label>
    Zip Code:
    <input type="text" formControlName="zip">
  </label>
</div>
```

#### Partial model updates

There are two ways to update the model value:

- Use the **setValue()** method to set a new value for an individual control. The setValue() method strictly adheres to the structure of the form group and replaces the entire value for the control.
- Use the **patchValue()** method to replace any properties defined in the object that have changed in the form model.

```javascript
// profile-editor.component.ts
updateProfile() {
  this.profileForm.patchValue({
    firstName: 'Nancy',
    address: {
      street: '123 Drew Street'
    }
  });
}
```

```html
<!--profile-editor.component.html-->
<p>
  <button (click)="updateProfile()">Update Profile</button>
</p>
```

#### Generating form controls with FormBuilder

```javascript
// profile-editor.component.ts
import { Component } from '@angular/core';
import { FormBuilder } from '@angular/forms'; // Importing the FormBuilder class

@Component({
  selector: 'app-profile-editor',
  templateUrl: './profile-editor.component.html',
  styleUrls: ['./profile-editor.component.css']
})
export class ProfileEditorComponent {
  profileForm = this.fb.group({
    firstName: [''],
    lastName: [''],
    address: this.fb.group({
      street: [''],
      city: [''],
      state: [''],
      zip: ['']
    }),
  });

  constructor(private fb: FormBuilder) { } // Injecting the FormBuilder service
}
```

**Question:** Angular form builder vs form control and form group
**Answer**
- The FormBuilder provides syntactic sugar that shortens creating instances of a FormControl, FormGroup, or FormArray. It reduces the amount of boilerplate needed to build complex forms.
- The FormBuilder service is an injectable provider that is provided with the reactive forms module.
- If you read more you see that the form builder is a service that does the same things as form-group, form-control and form-array. The official docs describe it as:
- Creating form control instances manually can become repetitive when dealing with multiple forms. The FormBuilder service provides convenient methods for generating controls.
- So basically saying that FormBuilder is a service that is trying to help us reduce boiler-plate code.An example of how FormBuilder is used to reduce boiler plate code can be seen here. To answer the question:

**Question:** So is there any advantage of one over the other way of doing it or is it just preference ? 
**Answer** Well there is no technical advantage and whichever code you use all boils down to your preference.

**Using form control and form group**
```javascript
profileForm = new FormGroup({
  firstName: new FormControl(''),
  lastName: new FormControl(''),
  address: new FormGroup({
    street: new FormControl(''),
    city: new FormControl(''),
    state: new FormControl(''),
    zip: new FormControl('')
  })
});
```
**Using form builder**
```javascript
profileForm = this.fb.group({
  firstName: [''],
  lastName: [''],
  address: this.fb.group({
    street: [''],
    city: [''],
    state: [''],
    zip: ['']
  }),
});
```
