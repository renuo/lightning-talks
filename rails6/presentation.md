---
marp: true
theme: renuo
---
<!-- _class: renuo -->

# What's new in Rails 6.x
## a Guide for Renuo developers

##### 2019-10-18 by Alessandro Rodi

---

# :thinking:

- Collect sources and interesting articles/podcasts
- See what is interesting as a Renuo developer

---

# Upgrade

Upgrading from Rails 5.2 to Rails 6.0 is easy.

---
# Upgrade: Zeitwerk

* Review how are concerns are written. Remove the `Concern` namespace.

`Concern::Codable` :arrow_right: `Codable`

---
# Upgrade: Webpacker

* Move Javascript resources from Sprockets to Webpack. Follow a guide.
* What about the other resources: you can move them, but you can also leave them, since Rails suggests to use webpack only for Javascript.

---

# New features

## :mailbox: Action Mailbox

---

# New features

## :pencil: Action Text

---

# New features

## :rocket: Parallel testing 

But RSpec does not support it yet so...forget about it for now :pensive:

---

# New features

## Multiple databases support

Very easy to work with multiple databases. Not really needed for us. 
We can deep dive the day we need it.

---

# New features

## :heart_eyes: ActionView::Components

https://www.youtube.com/watch?v=y5Z5a6QdA-M

IMHO one of the best improvements in Rails in the last years. Only for Rails >= 6.1

---

<!-- _class: renuo -->

# ![drop-shadow portrait](../images/alessandro.jpg)

* https://www.youtube.com/watch?v=y5Z5a6QdA-M
* https://github.com/rspec/rspec-rails/issues/2104
* https://www.driftingruby.com/episodes/what-s-new-in-rails-6

# Thanks!