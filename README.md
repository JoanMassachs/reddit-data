# Reddit politics data in 2012 and 2016

Reddit data set `reddit-politics-12-16` used in *Roots of Trumpism: Homophily and Social Feedback in Donald Trump Support on Reddit*.

If you use this data set in your research we would appreciate a citation to the paper:

```
@inproceedings{massachs2020,
  title={Roots of Trumpism: Homophily and Social Feedback in Donald Trump Support on Reddit},
  author={Massachs, Joan and Monti, Corrado and De Francisci Morales, Gianmarco and Bonchi, Francesco},
  booktitle={Proceedings of the 12th ACM conference on Web Science},
  year={2020}
}
```

## Considered users

To define our focus group, we first need to define the set of subreddits we wish to consider.
Since we are interested in political discussion, we choose `r/politics` as our seed.
We then pick the 50 most similar subreddits to `r/politics` according to [this method](https://www.shorttails.io/interactive-map-of-reddit-and-subreddit-similarity-cal culator).

The set of considered users are those that have written at least 10 comments in 2012 and 10 comments in 2016 in any of these subreddits.
This set contains 44924 users.

## Features overview

The type of the feature is in the first row and the subreddit of the feature is in the second row of the dataset. So, to load it in Python you can do:

```
import pandas as pd 

df = pd.read_csv('reddit-politics-12-16.csv.bz2',
                 header=[0, 1], index_col=0)
```

For each user, we report:

**Participation:**

- The feature `participation_16, r` is true when the user has participated in subreddit *r* in 2016, i.e., they have written a comment on *r*.
- The feature `participation_12, r` is true when the user has participated in subreddit *r* in 2012.

**Scores:**

- The feature `pos_scores_12, r` is the average of the positive scores of the comments by the user in subreddit *r* in 2012.
- The feature `neg_scores_12, r` is the average of the negative scores of the comments by the user in subreddit *r* in 2012.

**Interactions:**

- The feature `num_int_12` is the total number of direct interactions that the user has had in 2012.
- The feature `dist_int_12, r` is the fraction of direct interactions that the user has had with users participating to the subreddit *r* in 2012. 
- The feature `pos_int_12, r` is the fraction of *non-conflictual* direct interactions with users participating to the subreddit *r* among the direct interactions with users participating to *r* in 2012.

**Trump supporters:**

- The feature `y` is true when the user participated significantly to `r/The_Donald`, the Donald Trump subreddit, in 2016.


## Explanation of each feature

### Participation
Users may have similar behavior because they already have similar characteristics and interests.
We capture this notion by looking at the participation of an active user *u* to a subreddit *r* among the top 1000 most popular ones.
Users with similar interests are likely to belong to the same communities, which is a form of **homophily**.
We experimented with both numerical (number of comments) and binary versions of these features, and found the results to be similar.
Given that the latter version is simpler to interpret, henceforth we report results for the binary feature.

### Scores
We consider the scores received by an active user *u* on a political subreddit *r* in 2012 as a proxy for the **social feedback** given by *r* to *u*.
The positive and negative scores are considered separately, as forms of positive and negative reinforcement, respectively.
We use average scores to normalize the score across different levels of user activity.
The higher the average positive score of a user, the better received their attitude is in the given community.
Conversely, the average negative score shows how much a given community disapproves of the attitude of a given user.

### Interactions
We say that an active user *u* interacts with the political subreddit *r* when *u* answers a message, in any of the top 1000 most popular subreddit, made by another user *v* who has posted in the subreddit *r* in 2012.
This notion of **direct influence** captures the idea that *u* interacts with *v*, who is a user belonging to the community *r*, and therefore is possibly exposed to the attitudes of that community, irrespective of where the interaction takes place.

Furthermore, we consider an interaction *conflictual* when one of the two messages has a score of at least 10 and the other one has a score of at most -10.
This definition captures the notion that the two attitudes expressed in the messages differ, and that the interaction possibly represents a conflict.
For each active user and political subreddit, we compute how many times the user has interacted with the subreddit, and how many of these interactions are conflictual.

### Trump supporters
In our work, we wish to predict which users will support Trump in 2016, the year Trump was elected president of the United States, by looking only at data from 2012, the year of the previous presidential elections.
It is worth mentioning that in 2012 the subreddit `r/The_Donald` did not exist yet, so we have no notion of Trump supporters in 2012.
However, simply taking all users who commented in `r/The_Donald` is too loose and noisy as an operational definition.
As a first approximation, we define a user to be a Trump supporter if they have at least 4 comments on `r/The_Donald`, and the sum of their scores is at least 4.
This corresponds to 7427 users; however, we note that 1200 of those users have also posted on
the subreddit devoted to the other presidential candidate, Hillary Clinton (`r/hillaryclinton`).
Therefore, in order to take into account the general political activity of a user, we consider a user as Trump supporter in 2016 if they have at least 4 comments more in `r/The_Donald` than in `r/hillaryclinton`, and the sum of the scores (both positive and negative) on `r/The_Donald` is at least 4 points higher than the one in `r/hillaryclinton`.
This definition allows us to have a data set with limited class imbalance while maximizing the confidence in the label attribution.
With this method, we discard 344 users (4.6% of our first set) that, according to this definition, are not clearly supporting Trump in 2016.
Therefore, in our focus group of 44924 users, 7083 (15.8%) are labeled as Trump supporters and 37841 (84.2%) are labeled as non Trump supporters.
