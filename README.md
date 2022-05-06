# Breadth First Search
## AIM

To develop an algorithm to find the route from the source to the destination point using breadth-first search.

## THEORY
Breadth-first search, also known as BFS, finds shortest paths from a given source vertex to all other vertices, in terms of the number of edges in the paths.BFS stands for Breadth First Search is a vertex-based technique for finding the shortest path in the graph. It uses a Queue data structure that follows first in first out. In BFS, one vertex is selected at a time when it is visited and marked then its adjacent are visited and stored in the queue.

## DESIGN STEPS

### STEP 1:
Identify a location in the google map:

### STEP 2:
Select a specific number of nodes with distance

### STEP 3:
Import required packages.

### STEP 4:
Include each node and its distance separately in the dictionary data structure.

### STEP 5:
End of program.


## ROUTE MAP
#### Example map
![image](https://github.com/veerapallijanith/BreadthFirstSearch/blob/main/IMG_20220506_210629%20(1).jpg)



## PROGRAM
```python
NAME : VEERAPALLI JANITH CHOWDARY
REG NO : 212220230057
 ```
```python
%matplotlib inline
import matplotlib.pyplot as plt
import random
import math
import sys
from collections import defaultdict, deque, Counter
from itertools import combinations

class Problem(object):
   def __init__(self, initial=None, goal=None, **kwds): 
       self.__dict__.update(initial=initial, goal=goal, **kwds) 
       
   def actions(self, state):        
       raise NotImplementedError
   def result(self, state, action): 
       raise NotImplementedError
   def is_goal(self, state):        
       return state == self.goal
   def action_cost(self, s, a, s1): 
       return 1
   
   def __str__(self):
       return '{0}({1}, {2})'.format(
           type(self).__name__, self.initial, self.goal)
           
class Node:
   def __init__(self, state, parent=None, action=None, path_cost=0):
       self.__dict__.update(state=state, parent=parent, action=action, path_cost=path_cost)

   def __str__(self): 
       return '<{0}>'.format(self.state)
   def __len__(self): 
       return 0 if self.parent is None else (1 + len(self.parent))
   def __lt__(self, other): 
       return self.path_cost < other.path_cost
       
failure = Node('failure', path_cost=math.inf) 
cutoff  = Node('cutoff',  path_cost=math.inf)

def expand(problem, node):
   "Expand a node, generating the children nodes."
   s = node.state
   for action in problem.actions(s):
       s1 = problem.result(s, action)
       cost = node.path_cost + problem.action_cost(s, action, s1)
       yield Node(s1, node, action, cost)
       

def path_actions(node):
   "The sequence of actions to get to this node."
   if node.parent is None:
       return []  
   return path_actions(node.parent) + [node.action]


def path_states(node):
   "The sequence of states to get to this node."
   if node in (cutoff, failure, None): 
       return []
   return path_states(node.parent) + [node.state]
   
FIFOQueue = deque

def breadth_first_search(problem):
   "Search shallowest nodes in the search tree first."
   node = Node(problem.initial)
   if problem.is_goal(problem.initial):
       return node
   frontier = FIFOQueue([node])
   reached = {problem.initial}
   while frontier:
       node = frontier.pop()
       for child in expand(problem, node):
           s = child.state
           if problem.is_goal(s):
               return child
           if s not in reached:
               reached.add(s)
               frontier.appendleft(child)
   return failure
   
class RouteProblem(Problem):
   """A problem to find a route between locations on a `Map`.
   Create a problem with RouteProblem(start, goal, map=Map(...)}).
   States are the vertexes in the Map graph; actions are destination states."""
   
   def actions(self, state): 
       """The places neighboring `state`."""
       return self.map.neighbors[state]
   
   def result(self, state, action):
       """Go to the `action` place, if the map says that is possible."""
       return action if action in self.map.neighbors[state] else state
   
   def action_cost(self, s, action, s1):
       """The distance (cost) to go from s to s1."""
       return self.map.distances[s, s1]
   
   def h(self, node):
       "Straight-line distance between state and the goal."
       locs = self.map.locations
       return straight_line_distance(locs[node.state], locs[self.goal])
       
class Map:
   """A map of places in a 2D world: a graph with vertexes and links between them. 
   In `Map(links, locations)`, `links` can be either [(v1, v2)...] pairs, 
   or a {(v1, v2): distance...} dict. Optional `locations` can be {v1: (x, y)} 
   If `directed=False` then for every (v1, v2) link, we add a (v2, v1) link."""

   def __init__(self, links, locations=None, directed=False):
       if not hasattr(links, 'items'): # Distances are 1 by default
           links = {link: 1 for link in links}
       if not directed:
           for (v1, v2) in list(links):
               links[v2, v1] = links[v1, v2]
       self.distances = links
       self.neighbors = multimap(links)
       self.locations = locations or defaultdict(lambda: (0, 0))

       
def multimap(pairs) -> dict:
   "Given (key, val) pairs, make a dict of {key: [val,...]}."
   result = defaultdict(list)
   for key, val in pairs:
       result[key].append(val)
   return result
   
saveetha_nearby_locations = Map(
   {('Tirupathi', 'Peruru'): 3,('Tirupathi', 'Cherlopalli'): 9,('Peruru', 'Thondawada'): 5,('Thondawada', 'Chandragiri'): 3,('Chandragiri', 'Agarala'): 8,('Agarala', 'Ithepalli'): 9,('Ithepalli', 'Mamanduru'): 14,('Mamanduru', 'Nadimpalli'): 3,
   ('Nadimpalli', 'Maravapalli'): 16,('Maravapalli', 'Kasipentla'): 8,('Agarala', 'Kothaindlu'): 7,('Kothaindlu', 'Panapakam'): 5,('Panapakam', 'Nendragunta'): 8,('Nendragunta', 'Sankampalli'): 6,('Sankampalli', 'Thotapalli'): 8,('Thotapalli', 'Reddypalli'): 5,('Reddypalli', 'Kasipentla'): 11,
   ('Cherlopalli', 'Vemu'): 8,('Vemu', 'Pakala'): 10,('Pakala', 'Gorpadu'): 7,('Pakala', 'Putalapattu'): 8, ('Mogarala', 'Yellampalli'): 4,('Yellamaplli', 'Kasipentla'): 5})


r0 = RouteProblem('Tirupathi', 'Vemu', map=saveetha_nearby_locations)
r1 = RouteProblem('Agarala', 'Maravapalli', map=saveetha_nearby_locations)
r2 = RouteProblem('Peruru', 'Panapakam', map=saveetha_nearby_locations)
r3 = RouteProblem('Pakala', 'Putalapattu', map=saveetha_nearby_locations)
r4 = RouteProblem('Nendragunta', 'Reddypalli', map=saveetha_nearby_locations)

goal_state_path=breadth_first_search(r0)
path_states(goal_state_path)
print("GoalStateWithPath:{0}".format(goal_state_path))
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))
goal_state_path=breadth_first_search(r1)
path_states(goal_state_path)
print("GoalStateWithPath:{0}".format(goal_state_path))
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

```

## OUTPUT:

![image](https://github.com/veerapallijanith/BreadthFirstSearch/blob/main/jani1.png)



## SOLUTION JUSTIFICATION:

Route follow the minimum distance between locations using breadth-first search.BFS is a traversing algorithm where you should start traversing from a selected node (source or starting node) and traverse the graph layerwise thus exploring the neighbour nodes (nodes which are directly connected to source node). You must then move towards the next-level neighbour nodes.
As the name BFS suggests, you are required to traverse the graph breadthwise as follows:
1.First move horizontally and visit all the nodes of the current layer
2.Move to the next layer

## RESULT:
Thus an algorithm to find the route from the source to the destination point using breadth-first search is developed and executed successfully.
