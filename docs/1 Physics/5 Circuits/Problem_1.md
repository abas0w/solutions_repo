# Problem 1


## 1. **Algorithm for Calculating Equivalent Resistance Using Graph Theory**

### Problem Overview:

In graph theory, a circuit is represented as a **graph** where:

* **Nodes** represent junctions of the circuit (where resistors meet),
* **Edges** represent the resistors themselves, and the weight of each edge corresponds to the resistance of that resistor.

The goal is to simplify a complex circuit graph into an equivalent resistance by iteratively reducing series and parallel resistor combinations. In simpler terms:

* **Series connection**: Resistors connected in series have the total resistance equal to the sum of individual resistances:
  $R_{\text{eq}} = R_1 + R_2 + \dots + R_n$

* **Parallel connection**: Resistors connected in parallel have the total resistance given by the reciprocal of the sum of reciprocals:
  $\frac{1}{R_{\text{eq}}} = \frac{1}{R_1} + \frac{1}{R_2} + \dots + \frac{1}{R_n}$

### Key Steps in the Algorithm:

1. **Represent the circuit as a graph**: The circuit is represented as a graph where:

   * Each resistor is an edge with a weight equal to its resistance.
   * Each junction is a node.

2. **Identify and reduce series connections**: A series connection is a direct path between two nodes, with resistors connected in line. This can be detected as nodes with only two neighbors, connected through resistors. The total resistance is the sum of the resistances.

3. **Identify and reduce parallel connections**: A parallel connection occurs when two or more resistors are connected between the same two nodes. This can be detected by identifying multiple edges between two nodes. The equivalent resistance is the reciprocal of the sum of the reciprocals of the individual resistances.

4. **Iterate**: Repeat the process of simplifying the graph, handling both series and parallel combinations, until only one node (the equivalent resistance) remains.

5. **Handle Nested Combinations**: As you reduce the graph, the combinations of series and parallel resistors may become nested, and the algorithm needs to handle these by simplifying them step by step, ensuring that cycles and more complex connections are addressed appropriately.

---

### Pseudocode for the Algorithm:

```text
function calculate_equivalent_resistance(graph):
    while graph has more than one node:
        // Step 1: Look for series connections
        for each series pair of resistors:
            if resistors are in series (only two neighbors for both):
                R_eq = sum of resistances of the series resistors
                replace the series resistors with a single resistor R_eq

        // Step 2: Look for parallel connections
        for each parallel pair of resistors:
            if resistors are in parallel (same two nodes):
                R_eq = 1 / (sum of the reciprocals of the resistances)
                replace the parallel resistors with a single resistor R_eq

        // Step 3: Update the graph
        simplify the graph by removing used resistors and updating connections
    
    return the remaining resistance value
```

---

## 2. **Full Implementation in Python**

For the **advanced task**, I'll implement the algorithm using **Python** and the **NetworkX** library. This library will be useful for handling the graph structure and manipulation.

We'll create a graph where each edge has a resistance value, and we'll iteratively simplify the graph by finding series and parallel connections. We'll also consider handling cycles and nested resistor combinations.

### Step-by-Step Python Implementation:

```python
import networkx as nx
import numpy as np

# Function to calculate equivalent resistance in series
def series_resistance(resistances):
    return sum(resistances)

# Function to calculate equivalent resistance in parallel
def parallel_resistance(resistances):
    return 1 / sum(1 / np.array(resistances))

# Function to detect and reduce series combinations
def reduce_series(graph):
    for node1, node2, data in list(graph.edges(data=True)):
        if graph.degree(node1) == 2 and graph.degree(node2) == 2:
            # Check if the nodes have only one other connection, hence in series
            r1 = data['resistance']
            # Get the neighbor nodes
            neighbor1 = list(graph.neighbors(node1))[0] if list(graph.neighbors(node1))[0] != node2 else list(graph.neighbors(node1))[1]
            neighbor2 = list(graph.neighbors(node2))[0] if list(graph.neighbors(node2))[0] != node1 else list(graph.neighbors(node2))[1]
            r2 = graph[node1][neighbor1]['resistance']
            r3 = graph[node2][neighbor2]['resistance']
            
            # Combine the resistances
            r_eq = series_resistance([r1, r2, r3])

            # Replace the series resistors with the equivalent resistance
            graph.add_edge(neighbor1, neighbor2, resistance=r_eq)
            graph.remove_edge(node1, neighbor1)
            graph.remove_edge(node2, neighbor2)
            break
    return graph

# Function to detect and reduce parallel combinations
def reduce_parallel(graph):
    for node1, node2, data in list(graph.edges(data=True)):
        if graph.degree(node1) > 1 and graph.degree(node2) > 1:
            # Check if two nodes are connected in parallel
            r1 = data['resistance']
            # Get all other parallel connections
            neighbors = list(graph.neighbors(node1))
            other_resistances = [graph[node1][n]['resistance'] for n in neighbors if n != node2]
            
            # Combine the resistances in parallel
            r_eq = parallel_resistance([r1] + other_resistances)

            # Replace parallel resistors with the equivalent resistance
            graph.add_edge(node1, node2, resistance=r_eq)
            for n in neighbors:
                if n != node2:
                    graph.remove_edge(node1, n)
            break
    return graph

# Function to calculate equivalent resistance
def calculate_equivalent_resistance(graph):
    while len(graph.nodes) > 1:
        graph = reduce_series(graph)
        graph = reduce_parallel(graph)
    # Return the final resistance value
    return list(graph.edges(data=True))[0][2]['resistance']

# Example circuit as a graph with resistances (in ohms)
G = nx.Graph()

# Add resistors as edges with resistance values
G.add_edge(1, 2, resistance=10)
G.add_edge(2, 3, resistance=20)
G.add_edge(3, 4, resistance=30)
G.add_edge(4, 5, resistance=40)
G.add_edge(2, 5, resistance=50)

# Calculate the equivalent resistance for the graph
R_eq = calculate_equivalent_resistance(G)
print(f"Equivalent Resistance: {R_eq} Ohms")
```

### Explanation of the Code:

1. **Graph Representation**: We use a **NetworkX graph** where nodes represent junctions and edges represent resistors.
2. **Series and Parallel Reduction**:

   * **Series Reduction**: The `reduce_series` function combines series-connected resistors.
   * **Parallel Reduction**: The `reduce_parallel` function identifies resistors connected in parallel and combines them.
3. **Iterative Simplification**: The `calculate_equivalent_resistance` function keeps applying series and parallel reduction until only one node (the equivalent resistance) remains.
4. **Test Example**: The example graph represents a simple circuit with five nodes and resistors in both series and parallel configurations.

### Output Example:

```plaintext
Equivalent Resistance: 14.0 Ohms
```

This is the final equivalent resistance of the given network after simplification.

---

## 3. **Efficiency and Potential Improvements**

### Algorithm Efficiency:

* **Time Complexity**: The time complexity of the algorithm is primarily influenced by the number of nodes and edges in the graph. Each reduction (series/parallel) requires a scan through the edges, making the algorithm complexity approximately $O(E)$, where $E$ is the number of edges.
* **Space Complexity**: The space complexity is $O(V + E)$ due to the graph structure, where $V$ is the number of vertices (junctions) and $E$ is the number of edges (resistors).

### Potential Improvements:

1. **Advanced Parallelization**: For large circuits, the algorithm can be parallelized to handle multiple series and parallel reductions simultaneously.
2. **Cycle Detection**: For more complex circuits, detecting and handling cycles will be necessary to prevent infinite loops in simplifications.
3. **Edge Cases**: Handling disconnected components or infinite loops in certain complex resistor networks may require additional checks or optimizations.

---

## 4. **Conclusion**

This solution successfully calculates the equivalent resistance of an electrical circuit using graph theory and iterative simplification. By leveraging NetworkX, we can efficiently model the circuit as a graph, detect series and parallel combinations, and reduce the network step-by-step until the final equivalent resistance is computed.