---
marp: true
theme: renuo
---
<!-- _class: renuo -->

# Optimise a search
## How we reached a solution for the search in corsidia.com 

##### 2020-02-21 by Alessandro Rodi

---

# What is the issue?

Corsidia is a website where you can search for a course in a city.
Any kind of course, in any city.
Some data:

- The searches are about 1’000’000
- The courses are about 10’000
- The matters are about 4’000
- Custom searches will grow in number
- About 648’984 searches are optimised for search engines and are fast (400ms)
- All the others are coming from the users and are 600ms < slow < 5 seconds
- Each course has N matters, sorted by importance

---

# Requirements

Search results should place on the top the most relevant courses. Keeping in consideration:
- Matter position in the assignment
- Synonyms of the matter
- Ranking of the school
- Quality of the course
- Distance from the place where you search a course


---

# Ideas - SOLR / lucene / Algolia

Discarded immediately. A lot of overhead and additional infrustructure. 
Algolia is the only proper service but is f***ing expensive.

---

# Ideas - ranking table

A background process can calculate the position of each course for each search.

When a search is performed and already exists, we fetch the courses from the ranking table directly and is mega-fast.

When a search is new, we fallback to the previous method. Slow, but only until it will be indexed overnight.

---

# Ideas - ranking table

### searches (1mln) * courses (10k) = 
### 10’000’000’000 records (to start with...)

---

# Ideas - DB view and postgres full text search

```ruby
create_view :searchable_course do |t|
  t.integer :course_id
  t.string :matters1, array: true
  t.string :matters2, array: true
  t.string :matters3, array: true
  t.integer :school_ranking
  t.integer :whatever
end
```

---

# Ideas - DB view and postgres full text search

```sql
SELECT * 
FROM searchable_courses
WHERE 'english' = ANY(matters1) OR
'english' = ANY(matters2) OR
'english' = ANY(matters3)
ORDER BY 'where_you_found_the_word_super_complicated' + 
          school_ranking + 
          whatever
```

---

# Ideas - Decision

Let's go with the query because we can do it in about 30 minutes and check if the results are satisfiying.

---

# Ideas - The results

### The query takes, in average 600ms

---

# Ideas - The results

# ☹️

---

# Let's step back

> "Where do you make 💰?"

> _"90% of earnings come from 1'000 searches"_

---

# Let's step back

> "Why did we calculate 1bln records?"

> _"Each results page is paginated. Can't we just rank the first 40 courses of the most used 1'000 searches?"_

> "This will bring us down to 40'000 records."

---

# Let's step back

> "You might also want to manipulate these search results. If tomorrow you want to place a course before another one, no matter what, how would you do it with the query?"

> _"With the ranking table, you have more degrees of freedom: boost a school, boost a course, or boost a course for a specific search"_
---

# Let's step back

> "And there's another cool advantage. Your logic is in ruby and not SQL"

---

# The implementation

This solution has been implemented by Duccio in about three to four working days and is already in production. No data can be extracted at the moment, but the pages are much faster and the results in each page are much better than before.

---

# Looks something like

```ruby
class MegaRanker
  self.call(search)    
    courses = find_courses(search)
    courses.each do |course|
     calculated_rank = 0
     calculated_rank += course.school_ranking
     calculated_rank += course.whatever
     calculated_rank += (10 - course.matters.find_index(search.matter)) * 100
     search.ranked_coursed.create(course, rank: calculated_rank)
    end
  end
end
```

---

# Take Home Message
or: why the hell did you tell us about this story?

- Look always at the business side
- Understand where your customer earns the money
- Apply the Pareto principle: 80% of earnings might come from 20% of the features.
- Double-check your premises and requirements
- Once you find a solution, verify if the initial requirements are fulfilled.
- Again: can you give 80% of the value, with the 20% of the work? 
- Trying the SQL was good. Why? Because it was very cheap to try! And it forced us to rethink and look again at the requirements. So...
- Never be afraid of trying and failing if is cheap :smile:. At least you can sleep at night.

---

<!-- _class: renuo -->

# ![drop-shadow portrait](../images/alessandro.jpg)

# Thank you

### https://corsidia.com/
