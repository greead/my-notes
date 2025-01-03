[Reference](https://youtu.be/WwkuAqObplU?si=Ua18fALwDR2cdC_b)
### Real-time Systems
Take input, deliver output within a deadline.
Physical resource constraints.
Known range of inputs and outputs.
Deadline of ~16ms for 60FPS.

![[Pasted image 20250102181611.png]]

### Rules for Fast Data
1. Efficient layout and storage
	1. Use smallest data types (fit more in cache line)
	2. Store data by locality (Store data used together)
	3. Don't waste the cache line
2. Single Task Repetition
	1. Do things in bulk when you can
	2. If you can repeat an operation multiple times on the same data, do it
	3. Should a function accept one object or an array?
3. Preprocess, Precompute, Bake
	1. Precompute and bake when possible
	2. Do things on startup before frame loop
	3. Prepare before real-time constraints
4. Parallelism*
	1. If you can run in parallel, do it
	2. Avoid synchronous work
	3. Cheap combination after asynchronous work
	4. Each CPU core has its own cache

### Practical Applications
**Before optimization**
Single GameObject class
```cs
class Enemy: GameObject {
	public bool dead;
	public string id;
	public bool frenzy;
	public Action<string> onDeath;

	public void Update() {
		if (dead) {
			onDeath(id);
		} else if (GlobalConfig.globalFrenzy || frenzy) {
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
}**
1.  Update manager paradigm: Single Class -> Data Class + Bulk Data Processing


```

**Update Manager Paradigm**
Data Class + Bulk Data Processing
**Why?** Processing data of the same kind all at once is much quicker.
```cs
// Data class
class Enemy {
	bool dead;
	string id;
	bool frenzy;
}

// Bulk data processsing function
void ProcessEnemies(List<Enemy> enemies) {
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		if (enemy.dead) {
			onDeath(enemy);
		} else if (GlobalConfig.globalFrenzy || frenzy) {
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
}
```

**Reduce use of the Observer Pattern****
Remove callback functions (delegates)
**Why?** Control what occurs and do not rely on another function to control processing.
```cs
// Data class
class Enemy {
	bool dead;
	string id;
	bool frenzy;
}

// Bulk data processsing function
void ProcessEnemies(List<Enemy> enemies) {
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		if (enemy.dead) {
			enemies.RemoveAt(i--); // Callback -> Inline processing
		} else if (GlobalConfig.globalFrenzy || frenzy) {
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
}
```

**Hoisting Invariants**
Move invariant data to the top of function calls
**Why?** Access an invariant once rather than multiple times.
```cs
// Data class
class Enemy {
	bool dead;
	string id;
	bool frenzy;
}

// Bulk data processsing function
void ProcessEnemies(List<Enemy> enemies) {
	var globalFrenzy = GlobalConfig.globalFrenzy; // Hoisted invariant access
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		if (enemy.dead) {
			enemies.RemoveAt(i--); // Callback -> Inline processing
		} else if (globalFrenzy || frenzy) { // Access local invariant
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
}
```

**Convert Data Classes to Structs**
Change data classes into structs
**Why?** An array of structs are located in-place, but an array of classes are pointers to a heap.
```cs
// Data class -> Data struct
struct Enemy {
	bool dead;
	string id;
	bool frenzy;
}

// Bulk data processsing function
void ProcessEnemies(List<Enemy> enemies) {
	var globalFrenzy = GlobalConfig.globalFrenzy; // Hoisted invariant access
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		if (enemy.dead) {
			enemies.RemoveAt(i--); // Callback -> Inline processing
		} else if (globalFrenzy || frenzy) { // Access local invariant
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
}
```

**Memory Alignment**
Rearrange declaration of struct elements to maximize memory efficiency
**Why?** Values in a struct must all have the same memory size, so different size items leads to lots of waste. The size of the memory blocks is equivalent to the largest necessary size. For instance, a struct with a string (8 bytes) would require a minimum of 8 bytes of space, even for accompanying bools. Rearranging declaration order can allow smaller items to occupy the same memory block instead of wasting blocks.
Order largest to smallest to guarantee packing.
```cs
// Data class -> Data struct
struct Enemy {
	string id; // Alignment (8 of 8x1 vs 8 of 8x2)
	bool dead; // Alignment (2 of 8x2 vs 2 of 8x1)
	bool frenzy; // Alignment (4 of 8x2 vs 2 of 8x3)
	
	// Original register [X| | | | | | | ][X|X|X|X|X|X|X|X][X| | | | | | | ]
	// Total space: 24b; Used space: 10b; Wasted space: 14b
	
	// New register [X|X|X|X|X|X|X|X][X|X| | | | | | ]
	// Total space: 16b; Used space: 10b; Wasted space: 6b
}

// Bulk data processsing function
void ProcessEnemies(List<Enemy> enemies) {
	var globalFrenzy = GlobalConfig.globalFrenzy; // Hoisted invariant access
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		if (enemy.dead) {
			enemies.RemoveAt(i--); // Callback -> Inline processing
		} else if (globalFrenzy || frenzy) { // Access local invariant
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
}
```

**Enum Encoding of Booleans**
Encode valid states as enums rather than multiple Booleans
**Why?** Addition of Booleans is a combinatorial increase in states, switch to enums to reduce states
```cs
// Encode Boolean states as an Enum
enum EnemyState {
	ALIVE_NORMAL,
	ALIVE_FRENZY,
	DEAD
}

// Data class -> Data struct
struct Enemy {
	string id; // Alignment (8 of 8x1)
	EnemyState state; // Alignment (4 of 8x2)
	// Original register [X| | | | | | | ][X|X|X|X|X|X|X|X][X| | | | | | | ]
	// Total space: 24b; Used space: 10b; Wasted space: 14b
	
	// New register [X|X|X|X|X|X|X|X][X|X|X|X| | | | ]
	// Total space: 16b; Used space: 12b; Wasted space: 4b
}

// Bulk data processsing function
void ProcessEnemies(List<Enemy> enemies) {
	var globalFrenzy = GlobalConfig.globalFrenzy; // Hoisted invariant access
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		if (enemy.dead) {
			enemies.RemoveAt(i--); // Callback -> Inline processing
		} else if (globalFrenzy || frenzy) { // Access local invariant
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
}
```

**Enum Sizing**
Reduce size of enum to only be as large as necessary
**Why?** Enums have a larger size than necessary, if able, we can reduce the size for a speed increase.
```cs
// Encode Boolean states as an Enum
enum EnemyState : byte { // Size the container
	ALIVE_NORMAL,
	ALIVE_FRENZY,
	DEAD
}

// Data class -> Data struct
struct Enemy {
	string id; // Alignment (8 of 8x1)
	EnemyState state; // Alignment (1 of 8x2)
	// Original register [X| | | | | | | ][X|X|X|X|X|X|X|X][X| | | | | | | ]
	// Total space: 24b; Used space: 10b; Wasted space: 14b
	
	// New register [X|X|X|X|X|X|X|X][X| | | | | | | ]
	// Total space: 16b; Used space: 9b; Wasted space: 7b
}

// Bulk data processsing function
void ProcessEnemies(List<Enemy> enemies) {
	var globalFrenzy = GlobalConfig.globalFrenzy; // Hoisted invariant access
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		if (enemy.dead) {
			enemies.RemoveAt(i--); // Callback -> Inline processing
		} else if (globalFrenzy || frenzy) { // Access local invariant
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
}
```

**Remove Strings**
Replace strings with equivalent structures
**Why?** Strings are huge and often don't need as much space as they request. They can increase by a lot in size over time, so it is best to use non-string structures if a human isn't expected to read it.
```cs
// Encode Boolean states as an Enum
enum EnemyState : byte { // Size the container
	ALIVE_NORMAL,
	ALIVE_FRENZY,
	DEAD
}

// Data class -> Data struct
struct Enemy {
	uint id; // Alignment (4 of 4x1)
	EnemyState state; // Alignment (1 of 4x2)
	// Original register [X| | | | | | | ][X|X|X|X|X|X|X|X][X| | | | | | | ]
	// Total space: 24b; Used space: 10b; Wasted space: 14b
	
	// New register [X|X|X|X][X| | | ]
	// Total space: 8b; Used space: 5b; Wasted space: 3b
}

// Bulk data processsing function
void ProcessEnemies(List<Enemy> enemies) {
	var globalFrenzy = GlobalConfig.globalFrenzy; // Hoisted invariant access
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		if (enemy.dead) {
			enemies.RemoveAt(i--); // Callback -> Inline processing
		} else if (globalFrenzy || frenzy) { // Access local invariant
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
}
```

**Bulk Tasks**
Do tasks that occur all at once in one action if possible.
**Why?** Doing the same task multiple times often incurs extra performance hits, but doing it all at once can increase performance in many cases.
```cs
// Encode Boolean states as an Enum
enum EnemyState : byte { // Size the container
	ALIVE_NORMAL,
	ALIVE_FRENZY,
	DEAD
}

// Data class -> Data struct
struct Enemy {
	uint id; // Alignment (4 of 4x1)
	EnemyState state; // Alignment (1 of 4x2)
	// Original register [X| | | | | | | ][X|X|X|X|X|X|X|X][X| | | | | | | ]
	// Total space: 24b; Used space: 10b; Wasted space: 14b
	
	// New register [X|X|X|X][X| | | ]
	// Total space: 8b; Used space: 5b; Wasted space: 3b
}

// Bulk data processsing function
void ProcessEnemies(List<Enemy> enemies) {
	var globalFrenzy = GlobalConfig.globalFrenzy; // Hoisted invariant access
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		// Stop checking and removing enemies at each loop
		if (globalFrenzy || frenzy) { // Access local invariant
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
	// Remove all dead enemies all at once.
	enemies.RemoveAll(e => e.dead);
}
```

**Implement Faster Data Structures (Swapback Array)**
Switch to more relevant data structures when possible. 
**Why?** Many data structures have hidden costs associated with them. For instance, a list must maintain an order, but maintaining order has a cost (removal and compaction). If we don't care about order, we can use another structure like a "swapback array" which is much faster due to not needing to maintain order.
```cs
// Encode Boolean states as an Enum
enum EnemyState : byte { // Size the container
	ALIVE_NORMAL,
	ALIVE_FRENZY,
	DEAD
}

// Data class -> Data struct
struct Enemy {
	uint id; // Alignment (4 of 4x1)
	EnemyState state; // Alignment (1 of 4x2)
	// Original register [X| | | | | | | ][X|X|X|X|X|X|X|X][X| | | | | | | ]
	// Total space: 24b; Used space: 10b; Wasted space: 14b
	
	// New register [X|X|X|X][X| | | ]
	// Total space: 8b; Used space: 5b; Wasted space: 3b
}

class SwapbackArray<T> {
	// Implementation
	// On removal, swap last element with element to remove then reduce count
}

// Bulk data processsing function
void ProcessEnemies(SwapbackArray<Enemy> enemies) { // Switch data structures
	var globalFrenzy = GlobalConfig.globalFrenzy; // Hoisted invariant access
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		// Stop checking and removing enemies at each loop
		if (globalFrenzy || frenzy) { // Access local invariant
			// HANDLE frenzy		
		} else {
			// Standard behavior
		}
	}
	// Remove all dead enemies all at once.
	enemies.RemoveAll(e => e.dead);
}
```

**Avoid Last-Minute Decision Making (Taking information out-of-band)**
Split processing into separate "channels" of processing
**Why?** Switching operations using last-minute decision structures (if-statements) can lead to lots of task-swapping, but we can instead group operations together with the data that needs to be operated on to do the same operations in bulk.
```cs
// Now unnecessary
// Encode Boolean states as an Enum
// enum EnemyState : byte { // Size the container
//     ALIVE_NORMAL,
//     ALIVE_FRENZY,
//	   DEAD
// }

// Data class -> Data struct
struct Enemy {
	uint id; // Alignment (4 of 4x1)
	// EnemyState state; Now unnecessary
	// Original register [X| | | | | | | ][X|X|X|X|X|X|X|X][X| | | | | | | ]
	// Total space: 24b; Used space: 10b; Wasted space: 14b
	
	// New register [X|X|X|X]
	// Total space: 4b; Used space: 4b; Wasted space: 0b
}

class SwapbackArray<T> {
	// Implementation
	// On removal, swap last element with element to remove then reduce count
}

// Arrays that also maintain state and process
// Membership now infers state
var enemyNormal = new SwapbackArray<Enemy>(N);
var enemyFrenzy = new SwapbackArray<Enemy>(N);
var enemyDead = new SwapbackArray<Enemy>(N);

// Bulk data processsing function
void ProcessNormalEnemies(SwapbackArray<Enemy> enemies) { // Switch data structures
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		// Standard behavior
	}
}

void ProcessFrenzyEnemies(SwapbackArray<Enemy> enemies) {
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		// HANDLE frenzy		
	}
}

void ProcessDeadEnemies(SwapbackArray<Enemy> enemies) {
	enemies.RemoveAll(e => e.dead);
}
```

**Simplify**
Some things can be simplified now.
**Why?** The change of structures, data, processing, and etc. allows for simplification on many fronts.
```cs
// Now unnecessary
// Encode Boolean states as an Enum
// enum EnemyState : byte { // Size the container
//     ALIVE_NORMAL,
//     ALIVE_FRENZY,
//	   DEAD
// }

// Data class -> Data struct
// Struct is now unneccesary
// struct Enemy {
//	   uint id; // Alignment (4 of 4x1)
	// EnemyState state; Now unnecessary
	// Original register [X| | | | | | | ][X|X|X|X|X|X|X|X][X| | | | | | | ]
	// Total space: 24b; Used space: 10b; Wasted space: 14b
	
	// New register [X|X|X|X]
	// Total space: 4b; Used space: 4b; Wasted space: 0b
// }

class SwapbackArray<T> {
	// Implementation
	// On removal, swap last element with element to remove then reduce count
}

// Arrays that also maintain state and process
// Membership now infers state
var enemyNormal = new SwapbackArray<uint>(N); // Swap to uint ID
var enemyFrenzy = new SwapbackArray<uint>(N); // Swap to uint ID
var enemyDead = new SwapbackArray<uint>(N);   // Swap to uint ID

// Bulk data processsing function
void ProcessNormalEnemies(SwapbackArray<uint> enemies) { // Switch data structures
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		// Standard behavior
	}
}

void ProcessFrenzyEnemies(SwapbackArray<uint> enemies) {
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		// HANDLE frenzy		
	}
}

// Simple inline action now
// void ProcessDeadEnemies(SwapbackArray<uint> enemies) {
//  	enemies.RemoveAll(e => e.dead);
// }

void ProcessEnemies(
	SwapbackArray<uint> normalEnemies, 
	SwapbackArray<uint> frenzyEnemies, 
	SwapbackArray<uint> deadEnemies
	) {
		ProcessNormalEnemies(normalEnemies);
		ProcessFrenzyEnemies(frenzyEnemies);
		deadEnemies.clear();	
	}
```

**Final Implementation**
```cs
class SwapbackArray<T> {
	// Implementation
	// On removal, swap last element with element to remove then reduce count
}

var enemyNormal = new SwapbackArray<uint>(N); // Swap to uint ID
var enemyFrenzy = new SwapbackArray<uint>(N); // Swap to uint ID
var enemyDead = new SwapbackArray<uint>(N);   // Swap to uint ID

void ProcessNormalEnemies(SwapbackArray<uint> enemies) { // Switch data structures
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		// Standard behavior
	}
}

void ProcessFrenzyEnemies(SwapbackArray<uint> enemies) {
	for (int i = 0; i < enemies.count; i++) {
		var enemy = enemies[i];
		// HANDLE frenzy		
	}
}

void ProcessEnemies(
	SwapbackArray<uint> normalEnemies, 
	SwapbackArray<uint> frenzyEnemies, 
	SwapbackArray<uint> deadEnemies
	) {
		ProcessNormalEnemies(normalEnemies);
		ProcessFrenzyEnemies(frenzyEnemies);
		deadEnemies.clear();	
	}
```
