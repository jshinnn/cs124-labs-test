# Week 9: Collaborative Filtering & Ethical Use of LLMs

<sub><sup>*written by veronica rivera & jeong shin, cs124 staff team, winter 2025*</sup></sub>

Agenda:
 - Collaborative Filtering
 - LLM Ethics

## Part 1: Collaborative Filtering

Let's work through an example of item-item collaborative filtering similar to what is used in PA7:

It will help to make a copy and follow along in [this spreadsheet](https://docs.google.com/spreadsheets/d/1RalPHyrnGHc3dGnDVzAZNZG4uNiZxrcMiO--Xo8awsw/edit#gid=0).

Let's say we have this matrix of movie reviews from various users:

|    | U1 | U2 | U3 | U4 |
|----|---:|---:|---:|---:|
| M1 |  1 |  5 |  3 |    |
| M2 |    |    |  5 |  4 |
| M3 |  2 |  4 |    |  1 |
| M4 |    |  2 |  4 |    |
| M5 |    |  4 |  3 |  4 |
| M6 |  1 |    |  3 |    |

We have a new user with the following preferences:

U = [M1: ??, M2: ??, M3: 5, M4: 2, M5: 3, M6: ??]

Out of the movies they have not seen (M1, M2, and M6) we want to recommend the film that they are likely to rate the highest.

### Step 1.  Binarize the Ratings

Start by binarizing the ratings!  We will use the following cutoffs (same as PA7):

0 $\lt$ rating $\leq$ 2.5: **-1**

Unrated: **0**

2.5 $\lt$ rating $\leq$ 5: **1**

We fill out this table for you in the interest of time!  Please ensure you understand how we got this matrix!

Binarized Matrix:
|    | U1 | U2 | U3 | U4 |
|----|---:|---:|---:|---:|
| M1 | -1 |  1 |  1 |  0 |
| M2 |  0 |  0 |  1 |  1 |
| M3 | -1 |  1 |  0 | -1 |
| M4 |  0 | -1 |  1 |  0 |
| M5 |  0 |  1 |  1 |  1 |
| M6 | -1 |  0 |  1 |  0 |


New User:
| movie | binarized rating |
|-------|------------------|
|    M1 |                0 |
|    M2 |                0 |
|    M3 |                1 |
|    M4 |               -1 |
|    M5 |                1 |
|    M6 |                0 |


### Step 2: Compute Similarity Scores

**Important Note:** In lecture and Quiz 8, you will use mean-centered overlapping cosine similarity. But here (and in PA7), we will use raw cosine similarity, NOT mean-centered overlapping item cosine similarity.

Recall the formula for the cosine similarity of two vectors:

$$\texttt{sim}(v_1,v_2) = \frac{v_1 \cdot v_2}{||v_1||||v_2||} = \frac{v_1 \cdot v_2}{\sqrt{\sum\nolimits_{i=1}^{n} v_{1,i}^{2}} \cdot \sqrt{\sum\nolimits_{i=1}^{n} v_{2,i}^{2}}}$$

Use the binarized vectors when computing the cosine similarity.  We provide a few of the calculations for you, fill in the similarities for $\texttt{sim}(M1, M3)$, $\texttt{sim}(M1, M5)$, and $\texttt{sim}(M5, M6)$.

Note this is a symmetric matrix, that is $\texttt{sim}(M1,M2) = \texttt{sim}(M2,M1)$.

Compute the cosine similarity over all the ***MOVIE*** (item) vectors.  These are the rows of the above matrix.

|    | M1 | M2 |            M3 |           M4 |           M5 |          M6 |
|----|---:|---:|--------------:|-------------:|-------------:|------------:|
| M1 |  1 | 0.41 |          **??** |            0 |         **??** |        0.82 |
| M2 |    |  1 | -0.41         | 0.5          | 0.82         |         0.5 |
| M3 |    |    |             1 |        -0.41 |            0 |        0.41 |
| M4 |    |    |               |            1 |            0 |         0.5 |
| M5 |    |    |               |              |            1 |         **??**  |
| M6 |    |    |               |              |              |          1  |

$\texttt{sim}(M1, M3)$ = ??

$\texttt{sim}(M1, M5)$ = ??

$\texttt{sim}(M5, M6)$ = ??

### Step 3: Compute New User's Ratings

**Important Note:** In lecture and Quiz 8, you will normalize the rating by dividing the sum of the similarity scores. But here (and in PA7), we will NOT do normalization.

Based on the New User's provided ratings for movies 3, 4, and 5, predict how they would rate movies 1, 2, and 6.

We will take the weighted average over the binarized ratings of all movies rated by the new user.  We will weigh on similarity.

For example the predicted rating for movie 2 is:

$\texttt{Rating}(M2) = \texttt{sim}(M2,M3) \cdot \texttt{binarized rating M3} + $
$\texttt{sim}(M2,M4) \cdot \texttt{binarized rating M4} + $
$\texttt{sim}(M2,M5) \cdot \texttt{binarized rating M5}$

$\texttt{Rating}(M2) = (-0.41)(1) + (0.5)(-1) + (0.82)(1) = -0.09$

Now you calculate for M1 and M6.

### Step 4: Recommend a Movie

Now that we have the expected ratings of the user for the movies they have not seen we need to actually recommend a movie.  Recommend the movie with the highest predicted rating!

## Part 2: The use of LLMs in the classroom

