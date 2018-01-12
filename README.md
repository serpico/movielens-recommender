# utilisation
Ouvrir une console en tant que *user simple* ( xterm, konsole,
gnome-terminal… ) dans une session graphique X ( Xorg ).
```
git clone https://github.com/nchah/movielens-recommender.git
```
exporter votre liste personnelle de ratings depuis https://movielens.org/profile/settings/import-export puis renommer la liste exportée en **v1-input-ratings.csv** et la placer ce fichier dans :

    ~/data/input 
    
(remplacer le fichier d'exemple existant du même nom si nécessaire)

télécharger le dernier dataset movielens disponible : http://grouplens.org/datasets/movielens/latest/
extraire le fichier .zip, le renommer **ml-ratings-lean.csv** et le placer dans :

    ~/data 
    
(remplacer le fichier d'exemple existant du même nom si nécessaire)

```
cd movielens-recommender/python
python mlr-engine.py 
```
# durée approximative de traitement (Thinkpad x220 i7 8Go RAM):
    s1 : ~ 3O minutes
    s2 : instantané
    s3 : instantané
    s4 : ~ 30 minutes
    s5 :
    s6 :
    s7 :

# movielens-recommender
This implementation was part of a final project for a graduate course in Data Analytics at the University of Toronto (Winter term, 2016). 
Our group set out to create a movie recommendation engine that would recommend movies that would have a high chance of being enjoyed by the user.

## Background

This was a final project for a graduate course offered in the Winter Term (January-April, 2016) at the University of Toronto, Faculty of Information: INF2190 Data Analytics: Introduction, Methods, and Practical Approaches. 
Our group's full tech stack for this project was expressed in the acronym *MIPAW*: MySQL, IBM SPSS Modeler, Python, AWS, and Weka.

Running the model on the millions of MovieLens ratings data produced movie recommendations where >85% of the films would have been received with favorable ratings.

## Directory Tree

The file structure for this repository, with file sizes in SI units. 

```
$ tree --si

├── [6.9k]  README.md
├── [ 272]  data
│   ├── [ 102]  input
│   │   └── [ 307]  v1-input-ratings.csv
│   ├── [1.4M]  ml-movies.csv
│   ├── [1.2M]  ml-ratings-100k-sample.csv
│   ├── [300M]  ml-ratings-lean.csv  # <-- original file too large for GitHub
│   └── [ 408]  output
│       ├── [600k]  a1-ratings-extract.csv
│       ├── [  48]  a2-ratings-extract-counts-by-user-distrib.csv
│       ├── [243k]  a2-ratings-extract-counts-by-user.csv
│       ├── [  93]  a3-userids-extract-agreeing-users.csv
│       ├── [570k]  a4-ratings-extract-recommended.csv
│       ├── [ 69k]  a5-ratings-extract-recommended-sorted-counts.csv
│       ├── [608k]  a5-ratings-extract-recommended-sorted.csv
│       ├── [ 15k]  a6-movies-extract-recommended.csv
│       └── [ 36k]  a7-movies-extract-recommended-films.csv
├── [ 102]  docs
│   └── [448k]  eng-py2.gif
└── [ 204]  python
    ├── [9.9k]  eng-q1.py
    ├── [ 10k]  mlr-engine-quick.py
    └── [ 10k]  mlr-engine.py
```

## Datasets

The following main data sources were used for this project.

- [MovieLens](http://grouplens.org/datasets/movielens/)
- [MovieLens Tag Genome](http://grouplens.org/datasets/movielens/tag-genome/)
- [IMDb](http://www.imdb.com/interfaces)

### MovieLens 

The MovieLens movie ratings data is provided by GroupLens Research in datasets ranging in size from 100K to 20 million. There is a "Latest" dataset that includes more recent ratings data up to 2016. Our team chose to use the stable 20 million (MovieLens 20M) count dataset and the Latest dataset.

Permalink (20M): http://grouplens.org/datasets/movielens/20m/

Permalink (Latest): http://grouplens.org/datasets/movielens/latest/

### MovieLens Tag Genome

Tag Genome data is also from GroupLens Research. In this dataset, the relevance of certain tags to a range of movies was calculated using a machine learning algorithm by the research team.

Permalink: http://grouplens.org/datasets/movielens/tag-genome/

### IMDb

The IMDb plain text data dumps are available through 2 FTP sites. 

Permalink: http://www.imdb.com/interfaces


## Recommendation System

### Tech Stack

Each part of the tech stack for this project was utilized to varying degrees by the different team member. 

- MySQL - primary database
- IBM SPPS Modeler - analytics software for generating models
- Python - data pre-processing and data analytics implementation
- Amazon Web Services - cloud computing in conjunction with MySQL
- Weka - data mining tool for pre-processing data

### Technique

I undertook my share of the project with inspiration from the [Collaborative Filtering](https://en.wikipedia.org/wiki/Collaborative_filtering) technique, which takes advantage of the predictive power of analyzing like-minded users. 
At its most basic level, collaborative filtering is present in everyday affairs as it is “the process of filtering or evaluating items using the opinions of other people” (Schafer, Frankowski, Herlocker, & Sen, 2015).

Collaborative filtering was also covered in Domingo's The Master Algorithm: "In 1994, a team of researchers from the University of Minnesota and MIT built a recommendation system based on what they called "a deceptively simple idea": people who agreed in the past are likely to agree again in the future. That notion led directly to the collaborative filtering systems that all self-respecting e-commerce sites have." (pg. 183).

For example, if each tuple is structured as [id, movieId, rating], then the tuples [246, 2, 3.5] and [357, 2, 3.5] would suggest that both users 246 and 357 are like-minded in that they rated movie "2" with a "3.5" rating. 
This underlying technique was combined with further processing steps and implemented in a Python CLI tool.

### Python Implementation

The Python script works by first parsing an input set of tuples that represent the initial movies that a user has rated. 
The movie-rating tuples are then extracted and the movies data is scanned for other users where similar movie-rating tuples occur. 
As this catches any user that agrees with the input user on any movie-rating tuple, a sorting algorithm is applied to only select users where there is a high level of agreement with the input user. 
For example, only users that have at least eight out of ten similar movie-rating tuples with the input user are accepted. 
With this list of users, the script obtains all of their cumulative movie-rating tuples. 
Once again, this data is further filtered by only selecting movie-ratings that occur above a certain threshold in frequency and in minimum movie rating. 
As a result, the final output file contains movie recommendations that are both high in favorable ratings and occur frequently in the network of like-minded movie watchers.

A number of Python libraries were tested to parse the full set of data (>300 MB of tuples in the `ml-ratings.csv` file). The `numpy`, `pickle`, and additional Python libraries did not lead to significant speed improvements, so the script parses the CSV tuples without any specialized support. The original script writes to stdout to show realtime progress, but removing this leads to a significant speed up. 


```
$ python eng-q1.py # v1.0 for the project
...

$ python mlr-engine.py # refactored version
...


# Some rough speed metrics (MacBook Pro 2015, 2.7 GHz i5):
$ time python mlr-engine.py
real    4m7.895s
user    2m41.626s
sys     0m46.439s

$ time python mlr-engine-quick.py # No stdout printing
real    1m18.558s
user    0m56.948s
sys     0m10.179s

```

Running Python implementation on the command line.
![Python CLI GIF](/docs/eng-py2.gif)


### Results

This algorithm was tested against a sample of data as follows. 
Using an initial input of ten tuples (the movies rated favorably by a single user), the system was able to recommend over 500 movies. 
The system would attempt to provide movie recommendations from like-minded movie watchers based on these ten tuples.
When the recommended films were compared to the actual ratings by the user, it was found that over 85% of the >500 movie recommendations were rated favorably (with a rating of at least 4 out of 5) when compared to the user's actual ratings for the films. 
These preliminary results suggest that the collaborative filtering system was able to effectively recommend movies that would be favorably received by the end user. 


![Chart of Actual Ratings for the Recommended Films](/docs/ratings-of-recommended-films.png)


## Next Steps

This preliminary system can be developed further in a number of ways.

- Supervised machine learning
    - Further machine learning extension. It may be worthwhile to train a full-blown ML model based on millions of film rating tuples. This was partly done with the IBM SPSS Modeler implementation by a fellow group member.
- Unsupervised machine learning
    - Clustering the ratings data. There may be interesting trends or patterns in the ratings data when clustered according to year, genre, average rating, etc.



## References

Domingos, P. (2015). The master algorithm: How the quest for the ultimate learning machine will remake our world. Basic Books.
Chicago	

Schafer, J. B., Frankowski, D., Herlocker, J., & Sen, S. (2007). Collaborative filtering recommender systems. In The Adaptive Web (pp. 291-324). Springer Berlin Heidelberg.


