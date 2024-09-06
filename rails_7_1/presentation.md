---
marp: true
theme: renuo
---
<!-- _class: renuo -->

# Some fancy Rails 7.1 features

##### 2024-09-06 by Simon I.

--- 

# Part 1: `ActiveRecord::Base::Normalization`

> Normalization rules for model attributes

---

# Before

```ruby
class User < ApplicationRecord
  before_save :normalize_name, if name.present?

  private

  def normalize_name
    self.name = name.strip.titlecase
  end
end
```

---

# After

```ruby
class User < ActiveRecord::Base
  normalizes :name, with: -> name { name.strip.titlecase }
end
```

---

# ✨ But when does this get called now? ✨ 

---

- `=` (except for nil values)
- `User.normalize_value_for(:name, " JOHN DOE\n")`
- **finders**

---

```ruby
User.create(name: "\tJOHN DOE ")

user = User.find_by(name: "\tJOHN DOE ")
user.name # => "John Doe"
user.name_before_type_cast # => "John Doe"
```

---

# but not with SQL fragments 👀

```ruby
User.where(name: "\t JOHN DOE ").count # => 1
User.where(["name = ?", "\tJOHN DOE "]).count # => 0
```

---

# Part 2: Strict locals in templates
> Define locals a template can accept

---

# ✨ comment

```ruby
#  app/views/dashboard/_contact_us.html.erb
<%# locals: (address:, mobile:) -%>

<%= address %>
<%= mobile %>
```

---

# Missing local

```ruby
# app/views/homepage/index.html.erb

<%= render partial: 'contact_us', locals: { address: 'Mumbai', mobile: '987654321', company: 'Test Company' } %>
```

results in
```ruby
ActionView::Template::Error (unknown local: :company):
  
app/views/homepage/index.html.erb:2
```

---

# Default values

```ruby
# app/views/dashboard/_contact_us.html.erb

<%# locals: (address: 'Delhi', mobile: 1234567890) -%>

<%= address %>
<%= mobile %>
```

---

# How does this ✨ work?

- Arguments are compiled as the method signature of the template
- Might unlock the ability to pre-compile templates (open discussion)

```ruby
# actionview/lib/action_view/template.rb
EXPLICIT_LOCALS_REGEX = /\#\s+locals:\s+\((.*)\)/

def explicit_locals!
  self.source.sub!(EXPLICIT_LOCALS_REGEX, "")
  @explicit_locals = $1

  return if @explicit_locals.nil? # Magic comment not found

  @explicit_locals = "**nil" if @explicit_locals.blank?
end

```

---

# No multiline support yet :/ 

```ruby
EXPLICIT_LOCALS_REGEX = /\#\s+locals:\s+\((.*)\)/
```

---

<!-- _class: renuo -->

# ![drop-shadow portrait](../images/simon-i.jpg)

# Thanks

https://github.com/renuo/lightning-talks

### Sources
- https://github.com/rails/rails/pull/45602
- https://api.rubyonrails.org/classes/ActiveRecord/Normalization/ClassMethods.html