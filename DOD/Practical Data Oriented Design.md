[Reference](https://youtu.be/IroPQ150F6c?si=vciaK1h9MByxx7c_)
### Memory Footprint Reduction Strategies
- Use indexes instead of pointers
	- Switching to uint32 saves on x64 CPUs
- Store Booleans out-of-band
	- Switching to multiple data structures with implicit Boolean encoding removes extra bools.
- Eliminate padding with Struct-of-Arrays
	- Switching to a MultiArrayList (or similar data structure) can remove padding.
- Store sparse data in hash maps
	- Hash maps are good when data is sparse, allowing for fast lookups in rare cases.
- Use encodings
	- Using enums and extra information indices can help to encode information using memory dense data structures, which reduces bloat.

### Summary
![[Pasted image 20250103152303.png]]
- Add CPU Cache to your mental model of computers
- CPU is fast, but main memory is slow.
- Identify where you have many things in memory, and make the size of them smaller.
	- Many tools for this
		- Use indexes instead of pointers
			- Watch out for type safety
		- Store bools out-of-band
		- Eliminate padding with struct-of-arrays
		- Store sparse data in hash maps
		- Use encodings instead of OOP/Polymorphism
- These performance gains are massive