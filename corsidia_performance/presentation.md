---
marp: true
theme: renuo
---
<!-- _class: renuo -->

# How to ~~not~~ search
## Performance optimization on corsidia.com 

##### 2020-10-07 by Alessandro Rodi

---

# What is corsidia?

Corsidia is an Italian website where you can search for a course in a city.
Any kind of course, in any city.

# ![w:600](./images/homepage.png)

---

# What data are we talking about?

Some data:

- The searches are about 1'000'000 (total different searches performed)
- The courses are about 20'000
- The matters are about 4'000
- Custom searches keeps growing in number (linear)
- About 648’984 searches are optimised for search engines and are fast (< 400ms)
- All the others are coming from the users and are loading between 600ms and 5 seconds
- Each course has N matters, sorted by importance

---

# Requirements

Search results should place on the top the most relevant courses. Keeping in consideration:
- Matter position in the assignment (the first matter is more important)
- Synonyms of the matter (if I search for excel, I want to get results also for msexcel)
- Ranking of the school
- Quality of the course
- Distance from the place where you search a course

---

# Requirements

These are WTF requirements.

---

# Ideas :bulb:

---

# SOLR / elasticserarch / Algolia

Do we really want to deal with a separate app for the indexing? 

This is very expensive in terms of maintainance and/or pricing.

* Keep another database just for indexing
* Maintaince of a new infrastructure / pay expensive pricing
* Keep data aligned
* Write the queries / searches in a specific language / format

---

# New Idea! :bulb:

---

# Ranking table

A background ruby process can calculate the position of each course for each search.

When a search is performed and already exists, we fetch the courses from the ranking table directly and is mega-fast.

When a search is new, we fallback to the previous method. Slow, but only until it will be indexed overnight.

---

# Ranking table

### searches (1mln) * courses (10k) = 
### 10’000’000’000 records (to start with...)

# :confused:

---

# New Idea! :bulb:

---
# DB view and postgres full text search

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

# DB view and postgres full text search

```sql
SELECT * 
FROM searchable_courses
WHERE 'english' = ANY(matters1) OR
'english' = ANY(matters2) OR
'english' = ANY(matters3)
ORDER BY 'where_you_found_the_word' + 
          school_ranking + 
          whatever
```

---

# Decision

Let's go with the postgres full text search solution.

Why?

Because we can implement it in about 30 minutes and check if the results are satisfiying.

---

# Lesson learned

When evaluating solutions, it's important to also evaluate 

> how long does it take me to test it?

---

# The results

### The query takes, in average 600ms

---

# The results

Satisfying but not really

# ☹️

---

# What do we do now? 
# :man_shrugging:

---

# Let's step back

We sat again and started from scratch. Here is the most important question that came out and lead to the final solution

---

# Let's step back

> "Where do you make 💰?"

> _"90% of earnings come from about 1'000 searches"_

> "so?"

---

# Let's step back

> "so why did we calculate 1bln records for the ranking table solution?"

> _"Each results page is paginated. Can't we just rank the first 40 courses of the most used 1'000 searches?"_

> "This will bring us down to ~ 40'000 records."

---

# Let's step back

> "You might also want to manipulate these search results, right? If tomorrow you want to place a course before another one, no matter what (pun intended), how would you do it with the postgres full text search solution?"

> _"With the ranking table, you have more degrees of freedom: boost a school, boost a course, or boost a course for a specific search!"_

---

# Let's step back

> "What about the distance requirement. Do you really need to give a boost for the distance?"

> _"Well...no...not really...but it would be nice!"_

---

# Let's step back

> "And there's another cool advantage...your entire sorting logic is in ruby and not SQL!"

---

# The implementation

This solution has been implemented by Duccio in about three to four working days and has been put in production in February. 

Pages are much faster and the results in each page are much better than before. :muscle:

---

# First implementation

```ruby
class Ranker
  self.call(search)    
    courses = find_courses(search)
    courses.each do |course|
     calculated_rank = 0
     calculated_rank += course.school_ranking # add the school ranking
     calculated_rank += course.whatever # add more points
     calculated_rank += (10 - course.matters.find_index(search.matter)) * 100 # add point for matters, depending on the order
     search.ranked_coursed.create(course, rank: calculated_rank)
    end
  end
end
```

---

# Take Home Message(s)

> why the hell did you tell us about this story?

---

## Look always at the business side

---

## Understand where your customer earns the money

## Apply the Pareto principle: 80% of earnings might come from 20% of the features.

---

## Double-check your premises and requirements. Challenge them. Multiple times.

## Once you find a solution, verify if the initial requirements are fulfilled.

---

## Again: can you give 80% of the value, with the 20% of the work? 

---

## Don't go straight to the "usual suspects" (Solr, Elastic Search & co.)

---

## Trying the SQL was good. 

Why? Because it was very cheap to try! And it forced us to rethink and look again at the requirements. So...

---

## Never be afraid of trying and failing
if is cheap :smile:. 

---

<!-- _class: renuo -->

# ![drop-shadow portrait](../images/alessandro.jpg)

# Thank you

## References

* https://corsidia.com/
* https://www.amazon.com/How-Fail-Almost-Everything-Still-ebook/dp/B00COOFBA4
* https://serp.co/seo/google-dance/

---

## The current implementation

This approach allowed a step-by-step fine tuning of the ranker. After 6 months, the algorithm is more elaborated, and everything is kept in a ruby class :heart:

```
def calculate_points_for(course, search)
  random_points +
  matter_points +
  filter_points +
  kpi_points +
  distance_points
end
```
---

* the jobs runs on 1/5 of all searches every night.
* every search is refreshed every 5 days
* the job takes about 2 hours to run

---

Is made of:

* random: assign a small random number of points, this is known as "Google Dance"
* matter: how specific is the searched matter for this course
* filter: how many of the filters does the course match?
* kpi: 5 values assigned to the course/school
  * did they fill their profile/course information?
  * did they give further details for the course?
  * how much is the school paying?
  * how are the feedback from the customers?
  * how many reviewsdid the school receive?
* distance: how far is the course from the search location?  

---

It's possible to rank all courses for a search...
but also rank a course in all the searches (so they don't have to wait 5 days)