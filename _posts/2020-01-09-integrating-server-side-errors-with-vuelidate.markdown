---
author: Philip Hendry
title: Integrating Server Side Errors with Vuelidate
date: 2020-01-09 20:00
comments: true
layout: post
tags:
- vue-js
---
I've been busy getting my ASP.NET WebAPI to return validation errors to my clients with as little coding effort as possible (see  [ASP.NET Web Api Complex Model Binding and Validation]({% post_url 2019-12-07-asp-net-web-api-complex-model-binding-and-valiation %}).) However, it's time to focus on the client of these errors.

My client application is written using [Vue.JS](https://vuejs.org/) with [Vuelidate](https://vuelidate.js.org/) providing the framework for creating validation rules against my data models. However, I don't have a way to submit the data to the server and then respond to errors returned which gets returned as JSON looking something like this:

```json
{
    "Message": "The request is invalid.",
    "ModelState": {
        "person.Name": [
            "The field Name must be a string with a maximum length of 10."
        ]
    }
}
```

So this blog will present my way of integrating errors from a server into my client application.

# Vuelidate
Vuelidate is my validation framework of choice and I love the way validation rules are created declaratively alongside the data and things just seem to work. However, it's taken me a while to realise that Vuelidates' purpose is to run a rule then output a true/false, valid/not valid and that is all. Message rendering is up to you (or another tool such as [vuelidate-error-extractor](https://dobromir-hristov.github.io/vuelidate-error-extractor/overriding_messages.html).)

With this in mind my solution is to leverage the fact that the defined rules in Vuelidate can be [dynamic](https://vuelidate.js.org/#sub-dynamic-validation-schema) and that those rule could be provided by a computed property.

# Requirements
Before leaping into the implementation I'd like to cover some of the requirements I need covered:

1. Client-side errors are validated as usual, preventing submission and before any server-side errors will contribute.
2. When the form is submitted and errors are returned, each error is displayed against the appropriate form field. The message itself is provided by the server.
3. When the server-side error is displayed it must contribute to the [$invalid](https://vuelidate.js.org/#sub-error-vs-any-error) flags. In other words, if the field is nested in the data then the $invalid flag at the root of the data must indicate this failure.
4. When a field with a server-side error is modified the error is cleared immediately and can only re-appear when re-submitted to the server.
5. I want to be able to re-submit and any existing server-side errors will be cleared and the form submitted. Client-side errors will always prevent submission until manually fixed.

# Solution

Here's the script section first with inline comments describing the relevance of each part:

```javascript
<script>
import { required } from "vuelidate/lib/validators"
import { merge } from "lodash"

export default {
    data() {
        return {
            // The form object will contain data we're binding to inputs
            form: {
                name: "Bob"
            },
            // Rather than define vuelidate rules in the validations() section
            // they're defined here ready to be merged with server errors.
            clientValidation: {
                form: {
                    name: { required }
                }
            },
            // Server errors converted from the json format they're sent in
            // over the wire and converted to the vuelidate format and stored
            // here.
            serverValidation: {}
        },
    },
    computed: {
        // When the serverValidation property is set this computed property 
        // fires and merges the client and server validation
        rules() {
            return merge({}, this.serverValidation, this.clientValidation);
        }
    },
    // The Vuelidate validations are defined here and react to changes in the
    // computed property it uses.
    validations() {
        return this.rules;
    },
    methods: {
        // onSubmit is called when the user attempts to submit the form to the server
        onSubmit: function() {
            // Touch the form to force client errors to be displayed
            this.$v.$touch();
            // But clear server errors as they shouldn't prevent re-submission
            this.clearServerErrors();

            // Don't allow submission if any client errors are invalid
            if (this.$v.$invalid) {
                alert("Form is invalid");
            } else {
                // The following assignment represents the returned errors from the server
                const serverMessages = {
                    serverErrors: {
                        name: "The name field is invalid server-side"
                    }
                };

                // Merge the server errors into the data. This doesn't yet cause
                // any validation errors but does make the messages (and therefore state)
                // accessible to the template.
                merge(this.form, serverMessages);

                // From the serverMessage we should create a validation object which will
                // be merged with the client validation objects. It's at this point the
                // computed property changes and Vuelidate updates it's validation
                this.serverValidation = {
                    form: {
                        name: {
                            serverError: serverError("nameField", false)
                        }
                    }
                }
            };
        },
        clearServerErrors: function() {
            // Clearing the server validation removes the rules and
            // therefore server errors no longer contribute to validation state.
            this.serverValidation = {};
            // Clearing the server errors from the data isn't necessary either
            // but again makes for good housekeeping and we'd need to do this
            // if submitting this data to the server.
            removeProp(this.form, "serverErrors");
        },
        clearServerError: function(model, fieldName) {
            // When an input control is modified we see if there are any 
            // server errors and clear them. This causes the serverError validation
            // rule to trigger and clear it's error state.
            if (model.hasOwnProperty("serverErrors")) {
                if (model.serverErrors.hasOwnProperty(fieldName)) {
                    delete model.serverErrors[fieldName];
                }
            }
        }
    }
}
</script>
```

There are a couple of omissions here. One is the helper function `removeProp` which is used to remove the `serverErrors` objects added to the data:

```javascript
function removeProp(obj, propName) {
  for (var p in obj) {
    if (obj.hasOwnProperty(p)) {
      if (p === propName) {
        delete obj[p];
      } else if (typeof obj[p] === "object") {
        removeProp(obj[p], propName);
      }
    }
  }
  return obj;
}
```

The other omission is the key to making this work:

```javascript
const serverError = function(fieldName) {
  return (value, vm) => {
    return !(
      vm.hasOwnProperty("serverErrors") &&
      vm.serverErrors.hasOwnProperty(fieldName)
    );
  };
};
```

This is a [custom validator](https://vuelidate.js.org/#sub-simplest-example) which is passed the field name which it's validating and sees if there's a server error item - if there is there's a validation error. The trick here is the merge of server errors into the form (`merge(this.form, serverMessages)`) produces a data object like this:

```json
    {
        name: 'bob',
        serverErrors: {
            name: 'The name if invalid according to the server'
        }
    }
```

When the input, bound to name, is modified the `serverErrors.name` property is removed by the `clearServerError` method and so the `serverError` validator sees the field as become valid.

Finally, the template needs to respond to the validators and display messages:

```html
<template>
    <form @submit.prevent="onSubmit">
        <input 
            type="text"
            v-model="$v.form.nameField.$model"
            @input="clearServerError($v.form.$model, 'nameField')"
        >
        <div class="errors">
            <p v-if="$v.form.nameField.required === false">
                Field is required
            </p>
            <p v-if="$v.form.nameField.serverError === false">
                {{$v.form.$model.serverErrors.nameField}}
            </p>
        </div>
    </form>
</template>
```

This template has standard Vuelidate syntax such as the v-model binding and the display of the required message. That slight updates for server-side include the `@input` event binding to clear the server-error as soon as the input changes and the display of the server errors from the data.

# Next Steps

There's a lot of code in the template that's quite unecessary so pushing that to generic components will make things simpler. There's also a step missing here which converts the response returned from the server into a compatible response for the data object and that's something I'll return to from an ASP.NET integration perspective.