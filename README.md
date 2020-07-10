# NexoPOS 4
## Menu API
The menu API is exposed by the MenuService. This class registers and displays menus on the dashboard.
Every menu should have as a key prefixed with a unique identifier. Similar keys are just merged. This could
be a good way to overwrite existing menu. Preferably, all menu should have unique identifier.

Menu Example : 

```php
$menus  =   [
    'dashboard' =>  [
        'href'          =>  url( '/dashboard' ),
        'label'         =>  __( 'Dashboard' ),
        'icon'          =>  'la-home',
        'notification'  =>  10
    ]
]
```

A menu might have childrens which are sub links. Usually, children should be used to group same menu together.

```php
$menus  =   [
    'dashboard' =>  [
        'href'          =>  url( '/dashboard' ),
        'label'         =>  __( 'Dashboard' ),
        'icon'          =>  'la-home',
        'notification'  =>  10,
        'childrens'     =>  [

        ]
    ]
]
```

Providing an `href` for the top menu is not necessary while having children. This should make sure no navigation is triggered while
clicking on a menu that has children, so that these latest are revealed to the user. A menu without `href` key has "javascript:void(0)" as replacement. 

Submenu doesn't support icones, nor notifications.

# Javascript API
NexoPOS 4 is built on top of Vue.js. It provides bunch of components that helps to prototype UI quickly. Some part of the applications are running as SPA, such as the setup page. This section will disclose the internal JavaScript API to help understanding how it works.

## Form Validation
The Form validation helps to make sure a form is valid before submition. On NexoPOS 4.x a reactive approach is used to build a form. That ensures dynamic form creation (from remote server). The component `ns-input`, `ns-select`, `ns-radio`, `ns-datetime`, `ns-date`, `ns-textarea` are all compatible with the validation. Refer to each components to understand how the integration is made.

The Form Validation class is available on "resources/js/libraries/form-validation.js". This class expose some methods the helps interactif with a Form Array. A Form Array consist of list of fields. 

Here is how to defined a field.

### Creating An Array Of Fields
```js
const myFields    =   [
    {
        'label' =>  'Your Username',
        'name'  =>  'username',
        'description'   =>  '',
        'validation'    =>  'required',
    }
];
```

While looping the fields, the Form Validation class uses the `validation` attribute of each field as a reference for the validation. On the previous example, the rule `required` is applied. But a validation my contains more than one rule, splitted by the `|` character. 

### Init Validation Class On Fields
Ideally, it's recommended to initialize an array of field using the `createForm` method of the Form Validation class. This method ensures that the form is populated with useful properties (for the validation). This can be made this way :

```js
const myFields    =   [
    {
        'label' =>  'Your Username',
        'name'  =>  'username',
        'description'   =>  '',
        'validation'    =>  'required',
    }
];
const form      =   require( './path/to/form-validation' );
const fields    =   form.createForm( myFields );
```

### Render Forms Dynamically
As we've said that a reactive approach is used, you can use existing form components to render your form. This should be made this way.

```html
<template v-for="field of fields">
    <ns-input v-if="[ 'text', 'password' ].includes( field.type )" :field="field" @change="form.validateField( field )"></ns-input>
</template>
```

### Form Validation
Before proceeding to the validaton, it's required to make sure the provided data are valid using the ruleset defined on each field. This can be made using the `validateForm` methods which accepts an array of fields (initialized with `createForm`). Here is how you can acheive that : 

```js
// within a component
methods: {
    submitForm() {
        if ( this.form.validateForm( this.fields ) ) {
            // means the form is valid.
        }
    }
}
// ...
```
