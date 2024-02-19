    #include <iostream>
     #include <unordered_map>
    #include <vector>
    #include <string>

    using namespace std;

     // Define a structure to represent a movie
      struct Movie {
    string title;
    vector<double> ratings; // Ratings given by users
          };

      // Define a function to calculate similarity between two movies based on ratings
     double calculateSimilarity(const vector<double>& ratings1, const vector<double>& ratings2) {
    // Implement a similarity metric (e.g., Pearson correlation coefficient, cosine similarity)
    // For simplicity, let's use a simple average difference method here
    double sum = 0.0;
    for (size_t i = 0; i < ratings1.size(); ++i) {
        sum += (ratings1[i] - ratings2[i]) * (ratings1[i] - ratings2[i]);
    }
    return 1.0 / (1.0 + sqrt(sum)); // Return a similarity score between 0 and 1
      }

     // Define a function to recommend movies to a user based on collaborative filtering
     vector<string> recommendMovies(const unordered_map<string, Movie>& movies, const string& targetMovie, const string& userId) {
    // Find the movie with the given title
    auto it = movies.find(targetMovie);
    if (it == movies.end()) {
        cout << "Movie not found!" << endl;
        return {};
    }

    // Calculate similarity between the target movie and all other movies
    const Movie& target = it->second;
    unordered_map<string, double> similarityScores;
    for (const auto& pair : movies) {
        const Movie& movie = pair.second;
        if (movie.title != targetMovie) {
            double similarity = calculateSimilarity(target.ratings, movie.ratings);
            similarityScores[movie.title] = similarity;
        }
    }

    // Sort movies by similarity scores
    vector<pair<string, double>> sortedMovies;
    for (const auto& pair : similarityScores) {
        sortedMovies.push_back(pair);
    }
    sort(sortedMovies.begin(), sortedMovies.end(), [](const auto& a, const auto& b) {
        return a.second > b.second; // Sort in descending order of similarity
    });

    // Recommend top N similar movies to the user
    vector<string> recommendations;
    const int N = 5; // Number of recommendations
    for (int i = 0; i < N && i < sortedMovies.size(); ++i) {
        recommendations.push_back(sortedMovies[i].first);
    }

    return recommendations;
    }

     int main() {
    // Sample movie data (title and ratings)
    unordered_map<string, Movie> movies = {
        {"Movie 1", {"Movie 1", {5, 4, 3, 2, 1}}},
        {"Movie 2", {"Movie 2", {4, 3, 2, 1, 5}}},
        {"Movie 3", {"Movie 3", {3, 2, 1, 5, 4}}},
        // Add more movies here...
    };

    // Simulate user ratings (dummy data)
    string userId = "user123";
    string targetMovie = "Movie 1";

    // Recommend movies to the user
    vector<string> recommendations = recommendMovies(movies, targetMovie, userId);

    // Display recommendations
    cout << "Top Recommendations for User (" << userId << ") based on " << targetMovie << ":" << endl;
    for (const string& movie : recommendations) {
        cout << "- " << movie << endl;
    }

    return 0;
}
