---
layout: post
title: 'Graph Search Algorithms'
tags: [algorithms, graphs]
featured_image_thumbnail:
featured_image: assets/images/posts/2019/patterns.jpeg
featured: true
hidden: true
---

This is a collection of algorithms programmed in an Artificial Intelligence class. While relatively simple, these algorithms form the foundations of many more sophisticated techniques for optimization. Included traversal algorithms are Breadth First Search, Depth First Search, Uniform Cost Search, and A star. The algorithms are demonstrated on a weighted undirected graph of connected cities, as well as a maze that is represented as a matrix of binary values.

## Breadth First Search

```
def breadth_first(start, goal, state_graph, return_cost=False):
    frontier = [start]
    visited = [start]
    parent = {start:None}
    
    if start == goal:
        solution = path(parent, goal)
        if(return_cost == False):
            return solution
        else:
            try:
                solution_cost = pathcost(solution, state_graph)
            except:
                solution_cost = 0
            return solution, solution_cost
        
    while frontier:
        currNode = frontier.pop(0)

        for child in state_graph[currNode]:
            if child not in visited:
                visited.append(child)
                parent[child] = currNode
                frontier.append(child)
            if child == goal:
                bfs_path = get_path(start, goal, parent)
                solution = path(bfs_path, goal)
                if(return_cost==False):
                    return solution
                else:
                    try:
                        solution_cost = pathcost(solution, state_graph)
                    except:
                        step_cost = 1
                        solution_cost = step_cost*(len(solution))
                    return solution, solution_cost
    return 0
```

## Depth First Search
```
def depth_first(start, goal, state_graph, return_cost=False):
    parent = {start:None}
    frontier = [start]
    visited = []
    
    if start == goal:
        solution = path(parent, goal)
        if(return_cost == False):
            return solution
        else:
            try:
                solution_cost = pathcost(solution, state_graph)
            except:
                solution_cost = 0
            return solution, solution_cost
    
    while frontier:
        currNode = frontier.pop()
        visited.append(currNode)
        for child in state_graph[currNode]:
            if child not in visited:
                parent[child] = currNode
                frontier.append(child)
                if child == goal:
                    visited.append(goal)
                    dfs_path = get_path(start, goal, parent)
                    solution = path(dfs_path, goal)
                    if(return_cost==False):
                        return solution
                    else:
                        try:
                            solution_cost = pathcost(solution, state_graph)
                        except:
                            step_cost = 1
                            solution_cost = step_cost*(len(solution))
                        return solution, solution_cost
    return 0
```

## Usage
Run all of the algorithms above on a weighted graph of US cities.
Then, run on a maze.  



