# sixDegrees
This project is the implementation of an Artificial Inteligence. It's based on the game "Six Degrees of Bacon". The AI will search for two given actor or actress and will try to find the connection between them. How many people and who connects these two people we want to find?

The database is from IMDB

This is my implementation of a project from an AI course. You can check the original here: https://cs50.harvard.edu/ai/2020/projects/0/degrees/

# The project
For this project we have to have three csv files in the same directory of the main code 'degrees.py'. These files are our database.

We are using two classes: Node and Queue. But there is one more class in the project: Stack. Queue class inherits from the Stack class.

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
            
## Helper functions

### Person id for name
Given a name the function returns the id for this person, searching in the people dictionary

### Neighbors for person
Given a person_id the function returns a set of (movie_id, person_id) pairs for people who starred with the person. We are using the people and movies dictionaries to look for it.

### Shortest path
This is where the magic happens. Given two names, the source and the target, the function returns the shortest list of (movie_id, person_id) pairs that connect the source to the target

    actor = Node(state=source, parent=None, action=None)
    queue = QueueFrontier()
    queue.add(actor)
    explored = set()
    solution = []

Here we are using the two classes: Node and Queue

We start adding an actor(Node) into a queue(Queue) and initializing other variables. In the Node class, state is the source, parent is going to be the node who 'generated' the current node and action is the movie that connects the nodes.

    while True:

        if queue.empty():
            return None

        # Choose a node from the frontier
        node = queue.remove()

        for action, state in neighbors_for_person(node.state):
            if state == target:
                node = Node(state=target, parent=node, action=action)
                while node.parent is not None and node.action is not None:
                    solution += [(node.action, node.state)]
                    node = node.parent

                solution.reverse()

                return solution

        # Add node to explored set
        explored.add(node.state)

        # Add unexplored node to queue
        for action, state in neighbors_for_person(node.state):
            if not queue.contains_state(state) and state not in explored and state != node.state:
                child = Node(state=state, parent=node, action=action)
                queue.add(child)
           
The first for loop inside the while loop is looking for a connection based on neighbors for person. It will be enough just if the source and the target starred on the same movie. Otherwise, we go to the second for loop to add an unexplored node and search inside it.

            if state == target:
                node = Node(state=target, parent=node, action=action)
                while node.parent is not None and node.action is not None:
                    solution += [(node.action, node.state)]
                    node = node.parent

When we find the target, this while loop will backtrack to show us all the connections.

# Example
source = 'Tom Hanks' id = 158
target = 'Kevin Bacon' id = 102

actor = Node(state=158, parent=None, action=None)
queue = {actor}

action, state = neighbors_for_person()
node = Node(state=102, parent=158, action=112384) # this action is the id for the movie Apollo 13, which Tom Hanks and Kevin Bacon starred together

solution = [102, 112384]
