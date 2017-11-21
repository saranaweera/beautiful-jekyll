---
layout: post
title: Connect the Dots
subtitle: A brief refresher on Graph Theory
image: https://momath.org/wp-content/uploads/2013/10/graph_theory-692x674.jpeg
bigimg: https://momath.org/wp-content/uploads/2013/10/graph_theory-692x674.jpeg
---

I have been prepping for a final round interview at Two Sigma. I am excited and nervous on what to expect, as the day long interview is known to be quite grueling. Since I haven't looked at graph theory in a little while, I decided to go through some material on the subject. I will share some of my code in case anyone finds this helpful.

I wrote this code my self, so if you find any errors please let me know.

## Implement a Graph using a Dictionary

Here the Key of the dictionary will be the current node and the value will be a *List* containing neighbors.

``` python
graph_dict = {
    'A':['B','C','E'],
    'B':['C','E','D'],
    'C':['F','D'],
    'D':['F', 'E'],
    'E':['A'],
    'F':['G'],
    'G':[]
}

```

## Depth First Search (DFS) ##

``` python
def dfs(node, graph_dict, visited_dict):

    # mark current node as visited
    visited_dict[node] = True

    print('visiting', node)

    # visit unvisited neighbors
    for neighbor in graph_dict[node]:  
        if not neighbor in visited_dict:
            dfs(neighbor, graph_dict, visited_dict)
        else:
            print('skipping', neighbor)

```

## Breadth First Search (BFS) ##

``` python
def bfs(node, graph_dict, visited_dict):
    queue = []

    # insert the starting node into the queue
    queue.append(node)

    while queue:
        current_node = queue.pop(0)
        if not current_node in visited_dict:
            visited_dict[current_node] = True

            print('visiting', current_node)

            # insert unvisited neighbors into the queue
            [queue.append(neighbor) for neighbor in graph_dict[current_node] if not neighbor in visited_dict]


```

## Find Shortest Path using Bread First Search ##

``` python
# generates dictionary of previous nodes for each node
def bfs_shortestPaths(node, graph_dict, visited_dict):
    queue = []

    # insert the starting node into the queue
    queue.append(node)

    shortest_path = {}

    while queue:
        current_node = queue.pop(0)
        if not current_node in visited_dict:
            visited_dict[current_node] = True

            print('visiting', current_node)

            # insert unvisited neighbors into the queue
            for neighbor in graph_dict[current_node]:
                if not neighbor in visited_dict:
                    queue.append(neighbor)
                    if not neighbor in shortest_path:
                        shortest_path[neighbor] = current_node
                    else:
                        print(neighbor, 'already in shortest path')

    return shortest_path

# traverses the shortest path dictionary and creates a stack of nodes
def findShortestPath(starting_node, destination_node, shortestPaths_dict):
      path = []

      previous_node = shortestPaths_dict[destination_node]
      path.insert(0,destination_node)
      while previous_node:
          path.insert(0,previous_node)
          if previous_node == starting_node:
              break
          previous_node = shortestPaths_dict[previous_node]

      return path

  ```

**Shortest path dictionary for the graph:** . 
input:
``` python

queue = []
path = {}
bfs_shortestPaths('A',{},graph_dict)
```
output:
``` python
{
 'B': 'A',
 'C': 'A',
 'D': 'B',
 'E': 'A',
 'F': 'C',
 'G': 'E'
 }
 ```

 **Shortest Path to *G* :** .    
 input:
 ``` python
 findShortestPath('A',
                 'G',
                 bfs_shortestPaths('A',graph_dict, {})
                )
```

output:
``` python
['A', 'E', 'G']
```
