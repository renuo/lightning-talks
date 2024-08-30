---
marp: true
theme: renuo
---
<!-- _class: renuo -->

# Some fancy Rails 7.1 features

##### 2019-07-30 by Simon I.

--- 

# Part 1: `ActiveRecord::Base::Normalization`

> Unique normalization rules for model attributes

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
- finders

---

```ruby
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

<!-- _class: renuo -->

# ![drop-shadow portrait](../images/simon-i.jpg)

# Thanks

### https://github.com/renuo/lighning-talks