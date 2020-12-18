---
layout: post
title: 'Graph Search Algorithms 3: Simulated Annealing'
tags: [algorithms, AI]
featured_image_thumbnail: assets/images/posts/2020/simulated-annealing/final_path.png
featured_image: assets/images/posts/2020/simulated-annealing/final_path.png
featured: true
hidden: true
---

In this third series on graph search algorithms, I use simulated annealing to find solutions to the traveling salesman problem.

# Simulated Annealing
Summarize Simulated Annealing, basic concepts

## Temperature and Probability Functions

### Temperature Function
The temperature function chosen was $T(t)=\frac{C}{(t+1)^p}$, where $t$ is time and $C$ and $p$ are tunable constants. As $t\rightarrow n_{iterations}$, the temperature will decay to 0. The temperature tells us how far from the current state we are allowed to propose new candidates. Thus, a decaying temperature allows for very large changes initially and only small, local changes towards the end. A high temperature means to explore states all over the function and check them. Since the value $C \approx$ the largest value of the temperature, the value $C$ represents the largest change we are allowed to make at the beginning. I found $C\approx\sqrt{57}\approx7$ seemed to work well for 100,000 iterations through trial and error. Then, I adjusted $p$ so that $p_{accept}$ looked reasonable (explanation below). The choice for $p$ was dependent on the max number of iterations.


### Probability of Acceptance Function
The probability of acceptance function chosen was $p_{accept}=e^{\frac{\Delta E}{T(t)}}$, where $\Delta E = \text{objective_function}(current state) - \text{objective_function}(proposed state)$ and $T(t)$ is the temperature function. Note that when the cost of the current state is better than the cost of the proposed state $\Delta E >0$ and when the cost of the proposed state is better than the cost of the current state, $\Delta E <0$. Therefore, $p_{accept}>1$ (and grows exponentially) when the current state is better and $0<p_{accept}\le1$ and decays when the proposed state is better than or equal to the current state. Additionally, note that (if $\Delta E <0$) the worse the proposed state is compared to the current state, the faster $p_{accept}$ decays; this means that the probability of acceptance of *much* worse proposed solutions will decrease much more quickly than the probability of acceptance of *slightly* worse proposed solutions (though the probabilities of both still decrease).

The behavior of this function, in theory, depends on the sign of $\Delta E$. However since I really only care about the value of $p_{accept}$ when the proposed state is worse than the current state (otherwise it is just 1), I only consider the case when $\Delta E < 0 \Rightarrow p_{accept}=e^{\frac{-|\Delta E|}{T(t)}}$. 

In the graphs below, I chose to test $p_{accept}$ with a $\Delta E = 0.2$. Through trial and error, I learned that a smooth decay of $p_{accept}$ (i.e. didn't decay too quickly) worked best. Additionally, I found that if the difference between the proposed state and the current state is a path cost of 0.2, a probability of acceptance somewhere between 10% and 20% worked best.

This resulted in a choice of $C=7$ and $P=0.37$ for $n_{iterations} = 100,000$ and a choice of $C=4$ and $P=0.45$ for $n_{iterations} = 10,000$. The temperature functions and probability of acceptance functions for the two choices of stopping iterations are plotted below.

\\ Plot of probability and Temp functions

## Annealing Implementation
First, I define an annealing class with attributes _________.

<pre><code class="language-python">
class state:
    #keep track of states- the current path and its cost/value
    def __init__(self, path, value):
        self.path = path
        self.value = value
                       
class problem_annealing:
    # Define the problem
    # initial state - give a path that visits all the nodes. This will just be the list of nodes how they are.
    # current state - the current path we are checking
    # best state - the best state we have found so far
    # objective function - minimize the total path cost, this will be the path_cost() function.
    # choices of action - schedule function and probability of acceptance function
    # information we need to choose our action

    def __init__(self, initial, best, objective_function, schedule_function, stepsize):
        self.initial_state = initial
        self.current_state = initial
        self.best_state = best
        self.objective_function = objective_function
        self.stepsize = stepsize
        self.schedule_function = schedule_function
        
#for annealing: moves are somehow random: we will use temperature schedule to change candidates for all_moves
    def random_move(self):
        numNodes = len(self.current_state.path)
        newPath = self.current_state.path.copy()
        
        strReverseStart= np.random.randint(0, numNodes-1) #should be a random number from 0 to 56, don't include last node
        strReverseEnd= np.random.randint(2, numNodes-1)
        
        while strReverseEnd <= strReverseStart:
            strReverseStart= np.random.randint(0, numNodes-1)
            strReverseEnd= np.random.randint(2, numNodes-1)
            
        reverse_string(newPath, strReverseStart, strReverseEnd)

        newPath[57] = newPath[0]
        nextMove = newPath
        return nextMove, self.objective_function(nextMove)
</code></pre>

Define some helper functions:

<pre><code class="language-python">
def make_graph(nodes):
    '''This function just makes the additional graph connections for the traveling salesman problem.'''
    graphDict={} # big dictionary
    numNodes = len(nodes)
    for n in nodes:
        graphDict[(n[0],n[1])]={}
    distancesList = np.zeros((numNodes,3))
    distancesList = distancesList.astype('object')
    
    for n in nodes: # each node should have a smaller dictionary, connecting it to its two closest nodes
        i=0
        temp = {}
        for m in nodes:
            dist = distance_formula(n, m) #Find distance between all pts connected to point n
            distancesList[i][0]= n
            distancesList[i][1]= m
            distancesList[i][2]= dist
            i+=1

        df= pd.DataFrame(data=distancesList, columns=['currPoint','nextPoint', 'distance'])
        df = df.astype({'distance': float,} ) #Convert object type of 'distance' column to float
        df = df[df.distance != 0.0] # Removes rows with a distance of 0 from the dataframe (this is the node itself)
        
        # This runs for EACH n.
        for i in range(numNodes-1):
            cx = df.values[i][1][0] #x-val of neighboring point
            cy = df.values[i][1][1] #y-val of neighboring point
            temp[(cx, cy)] = df.values[i][2] #
        graphDict[(n[0],n[1])] = temp
            
    return graphDict

def create_initial_path(nodes):
    # create a list of nodes in the form of the desired solution
    # i.e. a list of every node, beginning at one (arbitrarily chosen)node and ending at that same node
    numNodes = len(nodes)
    path = np.zeros(numNodes+1)
    path = path.astype('object')
    for i in range(numNodes):
        path[i] = (nodes[i][0], nodes[i][1])
    path[numNodes] = (nodes[0][0],nodes[0][1])
    return path

def reverse_string(array, strStart, strEnd):
    temp = array[strStart:strEnd]
    array[strStart:strEnd] = temp[::-1]
    return
</code></pre>

Define a schedule function:

<pre><code class="language-python">
def schedule(time, C=C_input, p=p_input):
    '''some sort of mapping from time to temperature, to represent "cooling off" - 
    that is, accepting wacky solutions with lower and lower probability as time increases'''
    T = C/(time+1)**p #temperature
    return T
</code></pre>

Finally, write simulated annealing.

<pre><code class="language-python">
def simulated_annealing(problem, n_iter):
    current_state = problem.initial_state
    best_state = problem.best_state
    times = []
    values = []
    
    for t in range(int(n_iter)):
        temperature = problem.schedule_function(t)
        nextMove, nextValue = problem.random_move() #from randomly generated moves
        
        if nextValue < best_state.value:
            problem.best_state.path = nextMove
            problem.best_state.value = nextValue
            
        # Next value is better than current value
        if nextValue < current_state.value:
            problem.current_state.path = nextMove
            problem.current_state.value = nextValue
            
        else: # next value is worse (or equal to) current value
            n_random = np.random.rand()
            E = -abs(nextValue-current_state.value) # E<0
            p_accept = np.exp(E/temperature) #should depend on quality of path
            if n_random < p_accept:
                problem.current_state.path, problem.current_state.value = nextMove, nextValue
            # else: do nothing, keep iterating

        if t % 100 ==0:
            times.append(t)
            values.append(best_state.value)
        if t==n_iter:
            times.append(t)
            values.append(best_state.value)
    return problem.best_state
</code></pre>

Run Annealing Once:

<pre><code class="language-python">
graph = make_graph(points) #dictionary of connected nodes
C_input = 7
p_input = 0.37
start_time = timer.time()
init_path = create_initial_path(df.values) #any path that connects all nodes
init_state = state(path=init_path, value=path_cost(init_path))
best_state = state(path=init_path, value=10000) #need to initialize two objects so memory wasn't tied together
problem_from_class = problem_annealing(initial=init_state, best=best_state, objective_function=path_cost,schedule_function=schedule)
solution = simulated_annealing(problem_from_class, n_iter=1e5) #100,000 iterations
print(solution.value)
end_time = timer.time()
print('time elapsed', end_time-start_time)
# print(solution.path, solution.value)
</code></pre>
<pre><code class="language-python">
105.39076734189328
time elapsed 16.607876777648926
</code></pre>


## Conclusions