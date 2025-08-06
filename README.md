## Thinking in Hoses and Wires, Not Bolted Connections: Protocol-Driven Development

***A rediscovery of message-passing systems, inspired by Smalltalk and the struggles of real-world application design.***


Most systems are built like cars: rigid, bolted together, and prone to break when you change a part. But what if we built them like hydraulic machines — with flexible connections and interchangeable components?

> “The big idea is **messaging**.” — *Alan Kay*

The Problem with Conventional Architecture
------------------------------------------

In most codebases, data is the center of gravity. "Entities" are exposed directly. "Services" are bloated function bags. Interfaces exist, but mostly for mocking or REST APIs. You often find yourself tracing brittle connections and tightly-coupled chains just to understand how one part talks to another.

This works — until you want to change anything.

## The Analogy: Vehicles vs Hydraulic Systems

### Traditional Software (Vehicles)

A car or truck is a tightly-coupled machine. Each component is bolted into place. If you want to change something — say, the transmission — you pretty much need the *exact part*, or some hacky adapter.

This is like most OOP systems today. Concrete services call concrete repos, and replacing one part often breaks three others. Finding bugs is hard as everything is interdependent or 'complected'.

### Protocol-Driven Development (Hydraulics)

Hydraulic machinery is composed of standardized parts connected by hoses and wires.
You have a power source (engine) a hydraulic pump (transmission) and actuators and cylinders. 
These are connected by simple adapters, hoses, and wires — not precision-fit gears.

The components don’t need to know each other — only how to send and receive **pressure** or **signals** (protocols). If the interface matches, the part just works.

This is what software should feel like.

> PDD isn’t about thinking in objects. It’s about thinking in **hoses and wires**, not bolted connections.

## What is Protocol-Driven Development (PDD)?

**PDD** in this article is a design approach that:
- Models behavior as **explicit message contracts**
- Separates logic from storage, UI, and timing
- Encourages **late binding** and **runtime substitution**
- Composes systems from minimal, focused **protocols**

This isn't “just using interfaces” — it's building your system around them.

## Quotes That Shaped This Way of Thinking

> “The key in making great and growable systems is much more to design how its modules communicate rather than what their internal properties and behaviors should be.”  
> — *Alan Kay*

> “OOP to me means only messaging, local retention and protection and hiding of state-process, and extreme late-binding of all things.”  
> — *Alan Kay*

> “Complexity is anything related to the structure of a system that makes it hard to understand or change.”  
> — *Rich Hickey*

## So why is it diffent?

### The Aha! Moment
I have been programming since around 1999/2000 where I started automating AutoCAD (v's 14/ & 2000) with VBA and AutoLisp. I thought I had grokked most paradigms having read many books, listened to and watched many talks and programmed in many languages from Assembly to scripting languages like Python. I have used much of this advice, principles (SOLID et al.) and design patterns but I was never happy with the finished product or how I got there. It worked, but it always seemed 'forced' and complex once you get to a certain sized codebase.

The penny finally dropped while working on a new personal project inspired by that original idea — a modern reimagining of message-based programming (ala SmallTalk), grounded in a practical way to reuse FFI's, scripting and create dynamic application structure.
I re-watched the talks by Alan Kay, Dan Ingalls et al. on SmallTalk, Rich Hickey's talk 'Simple Made Easy', re-read the SmallTalk spec's and even did some coding again in SmallTalk to get a better feel for what/how I wanted my project to be.
I finally grokked what Alan Kay was talking about, he was talking about objects like remote computers and the message (protocol) is how they talk to each other.

## Example: Protocols in Go

```go
type Repository[T any] interface {
    Save(t *T) (int64, error)
    Get(id int64) (*T, error)
    Delete(id int64) error
    List() ([]*T, error)
}

type Entity struct {
    ID   int64
    Name string
}

type EntityRepository interface {
    Repository[Entity]
}

type EntityManager struct {
    Repo EntityRepository
}

func (m *EntityManager) Rename(id int64, newName string) error {
    entity, err := m.Repo.Get(id)
    if err != nil {
        return err
    }
    entity.Name = newName
    _, err = m.Repo.Save(entity)
    return err
}
```

It compiles — and the system behaves — even if the repo is just a stub. That's the power of message-first design.

**Optional:** DI Without the Framework, just implement the protocol into a concrete form.

```go
repo := &InMemoryEntityRepo{}
manager := &EntityManager{Repo: repo}
manager.Rename(1, "New Name")
```

The behavior is plugged in via protocol — not dictated by framework glue.
The above code looks pretty much idiomatic and familiar, but it's 'composable' not complect, and very test-able.

## This Isn’t New — Just Rediscovered
Much of this thinking by Alan Kay et al. was present in Smalltalk and systems like ARPANET. Message-passing, autonomy, and protocol-based coordination was the crux of the vision. Smalltalk was never the end — it was the prototype.

OO languages sort of got the concept of encapsulation (black box objects), interfaces (protocols) and polymorphism (protocol implementations) but it was presented as 'Is A' and 'Has A'. If a class inherited from a parent class, it was the parent 'type', at least for polymorphism, but it was still all tightly coupled and wouldn't compile if it wasn't 'just so'.

It's all early bound (bolted connections) whereas SmallTalk is very late bound allowing for 'Just In Time' execution, you can even edit and swap in/out components within the running process, pretty cool!

Interfaces are touted as a de-coupling solution or class contract for polymorphism, and they work, but with a deeper understanding of how to use them as protocols they become much more powerful!

## What About Static vs Dynamic Typing:
Static types, while they can save a lot of headaches, they are a necessary evil of OO and other languages that are statically compiled. The tight coupling of the components/classes almost dictates that you use them if you don't want any surprises, especially in larger code bases. 
P-DD style programming can deal with most of the problems caused by using dynamic types simply by using protocols. Bugs are controlled by the boundaries of the component and the 'message' should be clear as to what you want when you ask.

Did I mention testing is easier?

## Final Thoughts
Basically, if your system can talk to itself through protocols, it can change itself through composition.

### Start with:
- Define you top level Components/Domains
- How do they **talk to each other**
- One message at a time
- One protocol per behavior
- Compose bigger protocols from a lot of small ones to create a larger Domain/Component protocol
- Concrete implementations later, just stub them out and review the message pipelines.

I find starting with the main Components or Domains and work out the questions/messages I need to ask of a component 'top down' to discover the protocols for each. 
Create the protocols then implement the concrete objects.

**Welcome to Protocol-Driven Development**, that's it!



---

## References/Inspirations

Alan Kay — "The Early History of Smalltalk"

Alan Kay — "OOP and Its Relationship to Messaging"

Rich Hickey — "Simple Made Easy" (thanks Rich for the word education - 'complect" is my new goto for complicated things :) )

Dan Ingalls — Design Principles Behind Smalltalk

Gary Bernhardt — Boundaries (talk)


---

### 💬 Contribute or Discuss

If you find this idea interesting or useful:
- Open an [Issue](https://github.com/MickDuprez/Protocol-Driven-Development/issues) to ask a question or propose a clarification
- Submit a Pull Request to refine phrasing or examples
- Star the repo to bookmark and follow its evolution

[Discuss on Hacker News](https://news.ycombinator.com/)

Thanks for reading!

Copyright (c) August, 2025 Mick Duprez

Licensed under the [MIT License](LICENSE)
