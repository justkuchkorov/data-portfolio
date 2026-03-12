---
title: 'Active Suspension HIL Simulation (Python + CODESYS)'
description: 'How I built a Hardware-in-the-Loop testbench from scratch to bridge Python physics with an industrial SoftPLC.'
pubDate: '12 March 2026'
heroImage: '../../assets/blog-placeholder-1.jpg'
---




### The Spark: Why I Even Started This?
Actually, since we have been starting to study in IPC, which stands for *Industrial Process Control*, I realized that we are actually studying the **“brains”** of Industries. Have you ever thought about how conveyor belt is running? How is the sensors working with robots simultaneously? What is that connection? Frankly speaking, I am still searching the answers for that question, and I hope I am on the right track ;)




Let’s now speak about that ***Active Suspension Project***. At that time, the beginning of new year 2026, I was searching a topic for my thesis work. (Because I am already senior year student and we have to work on a thesis work to graduate.) I don’t wanna say myself as a car-guy, but I am so addicted to ***F1***. My one of dreams and targets is to work on F1 teams, especially *AMG Mercedes Petronas F1 Team*. I found my area for my possible projects, it is Automation + Automotive Engineering. And I decided to stop on Active Suspension. So, it happened like that.    




### Down the Rabbit Hole
First of all, I started to search the definition of Active Suspension. Actually, what is that? Which part of car uses that thing? So, I dived into researching part. `Active Suspension - is an advanced automotive system that uses sensors, computers, and actuators to independently control each wheel's vertical movement in real-time.` That is a real definition. But is it understandable? I really make it easy to understand like that look back in Uzbekistan, we had a lots of potholed, bumpy roads, so, this system should not make people inside the car feel this bumpy road, meaning people should not feel that way. So, I got the definition.




I started researching how to physically simulate this without spending $10,000 on automotive testbench hardware. I realized I had to bridge the IT and OT worlds. To build a true Hardware-in-the-Loop simulation on a student budget, I narrowed the architecture down to three core pillars: **Python** (for the physics plant), **CODESYS SoftPLC** (for the industrial controller), and a high-speed protocol to link them.


### Modeling the Plant
Let’s talk about Python which should have played a role of physics of a real car. But of course, my python physics contains lots of easy stuff rather than a real physics of car.




\- **The Quarter-Car Mass Model** -
<br>I took the quarter-car mass model. It means i did not stimulate 2,000kg car, I divided the car into 4 part corners (300kg per corner). So, the code: <br>`mass = 300.0`




\- **Stochastic and Deterministic Road Disturbances** -
<br>Next important thing is the road, the physics of road. It shouldn’t be flat road, instead of it, it should be kinda bumpy, as you know. I used two different type of mathematical noises: a) *sine wave*, which defines continues changes in elevation (low-frequnecy disturbance), b) *random int*, which represents sudden potholes or rocks (high-frequency stochastic noise). So, my controller (brain) does not encounter smooth road only. The code: <br>`road_height = 50 + (15 * math.sin(time_step * 0.5)) + random.randint(-2, 2)`




\- **Hooke’s Law** -
<br>I used the simplified version of Hooke’s Law. This one helps to calculate the distance between the road and the chassis. With that one, we can find the how much the passive spring pushes back. It is like natural bounce of car before PLC gets involved. The code: <br>`spring_force = (road_height - car_height) * 10`




\- **Newtonian Kinematics (Euler Integration)** -
<br>This is Newton’s Second Law (`F=ma`). I get the total force (the natural spring + PLC’s actuator push) and divide it by 300kg mass to get the acceleration. I then add that acceleration to the car's current velocity. This means the car has real momentum, it doesn't just teleport to a new height, so,  it has to physically accelerate there. The code: <br>`velocity = velocity + (total_force / mass)`




### The "Aha!" Moment
What was the turning point? When you finally figured out how to use Modbus TCP/IP to serialize the data between the IT and OT environments, how did you do it?
*(Drop a snippet of your Modbus code here to prove it).*




### The Final Architecture (How it actually works)
Now you drop the hammer. Explain the final setup simply:
- **The Plant:** Python generating the road potholes.
- **The Controller:** The PLC calculating the counter-force.
- **The Result:** We hit <20ms latency and <1% error margin.


<a href="https://github.com/justkuchkorov/active-suspension-hil" target="_blank">
  <button style="padding: 10px 20px; background: #007bff; color: white; border: none; border-radius: 5px; cursor: pointer; margin-block: 20px; font-weight: bold;">
    View the Messy Source Code on GitHub
  </button>
</a>

<hr style="margin: 1rem 0; border: none; border-top: 1px solid #e2e8f0;" />

<div style="display: flex; gap: 1rem; align-items: center; justify-content: flex-start; margin-bottom: 1rem;">
  <a href="/projects" style="padding: 10px 20px; background: rgba(59, 130, 246, 0.1); color: #3b82f6; border-radius: 8px; font-weight: 600; text-decoration: none; transition: background 0.2s;">← Back to Projects</a>
  <a href="/" style="padding: 10px 20px; background: rgba(59, 130, 246, 0.1); color: #3b82f6; border-radius: 8px; font-weight: 600; text-decoration: none; transition: background 0.2s;">Home Page</a>
</div>