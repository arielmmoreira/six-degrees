# sixDegrees
This project is the implementation of an Artificial Inteligence. It's based on the game "Six Degrees of Bacon". The AI will search for two given actor or actress and will try to find the connection between them. How many people and who connects these two people we want to find?

The database is from IMDB

This is my implementation of a project from an AI course. You can check the original here: https://cs50.harvard.edu/ai/2020/projects/0/degrees/

# The project
For this project we have to have three csv files in the same directory of the main code 'degrees.py'. These files are our database.

## Loading data
First of all we are going to load the database into three dictionaries: names, people and movies.

The 'names' dictionary maps names to a set of corresponding person_ids. The data comes from the people.csv file

The 'people' dictionary maps person_ids to a dictionary of: name, birth, movies(a set of movie_ids) The data also comes form the people.csv and stars.csv files

The 'movie' dictionary maps movie_ids to a dictionary of: title, year, starts (a set of person_ids). The data comes from movies.csv and stars.csv files

## The main function
After loading data, we are ready to begin our search. First we need two inputs, 'source' and 'target'. We transform these inputs into a person_id using the function 'person_id_for_name'. 

With these two ids, we can use another function, 'shortest_path', to look for a path that connects our source and target.And here is the main part of the function

    path = shortest_path(source, target)
    
    if path is None:
        print("Not connected.")
    else:
        degrees = len(path)
        print(f"{degrees} degrees of separation.")
        path = [(None, source)] + path
        for i in range(degrees):
            person1 = people[path[i][1]]["name"]
            person2 = people[path[i + 1][1]]["name"]
            movie = movies[path[i + 1][0]]["title"]
            print(f"{i + 1}: {person1} and {person2} starred in {movie}")
