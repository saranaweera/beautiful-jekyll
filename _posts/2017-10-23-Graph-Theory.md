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
visited_dict = {}
def dfs(node, visited_dict, graph_dict):

    # mark current node as visited
    visited_dict[node] = True

    print('visiting', node)

    # visit unvisited neighbors
    for neighbor in graph_dict[node]:  
        if not neighbor in visited_dict:
            dfs(neighbor, visited_dict, graph_dict)
        else:
            print('skipping', neighbor)


```

## Breadth First Search (BFS) ##

``` python
visited_dict = {}
queue = []
def bfs(node, visited_dict, graph_dict):
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
