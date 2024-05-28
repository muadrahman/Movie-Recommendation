
# Import libraries


import numpy as np
import pandas as pd
import difflib
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import os


# Read the CSV file into a DataFrame


file_path = r"D:\CV things\ML projects\movies.csv"  # Specify the file path
movies_data = pd.read_csv(file_path)  
# Use 'movies_data' for further processing
print(movies_data.head())  # Displaying the first few rows as an example





# Selecting the relevant features for recommendation
selected_features = ['genres', 'keywords', 'tagline', 'cast', 'director']




# Check for Null Values
print(movies_data.isna().sum())





# Replace the null values with empty strings
for feature in selected_features:
    movies_data[feature] = movies_data[feature].fillna('')





# Combine all the selected features
movies_data['combined_features'] = movies_data['genres'] + ' ' + movies_data['keywords'] + ' ' + movies_data[
    'tagline'] + ' ' + movies_data['cast'] + ' ' + movies_data['director']





# Convert the text data to feature vectors using TF-IDF
vectorizer = TfidfVectorizer()
feature_vectors = vectorizer.fit_transform(movies_data['combined_features'])





similarity = cosine_similarity(feature_vectors)




# Function to recommend movies
def recommend_movie(movie_name, movies_data, similarity):
    list_of_all_titles = movies_data['title'].tolist()

    # Finding the close match for the movie name given by the user
    find_close_match = difflib.get_close_matches(movie_name, list_of_all_titles)
    close_match = find_close_match[0]

    # Finding the index of the movie with the title
    index_of_the_movie = movies_data[movies_data.title == close_match].index.values[0]

    # Get the similarity row for the selected index
    similarity_score = list(enumerate(similarity[index_of_the_movie]))

    # Sorting the movies based on their similarity score
    sorted_similar_movies = sorted(similarity_score, key=lambda x: x[1], reverse=True)

    # Print the name of similar movies based on the index – Top 30
    print('Movies suggested for you: \n')
    i = 1
    for movie in sorted_similar_movies:
        index = movie[0]
        title_from_index = movies_data[movies_data.index == index]['title'].values[0]
        if i < 30:
            print(i, '.', title_from_index)
            i += 1





# Get user input for a favorite movie
movie_name = input('Enter your favorite movie name: ')





# Make movie recommendations
recommend_movie(movie_name, movies_data, similarity)

