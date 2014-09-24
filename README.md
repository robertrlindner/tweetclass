tweetclass
==========

Robert Lindner
Didier Dominguez
Jing Mao

Section 0. Dependencies
-----------------------

1. Python 2.7+
2. Numpy
3. Scikit-learn
4. mallet (http://mallet.cs.umass.edu/)
5. Bob's Python module: ml
6. Didier's Python module: tweetsfeatures



Section I. Label the training data
---------------------------------------

    1.) Obtain JSON training data.
        E.g., run "my_twitterstream.py" or use
        George Fisher's script to download JSON data.
        Let's call it "data.json".


    2.) Run: $ dump_tweet_text data.json 

        This will dump all tweet message text into a 
        folder named "tweet_text" containing separate 
        files for all valid tweets.  The file names 
        are "tweet_{ID}.txt", where {ID} is the tweet ID.
        The program will also make a file called:
        "all_tweets.txt" which lists all IDs and text
        messages in a single file for easy data viewing.


    2.) Run: $ mallet_load_data

        This will read the tweets from directory tweet_text
        into the mallet data format.

        It expects to find the directory "tweet_text".
        If will create the file "tweet.mallet", data
        in the Mallet format.


    3.) Run: $ mallet_topic_model
 
        This will run latent dirichlet topic modelling 
        on the mallet data. Takes ~15 minutes for 
        100-200k tweets.  The outputs are:

        i.)   doc_topics.txt
        ii.)  topic_keys.txt

       Edit the topic_keys.txt file.
       Include a new "0th" column indicating the class as: 0  or 1.
       Save the new file as "topic_keys_labels.txt".
       This is the important step where the nature of the
       classes is implicitly determined by your choices.


    4.) Run: $ label_tweets

        This will label all tweets in the training set.
        It expects to find the two files:
 
                  1. "doc_topics.txt"
                  2. "topic_keys_labeled.txt"

        It creates "ids_and_labels", the file indicating the 
        class of all tweet ids (except those few which could
        not be parsed by JSON; see revisions)


Section II. Extract features from training data
-----------------------------------------------
    (to be continued)
    Desired format:
    |ID1, feature vector 1|
    |ID2, feature vector 2|
    | ...          ...    |
    |IDN, feature vector N|
    
    Creates "ids_and_features.txt"



Section III. Generate training data:
             Design matrix "X", and feature vector "Y".
------------------------------------------------------

    1. Run $ gen_training_data [ids_and_features] [ids_and_labels]

       This will find the common IDs between the features and the
       labels and produce pure numerical representations of the
       design matrix X and target vector Y for training.

       This step in required because the features and labels
       are as of yet as necessarily in the same order, or even
       have the same number of rows (see "revisions" # 4).

       Files created: "X" and "Y"
       I.e., design matrix and target vector


Section IV. Train the model
----------------------------

    1. Run $ bench $ bench [paramfile] [X] [Y]

    This will train a Gradient Boosted Decision Tree Classifier
    (scikit-learn) on the X, Y data using the hyperparameter 
     list set in the "paramfile".  To create the paramfile, make a 
     CSV file with each of the hyperparameters of the model that 
     you wish to be non-default, refer to documentation here:

     http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.GradientBoostingClassifier.html

     The example contents of a simple paramfile to scan over
     five values of the the learning_rate hyperparameter 
     would look like this:

      learning_rate,	n_estimators, max_depth, n_folds
      0.1,		100,	      4,	 2
      0.2,		100,	      4,	 2
      0.3,		100,	      4,	 2
      0.4,		100,	      4,	 2
      0.5,		100,	      4,	 2

     After completion, a new paramfile is created with additional
     columns describing the peformance of each model.
    








#Revisions to make:
1  Consider how to handle these characters during
   topic modeling:
    "@", "#", "\n", numbers

2   Keep "#" character in the tweet text, 
    will help with identifying hashtags?

3   Mallet is removing NUMBERS, leaving the letter fragments from URLS.
    Just remove everything after an "http"?

4  Very rarely (about 1 out of 100,000 tweets) a JSON
   record is unable to be parsed by json.loads().
   Currently, the exception is caught and the tweet is ignored
   which is why the output file may have fewer lines than the
   original json file.  Correct this at some point...

5 Allow user to keep a trained model for use later