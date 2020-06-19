---
marp: true
theme: renuo
---
<!-- _class: renuo -->

# Clone Heroku Databases
## You can test on production data

##### 2020-06-19 by Alessandro Rodi

---

# We need Production data?

Sometimes you want to test on production data.

* Specific data
* Performance issues
* Expected results

---

# Local testing

You can import the production data locally and run your tests locally, but the local environment is never the same as production.

---

# Local testing

```bash
heroku pg:backups:capture --app gifcoins-master
heroku pg:backups:download --app gifcoins-master
pg_restore --verbose --clean --no-acl --no-owner -h localhost -d gifcoins_development latest.dump
```

---

# Testing testing

If you want to try it in production, you can also clone production data on the testing or develop environment.

:warning: if the production data is too large, you will have to rollback after you tested, or upgrade the testing database.

---

# Testing testing

```
heroku pg:backups:capture --app gifcoins-testing
heroku pg:copy gifcoins-master::HEROKU_POSTGRESQL_[NAME]_URL DATABASE --app gifcoins-testing
heroku run rails db:migrate --app gifcoins-testing

# DO YOUR TESTS...

heroku pg:backups:restore [BACKUP_NUMBER FROM FIRST COMMAND] DATABASE_URL --app gifcoins-testing
```

---

<!-- _class: renuo -->

# ![drop-shadow portrait](../images/alessandro.jpg)

# Thank you

