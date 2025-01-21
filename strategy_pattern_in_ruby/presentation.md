---
marp: true
theme: renuo
---
<!-- _class: renuo -->

# Strategy Pattern in Ruby
## an example

##### 2024-11-01 by Alessandro Rodi

---

# Strategy Pattern

The Strategy Pattern is used to define a family of algorithms, encapsulate each one, and make them interchangeable.

---

# An example from Rails

```ruby
# in our tests
page = PagesDataSubmission.new
page.fill
```

---

```ruby
module Pages
  class DataSubmission
    include Capybara::DSL

    def migros? = ActsAsTenant.current_tenant.migros?
    def manor? = ActsAsTenant.current_tenant.manor?

    def fill
        if manor?
            manor_fill
        elsif migros?
            migro_fill
        end
    end

    def manor_fill
      choose "card_request_beneficial_owner_#{beneficial_owners.zero?}"
      LetterFiller.new.fill unless beneficial_owners.zero?
    end

    def migros_fill
      if beneficial_owners.zero?
        choose "card_request_beneficial_owner_type_main_cardholder"
      else
        choose "card_request_beneficial_owner_type_third_persons"
        fill_first_beneficial_owner if beneficial_owners >= 1
        fill_second_beneficial_owner if beneficial_owners >= 2
      end
    end
  end
end
```

---

```ruby
module Pages
  class DataSubmission
    module ManorBehavior
      def fill
        choose "card_request_beneficial_owner_#{beneficial_owners.zero?}"
        LetterFiller.new.fill unless beneficial_owners.zero?        
      end
    end
  end
end
```  

---

```ruby
module Pages
  class DataSubmission
    module MigrosBehavior
      def fill
        if beneficial_owners.zero?
            choose "card_request_beneficial_owner_type_main_cardholder"
        else
            choose "card_request_beneficial_owner_type_third_persons"
            fill_first_beneficial_owner if beneficial_owners >= 1
            fill_second_beneficial_owner if beneficial_owners >= 2
        end
      end
    end
  end
end
```

---

```ruby
module Pages
  class DataSubmission
    include Capybara::DSL

    def migros? = ActsAsTenant.current_tenant.migros?
    def manor? = ActsAsTenant.current_tenant.manor?

    def initialize
      extend(migros? ? MigrosBehavior : ManorBehavior)
    end
  end
end
```

---

```ruby
# in our tests
page = PagesDataSubmission.new
page.fill
```

---
# Advantages

* Splitting manor/migros code clearly
* Completely transparent to the caller
* Modify the filling behaviors in the future without changing the main class structure
* respects open/close principle: behaviors are open for modifications, but have a well defined interface (fill method) which allows to use them without knowledge of their internals

---

<!-- _class: renuo -->

# ![drop-shadow portrait](../images/alessandro.jpg)


# Thanks!