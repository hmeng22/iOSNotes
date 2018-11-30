# Gameplay

https://developer.apple.com/library/ios/documentation/General/Conceptual/GameplayKit_Guide/

https://developer.apple.com/library/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/

* Entities and Components
* State Machines
* The Minmax Strategist
* Randomization
* Pathfinding
* Agents, Goals, and Behaviors
* Rule Systems



## Entities and Components

```objectivec
GKEntity
```
```objectivec
GKComponentSystem
	|- GKComponent
		|- GKAgent (Agents represent an object's position, size, and velocity)
			|- GKAgent2D
```
```objectivec
GKComponent *c = GKComponent
c.node = node;
[c func];
[node.entity addComponent(c)];
```

## State Machines

```objectivec
GKState
	|- isValidNextState()
	|- didEnterWithPreviousState()
```
```objectivec
GKStateMachine
```

```objectivec
NSNotificationCenter *defaultCenter = [NSNotification defaultCenter];
[defaultCenter postNotificationName];
```





## Agents, Behaviors, and Goal

```objectivec
GKAgent & GKAgent2D <GKAgentDelegate>
	|- agentWillUpdate
	|- agentDidUpdate
```

```objectivec
GKBehavior
```

```objectivec
GKGoal
```





## Pathfinding

```objectivec
GKGraph
	|- GKGraphNode
	|
	|- GKObstacleGraph
	|	|- GKGraphNode2D
	|	|- GKObstacle
	|		|- GKCircleObstacle
	|		|- GKPolygonObstacle
	|- GKGridGraph
		|- GKGridGraphNode
```





## Random Value Generators

```objectivec
GKRandom
	|- GKRandomSource
	|	|- GKARC4RandomSource
	|	|- GKLinearCongruentialRandomSource
	|	|- GKMersenneTwisterRandomSource
	|- GKGaussianDistribution
		|- GKGaussianDistribution
		|- GKShuffledDistribution
```





## Rule System

```objectivec
GKRuleSystem
	|- GKRule
```

