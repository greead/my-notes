- Source (Original Code)
- Tokenizer (Source -> Tokens)
[Source](https://mitchellh.com/zig/tokenizer)
The structure of the tokenizer is equally simple (as shown below). The tokenizer stores the buffer, the current index into the buffer (starts at zero), and a potential invalid token (will be ignored in this article).
```c
pub const Tokenizer = struct { 
	buffer: [:0]const u8, 
	index: usize, 
	pending_invalid_token: ?Token, 
	// ... 
};
```

- Tokens
```c
pub const Token = struct { 
	tag: Tag, 
	loc: Loc, 
	
	pub const Loc = struct { 
		start: usize, 
		end: usize, 
	}; 
	
	pub const Tag = enum { 
		invalid, 
		// ... 
	}; 
};
```
- Parser (Tokens -> AST)
[Source](https://mitchellh.com/zig/parser)
```c
const Parser = struct { 
	gpa: Allocator, 
	source: []const u8, 
	
	token_tags: []const Token.Tag, 
	token_starts: []const Ast.ByteOffset, 
	tok_i: TokenIndex, 
	
	errors: std.ArrayListUnmanaged(AstError), 
	nodes: Ast.NodeList, 
	extra_data: std.ArrayListUnmanaged(Node.Index), 
	scratch: std.ArrayListUnmanaged(Node.Index), 
};
```
- AST (Abstract Syntax Tree)
Node
```c
pub const Node = struct { 
	tag: Tag, 
	main_token: TokenIndex, 
	data: Data, 
	
	pub const Data = struct { 
		lhs: Index, 
		rhs: Index, 
	}; 
	
	pub const Index = u32;
};
```

AST
```c
pub const Ast = struct {
    source: [:0]const u8,
    tokens: TokenList.Slice,
    nodes: NodeList.Slice,
    extra_data: []Node.Index,
    errors: []const Error,
};
```
- AstGen (AST -> ZIR)
[Source](https://mitchellh.com/zig/astgen)

- ZIR (Zig Intermediate Representation)
- Sema (ZIR -> AIR)
[Source](https://mitchellh.com/zig/sema)
- AIR (Analyzed Intermediate Representation)
- CodeGen (AIR -> MIR; Backend Representation)
	Direct to Machine Code
	- LLVM Codegen -> Machine Code
	MIR
	- WASM Codegen -> WASM MIR
	- x86 Codegen -> x86 MIR
	- ARM Codegen -> ARM MIR
	- RISC-V Codegen -> RISC-V MIR
	Other intermediary
	- C Codegen -> C Code
	- SPIR-V Codegen -> SPIR-V Code
- MIR (Machine Intermediate Representation)
- Emit
	- WASM MIR -> ISEL -> Web Assembly
	- x86 MIR -> ISEL -> x86 Machine Code
	- ARM MIR -> ISEL -> ARM Machine Code
	- RISC-V MIR -> ISEL -> RISC-V Machine Code
- Machine Code