---
theme: default
title: JS plugins
class: text-center
drawings:
  persist: false
transition: slide-left
mdc: true
---

<style>
h1 {
  font-size: 3em !important;
  font-weight: bold;
}

.slidev-vclick-target {
  transition: opacity 700ms ease !important;
}
</style>

# Oxc 1.0

## 5 item wish list

<div style="display: flex; justify-content: center; align-items: center; gap: 40px; margin-top: 40px;">
  <img src="./images/oxc-logo.svg" style="width: 300px; height: 300px;" />
</div>

---
transition: slide-left
---

# Aims

<div style="font-size: 2em;">

* Simpler APIs - easier for humans and agents
* Breaking changes needed for performance
* Polish data structures

</div>

---
transition: none
---

# 1. AST

````md magic-move
```rs
pub struct Function<'a> {
    pub node_id: Cell<NodeId>,
    pub span: Span,
    pub r#type: FunctionType,
    pub id: Option<BindingIdentifier<'a>>,
    pub params: Box<'a, FormalParameters<'a>>,
    pub body: Option<Box<'a, FunctionBody<'a>>>,
    pub scope_id: Cell<Option<ScopeId>>,
}
```
```rs
pub struct FunctionDeclaration<'a> {
    pub node_id: Cell<NodeId>,
    pub span: Span,
    pub id: BindingIdentifier<'a>,
    pub params: Box<'a, FormalParameters<'a>>,
    pub body: Option<Box<'a, FunctionBody<'a>>>,
    pub scope_id: Cell<Option<ScopeId>>,
}

pub struct FunctionExpression<'a> {
    pub node_id: Cell<NodeId>,
    pub span: Span,
    pub id: Option<BindingIdentifier<'a>>,
    pub params: Box<'a, FormalParameters<'a>>,
    pub body: Box<'a, FunctionBody<'a>,
    pub scope_id: Cell<Option<ScopeId>>,
}
```
````

---
transition: slide-left
---

# 1. AST

````md magic-move
```rs
pub struct FunctionDeclaration<'a> {
    pub node_id: Cell<NodeId>,
    pub span: Span,
    pub id: BindingIdentifier<'a>,               // Not optional, bound in outer scope
    pub params: Box<'a, FormalParameters<'a>>,
    pub body: Option<Box<'a, FunctionBody<'a>>>, // Optional
    pub scope_id: Cell<Option<ScopeId>>,
}

pub struct FunctionExpression<'a> {
    pub node_id: Cell<NodeId>,
    pub span: Span,
    pub id: Option<BindingIdentifier<'a>>,        // Optional, bound in own scope
    pub params: Box<'a, FormalParameters<'a>>,
    pub body: Box<'a, FunctionBody<'a>,           // Not optional
    pub scope_id: Cell<Option<ScopeId>>,
}
```
````

<v-click at="1">

* Ditto `ClassDeclaration` + `ClassExpression`
* Matches ESTree
* Stop having to `.unwrap()` on `id`

</v-click>

---
transition: slide-left
clicks: 6
---

# 2. Pointer tagging

<div style="display: flex; gap: 24px;">
<div style="flex: 1;">

```rs
enum Expression<'a> {
    BooleanLiteral(Box<'a, BooleanLiteral>),
    NullLiteral(Box<'a, NullLiteral>),
    NumericLiteral(Box<'a, NumericLiteral<'a>>),
    BigIntLiteral(Box<'a, BigIntLiteral<'a>>),
    RegExpLiteral(Box<'a, RegExpLiteral<'a>>),
    StringLiteral(Box<'a, StringLiteral<'a>>),
    TemplateLiteral(Box<'a, TemplateLiteral<'a>>),
    // ...
}
```

</div>
<div v-click="4" style="flex: 1;">

````md magic-move {at:5}
```rs
struct BinaryExpression<'a> {
    node_id: u32,
    span: Span,
    left: Expression<'a>,
    operator: BinaryOperator,
    right: Expression<'a>,
}
```
```rs
struct BinaryExpression<'a> {
    node_id: u32,
    span: Span,
    left: Compact<Expression<'a>>,
    operator: BinaryOperator,
    right: Compact<Expression<'a>>,
}
```
````

<div style="font-family: monospace; font-size: 1.5em; font-weight: bold; color: #aaa; margin-top: 8px; text-align: center;">
  {{ $clicks >= 5 ? '32 bytes' : '48 bytes' }}
</div>

</div>
</div>

<div style="margin-top: 32px; font-family: monospace; font-size: 0.75em;">
  <div style="display: flex; align-items: flex-start; gap: 32px;">
  <div>
  <div style="position: relative; display: inline-block;">
    <div style="display: flex; border-radius: 6px; overflow: hidden;">
      <div :style="{ display: 'flex', opacity: $clicks >= 3 ? 0 : 1, transition: 'opacity 0.4s ease' }">
        <div style="width: 40px; height: 48px; background: repeating-linear-gradient(45deg, #2a2a2a, #2a2a2a 3px, #444 3px, #444 4px); border-right: 1px solid #555;"></div>
        <div style="width: 40px; height: 48px; background: repeating-linear-gradient(45deg, #2a2a2a, #2a2a2a 3px, #444 3px, #444 4px); border-right: 1px solid #444;"></div>
        <div style="width: 40px; height: 48px; background: repeating-linear-gradient(45deg, #2a2a2a, #2a2a2a 3px, #444 3px, #444 4px); border-right: 1px solid #444;"></div>
        <div style="width: 40px; height: 48px; background: repeating-linear-gradient(45deg, #2a2a2a, #2a2a2a 3px, #444 3px, #444 4px); border-right: 1px solid #444;"></div>
        <div style="width: 40px; height: 48px; background: repeating-linear-gradient(45deg, #2a2a2a, #2a2a2a 3px, #444 3px, #444 4px); border-right: 1px solid #444;"></div>
        <div style="width: 40px; height: 48px; background: repeating-linear-gradient(45deg, #2a2a2a, #2a2a2a 3px, #444 3px, #444 4px); border-right: 1px solid #444;"></div>
        <div style="width: 40px; height: 48px; background: repeating-linear-gradient(45deg, #2a2a2a, #2a2a2a 3px, #444 3px, #444 4px); border-right: 1px solid #444;"></div>
        <div style="width: 40px; height: 48px; background: repeating-linear-gradient(45deg, #2a2a2a, #2a2a2a 3px, #444 3px, #444 4px); border-right: 1px solid #555;"></div>
      </div>
      <div style="width: 40px; height: 48px; background: #34b99d; border-right: 1px solid #4a7a4a;"></div>
      <div style="width: 40px; height: 48px; background: #34b99d; border-right: 1px solid #4a7a4a;"></div>
      <div style="width: 40px; height: 48px; background: #34b99d; border-right: 1px solid #4a7a4a;"></div>
      <div style="width: 40px; height: 48px; background: #34b99d; border-right: 1px solid #4a7a4a;"></div>
      <div style="width: 40px; height: 48px; background: #34b99d; border-right: 1px solid #4a7a4a;"></div>
      <div style="width: 40px; height: 48px; background: #34b99d; border-right: 1px solid #4a7a4a;"></div>
      <div style="width: 40px; height: 48px; background: #34b99d; border-right: 1px solid #4a7a4a;"></div>
      <div style="width: 40px; height: 48px; position: relative; background: #34b99d;">
        <div v-click="1" style="position: absolute; inset: 0; background: repeating-linear-gradient(45deg, #2a2a2a, #2a2a2a 3px, #444 3px, #444 4px);"></div>
      </div>
    </div>
    <!-- Border overlay: surrounds all 16 cells normally, shrinks to bytes 8-15 at click 3 -->
    <div :style="{
      position: 'absolute',
      top: '-2px', bottom: '-2px',
      left: $clicks >= 3 ? '318px' : '-2px',
      right: '-2px',
      border: '2px solid #555',
      borderRadius: '6px',
      pointerEvents: 'none',
      zIndex: 3,
    }"></div>
    <!-- Green discriminant cell: slides from byte 0 to byte 15 on click 2 -->
    <div :style="{
      position: 'absolute', top: '0', left: '0',
      width: '40px', height: '48px',
      background: '#00bc5e',
      zIndex: 10,
      transform: $clicks >= 2 ? 'translateX(600px)' : 'translateX(0)',
      transition: $clicks >= 2 ? 'transform 0.6s ease' : 'none',
    }"></div>
  </div>
  <!-- Byte index labels -->
  <div style="display: flex; font-size: 0.7em; margin-top: 8px; color: #aaa;">
    <div :style="{ display: 'flex', opacity: $clicks >= 3 ? 0 : 1, transition: 'opacity 0.4s ease' }">
      <div style="width: 40px; text-align: center;">0</div>
      <div style="width: 40px; text-align: center;">1</div>
      <div style="width: 40px; text-align: center;"></div>
      <div style="width: 40px; text-align: center;"></div>
      <div style="width: 40px; text-align: center;"></div>
      <div style="width: 40px; text-align: center;"></div>
      <div style="width: 40px; text-align: center;"></div>
      <div style="width: 40px; text-align: center;">7</div>
    </div>
    <div style="width: 40px; text-align: center;">{{ $clicks >= 3 ? '0' : '8' }}</div>
    <div style="width: 40px; text-align: center;"></div>
    <div style="width: 40px; text-align: center;"></div>
    <div style="width: 40px; text-align: center;"></div>
    <div style="width: 40px; text-align: center;"></div>
    <div style="width: 40px; text-align: center;"></div>
    <div style="width: 40px; text-align: center;"></div>
    <div style="width: 40px; text-align: center;">{{ $clicks >= 3 ? '7' : '15' }}</div>
  </div>
  <!-- Field annotations -->
  <div style="display: flex; margin-top: 6px;">
    <div :style="{ display: 'flex', opacity: $clicks >= 3 ? 0 : 1, transition: 'opacity 0.4s ease' }">
      <div style="width: 40px; text-align: center; color: #00e676;">Disc rim</div>
      <div style="width: 280px; text-align: center; color: #666;">padding</div>
    </div>
    <div style="width: 280px; text-align: center; color: #34b99d;">Pointer</div>
    <div v-click="1" style="width: 40px; text-align: center;">
      <span :style="{ color: $clicks >= 2 ? '#00e676' : '#aaa' }">{{ $clicks >= 2 ? 'Disc rim' : 'High bits' }}</span>
    </div>
  </div>
  </div>
  <div style="align-self: center; margin-top: -70px; font-size: 2em; font-weight: bold; color: #aaa; white-space: nowrap;">
    {{ $clicks >= 3 ? '8 bytes' : '16 bytes' }}
  </div>
  </div>
  <div v-click="6" style="margin-top: 10px; font-size: 3em; font-weight: bold; color: #66bb6a;">
    15%-20% reduction in AST memory
  </div>
  <div v-click="6">
    <a href="https://github.com/oxc-project/backlog/issues/91" target="_blank">https://github.com/oxc-project/backlog/issues/91</a>
  </div>
</div>

---
transition: fade-out
---

# 3. Semantic

<div style="font-size: 1.4em;">

* Methods on types, not on `Semantic` / `Scoping`
* `NodeId`s in AST nodes unlocks a lot

</div>

<div style="font-size: 1.2em;">

Before:

```rs
let ast_node: AstNode = /* ... */;
let parent: AstKind = ctx.nodes.parent_kind(ast_node.id());
```

After:

```rs
let func: Function = /* ... */;
let parent: AstKind = func.parent(ctx);
```

* `AstNode` can be removed from codebase - no longer needed

</div>

<style>
pre code { font-size: 1.8em; }
</style>

---
transition: slide-left
---

# 3. Semantic

<div style="font-size: 1.4em;">

* Methods on types, not on `Semantic` / `Scoping`
* `NodeId`s in AST nodes unlocks a lot

</div>

<div style="font-size: 1.2em;">

Before:

```rs
let func: Function = /* ... */;
let scope_id: ScopeId = func.scope_id();
let parent_scope_id: ScopeId = ctx.scoping.parent_scope_id(node_id);
let parent_scope_flags: ScopeFlags = ctx.scoping.scope_flags(parent_scope_id);
```

After:

```rs
let func: Function = /* ... */;
let parent_scope_flags: ScopeFlags = func.parent_scope(ctx).flags;
```

</div>

<style>
pre code { font-size: 1.5em; }
</style>

---
transition: fade-out
---

# 4. Ast type

<div style="font-size: 2em;">

* Now: Allocator owns AST
* Instead: AST owns Allocator

</div>

---
transition: fade-out
---

# 4. Ast type

<div style="font-size: 1.5em;">

## **Now:**

```rs
let allocator = Allocator::default();

let parser_ret = Parser::new(&allocator, source_text, source_type).with_options(parse_options).parse();
assert!(parser_ret.errors.is_empty());
let program = parser_ret.program;

let semantic_ret = SemanticBuilder::new().build(&program);
assert!(semantic_ret.errors.is_empty());
let scoping = semantic_ret.semantic.into_scoping();

let trans_ret = Transformer::new(&allocator, path, transform_options).build_with_scoping(scoping, &mut program);
assert!(trans_ret.errors.is_empty());

Minifier::new(options).minify(&allocator, &mut program);

let mangler_ret = Mangler::new().with_options(mangle_options).build(&program);

let out = Codegen::new().with_options(codegen_options).with_scoping(Some(mangler_ret.scoping))
  .with_private_member_mappings(Some(mangler_ret.class_private_mappings)).build(&program);
```

</div>

<style>
pre code { font-size: 1.05em; }
</style>

---
transition: fade-out
---

# 4. Ast type

<div style="font-size: 1.5em;">

## **Proposed:**

```rs
let source: Source = Source::from_file(path).unwrap();

let ast: Ast = source.parse_with_options(parse_options).unwrap();

let ast: SemanticAst = ast.semantic().unwrap();

let mut ast: ScopedAst = ast.transform(transform_options).unwrap();

ast.minify(minify_options);

ast.mangle(mangle_options);

let out: CodegenReturn = ast.print(codegen_options);
```

</div>

<style>
pre code { font-size: 1.65em; }
</style>

---
transition: slide-left
---

# 4. Ast type

<div style="font-size: 1.5em;">

## **Why?**

* Simpler API (sugar)
* Everything held together in one object
* `Ast` is lifetime-less
* Branded lifetimes - remove all bounds checks
* Remove `Cell`s from AST - AST becomes `Sync`

</div>

---
transition: slide-left
---

# 5. Allocator

<div style="font-size: 1.5em;">

* Unify 2 `Allocator` impls
* All `Allocator`s support raw transfer
  * Unblocks Rolldown from using raw transfer for transform plugins
  * Fix Windows OOM in Oxlint
* AST and Semantic share strings arena
* Shared "scratch" allocator
* 1 `Allocator` per thread - good for CPU cache
* Bounds check-less allocation
* On disc AST caching with `mmap` (Rolldown)

</div>

---
transition: slide-left
---

# How we do it

<div style="font-size: 1.6em;">

* Boris Tane "How I Use Claude Code" https://boristane.com/blog/how-i-use-claude-code/
* Design docs committed to repo
* Revise designs via PRs to design docs
* Design -> Implementation plan - also committed
* Set Claude on it, but step-by-step.
* Keep human review.
* Custom Clippy rules, style guide, etc.

</div>
