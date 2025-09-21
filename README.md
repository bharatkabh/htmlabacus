# htmlabacus
html abacus mobile firendly

That line of code is the "brain" of the abacus, responsible for reading the beads and calculating the final number. Let's break it down into simple, understandable parts.


const updateCalculation = () => { let t=0n;abacusFrame.querySelectorAll('.rod').forEach((r,i)=>{let v=0;r.querySelectorAll('.bead.active').forEach(b=>v+=parseInt(b.dataset.value));t+=BigInt(v)*(10n**BigInt(NUM_RODS-1-i))});resultDisplay.textContent=t.toLocaleString()};



In short, that expression reads the position of all the 'active' beads on the abacus, calculates the total number they represent by considering each rod's place value, and displays the final result with commas.

How It Works, Step-by-Step
Here’s a detailed look at each piece of the code.

1. let t = 0n;
This initializes the grand total.

let t: Declares a variable named t (for total).

= 0n: Sets its starting value to zero. The n at the end is crucial—it makes the number a BigInt (Big Integer). This is used so the abacus can handle extremely large numbers (like trillions or more) without running into JavaScript's normal number limits. 

2. abacusFrame.querySelectorAll('.rod').forEach((r, i) => { ... });
This is the main loop that processes the abacus rod by rod, from left to right.

abacusFrame.querySelectorAll('.rod'): This selects all 13 rod elements from the HTML.

.forEach((r, i) => { ... }): This method runs a function for each rod it found.

r: Represents the current rod element being processed in the loop.

i: Represents the index (position) of that rod, from 0 (leftmost) to 12 (rightmost). This index is essential for the next steps.

3. let v = 0; r.querySelectorAll('.bead.active').forEach(...)
Inside the main loop, this part calculates the value of the single rod currently being examined.

let v = 0;: Initializes a variable v (for value) to zero. This will hold the value of just one rod (a number from 0 to 9).

r.querySelectorAll('.bead.active'): This is the key part. It looks only within the current rod (r) and finds all the bead elements that have the class .active (meaning they have been pushed to the center bar to be counted).

.forEach(b => v += parseInt(b.dataset.value)): It loops through each of those active beads.

b: Represents the current active bead.

b.dataset.value: Reads the bead's value (either '5' for a heaven bead or '1' for an earth bead) from its HTML data-value attribute.

v += ...: It adds this value to v. For example, if the heaven bead (5) and two earth beads (1+1) are active on a rod, v will become 7.

4. t += BigInt(v) * (10n ** BigInt(NUM_RODS - 1 - i))
This is the most important calculation. It takes the simple value of the rod (like 7) and gives it the correct place value (turning it into 7, 70, 700, etc.) before adding it to the grand total.

NUM_RODS - 1 - i: This calculates the exponent for the place value.

If NUM_RODS is 13:

For the rightmost rod (the "ones" place), i is 12. The calculation is 13 - 1 - 12 = 0.

For the next rod (the "tens" place), i is 11. The calculation is 13 - 1 - 11 = 1.

...and so on.

10n ** ...: This is the exponentiation operator (**). It calculates 10 to the power of the number figured out above. For the "ones" place it's 10^0 = 1. For the "tens" place it's 10^1 = 10, and so on.

BigInt(v) * ...: It multiplies the rod's value (v) by its calculated place value. If the rod's value is 7 and its place value is 100, the result is 700.

t += ...: It adds this result to the grand total, t.

5. resultDisplay.textContent = t.toLocaleString()
After the loop has finished processing all the rods, this final part displays the result.

resultDisplay: This is the text box element at the top of the abacus.

.textContent = ...: This sets the text that appears inside the box.

t.toLocaleString(): This is a handy built-in function that converts the final BigInt total into a human-readable string, automatically adding commas where needed (e.g., 1234567 becomes 1,234,567).









=====================================================================================


 const handleBeadTap = (b)=>{if(tutorial)return;if(b.classList.contains('heaven-bead')){b.classList.toggle('active')}else{const r=b.closest('.rod'),bs=Array.from(r.querySelectorAll('.earth-bead')),x=parseInt(b.dataset.index);if(b.classList.contains('active')){if(!bs[x+1]||!bs[x+1].classList.contains('active'))b.classList.remove('active')}else{if(!bs[x-1]||bs[x-1].classList.contains('active'))b.classList.add('active')}}updateCalculation()};
            abacusFrame.addEventListener('touchstart',(e)=>{if(e.target.classList.contains('bead')){e.preventDefault();const b=e.target;if(tutorial)return;b.classList.add('highlight');const end=()=>{b.classList.remove('highlight');handleBeadTap(b);b.removeEventListener('touchend',end);b.removeEventListener('touchcancel',end)};b.addEventListener('touchend',end);b.addEventListener('touchcancel',end)}});








Those two expressions work together to create the complete touch interaction for the abacus beads. The first is the "brain" that decides if a move is valid, and the second is the "nervous system" that detects the touch and triggers the brain.

Let's break them down.

1. The handleBeadTap Function (The "Brain")
JavaScript

const handleBeadTap = (b) => { ... };
This function is the core logic that runs after a bead has been tapped. Its job is to enforce the physical rules of the abacus, ensuring beads can't jump over each other.

Here's a step-by-step explanation:

if(tutorial) return;
This is a safety check. If a tutorial is active, the function stops immediately. This prevents you from manually moving beads during a lesson.

if(b.classList.contains('heaven-bead'))
It first checks if the tapped bead (b) is a top "heaven bead" (value 5).

If it is, it simply toggles its .active class (b.classList.toggle('active')). Since there's only one heaven bead per rod, it doesn't need to check for collisions.

else { ... }
If the bead is a bottom "earth bead", the logic is more complex.

const x = parseInt(b.dataset.index);: It gets the bead's position on the rod (0 is the top, 3 is the bottom).

const bs = Array.from(...): It gets a list of all 4 earth beads on the same rod.

Collision Rules 

If you tap an ACTIVE bead (to move it DOWN):
if(!bs[x+1] || !bs[x+1].classList.contains('active'))
The code checks the bead directly below it (bs[x+1]). The move is only allowed if there is no bead below it OR the bead below is inactive. This correctly simulates that you must clear beads from the bottom up.

If you tap an INACTIVE bead (to move it UP):
if(!bs[x-1] || bs[x-1].classList.contains('active'))
The code checks the bead directly above it (bs[x-1]). The move is only allowed if there is no bead above it OR the bead above is already active. This simulates that you must push beads up from the top down.

updateCalculation()
Finally, after any valid move, this function is called to update the number in the result box.

2. The addEventListener Function (The "Nervous System")
JavaScript

abacusFrame.addEventListener('touchstart', (e) => { ... });
This function listens for the physical touch on the screen. It's responsible for the immediate visual feedback (highlighting) and for telling the handleBeadTap function to run when the touch is released.

Here's the sequence of events:

abacusFrame.addEventListener('touchstart', ...)
It attaches a single listener to the entire abacus frame. The moment your finger makes contact with the screen inside the frame, this event fires.

if(e.target.classList.contains('bead'))
It checks if the item you touched was actually a bead. If you touch the frame or a rod, it ignores the event.

e.preventDefault();
This stops the web page from scrolling or zooming when you interact with a bead, ensuring a smooth app-like experience.

b.classList.add('highlight');
This provides instant feedback. The moment you touch a bead, it gets the .highlight class, which makes it scale up slightly. This confirms your touch was registered. 👆

b.addEventListener('touchend', end);
After highlighting, it immediately starts listening for when your finger is lifted from the screen (touchend).

const end = () => { ... }
When your finger is finally lifted, this end function runs. It does two things:

It removes the highlight (b.classList.remove('highlight')).

It calls the "brain" function, handleBeadTap(b), to execute the actual move logic we discussed above.
