---
date: 2025-09-09
type: "projects"
title: 'Unity Game Project - Game Programming'
tags: ["IM3180", "Game Dev", "Unity", "C#"]
---

# Preface - Things to Know Before Writing Any Scripts

- `void Start()` function is called once when the gameObject is first initialised or when the scene starts

- `void update()` function is called every frame (60 times per second if we are doing 60 FPS). This is done for anything that needs continuous checking like player inputs or playing animations

## 1. Moving Our Character
First, we initialize a **_Rigidbody2D_** variable as _rb_. Then, in the `void Start()` class, we do:
```text
rb = GetComponent<Rigidbody2D>()
```

This is to assign the _rb_ component to the player _GameObject_, which is why the script is attached to the player in the first place. We do this in the `void Start()` function because we only want to get the component once and never change it again.

{{< figure src="https://i.imgur.com/eb9bbyv.png" alt="RigidBodyComponent" class="center" >}}  

Unfortunately, this is not enough to move the player.  
We need to then declare a private float variable, _xAxis_. Then, we create a void function, **_GetInputs()_**.
Void functions do not return anything, but serve as methods that will be called when a player inputs anything. Thus, next we write inside the **_GetInputs()_** method:
```text
void GetInputs() {
    xAxis = Input.GetAxisRaw("Horizontal");
}
```
All this does is:
- Return 1 if player presses right (D or right arrow)
- Return -1 if player presses left (A or left arrow)
- Return 0 if player does not press anything

And stores them into the _xAxis_ float variable.
After this, we create a function `private void Move()` and insert:
```text
private void Move() {
    rb.linearVelocity = new Vector2(walkSpeed * xAxis, rb.linearVelocity.y);
    ...
}
```
_rb_ refers to the _RigidBody2D_ component. So, _rb.linearVelocity_ means the linear velocity of the rb which is our player. This will equal the RHS of the equation.

Let’s breakdown the RHS:

- _new Vector2D()_: This basically means we are initialising a new fresh _Vector2D_ object with values every time **_Move()_** is called, which is why we put it inside the **_Update()_** function (refer to top)

> But what is _Vector2D_? We all took Physics in our 1st Year, do you remember what is a vector? 

- Unity basically stores the x and y components in a “thing” called Vector2D. We are essentially telling the script to store the velocity component of our inputs to the _rb.linearVelocity_ which will be updated every frame. For example: 
- - (5, 0) means moving to the right  
- - (-5, 0) means moving to the left  
- - (0, -10) means moving straight down  

- Inside the _Vector2D()_, _xAxis * walkSpeed_ will give us the speed and direction of the player.

- Why is _rb.linearVelocity.y_ not changed? This is because we already have a gravity function in the RigidBody2D component inside unity:
- {{< figure src= "https://i.imgur.com/CMsQkHN.png" alt="Gravity" class="center" >}}
- ...so we can ignore it for now.

Finally, we put the **_Move()_** function inside **_Update()_**, and our player _GameObject_ can now move when we press our inputs!

{{< figure src="https://i.imgur.com/W2Qntx2.gif" alt="moving" class="center">}}