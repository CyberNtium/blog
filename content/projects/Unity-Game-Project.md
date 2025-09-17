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


## 2. Jumping

### 2.1 Basic Jump() Concept

To make our codes easy to understand, it’s important to keep track of what state the player is at. If we initialise a new state every time a function is called, it is not a very efficient code.  
Therefore, let’s make a new script called `PlayerStateList` to keep track of what state the player is at according to user inputs:

```C#
public class PlayerStateList : MonoBehaviour 
{
    public bool jumping = False; //Don't forget this is in the PlayerStateList script
}
```
Let jumping to be false by default since the player should not be jumping at the start of the game for no reason. Then, we need to reference this to the player controller file in the Start function:

```C#
void Start ()
{
    pState = GetComponent<PlayerStateList>();
    rb = GetComponent<Rigidbody2D>(); // From Move()
}
```

`pState` stands for player state, this line looks for the component PlayerStateList which is the script we made just now. Now go back to Unity and drag the PlayerStateList script onto the Player gameObject.  

Let's talk about the actual jump function now:  

```C#
if (Input.GetButtonUp("Jump") && rb.linearVelocity.y > 0)
{
    rb.linearVelocity = new Vector2(rb.linearVelocity.x, 0);
    pState.jumping = false;
}
```
This is the first part of `Jump()`:  
- `Input.GetButtonUp(“Jump”)` basically means the moment immediately after you pressed and RELEASE the “Jump” button (in this case is Spacebar)
- `rb.linearVelocity.y` checks if the rigidbody of Player is still moving upwards
- `rb.linearVelocity = new Vector2(rb.linearVelocity.x, 0)` 
- This creates a new Vector2 with:
- - `rb.linearVelocity.x`, this is your current horizontal movement, meaning you keep all your momentum in the X-axis, that’s why you can jump left and right.
Set your Y component to zero. This means that you kill upwards velocity
- - This ultimately means that you stop going higher the moment you release the “Jump” button.
- Finally, we set `pState.jumping = false` because we have in fact stopped jumping after releasing the “Jump” button  

And that’s basically how the main `Jump()` would work in an ideal scenario. 

### 2.2 Grounded() 
Let's go through the `Grounded()` function:
```C# 
public bool Grounded()
{
    if (Physics2D.Raycast(groundCheckPoint.position, Vector2.down, groundCheckY, whatIsGround)
        || Physics2D.Raycast(groundCheckPoint.position + new Vector3(groundCheckX, 0, 0), Vector2.down, groundCheckY, whatIsGround)
        || Physics2D.Raycast(groundCheckPoint.position + new Vector3(-groundCheckX, 0, 0), Vector2.down, groundCheckY, whatIsGround))
    {
        return true;
    }
    else
    {
        return false;
    }
}
```

We declare a public method called `Grounded()` that returns a boolean value, true or false.  

Then, let’s explain what is `Physics2D.Raycast`:

The general formula or format for this function is as follows:

**_Physics2D.Raycast(origin, direction, distance, layerMask)_**  

Physics2D.Raycast function shoots an invisible ray according to your configuration and reports if the ray hits something in game. You can imagine it as a sensor in the form of a layer pointer.
- origin = a vector2 that specifies the starting point of your Raycast
- direction = a vector 2 that defines the angle/path of your Raycast. Is it up or down or 45 degrees up and so on
- distance = this is a float that we can customise for the maximum length of the Raycast which it can travel before cutting off
- layerMask = this is an integer that filters which gameObject the Raycast can see. If you look at top right when you have a gameObject selected you can see:

- {{< figure src="https://i.imgur.com/wxyICVd.png" alt="layerMask" class="center" >}}

- Layer = Ground, which we have set it to be since it’s the floor  

Now, let's go back to our orignal Raycast:

`Physics2D.Raycast(groundCheckPoint.position, Vector2.down, groundCheckY, whatIsGround)`  

These are some new variables, so let’s initialise them above:  
```C#
[Header("Ground Check Settings:")]
[SerializeField] private Transform groundCheckPoint;
[SerializeField] private float groundCheckY = 0.2f;
[SerializeField] private float groundCheckX = 0.5f;
[SerializeField] private LayerMask whatIsGround;
```
- `groundCheckPoint` is declared as a public field under your PlayerController, thus we can link it with an empty gameObject as a child under the Player gameObject and drag it to be at our player’s feet or most bottom position like so:
- {{< figure src="https://i.imgur.com/FVMlFVB.png" alt="stuff" class="center" >}}

- And then finally, we drag the empty gameObject “Ground Check Point” to the public field that we declared just now in PlayerController:
- {{< figure src="https://i.imgur.com/OSsRWgM.png" alt="text" class="center" >}}

- groundCheckY is declared as a float type, that represents the length of the Raycast
- groundCheckX is the offset of the Raycast in the horizontal axis, which will be explained later
- whatIsGround is basically checking what type of layer what we want our Raycast is hitting, in this case will be ground, which we need to indicate in Unity when saving the script:

- {{< figure src="https://i.imgur.com/opLxWSs.png" alt="layerMask" class="center" >}}
- After everything here, let’s go back to the main Ground() function:

```C#
public bool Grounded()
{
    if (Physics2D.Raycast(groundCheckPoint.position, Vector2.down, groundCheckY, whatIsGround)
        || Physics2D.Raycast(groundCheckPoint.position + new Vector3(groundCheckX, 0, 0), Vector2.down, groundCheckY, whatIsGround)
        || Physics2D.Raycast(groundCheckPoint.position + new Vector3(-groundCheckX, 0, 0), Vector2.down, groundCheckY, whatIsGround))
    {
        return true;
    }
    else
    {
        return false;
    }
}
```
Inside the if statement, we are checking if:
- groundCheckPoint.position = position at our feet
- Vector2.down = downward direction
- groundCheckY = length of 0.2f
- whatIsGround = checking of gameObjects tagged with “Ground”

OR:
- groundCheckPoint.position + new Vector3(groundCheckX, 0, 0) …

AND:
- groundCheckPoint.position + new Vector3(-groundCheckX, 0, 0) …

What the heck are these two ORs inside the if statement:  
Starting at the original place at the Player’s feet, we do an offset of groundCheckX = 0.5 and -0.5 to ensure that even if the player is a bit off the ground, the player will still be considered grounded. A simple illustration is this:
{{< figure src="https://i.imgur.com/x48n7MP.jpeg" alt="raycast" class="center" >}}

> Another question is why use Vector3 for the offsets?

Well, Unity’s Transform has always been in 3D even if the game we are developing is in 2D. Thus, when we use something.position, the .position returns a Vector3 component, so if we just do `something.position + new Vector2(groundCheckX, 0, 0)`, this will give us an error because a Vector3 cannot be added to a Vector2 directly. 

### 2.3 UpdateJumpVariables()

Let's initialise some variables first:
```C#
[Header("Vertical Movement Settings:")]
[SerializeField] private float jumpForce = 45;
private int jumpBufferCounter = 0;
[SerializeField] private int jumpBufferFrames;
prviate float coyoteTimeCounter = 0;
[SerializeField] private float coyoteTime;
private int airJumpCounter = 0;
[SerializeField] prviate int maxAirJump;
```
- jumpForce, a float type that is serializable in Unity is basically the strength of our player’s jump
- jumpBufferCounter is an int type that is used in the jump buffer logic. It is set as zero at start. Explained later
- jumpBufferFrames is a serializable int type that can be adjusted in game which defines how long the jump buffer would last. Explained later.
- coyoteTimeCounter is another int type that is used to track coyote time. Not serializable.
- coyoteTime is a serializable type that represents the coyote time and how long it lasts. Explained later
- airJumpCounter is an int that functions as a counter that tracks how many times the player has jumped. Not serializable.
- maxAirJump, serializable, the total amount of jumps the player is allowed to make. Explained later.

Let’s start with the UpdateJumpVariable() function:  
```C#
void updateJumpVariables() {
    if (Grounded()) 
    {
        p.State.jumping = false;
        coyoteTimeCounter = coyoteTime;
        airJumpCounter = 0;
    }
    else {
        coyoteTimeCounter -= Time.deltaTime;
    }

    if (Input.GetButtonDown("Jump")) {
        jumpBufferCounter = jumpBufferFrames;
    }
    else {
        jumpBufferCounter --;
    }
}
```
- The first if statement checks if the player is Grounded() or touching the ground (explained just now). If the player is touching the ground, we set the jumping player state to be false.   
We also set the coyoteTimeCounter to be assigned to the coyoteTime we set in Unity, or another way of saying, reset the coyoteTimeCounter. We also reset our airJumpCounter back to 0 so that players can double jump again after landing on ground.  

- If the player is not touching ground however, we set `coyoteTimeCounter -= Time.deltaTime`. This basically means that coyoteTimeCounter continuously counts down by subtracting its value by time.deltaTime which is the seconds since the last frame until coyoteTime runs out.

- The next if statement checks if the “Jump” button was pressed at a particular point of time. Then we set jumpBufferCounter to be equal to jumpBufferFrames, meaning resetting the jump buffer counter to its max value. Thus, when you land soon, you will still jump automatically.

- The else statement here checks if the “Jump” button was not pressed in this frame, if so, it decreases the jump buffer counter continuously eventually reaching zero and “forgets” the jump input.

### 2.4 Final Jump() Method

```C#
void Jump() {

    if (Input.GetButtonUp("Jump") &* rb.linearVelocity.y > 0) {

        rb.linearVelocity = new Vector2(rb.linearVelocity.x, 0);

        pState.jumping = false;

    }

    if (!pState.jumping) {

        if (jumpBufferCounter > 0 && coyoteTimeCounter > 0) {

            rb.linearVelocity = new Vector2(rb.linearVelocity.x, jumpForce);

            pState.jumping = true;
        }
        else if (!Grounded() && airJumpCounter < maxAirJump && Input.GetButton("Jump")) {

            pState.jumping = true;

            airJumpCounter ++;

            rb.linearVelocity = new Vector2(rb.linearVelocity.x, jumpForce);
        }
    }
}
```
The first if statement was already explained in section 2.1, the rest implements all the other components into our Jump() method.  

The 2nd if statement checks if the “Jump” has been pressed, then we run everything inside the block, which consists of nested statements:
- 1st IF: this checks if the player pressed the “jump” button within the last frames. Also, it checks if the coyote time is above zero
- If both these conditions check out, we do: 
- - `rb.linearVelocity = new Vector2(rb.linearVelocityX, jumpForce);`
- - We set our player’s rigidBody linearVelocity to a new Vector2 like in the very first IF statement: for x component, since we want to keep the horizontal movement, it's the same. But for the Y component, we set it to be jumpForce which we have initialised earlier.
- - We set the player jump state to be True to prevent spamming the button.
- - Finally we set the jumpBufferCounter to be zero to prevent another jump to be triggered.

- ELSE IF statement: This checks if the player is not ground and the player has not reached their max air jumps:
- - If all these conditions are true:
- - We set player jumping state to be True
- - We increase the air jump counter by 1
- - Finally, we set the player rigidBody linearVelocity to a new Vector2:
- - - Where we keep the same horizontal movement, but set the vertical Y component to the jumpForce.

