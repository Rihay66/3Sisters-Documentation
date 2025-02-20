A Component is represented as a data structure that only contains data. So to create a component it would look likes this:

```cpp
// example component with data
struct Component{
	int value;
	float timer;
};
```

These components can be attached to an entity or numerous entities. Each entity can have the same component but they'll have a different instance of that same component, meaning similar components attached to different entities don't affect each other when a change only happens to one component or entity.

Within the [[ECS]]
(explain signatures and component types)
(show ECS functions that involve the component)