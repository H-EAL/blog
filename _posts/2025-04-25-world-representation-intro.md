---
title: "World Representation"
date: 2025-04-25
---

# Chapter 1: Virtual Memory

---

[Introduction](https://www.notion.so/Introduction-1daa9d0ec63280069f87f329bd863b49?pvs=21)

---

# Implementation

## The Case for Arrays

When it comes to representing a virtual world, data access patterns and memory usage quickly become critical. That’s why we opted for the most basic data structure available: the array.

Let’s break down why.

Arrays are:

- 📍 **Contiguous in memory** – ideal for cache efficiency and spatial locality.
- 🔒 **Fixed in size** – predictable, but rigid.
- 🧬 **Homogeneous** – each element is the same type.
- 🎲 **Random-accessible** – any element can be accessed in constant time.
- 🧭 **Consistently addressed** – index and pointer stability throughout the element's lifetime.

All this makes arrays a near-perfect fit for high-performance systems like game engines and simulations—especially when you’re dealing with thousands or even millions of entities every frame.

Now layer on the real-time constraint: if you're targeting 30–90 FPS, that leaves you with roughly 10–33ms per frame to run your entire pipeline. Efficient memory access isn't a luxury—it's a requirement.

But… arrays have one big drawback: their size is fixed.

And that’s where **virtual memory** comes in.

---

## Virtual Memory to the Rescue

When you allocate memory in a program, you're not working directly with physical RAM. Instead, you're interacting with **virtual memory**—a layer of indirection managed by the OS that maps virtual addresses to physical ones.

With virtual memory, you can reserve large address ranges without immediately consuming physical memory. The physical pages are only committed (i.e., backed by actual RAM) when needed. That’s perfect for sparse data structures like component arrays where most of the data might be unused or yet to be filled.

In other words: with virtual memory, we can allocate “virtually infinite” arrays without paying for them up front.

---

### Allocation Granularity

Virtual memory works in pages. On modern systems, a page is typically 4 KiB. You can’t commit less than one page, and any memory that crosses a page boundary commits both pages.

That means:

- Allocating 2 bytes that span 2 pages? That’s 8 KiB gone.
- Committing partial pages? Not a thing.
- But reserving gigabytes up front? No problem, until you actually touch them.

![shapes at 25-04-25 09.50.26.png](shapes_at_25-04-25_09.50.26.png)

---

### Virtual Memory on Windows

Here’s how to manage virtual memory on Windows:

### Reserve address space (no physical memory yet):

```cpp
void *reserve(int64_t sizeInByte) {
    return VirtualAlloc(nullptr, sizeInByte, MEM_RESERVE, PAGE_READWRITE);
}
```

### Commit memory (backed by physical RAM):

```cpp
void *commit(void *pAddr, int64_t size) {
    return VirtualAlloc(pAddr, size, MEM_COMMIT, PAGE_READWRITE);
}
```

### Reset memory (releases physical RAM but keeps the address):

```cpp
void *reset(void *pAddr, int64_t size) {
    return VirtualAlloc(pAddr, size, MEM_RESET, PAGE_NOACCESS);
}
```

### Release memory and address space completely:

```cpp
bool deallocate(void *pAddr) {
    return VirtualFree(pAddr, 0, MEM_RELEASE) == TRUE;
}
```

---

## Why This Matters

This system gives us the best of both worlds:

- Predictable memory layout
- Fast access
- Virtually unbounded size
- Sparse usage efficiency

For a virtual world engine that needs to represent and simulate complex environments in real time, these tradeoffs are absolutely worth it.

Next up: we’ll dive into how we leverage virtual memory to build our data structure: the virtual array.

---

[Chapter 2: Virtual Array](https://www.notion.so/Chapter-2-Virtual-Array-1daa9d0ec6328032a6e7dbbedeea5cde?pvs=21)

---
