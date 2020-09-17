---
marp: true
theme: renuo
---
<!-- _class: renuo -->

# Be nice to your clients
## Some tips and tricks to build APIs

##### 2020-09-17 by Alessandro Rodi

---

# Background

I recently worked on API definition and implementation for both Jumbo and Manor. I spent some time to make them good, and I want to share some hints.

---

# Good, readable, errors

Return to you clients good, readable errors. A good error response should fullfil the following:
* be able to display multiple errors
* easily understandable by a machine
* easily understandable by a human

---

# Good, readable, errrors

This is where we started:
```json
"error": {
    "status": 422,
    "detail": "model is invalid"
}
```

As a human I understand that something went wrong.

---

# Support multiple errors

```json
{
  "errors": [
    {
      "pointer": "first_name",
      "code": "blank"    
    },
    {
      "pointer": "salutation",
      "code": "include"    
    }
  ]
}
```

We should give people a more readable version.

---

# Provide good descriptions

```json
{
  "errors": [
    {
      "status": 422,
      "pointer": "first_name",
      "code": "blank",
      "detail": "First Name can't be blank"
    },
    {
      "status": 422,
      "pointer": "salutation",
      "code": "include",
      "detail": "Salutation is invalid. 'cat' is not a valid value. Please choose 'mr' or 'mrs'"
    }
  ]
}
```

Code can be found both in loyalty and cobra projects.

---

# Save the client some time

If your client is smart and uses `ETag` or `If-Modified-Since` headers, we can return them a `304 Not modified`.

```ruby
def show
  @token = Token.smart_find(params[:id])
  fresh_when @token
end
```

---

# Provide good, up-to-date, working swagger.

`rswag-ui` gem allows you to provide Swagger documentation and test environments for your APIs.

https://loyalty-develop.renuoapp.ch/api-docs/index.html


`rswag-api` is able to generate the swagger from the RSpec tests but we didn't use it.

---

<!-- _class: renuo -->

# ![drop-shadow portrait](../images/alessandro.jpg)

# Thank you

