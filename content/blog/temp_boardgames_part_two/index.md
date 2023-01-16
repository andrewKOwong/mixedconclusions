---
title: "Exploring Boardgames Part Two: Exploratory Data Analysis"
date: 2022-10-15
publishdate: 2022-10-15
tags: []
EnableMathJax: true
comments: true
draft: true
---
- Cover image word cloud


- Copy paragraph from notebook about ratings and dummy values
- [link to image]() plot of ratings sloping with dummy variables
- Paragraph about fitting the dummy variable parameters.
- Restricting games to >30 ratings and justify
    - Less noisy, since everyone can put a game up

- quick summary of interesting results of remaining dataset
    - size
    - missing values are 0's
    - broadly something about year of games, missing values are 0s
    - other parameters

- What is the golden age of board games?
- Set up definition of golden age of board games.
    - Average rating for board games + avg number of ratings by year.
- Is complexity related to quality?
    - maybe, correlation small
    - but need to plot
- Is there a surplus of medium complexity games or higher?
    - What are some games I like
- Fit a linear model to discover quality


## Discussion
- As of this writing, one of the recommend.games website is down.
- What data do I require?
    - API has user ratings, get those
    - build a recommender system using..
        - list some SVM or something


# TODO Boardgames
## Introduction
This is an analysis of boardgame data downloaded from boardgamegeek.com
on (DATE)

Described in part one (LINK).

Describe what I will look at.

## Overview of Data
The data consists of blah blah.
I'm gonna look at the 


Restricting the data set below.
Then cut off data by rated games etc.

## Rated vs Unrated Games

Games on BGG may be rated by users out of 10. Games that receive 30+ user ratings are assigned a ["geek rating"](https://boardgamegeek.com/wiki/page/BoardGameGeek_FAQ#toc13). This geek rating is used to rank board games against each other, and is necessary to prevent games with a few high rankings from appearing higher than games with a large number of rankings. For example, one may not want a game with five ratings of 10 to be considered a 'better' board game than a game with a ratings mean at 4.8 but 100K ratings. Games that don't meet the 30+ user rating threshold receive a geek rating of 0, and I'll refer to these games as "unrated" games.

The exact calculation method for the geek rating is not published by BGG, but it is apparently mostly a [Bayesian average](https://en.wikipedia.org/wiki/Bayesian_average).
Indeed, in the original XML data this field is called `bayesaverage`,
which I've renamed to `ratings_bayes_average`.
Its construction probably involves taking the user ratings 
and adding in something like [100](https://boardgamegeek.com/wiki/page/BoardGameGeek_FAQ) 
or [1500-1600](https://boardgamegeek.com/blogpost/109537/reverse-engineering-boardgamegeek-ranking) 
dummy ratings at a value of 5.5.

Of the entire dataset, about 85K games are unrated, 
vs 23K games with geeks ratings.

### Visualizing the Effect of Dummy Ratings

The following scatterplot visualizes the impact of the dummy ratings.
Bayes ratings are plotted along the x axis and the actual ratings along the y.
The number of ratings a game possesses is mapped to the color of the points.

When the number of ratings is low,
the Bayes rating is composed mostly of dummy ratings
and ends up at around 5.5.
As the number of ratings increase,
actual ratings swamp out the dummy ratings
resulting in the points drifting toward to the `y=x` diagonal.

![Scatterplot displaying the Bayes rating on the x axis and the mean user rating on the y axis, with each point representing a board game. The hue of each point represents the number of ratings a game has. For games with a small number of ratings (e.g. starting at 30), the points have a Bayes rating around 5.5 regardless of the user rating. As the number of ratings increase, a games Bayesian rating becomes more closely matched to its actual rating.](images/ratings_mean_v_bayes.png)

### Computing the Parameters of the Bayesian Average
If we assume the Bayesian average uses the model:

$$
r = 
    \frac
        { R m  + \sum_i^n x^i}
        {m + n}
$$

where Bayes average rating \\(r\\) is constructed from the sum of all user ratings
\\(x_i\\) and \\(m\\) dummy ratings of value \\(R\\), divided by the total of user ratings + dummy ratings,
then we can try to discover what values of \\(R\\) and \\(m\\) fits our data here.

A caveat here is that this doesn't take into account parameters
that BGG might be using, such as weighting certain user ratings based on 
information about users.

First let's set up functions for computing the Bayesian average and RMSD.
```python
def compute_bayesian_average(
    game_df: pd.DataFrame,
    dummy_rating: float,
    num_dummies: int,
    ratings_mean_key: str='ratings_mean',
    ratings_n_key: str='ratings_n'
    ) -> pd.Series:
    """Compute a Bayesian average using vectorized ops.
    
    Args:
        game_df: game dataframe with ratings info.
        dummy_rating: the mean rating for dummy values.
        num_dummies: number of dummy ratings to use.
        ratings_mean_key: col key for ratings mean.
        ratings_n_key: col key for number of ratings.
    
    Returns:
        pd.Series with computed Bayesian averages.
    """
    return (
        ((game_df[ratings_mean_key] * game_df[ratings_n_key]) 
            + (dummy_rating * num_dummies))
        /(game_df[ratings_n_key] + num_dummies)
        )

def compute_rmsd(
        y_trues: pd.Series|np.ndarray,
        y_preds: pd.Series|np.ndarray) -> float:
    """Compute the root mean squared deviation.
    
    Args:
        y_trues: 1D vector of .
        y_preds (np.ndarray or pd.Series): A 1D vector.
    Returns:
        RMSD as a scalar float.
    """
    # Check both are 1D vectors of same length.
    assert len(y_trues.shape) == 1
    assert y_trues.shape == y_preds.shape
    return np.sqrt(((y_trues - y_preds)**2).sum() / y_trues.shape[0])
```

Next, let's compute the RMSD assuming that the Bayesian averages are generated
using either 100 or 1500 dummy ratings at a value of 5.5.

```python
# actual rmsds
```

Now we'll use the `minimize` function from `scipy.optimize` to find
the values of $R$ and $m$ that minimizes the RMSD error of the
computed Bayesian averages.

We'll need to write a wrapper function that takes in $R$ and $m$ as an argument,
while outputting the RMSD error.
This function will used as one of the parameters
for `minimize`.

```python
def error_wrapper(
    dummy_args: tuple[float, int],
    game_df: pd.DataFrame,
    bayes_average_key: str='ratings_bayes_average'
    ) -> float:
    """Objective function to minimize Bayes dummy parameters.

    This function is meant to be used in by `scipy.optimize.minimize` as
    the objective function to be minimized. The `game_df` param will be
    supplied in the `args` parameter for `minimize`.
    
    Args:
        dummy_args: a tuple of (dummy_rating, num_dummies)
            i.e. args for compute_bayesian_average().
        game_df: game dataframe from which y_true bayes average ratings
            are taken.
        bayes_average_key: col key for bayes average ratings. 
    
    Returns:
        Float for RMSD error.
    """
    (dummy_rating, num_dummies) = dummy_args

    y_true = game_df[bayes_average_key] 
    y_pred = compute_bayesian_average(game_df, dummy_rating, num_dummies) 
    error = compute_rmsd(y_true, y_pred)
    return error 
```
Let's run the optimization procedure.

The cell below calls `minimize`, then prints info about the process and the result.

```python
#opt block
op_res = minimize(error_wrapper, (0, 0), args=(rated), options={'disp': True})
```
The closest we can get to reproducing the real Bayes rating values
seems to be when the number of dummy ratings is around 1972
with a rating value of 5.5.

In the following plot, the x axis plots the real Bayes rating values,
whereas the y axis plots the mean user rating,
Bayes rating values when using 100 dummy values,
1500 dummy values,
and the number of dummy we got from our optimization (~1972).
As we increase the number of dummy values, the generated Bayes rating
approached the real Bayes rating from BGG.


[![](images/optimizing_bayes_model.png)](images/optimizing_bayes_model.png)

This plot visualizes the remaining difference between real Bayes ratings
and the Bayes ratings we computed, with the y axis plotting the difference,
and the x axis plotting the number of ratings on a log scale.

The residual differences are less than 0.1 for almost all games,
although there are a small number of strong outliers.

[![](images/bayes_residuals.png)](images/bayes_residuals.png)

## What is the Golden Age of Board Games
![Ratings](images/test.png)



