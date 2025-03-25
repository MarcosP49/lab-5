# Lab 5 - Teacher Evaluations
Marcos Palominos

In this lab, we will be using the `dplyr` package to explore student
evaluations of teaching data.

**You are expected to use functions from `dplyr` to do your data
manipulation!**

# Part 1: GitHub Workflow

Now that you have the Lab 5 repository cloned, you need to make sure you
can successfully push to GitHub. To do this you need to:

-   Open the `lab-5-student.qmd` file (in the lower right hand corner).
-   Change the `author` line at the top of the document (in the YAML) to
    your name.
-   Save your file either by clicking on the blue floppy disk or with a
    shortcut (command / control + s).
-   Click the “Git” tab in upper right pane
-   Check the “Staged” box for the `lab-5-student.qmd` file (the file
    you changed)
-   Click “Commit”
-   In the box that opens, type a message in “Commit message”, such as
    “Added my name”.
-   Click “Commit”.
-   Click the green “Push” button to send your local changes to GitHub.

RStudio will display something like:

    >>> /usr/bin/git push origin HEAD:refs/heads/main
    To https://github.com/atheobold/introduction-to-quarto-allison-theobold.git
       3a2171f..6d58539  HEAD -> main

Now you are ready to go! Remember, as you are going through the lab I
would strongly recommend rendering your HTML and committing your after
**every** question!

# Part 2: Some Words of Advice

Part of learning to program is learning from a variety of resources.
Thus, I expect you will use resources that you find on the internet.
There is, however, an important balance between copying someone else’s
code and *using their code to learn*.

Therefore, if you use external resources, I want to know about it.

-   If you used Google, you are expected to “inform” me of any resources
    you used by **pasting the link to the resource in a code comment
    next to where you used that resource**.

-   If you used ChatGPT, you are expected to “inform” me of the
    assistance you received by (1) indicating somewhere in the problem
    that you used ChatGPT (e.g., below the question prompt or as a code
    comment), and (2) downloading and including the `.txt` file
    containing your **entire** conversation with ChatGPT.

Additionally, you are permitted and encouraged to work with your peers
as you complete lab assignments, but **you are expected to do your own
work**. Copying from each other is cheating, and letting people copy
from you is also cheating. Please don’t do either of those things.

## Setting Up Your Code Chunks

-   The first chunk of this Quarto document should be used to *declare
    your libraries* (probably only `tidyverse` for now).
-   The second chunk of your Quarto document should be to *load in your
    data*.

## Save Regularly, Render Often

-   Be sure to **save** your work regularly.
-   Be sure to **render** your file every so often, to check for errors
    and make sure it looks nice.
    -   Make sure your Quarto document does not contain `View(dataset)`
        or `install.packages("package")`, both of these will prevent
        rendering.
    -   Check your Quarto document for occasions when you looked at the
        data by typing the name of the data frame. Leaving these in
        means the whole dataset will print out and this looks
        unprofessional. **Remove these!**
    -   If all else fails, you can set your execution options to
        `error: true`, which will allow the file to render even if
        errors are present.

# Part 3: Let’s Start Working with the Data!

## The Data

The `teacher_evals` dataset contains student evaluations of reaching
(SET) collected from students at a University in Poland. There are SET
surveys from students in all fields and all levels of study offered by
the university.

The SET questionnaire that every student at this university completes is
as follows:

> Evaluation survey of the teaching staff of University of Poland.
> Please complete the following evaluation form, which aims to assess
> the lecturer’s performance. Only one answer should be indicated for
> each question. The answers are coded in the following way: 5 - I
> strongly agree; 4 - I agree; 3 - Neutral; 2 - I don’t agree; 1 - I
> strongly don’t agree.
>
> Question 1: I learned a lot during the course.
>
> Question 2: I think that the knowledge acquired during the course is
> very useful.
>
> Question 3: The professor used activities to make the class more
> engaging.
>
> Question 4: If it was possible, I would enroll for a course conducted
> by this lecturer again.
>
> Question 5: The classes started on time.
>
> Question 6: The lecturer always used time efficiently.
>
> Question 7: The lecturer delivered the class content in an
> understandable and efficient way.
>
> Question 8: The lecturer was available when we had doubts.
>
> Question 9. The lecturer treated all students equally regardless of
> their race, background and ethnicity.

These data are from the end of the winter semester of the 2020-2021
academic year. In the period of data collection, all university classes
were entirely online amid the COVID-19 pandemic. While expected learning
outcomes were not changed, the online mode of study could have affected
grading policies and could have implications for data.

**Average SET scores** were combined with many other variables,
including:

1.  **characteristics of the teacher** (degree, seniority, gender, SET
    scores in the past 6 semesters).
2.  **characteristics of the course** (time of day, day of the week,
    course type, course breadth, class duration, class size).
3.  **percentage of students providing SET feedback.**
4.  **course grades** (mean, standard deviation, percentage failed for
    the current course and previous 6 semesters).

This rich dataset allows us to **investigate many of the biases in
student evaluations of teaching** that have been reported in the
literature and to formulate new hypotheses.

Before tackling the problems below, study the description of each
variable included in the `teacher_evals_codebook.pdf`.

**1. Load the appropriate R packages for your analysis.**

``` r
# code chunk for loading packages
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ✔ purrr     1.0.2     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(dplyr)
```

**2. Load in the `teacher_evals` data.**

``` r
# code chunk for importing the data
teacher_evals <- read_csv("data-raw/teacher_evals.csv")
```

    Rows: 8015 Columns: 22
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr  (5): course_id, weekday, time_of_day, academic_degree, gender
    dbl (17): teacher_id, question_no, no_participants, resp_share, SET_score_av...

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

### Data Inspection + Summary

**3. Provide a brief overview (~4 sentences) of the dataset.**

``` r
# you may want to use code to answer this question
head(teacher_evals)
```

    # A tibble: 6 × 22
      course_id  teacher_id question_no no_participants resp_share SET_score_avg
      <chr>           <dbl>       <dbl>           <dbl>      <dbl>         <dbl>
    1 0000-BHP        54655         901             255     0.212           4.22
    2 0000-BHP        54655         903             255     0.208           3.68
    3 0000-BHP        54655         904             255     0.212           3.74
    4 0000-BHP        54655         905             255     0.208           3.30
    5 0000-BHP        54655         907             255     0.208           3.68
    6 0000-BW-NP       3432         902             998     0.0411          4.05
    # ℹ 16 more variables: stud_grade_avg <dbl>, stud_grade_std <dbl>,
    #   stud_grade_var_coef <dbl>, percent_failed <dbl>, stud_grade_avg_cur <dbl>,
    #   stud_grade_std_cur <dbl>, stud_grade_var_coef_cur <dbl>,
    #   percent_failed_cur <dbl>, class_duration <dbl>, weekday <chr>,
    #   time_of_day <chr>, SET_score_1sem <dbl>, maximum_score <dbl>,
    #   academic_degree <chr>, seniority <dbl>, gender <chr>

``` r
dim(teacher_evals)
```

    [1] 8015   22

The data is 8015 observations of 22 different variables. It includes a
course id and teacher id, then goes through averages of many of the
different questions asked in the survey. It also includes more
information about each class, such as day of the week it was held and
the percent of students which failed.

**4. What is the unit of observation (i.e. a single row in the dataset)
identified by?**

Each row in the dataset refers to a question that was answered by a
student.

``` r
# you may want to use code to answer this question
```

**5. Use *one* `dplyr` pipeline to clean the data by:**

-   **renaming the `gender` variable `sex`**
-   **removing all courses with fewer than 10 respondents**
-   **changing data types in whichever way you see fit (e.g., is the
    instructor ID really a numeric data type?)**
-   **only keeping the columns we will use – `course_id`, `teacher_id`,
    `question_no`, `no_participants`, `resp_share`, `SET_score_avg`,
    `percent_failed_cur`, `academic_degree`, `seniority`, and `sex`**

**Assign your cleaned data to a new variable named `teacher_evals_clean`
–- use these data going forward. Save the data as
`teacher_evals_clean.csv` in the `data-clean` folder.**

``` r
# code chunk for Q4
teacher_evals_clean <- teacher_evals |> 
  rename(sex = gender) |>
  filter(no_participants > 9) |>
  select(course_id, teacher_id, question_no, no_participants, resp_share, SET_score_avg, percent_failed_cur, academic_degree, seniority, sex) |>
  mutate(course_id = as.character(course_id)) |>
  mutate(teacher_id = as.character(teacher_id)) |>
  mutate(question_no = as.character(question_no)) |>
  mutate(no_participants = as.numeric(no_participants)) |>
  mutate(resp_share = as.numeric(resp_share)) |>
  mutate(SET_score_avg = as.numeric(SET_score_avg)) |>
  mutate(percent_failed_cur = as.numeric(percent_failed_cur)) |>
  mutate(academic_degree = as.character(academic_degree)) |>
  mutate(seniority = as.character(seniority)) |>
  mutate(sex = as.character(sex))
write_csv(teacher_evals_clean, "data-clean/teacher_evals_clean.csv")
```

**6. How many unique instructors and unique courses are present in the
cleaned dataset?**

``` r
# code chunk for Q5
n_distinct(teacher_evals_clean$course_id)
```

    [1] 939

``` r
n_distinct(teacher_evals_clean$teacher_id)
```

    [1] 297

939 unique courses and 297 unique teachers. **7. One teacher-course
combination has some missing values, coded as `NA`. Which instructor has
these missing values? Which course? What variable are the missing values
in?**

``` r
# code chunk for Q6
filter(teacher_evals_clean, if_any(everything(), is.na))
```

    # A tibble: 7 × 10
      course_id   teacher_id question_no no_participants resp_share SET_score_avg
      <chr>       <chr>      <chr>                 <dbl>      <dbl>         <dbl>
    1 PAB3SE004PA 56347      901                      32     0.0312             5
    2 PAB3SE004PA 56347      902                      32     0.0312             5
    3 PAB3SE004PA 56347      903                      32     0.0312             4
    4 PAB3SE004PA 56347      905                      32     0.0312             4
    5 PAB3SE004PA 56347      906                      32     0.0312             3
    6 PAB3SE004PA 56347      907                      32     0.0312             3
    7 PAB3SE004PA 56347      908                      32     0.0312             5
    # ℹ 4 more variables: percent_failed_cur <dbl>, academic_degree <chr>,
    #   seniority <chr>, sex <chr>

Course ID PAB3SE004PA with teacher ID 56347 has NA values for the
percent of students which failed. **8. What are the demographics of the
instructors in this study? Investigate the variables `academic_degree`,
`seniority`, and `sex` and summarize your findings in ~3 complete
sentences.**

``` r
# code chunk for Q7
table(teacher_evals_clean$academic_degree)
```


        dr     ma no_dgr   prof 
      4367   1852    467    131 

``` r
table(teacher_evals_clean$seniority)
```


       1   10   11    2    3    4    5    6    7    8    9 
     410  408  640 1722  367  585  280  867  408  778  352 

``` r
table(teacher_evals_clean$sex)
```


    female   male 
      3278   3539 

Most of the teachers had a doctorate at 4367, while 1852 had a master’s
degree and 467 had no degree. The most common amount of seniority for an
instructor to have is 2 years, but there is a range from 1 to 11 years.
The instructors are slightly predominantly male, with 3539 male
instructors and 3278 female instructors.

**9. Each course seems to have used a different subset of the nine
evaluation questions. How many teacher-course combinations asked all
nine questions?**

``` r
# code chunk for Q8
teacher_courses <- teacher_evals_clean |> 
  group_by(teacher_id, course_id) |>
  summarise(questions_asked = n_distinct(question_no)) |>
  filter(questions_asked == 9)
```

    `summarise()` has grouped output by 'teacher_id'. You can override using the
    `.groups` argument.

``` r
dim(teacher_courses)
```

    [1] 49  3

49 teacher-course combinations asked all nine of the questions. \## Rate
my Professor

**10. Which instructors had the highest and lowest average rating for
Question 1 (I learnt a lot during the course.) across all their
courses?**

``` r
# code chunk for Q9
teacher_avg_SET <- teacher_evals_clean |>
  group_by(teacher_id) |>
  summarise(avg_SET_score = mean(SET_score_avg)) |>
  arrange(avg_SET_score)
min_avg_row <- teacher_avg_SET |> filter(avg_SET_score == min(avg_SET_score))
max_avg_row <- teacher_avg_SET |> filter(avg_SET_score == max(avg_SET_score))
min_avg_row
```

    # A tibble: 1 × 2
      teacher_id avg_SET_score
      <chr>              <dbl>
    1 54201                  1

``` r
max_avg_row
```

    # A tibble: 29 × 2
       teacher_id avg_SET_score
       <chr>              <dbl>
     1 36250                  5
     2 3682                   5
     3 37013                  5
     4 37023                  5
     5 50714                  5
     6 50740                  5
     7 50759                  5
     8 50790                  5
     9 54309                  5
    10 54665                  5
    # ℹ 19 more rows

Teacher 54201 had the lowest SET score between all their classes at 1,
and 29 teachers had an average SET score of 5 across all their classes.
**11. Which instructors with one year of experience had the highest and
lowest average percentage of students failing in the current semester
across all their courses?**

``` r
# code chunk for Q10
teacher_one_year <- teacher_evals_clean |>
  filter(seniority == 1) |>
  group_by(teacher_id) |>
  summarise(per_failed = mean(percent_failed_cur)) |>
  arrange(per_failed)
min_avg_row <- teacher_one_year |> filter(per_failed == min(per_failed))
max_avg_row <- teacher_one_year |> filter(per_failed == max(per_failed))
min_avg_row
```

    # A tibble: 6 × 2
      teacher_id per_failed
      <chr>           <dbl>
    1 102379              0
    2 103092              0
    3 106126              0
    4 86222               0
    5 98650               0
    6 98651               0

``` r
max_avg_row
```

    # A tibble: 1 × 2
      teacher_id per_failed
      <chr>           <dbl>
    1 106692           0.68

6 teachers with 1 year of experience had 0 students fail, while teacher
id 106692 had 68% of students fail. **12. Which female instructors with
either a doctorate or professional degree had the highest and lowest
average percent of students responding to the evaluation across all
their courses?**

``` r
# code chunk for Q11
teacher_fdegree <- teacher_evals_clean |>
  filter(sex == "female") |>
  filter((academic_degree == "dr") | (academic_degree == "prof")) |>
  group_by(teacher_id) |>
  summarise(per_resp = mean(resp_share)) |>
  arrange(per_resp)
min_avg_row <- teacher_fdegree |> filter(per_resp == min(per_resp))
max_avg_row <- teacher_fdegree |> filter(per_resp == max(per_resp))
min_avg_row
```

    # A tibble: 1 × 2
      teacher_id per_resp
      <chr>         <dbl>
    1 59338        0.0148

``` r
max_avg_row
```

    # A tibble: 1 × 2
      teacher_id per_resp
      <chr>         <dbl>
    1 101508        0.522

Teacher ID 59338 had 1.48% of students respond, while teacher ID 101508
had 52.17% of students respond.
