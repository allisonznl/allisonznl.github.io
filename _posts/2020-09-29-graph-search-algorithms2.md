---
layout: post
title: 'Graph Search Algorithms 2'
tags: [algorithms, graphs]
featured_image_thumbnail:
featured_image: assets/images/posts/2020/search-algorithms/us_map_distances.png
featured: true
hidden: true
---

This is a collection of algorithms programmed in an Artificial Intelligence class. While relatively simple, these algorithms form the foundations of many more sophisticated techniques for optimization. Uniform Cost Search, and A star. The algorithms are demonstrated on a weighted undirected graph of connected cities, as well as a maze that is represented as a matrix of binary values.

## Uniform Cost Search
```
def uniform_cost(start, goal, state_graph, return_cost=False, return_nexp=False):
    parent = {start:None}
    frontier = Frontier_PQ(start, 0)
    visited = [start]
    found_path = []
    nodes_expanded = 0
    
    while frontier.q:
        if not frontier.q:
            print("Failure!")
            return
        currNode = frontier.pop() #pop lowest cost node
        currCity = currNode[1]
        cost = currNode[0]
        found_path = found_path + [currCity]
        if currNode[1] not in visited:
            visited.append(currNode[1])
        
        if currCity == goal:
            ucs_path = get_path(start, goal, parent)
            solution = path(ucs_path, goal)
            solution_cost = cost
            solution_cost2 = pathcost(solution, state_graph)
            if solution_cost != solution_cost2:
                print("Failure")
                return
            nodes_expanded = len(visited)-1
            if(return_cost==False and return_nexp==False):
                return solution
            elif(return_cost==True and return_nexp==False):
                return [solution, solution_cost]
            elif(return_cost==False and return_nexp==True):
                return [solution,None ,nodes_expanded]
            elif(return_cost==True and return_nexp==True):
                return [solution, solution_cost, nodes_expanded]

        for child in state_graph[currNode[1]]:
            child_cost = state_graph[currNode[1]][child]
            ttl_cost = cost+child_cost
            childNode = (cost, child)
            if (child not in visited) and (child not in frontier.states): #frontier.states
                frontier.add(child, ttl_cost)
                frontier.states[child] = ttl_cost
                parent[child]=currCity
            elif (child in frontier.states) and (ttl_cost < frontier.states[child]):
                frontier.replace(child, ttl_cost)
                frontier.states[child] = ttl_cost
                parent[child]=currCity
    return 0
```

```
# Takes instantiation arguments state, cost
# start is the initial state(e.g. start='chi'), cost is the initial path cost
class Frontier_PQ:
    #instantiation arguments
    def __init__(self, start, cost):
        self.start = str(start)
        self.cost = str(cost)
        self.states = {}
        self.q = [(cost,start)]
        heapq.heapify(self.q)
    
    def add(self, state, cost):
        heapq.heappush(self.q,(cost, state))
        return
    
    def pop(self):
        lowest_cost_node = heapq.heappop(self.q)
        state = lowest_cost_node[1]
        cost = lowest_cost_node[0]
        return lowest_cost_node
    
    def replace(self, state, cost):
        heapq.heapreplace(self.q, (cost, state))
        return
```

## A* Search
```
def astar_search(start, goal, state_graph, heuristic, return_cost=False, return_nexp=False):
    parent = {start:None}
    frontier = Frontier_PQ(start, 0)
    visited = [start]
    found_path = []
    nodes_expanded = 0
    
    while frontier.q:
        if not frontier.q:
            print("Failure!")
            return
        currNode = frontier.pop() #pop lowest cost node
        currCity = currNode[1]
        cost = currNode[0]+heuristic(currNode[1])
        found_path = found_path + [currCity]
        nodes_expanded +=1
        if currNode[1] not in visited:
            visited.append(currNode[1])
        
        if currCity == goal:
            astar_search = get_path(start, goal, parent)
            solution = path(astar_search, goal)
            solution_cost = pathcost(solution, state_graph)
            nodes_expanded = len(visited)-1
            if(return_cost==False and return_nexp==False):
                return solution
            elif(return_cost==True and return_nexp==False):
                return [solution, solution_cost]
            elif(return_cost==False and return_nexp==True):
                return [solution,None ,nodes_expanded]
            elif(return_cost==True and return_nexp==True):
                return [solution, solution_cost, nodes_expanded]

        for child in state_graph[currNode[1]]:
            child_cost = state_graph[currNode[1]][child]
            ttl_cost = cost+child_cost
            childNode = (cost, child)
            if (child not in visited) and (child not in frontier.states): #frontier.states
                frontier.add(child, ttl_cost)
                frontier.states[child] = ttl_cost
                parent[child]=currCity
            elif (child in frontier.states) and (ttl_cost < frontier.states[child]):
                frontier.replace(child, ttl_cost)
                frontier.states[child] = ttl_cost
                parent[child]=currCity
    return 0
```
```
def heuristic_sld_providence(state):
    return sld_providence[state]
```

## Usage
Run all of the algorithms above on a weighted graph of US cities. 
![us map distances]({{ a11isonliu.github.io}}assets/images/posts/2020/search-algorithms/us_map_distances.png)
![us map times]({{ a11isonliu.github.io}}assets/images/posts/2020/search-algorithms/us_map_times.png)
Find the optimal path from Chicago to New York using two different weights for the graph, distances and times.

```
Breadth First Path = ['chi', 'det', 'buf', 'syr', 'new'] , Distance (miles) = 943
Depth First Path = ['chi', 'ind', 'col', 'pit', 'bal', 'phi', 'new'] , Distance (miles) = 988
Uniform Cost Search Path = ['chi', 'cle', 'pit', 'phi', 'new'] , Distance (miles) = 881
```
```
Breadth First Path = ['chi', 'det', 'buf', 'syr', 'new'] , Time (mins) = 955
Depth First Path = ['chi', 'ind', 'col', 'pit', 'bal', 'phi', 'new'] , Time (mins) = 1110
Uniform Cost Search Path = ['chi', 'cle', 'buf', 'syr', 'new'] , Time (mins) = 935
```

Find the optimal path from Chicago to Providence.
```
Uniform Cost Search Path = ['chi', 'cle', 'buf', 'syr', 'bos', 'pro'] , Distance (miles) = 1046 , Nodes Expanded = 11
A* Search Path = ['chi', 'cle', 'buf', 'syr', 'bos', 'pro'] , Distance (miles) = 1046 , Nodes Expanded = 10 
```

Run BFS and DFS on a maze to get from start to finish.
```
breadth_first((1,1), (10,10), maze_graph, True)
depth_first((1,1), (10,10), maze_graph, True)

BFS Solution = [(1, 1), (1, 2), (1, 3), (1, 4), (1, 5), (1, 6), (2, 6), (3, 6), (3, 5), (4, 5), (5, 5), (6, 5), (7, 5), (8, 5), (9, 5), (10, 5), (10, 6), (10, 7), (10, 8), (9, 8), (8, 8), (7, 8), (6, 8), (6, 9), (6, 10), (7, 10), (8, 10), (9, 10), (10, 10)] , Path Length = 29

DFS Solution = [(1, 1), (2, 1), (3, 1), (4, 1), (5, 1), (6, 1), (7, 1), (8, 1), (9, 1), (10, 1), (10, 2), (10, 3), (9, 3), (8, 3), (7, 3), (6, 3), (5, 3), (4, 3), (3, 3), (3, 4), (3, 5), (4, 5), (5, 5), (6, 5), (7, 5), (8, 5), (9, 5), (10, 5), (10, 6), (10, 7), (10, 8), (9, 8), (8, 8), (7, 8), (6, 8), (6, 9), (6, 10), (7, 10), (8, 10), (9, 10), (10, 10)] , Path Length = 41
```
![final piece]({{ a11isonliu.github.io}}assets/images/posts/2020/search-algorithms/bfs_maze.png)
![final piece]({{ a11isonliu.github.io}}assets/images/posts/2020/search-algorithms/dfs_maze.png)



