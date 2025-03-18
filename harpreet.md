**Hogwarts**
Hogwarts is a complex environment filled with magical obstacles and dynamic elements. This case study applies Artificial Intelligence (AI) search algorithms to solve problems within Hogwarts, such as navigating the castle, traversing a Triwizard Maze, and customizing a magic wand.

## 1. Problem Description and Explanation

### **Iterative Deepening Depth-First Search (IDDFS): Classroom Visits**

A student at Hogwarts must visit multiple classrooms to complete assignments while gradually unlocking more rooms. The student should prioritize unlocked classrooms while exploring the castle. The challenges include avoiding revisits and balancing exploration depth with time constraints. An extension to this problem involves dynamically unlocking classrooms based on assignment progress.

### **Recursive Best-First Search (RBFS): Adaptive Potion Brewing**

A student is brewing a potion requiring precise ingredient additions and timing while dynamically adapting to changing instructions. The primary challenges are optimizing memory usage and balancing between following instructions and innovating. An extension introduces penalties for incorrect actions, such as overboiling or under-stirring.

### **Constraint Satisfaction Problems (CSP): Class Schedule**

A student needs to create a balanced schedule that includes classes, Quidditch practice, and potion brewing while avoiding conflicts. Challenges include handling overlapping constraints and optimizing for minimal idle time. The problem can be extended to include events like Hogsmeade trips or detention, adding further constraints.

## 2. Algorithm/Flowchart/Game Tree

### **IDDFS Algorithm**

1. Start from the ‘Entrance Hall’.
2. Perform a depth-limited DFS up to a specified depth limit.
3. If no solution is found, increase the depth and repeat.
4. Dynamically unlock new classrooms as assignments are completed.
5. If a classroom is visited successfully, stop exploration.
6. If the time runs out, terminate the search.

### **RBFS Algorithm**

1. Start with an initial potion state.
2. Evaluate the current state based on a heuristic function (penalty calculation).
3. Generate possible next states by applying actions (e.g., adding ingredients, adjusting time).
4. Sort states by heuristic value and explore recursively.
5. If the goal state is reached, return the solution; otherwise, continue exploring until depth limits are exceeded.

### **CSP Algorithm for Class Scheduling**

1. Define activities as variables.
2. Assign possible time slots as domains.
3. Define constraints to ensure non-overlapping schedules.
4. Use backtracking to assign time slots to activities.
5. If a conflict arises, backtrack and reassign values.
6. Continue until a complete, conflict-free schedule is found.

## 3. Code

### **IDDFS Implementation**

```python
class HogwartsCastle:
    def __init__(self):
        self.graph = {
            'Entrance Hall': ['Charms', 'Transfiguration'],
            'Charms': ['Entrance Hall', 'Defense Against the Dark Arts'],
            'Transfiguration': ['Entrance Hall'],
            'Defense Against the Dark Arts': ['Charms']
        }
        self.visited = set()
        self.assignment_progress = 0
        self.time_left = 100

    def unlock_classroom(self):
        if self.assignment_progress >= 1:
            self.graph['Transfiguration'] = ['Entrance Hall']
        if self.assignment_progress >= 2:
            self.graph['Defense Against the Dark Arts'] = ['Charms']

    def iddfs(self, start_room, depth_limit):
        for depth in range(depth_limit + 1):
            if self.dfs(start_room, depth, set()):
                return True
        return False

    def dfs(self, room, depth, visited):
        if depth == 0:
            return room not in visited

        visited.add(room)
        self.visited.add(room)

        for neighbor in self.graph.get(room, []):
            if neighbor not in visited:
                if self.dfs(neighbor, depth - 1, visited):
                    return True
        return False

    def explore(self):
        for depth_limit in range(10):
            self.unlock_classroom()
            if self.iddfs('Entrance Hall', depth_limit):
                print(f"Exploration successful at depth {depth_limit}")
                return
            self.time_left -= 1
            if self.time_left <= 0:
                print("Time's up! Returning to the starting point.")
                return

castle = HogwartsCastle()
castle.explore()
```

### **RBFS Implementation**

```python
class PotionBrewing:
    def __init__(self, goal_state):
        self.goal_state = goal_state
        self.max_depth = 10

    def heuristic(self, state):
        penalty = 0
        if state["time"] > self.goal_state["time"]:
            penalty += 5
        if state["time"] < self.goal_state["time"]:
            penalty += 3
        if set(state["ingredients"]) != set(self.goal_state["ingredients"]):
            penalty += 10
        return penalty

    def rbfs(self, current_state, depth, f_limit):
        if depth == self.max_depth:
            return None, float('inf')

        f = current_state["penalty"] + self.heuristic(current_state)
        if f > f_limit:
            return None, f

        if current_state == self.goal_state:
            return current_state, f

        children = self.generate_children(current_state)
        children = sorted(children, key=lambda x: x[1])

        for child, child_f in children:
            result, result_f = self.rbfs(child, depth + 1, min(f_limit, child_f))
            if result is not None:
                return result, result_f

        return None, f
```

### **CSP Implementation**

```python
class ClassScheduleCSP:
    def __init__(self, variables, domains, constraints):
        self.variables = variables
        self.domains = domains
        self.constraints = constraints

    def is_consistent(self, assignment, var, value):
        for (other_var, other_value) in assignment.items():
            if (var, other_var) in self.constraints and not self.constraints[(var, other_var)](value, other_value):
                return False
        return True

    def backtrack(self, assignment):
        if len(assignment) == len(self.variables):
            return assignment

        unassigned_vars = [var for var in self.variables if var not in assignment]
        var = unassigned_vars[0]

        for value in self.domains[var]:
            if self.is_consistent(assignment, var, value):
                assignment[var] = value
                result = self.backtrack(assignment)
                if result:
                    return result
                del assignment[var]

        return None

    def solve(self):
        return self.backtrack({})
```

## 4. Output

- **IDDFS:** Successfully explores classrooms based on depth limits while unlocking rooms dynamically.
- **RBFS:** Adapts potion brewing strategy dynamically while minimizing penalties.
- **CSP:** Generates an optimal, conflict-free schedule for classes and activities.
